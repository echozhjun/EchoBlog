---
layout: post
title: "为 Jekyll 装上瑞士军刀 Pandoc"
category: "Better Work In Linux"
tags: [jekyll, pandoc]
date: 2012-04-15 01:12
---
{% include JB/setup %}

Jekyll 原生支持 `maruku`，`rdiscount`，`kramdown`，`redcarpet` 等 markdwon 
渲染器，它们各有利弊。个人认为 maruku 虽然是 Jekyll 的默认选择，却是最不适合
中文博客的，因为它对中文列表的支持不好。rdiscount 算是中归中举，但对于
[MathJax][] 的用户来说却比较头疼，容易出诡异的问题。github 在后台则选用了
redcarpet 作为其文本渲染器，因为它安全性高且性能卓越，
同时它在基本 markdown 语法 的基础上增加了一些自己的特性。
kramdown 是这几个当中对基础 markdown 语法拓展最多，
也是最方便使用的，但跟这篇文章的主角 [Pandoc][] 来比，它就相形见绌了。

   [MathJax]: http://www.mathjax.org
   [Pandoc]: http://johnmacfarlane.net/pandoc

首先介绍一下 [Pandoc][]，它的功能是在多种常见的标记语言进行相互转换，
其中包括 markdown, reStructuredText, textilte, HTML, $\LaTeX$ 等。
利用它，你可以用简单的 markdown 语法生成与 $\LaTeX$ 一致的文档，还可以写
beamer 演示文稿。 更强大的是，它还能将以上提到的这些语言所写文件转换成 *docx*
文档，这对我们这些体制内码农很是实用。
下面这张图展示了 Pandoc 让人吃惊 的功能集，不愧为文件转换中的瑞士军刀：

<img src="http://johnmacfarlane.net/pandoc/diagram.png" alt="pandoc is awsome!" width="650"/>

对于用 Jekyll 写博客的人来说它还有以下优点：

* 支持在 markdown 文档中直接使用 $\LaTeX$
公式，最终生成的网页更是可以选择以图片、MathJax、jsMath 等等方式显示公式
* 支持代码高亮，并自带多种高亮方案可选。

虽然这两个功能都可以通过设置 Jekyll 实现，但由于我对 Pandoc 已经欲罢不能，
其它的渲染器都再也看不上眼了。下面我就来介绍如何让 Jekyll 使用 Pandoc 作为
markdown 渲染器。

------------------------------------

首先需要说明的是，由于 GithubPages 并不支持 pandoc，所以就不能将包含 markdown
日志等 Jekyll 文件夹直接放到 Github 库上指望 Github 帮我们渲染它们，
而是需要在本地将站点完全生成好后再将它送到某一库里。
有人可能觉得麻烦，但对我 来说却是一个优点，因为这样做回使 GithubPages 更新速度大大加快，
不会出现今晚提交，明早才被更新好的情况。

先要安装 pandoc，具体过程请参考：[安装 GHC，Cabal/Cabal-install 及 Pandoc][post]

   [post]:/Better%20Work%20In%20Linux/2012/03/31/pandoc-cabal


如果本机上已安装了 Jekyll 则需将其删除，因为下面要使用修改过的 Jekyll。

    (sudo) gem uninstall jekyll

如果本机还未安装 Jekyll，大概你也没装 ruby 了，那么请执行：
    
    (sudo) yum install ruby ruby-devel rubygems
    (sudo) gem install liquid classifier directory_watcher maruku kramdown albino 

下载修改过的 jekyll，它已被修改成 Pandoc 友好型 :)

    git clone git@github.com:yangzetian/jekyll

([这里][dsanson-jekyll] 有 [dsanson][] 的版本，作者称可以解决 GithubPages 不支持 Pandoc
的问题，不过我试了一晚愣是没搞定，所以自己写了一个。如果你用这个版本成功了，请告诉我。)

   [dsanson-jekyll]: https://github.com/dsanson/jekyll
   [dsanson]: http://www.davidsanson.com/

安装 pandoc-ruby，一个 pandoc 的 ruby 接口：

    (sudo) gem install pandoc-ruby

安装 jekyll：
    
    gem build jekyll.gemspec
    (sudo) gem install -l jekyll-0.11.2.gem

修改 jekyll 配置文件 `_config.yml` 如下：

    markdown: pandoc
    pandoc:
        extensions: [mathjax, standalone]

上面的 `extensions` 并不唯一，可以将任何 Pandoc 支持的选项加入其中。

好了，现在我们博客中所有的 markdown 文件已经是由 pandoc 渲染的了！

------------------------------------------

下面是 pandoc 演示环节

从

    $e^x = \sum_{n=0}^\infty \frac{x^n}{n!} = \lim_{n\rightarrow\infty} (1+x/n)^n$

到

$e^x = \sum_{n=0}^\infty \frac{x^n}{n!} = \lim_{n\rightarrow\infty} (1+x/n)^n$

从

    ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~ {.python .numberLines}
    class FSM(object):
    
    """This is a Finite State Machine (FSM).
    """
    
    def __init__(self, initial_state, memory=None):

        """This creates the FSM. You set the initial state here. The "memory"
        attribute is any object that you want to pass along to the action
        functions. It is not used by the FSM. For parsing you would
        typically pass a list to be used as a stack. """
         
        # Map (input_symbol, current_state) --> (action, next_state).
        self.state_transitions = {}
        # Map (current_state) --> (action, next_state).
        self.state_transitions_any = {}
        self.default_transition = None
        ...
    ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~ 

到

~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~ {.python .numberLines}
class FSM(object):

"""This is a Finite State Machine (FSM).
"""

def __init__(self, initial_state, memory=None):

    """This creates the FSM. You set the initial state here. The "memory"
    attribute is any object that you want to pass along to the action
    functions. It is not used by the FSM. For parsing you would
    typically pass a list to be used as a stack. """
     
    # Map (input_symbol, current_state) --> (action, next_state).
    self.state_transitions = {}
    # Map (current_state) --> (action, next_state).
    self.state_transitions_any = {}
    self.default_transition = None
    ...
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~ 

----------------------------------------

### 参考

   [dsanson 的博客](http://www.davidsanson.com)
