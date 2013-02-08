---
layout: post
title: "IPython Notebook: 交互计算新时代"
date: 2013-02-08 10:05
category: Python
tags: [interactive computing, ipython, notebook]
---
{% include JB/setup %}

在使用 Python/IPython, R, Matlab 等工具进行探索性数据分析时，你是否经常会遇到以下情景：

* 在命令行中输入了许多语句后，希望将其部分或全部保存至一个脚本文件中以便记录计算过程。
* 脚本运行出错，需要对脚本进行局部修改后继续运行，但不想重新运行之前已执行完的语句。
* 希望将详细计算过程及结果与他人共享，以便与他人讨论数据分析步骤或让他人重复你的结果。 
* 希望在本地的台式机或笔记本上对远程服务器中的数据进行分析，却不想将数据拷贝至本地。

传统的交互计算平台一般都以命令行的方式与数据分析者交互，即用户每输入一条语句，计算平台返回相应的结果。
虽然它们在用户体验方面做了许多努力，诸如自动命令补全，弹出帮助文档，命令历史记录等，但在上文提到的这些情景下，它们就显得有些力不从心。
[IPython Notebook](http://ipython.org/ipython-doc/dev/interactive/htmlnotebook.html) 的出世，正弥补了传统计算平台的不足，为上述情景提供了快捷直观的解决方案。

IPython Notebook 是一种新兴的交互式数据分析与记录工具，它定义了一种全新的计算文件格式，其中包含了代码，代码说明以及每一步的计算输出(数值或图片等)，也就是说这一个文件完整记录了计算过程中的所有相关信息。此外，该文件还可以嵌入网络视频，图片，$LaTeX$ 公式等众多副文本格式，实为交互计算，记录思维，传播思想的好帮手。

<div class=toc>
<ul>
<li><a href="#简介">简介</a></li>
<li><a href="#安装-notebook">安装 Notebook</a></li>
<li><a href="#使用-notebook">使用 Notebook</a><ul>
<li><a href="#本地运行">本地运行</a></li>
<li><a href="#远程运行">远程运行</a></li>
</ul></li>
<li><a href="#共享与应用">共享与应用</a><ul>
<li><a href="#用-ipython-notebook-写博客">用 IPython Notebook 写博客</a></li>
<li><a href="#在教学科研中的应用">在教学科研中的应用</a></li>
</ul></li>
<li><a href="#总结">总结</a></li>
<li><a href="#相关链接">相关链接</a></li>
</ul>
</div>


## 简介

IPython Notebook 既是一个交互计算平台，又是一个记录计算过程的「笔记本」。它由服务端和客户端两部分组成，其中服务端负责代码的解释与计算，而客户端负责与用户进行交互。
服务端可以运行在本机也可以运行在远程服务器，包含负责运算的 IPython kernel (与 [QT Console](http://ipython.org/ipython-doc/dev/interactive/qtconsole.html) 的 kernel 相同) 以及一个 HTTP/S 服务器 ([Tornado](http://www.tornadoweb.org/))。
而客户端则是一个指向服务端地址的浏览器页面，负责接受用户的输入并负责渲染输出。

千万不要小看了这个页面，首先，它几乎涵括了 QT Console 具有的全部交互式功能，如代码高亮，自动补全，实时帮助，内嵌显示绘图结果等；其次，计算过程及结果可以方便地保存为多种格式，如默认的 JSON 格式，Python 脚本以及 PDF 等；此外，借助 [Markdown](http://wowubuntu.com/markdown/basic.html) 及 [MathJax](http://www.mathjax.org)，用户可以在计算过程中插入详尽细致的描述，甚至以描述为主，计算为辅，从而将它当作科技类文章写作的工具。

正是由于 IPython Notebook 的灵活易用，方便传播等特点，它现已被用于可重复数据分析，课程教学，博客写作等众多领域。
当然它还可以应用在更多更广的现实场景中，那些就等着你来探索了。
下文就将介绍如何安装及使用 IPyhton Notebook，并以实例来介绍它的强大特性。

## 安装 Notebook

可以采取以下三种安装方案：

* 安装某个 Python 发行套件。
* 采用系统的包管理程序安装。
* 从源代码编译安装。

推荐选用某个 Python 发行套件，比如 [Enthough Python Distribution (EPD)](https://www.enthought.com/) 或 [Python(x,y)](https://code.google.com/p/pythonxy/)。
这些发行套件中都包含了常用的 Python 科学计算库，如 Numpy，Scipy，IPython，Matplotlib，Pandas 等，而且都是一键安装，非常方便快捷，省去了从源码安装的众多烦恼。下面介绍在 Linux 下安装 EPD。

从 EPD [网站](http://www.enthought.com/products/getepd.php)下载免费版或学术版^[学术版可通过以 .edu 为后缀的邮箱地址申请。]，之后运行下载好的文件：

    bash ped-7.3.2-rh5-x86_64.sh -p /path/to/your/install_dir

一路回车即可。

安装完成后，须将 EPD 的 bin 目录添加至环境变量 PATH 中：

    PATH=/path/to/epd/bin:$PATH
    export PATH

如果不想安装套件，可以用系统自带的管理程序单独安装 IPython，比如在 Arch 下：

    pacman -S ipython

除以上两种方法，还可以选择从源代码安装，但此方案比较复杂，需要考虑 IPython 的复杂依赖关系，如 Numpy，Scipy，Qt 等。
这条方案需慎重选择，因为光是 Numpy，Scipy 的安装就会让人焦头烂额^[可参考我一年前写的文章:[在 fedora 15 下从源代码安装 numpy 与 scipy](http://www.douban.com/note/203828349/)。]。

## 使用 Notebook

根据 IPython Notebook 服务端所在位置，可分为本地运行和远程运行两种情景。

### 本地运行

此种情形下，数据和计算资源均在本地机器上，需要首先在本地安装好 IPython，之后运行服务端：
    
    ipython notebook

或直接进入 pylab 模式：
    
    ipython notebook --pylab

如果想将 Matplotlib 生成的图片潜入网页内显示，则使用：

    ipython notebook --pylab inline

上面的命令会在本机开启 IPython Notebook 服务端，并在系统默认浏览器内打开一个指向服务端的页面，该页面显示了所有保存的 Notebook。
如需新建 Notebook，可以点击 `New Notebook` 按钮，或着将本地的文件直接拖入空白处，其中该文件即可以是编写好的 Python 程序文件 (.py)，也可以是之前运行过的 Notebook 文件 (.ipynb)。

在新打开的 Notebook 页面中，就可以进行各项交互式计算任务。Notebook 由一个个的执行单元组成，每个单元可以是一段 Python 代码，也可以是一段描述性文本，这些文本可以使用 Markdown 语法，也可以插入 $LaTeX$ 表达式，或者直接使用 HTML。
在单元上可以方便地进行删除，复制，粘贴，剪切，插入，分割，合并等操作。

单元需要被运行才能产生实际的运算，其中代码单元内的语句被输入服务端内的 kernerl 进行运算，并从 kernel 内获得输出结果显示在单元内，文本单元则通过 Markdown 渲染器渲染成相应的网页格式展现 (如图1)。

![图1: 一段 Notebook 的运行结果]({{ BASE_PATH }}/assets/images/ipynb.png)

在进行了一系列计算操作之后，会希望将当前的工作保存，这时可以选择 `Save` 或者 `Download`，它们都可以按 .ipynb 或 .py 格式保存 Notebook。
其中 `Save` 默认为 .ipynb 格式，如果希望将其改为 Python 可执行的 .py 格式, 可以在启动时加入 `--script` 后缀：

    ipython notebook --script

`Save` 和 `Download` 的不同在于 `Save` 将文件存储在服务端所在的存储位置，而 `Download` 则是存储在浏览器所在的存储位置。因此，当服务端运行在远程服务器上时，如果希望将 Notebook 存至本地，应使用 `Download`。

此外，还可以按网页或者 PDF 格式将当前的计算过程保存，缺陷是按这种方式进行保存下来的 Notebook 无法重新被载入运行。保存方法为点击 `Print View`，这里会打开一个当前 Notebook 的纯网页版本，这时可以将其又键另存为网页保存，或者打印成 PDF 文件。

### 远程运行

上文已提到多次可以将服务端放置远程服务器运行，这样有多个好处：一是可以不受物理空间的限制，在任何可以连接到服务器的场所办公，二是在客户端无需安装任何计算相关的软件，只需一个浏览器即可，三是可以利用服务器高效庞大的计算资源，实现高性能计算。

IPython Notebook 可以方便地部署在似有服务器或流行的云计算平台 (如 [Amazon EC2](http://aws.amazon.com/ec2/), [Windowns Azure](http://www.windowsazure.com/en-us/)) 上，下面以私有服务器为例，介绍如何与远程服务器实现交互计算。

首先创建一个名为 `nbserver` 的配置文件 ^[除特殊说明，以下操作均在服务器端进行]：

    ipython profile create nbserver

设置登陆密码：

~~~~~~~~~~~~~~ {.python}
IN [1]: from IPython.lib import passwd
IN [2]: passwd()
Enter password:
Verify password:
Out[2]: 'sha1:xxxxxxxxxxxxxxxxxxxxxxxxx'
~~~~~~~~~~~~~~

创建私人证书：

    openssl req -x509 -nodes -days 10000 -newkey rsa:1024 -keyout mycert.pem -out mycert.pem

进入 `nbserver` 配置文件所在目录 ^[~/.config/ipython/profile_nbserver 或 ~/.ipython/profile_nbserver] 并打开 `ipython_notebook_config.py`，设置以下属性：

~~~~~~~~~~~~~ {.python}
c = get_config()

c.IPKernerlApp.pylab = 'inline'
c.NotebookApp.ip = '*'
c.NotebookApp.open_browser = False
c.NotebookApp.password = u'sha1:xxxx your hashed password'
c.Notebook.App.port = 9999   #可设为其他端口
~~~~~~~~~~~~~~

为使 Notebook 正常工作，需在防火墙中打开上面设置的端口。可以编辑 `/etc/sysconfig/iptables`，在最后一条 `REJECT` 语句前加入：

    -A RH-Firewall-1-INPUT -m state --state NEW -m tcp -p tcp --dport 9999 -j ACCEPT
    -A OUTPUT -m state --state NEW -m tcp -p tcp --dport 9999 -j ACCEPT

用下面这条命令运行服务端：

    ipython notebook --profile=nbserver --certfile=/path/to/your/mycert.pem

在本地计算机中打开浏览器，输入
    
    https://ip/of/your/server:9999

即可打开客户端。

关于如何在 Amazon EC2 上搭建 IPython Notebook，可利用 [StarCluster](http://star.mit.edu/cluster/docs/latest/index.html) 提供的插件，或者 [NotebookCloud](https://notebookcloud.appspot.com/)，详情请分别参考[相关链接3和4](#相关链接)。此外，Windows Azure 官方也提供了一个在 Azure 上搭建 Notebook 的教程，见[相关链接5](#相关链接)。

## 共享与应用

使用 IPython Notebook 自定义的 .ipynb 文件可以方便地将自己的计算过程与他人共享，但是必须要求对方也装有 IPython Notebook 才能查看。为了突破这一限制，IPython 官方建立了 [IPython Notebook Viewer](http://nbviewer.ipython.org) 网站，帮助在线渲染 .ipynb 文件。

### 用 IPython Notebook 写博客

借助于 Ipython Notebook Viewer 提供的服务，只需下面一行代码，即可在博客中嵌入显示 Notebook：

    <iframe src="http://nbviewer.ipython.org/gist number/" width="700" height="400"></iframe>

不足之处是需要手工设置宽度与高度以符合页面大小。下面是我编写的有关 Numpy 数组维度操作的一个 Notebook ^[更多有关 Numpy 的技巧可参见我的[技术笔记](http://yangzetian.github.com/Yang-Tech-Notes/python.html)]：

<iframe src="http://nbviewer.ipython.org/4640808/" width="700" height="400"></iframe>

如果对嵌入式的显示方法不满，可以利用 [nbconvert](https://github.com/ipython/nbconvert) 工具将 .ipynb 文件转换成 html 或 Markdown 格式，再放入网页中，详细过程请参考[相关链接6](#相关链接)。

### 在教学科研中的应用

由于 IPython Notebook 的友好易用，已有[公司](http://software-carpentry.org/blog/2012/10/transitioning-to-the-ipython-notebook.html)将其作为教授 Python 编程的新平台，更有[教授](http://ivory.idyll.org)将其作为数据分析课程的课程平台，用它分发，收取作业，取得了不错的效果 (见[相关链接7](#相关链接))。

此外，IPython Notebook 对长久以来困扰科研界的计算结果可重复问题也有所帮助，试想如果研究者被要求将其完整的计算过程以 Notebook 的方式公开出来，结果造假或操控数据的可能性必将大大减小。

## 总结

IPython Notebook 的出现，弥补了传统命令行式计算平台的诸多不足，增强了交互式数据分析的用户体验，为交互计算提供了一全新的实践模式。此外，它所定义的一套计算记录格式大大方便了计算过程的传播与共享，借助于新兴的网页技术更是可以展示多样化的副文本内容，成为连接数据分析与先进网页技术的重要桥梁。由于以上诸多优点，它已被应用与科研与教学等众多现实场景中，再加上背后活跃开发社区的支持，它必定会在新时代下的交互计算领域崭露一片头角。

## 相关链接

1. [An HTML Notebook IPython](http://ipython.org/ipython-doc/dev/interactive/htmlnotebook.html)
2. [[IPython-User] Help troubleshooting notebook as public server](http://python.6.n6.nabble.com/IPython-User-Help-troubleshooting-notebook-as-public-server-td3289467.html)
3. [IPython Cluster Plugin](http://star.mit.edu/cluster/docs/latest/plugins/ipython.html#using-the-ipython-html-notebook)
4. [NotebookCloud Documentation](https://notebookcloud.appspot.com/docs)
5. [IPython Notebook on Windows Azure](http://www.windowsazure.com/en-us/develop/python/tutorials/ipython-notebook/)
6. [Blogging with the IPython notebook](http://blog.fperez.org/2012/09/blogging-with-ipython-notebook.html)
7. [Teaching with ipython notebooks -- a progress report](http://ivory.idyll.org/blog/teaching-with-ipynb-2.html)

