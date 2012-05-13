---
layout: post
title: "用 Glossary 学习 Python (1): 名字空间以作用域"
date: 2012-05-13 11:30
category: Python
tags: [python, glossary, name, binding, namespace, scope]
---
{% include JB/setup %}

在《如何阅读一本书》中作者提到，分析型阅读的第一步应该是弄清楚书中每个术语或专业词汇的语义，如果作者提供了一个词汇表(glossary)，那么就应该先好好将它读一读。我认为这条规则不仅对阅读理解书籍来说很有效，对读代码、文档等技术方面的材料来说也十分适用。因此我想到了最近自己一直在使用的 [Python][] 语言，它的[官方文档][pydoc]中就有一个这样的[词汇表][pyglossary]。在浏览了里面的条目后，我发现这个词汇表及其有用，对许多概念进行了精短的说明，且覆盖了 Python 语言的方方面面。因此我将其作为自己深入学习 Python 的一个入口，并打算写一系列的文章，每次从中挑选出与某一主题相关的词汇，对它们进行学习研究。


最近对 Python 的名字空间与作用域机制很感兴趣，读了一些相关的文章，这里对其作一个小结。

# 词汇

在 Glossary 中与名字空间或作用域相关的词汇有:

> *namespace* : 
> : The place where a variable is stored. Namespces are implemented
> as dictionaries. There are local, global and built-in namespaces as well as
> nested namespaces in objectes (in methods). Namespaces support modularity by
> preventing naming conflicts...
>
> *nested scope* : The ability to refer to a variable in encolsing definition. For
> instances, a function defined inside another function can refer to variables
> in the outer fucntion. Note that nested scopes work only for reference and
> not for assignment which will always write to the innermost scope.
> In contrast, local varibles both read and write in the innermost scope.
> Likewise. global variables read and write to the global namespace.

# 名字、绑定与变量类型

为了理解 Python 的名字空间机制，首先需要明确的是 Python 中的变量 (variable)
与其他语言的不同。在 Python 中并不存在一般意义上的变量，只有*名字*
(names) 和*对象* (objects)。对象是数据及属性实体，而名字是访问对象途径。
名字通过*绑定* (binding) 操作[^1]被引入至程序中。

绑定除了会使某个名字指代某个对象，还会给这个名字赋予一些额外的性质。
下面这段话来自 [Python 语言参考][name-binding]：

> 当我们在某个代码快内绑定一个名字时，它就是成为该代码块的局部变量 (local
> variable)。
> 而当我们在模块级别上绑定一个名字是，它就是一个全局变量 (global variable)。
> 由于模块也是一个代码快，因此这些变量即是全局的也是局部的。
> 如果在一个代码块中使用了一个未在该代码快内定义（绑定）的名字，它就被称为*自由变量*
> (free variale)。

在程序一旦写成之后，任何代码块中的任意变量都必定属于上述三种类型[^2]之一，这是在生成
字节码之前就确定下来的。因为这与虚拟机使用用哪种语句加载变量相关。
比如下面这段代码：

{% highlight python %}
def f():
    l = 2
    ll = g
{% endhighlight python %}
 
函数 `f` 的字节码如下：

    2           0 LOAD_CONST               1 (2)
                3 STORE_FAST               0 (l)

    3           6 LOAD_GLOBAL              0 (g)
                9 STORE_FAST               1 (ll)
               12 LOAD_CONST               0 (None)
               15 RETURN_VALUE        

可以看出对于局部变量 `f`，Python 使用的是 `LOAD_FAST/STORE_FAST`
一类指令，而对于全局变量则使用 `LOAD_GLOBAL/STORE_GLOBAL` 一类指令。

绑定的作用之一就是确定变量的类型：不论一个变量在当前代码块中的哪里被绑定，它都被认为是局部的，因此
该代码块内所有对其的使用都将指向它绑定的这个对象。这段话听起来十分坳口，我们用下面这个例子来说明：

{% highlight python %}
x = 1
def f():
    t = x
    x = 10
{% endhighlight python %}

函数 `f` 对应的字节码为：

    2           0 LOAD_FAST                0 (x)
                3 STORE_FAST               1 (t)

    3           6 LOAD_CONST               1 (10)
                9 STORE_FAST               0 (x)
               12 LOAD_CONST               0 (None)
               15 RETURN_VALUE

可以看到，第一句加载变量 `x` 时即使用的 `LOAD_FAST` 指令，说明 Python
认为它是一个局部变量，而对 `x` 的绑定却是在后面一条语句中才发生。
这个例子说明了 Python 的变量类型机制，虽然定义 `f` 时不会报错，但
在 `f` 运行时会报出 `UnboundLocalError` 的异常, 这个异常会在引用一个未绑定的局部变量时抛出。
这就出现一个有点违反直觉的现象，按一般的理解，第一条语句中的 `x`
应该是全局的，第二条语句不过是在本地重新生成了一个局部变量而已。
但就像上文所说的，当定义好 `f` 后，它里面的每个变量类型都已经确定了， 
尽管 `x` 在第二条语句中才被绑定，但还是被认为是一个局部变量，
既然是一个局部变量, 任何对其的使用就会访问局部代码块中该名字所绑定的对象,
如果它还没被绑定，当然就应该报错。


# 代码块

而每个被引入的名字都被保存至一个名字空间里。
在任何地方出现的名字都将指向 (refers to) 它在所绑定的对象。
由于一个名字可以在程序的不同地方绑定到不同的对象，所以就需要一个机制来确定当前使用的名字对应的对象。
这里就需要涉及到*代码块* (block) 以及*作用域* (scope) 这两个概念。
所谓代码块即指一段被当作一个单元执行的程序文本。诸如模块、函数体、类定义都是一个代码块。
至于作用域，[Guido][] 老爹在 [Tutorial][] 中给出的定义是：

> A *scope* is a textual region of a Python program where a namespace is
> directly accessible.

[Python 语言参考][name-binding] 中给出的定义则是：

> A *scope* defines the visibility of a name within a block.

可以这么理解，每个代码块都有一个属于自己的局部名字空间，而每个名字空间都有一个允许直接访问其内名字的的代码范围，在这个范围内这些名字都是可见的，
而在这个范围外，该名字空间的内的名字则不能直接使用。这个代码范围即是该名字空间（中变量）的作用域。


现在让我们把注意力集中在代码块的局部变量上, [语言参考][name-binding]中说：

> 在一个代码块中定义的局部变量的作用域为这个代码块本身。如果它定义在一个函数代码块中，
> 它的作用域将延伸至该代码块包含的所有内部代码块中，除非在子代码块里对其重新定义。
> 类代码块中定义的名字的作用域仅限于该代码块本身，它不会向类中的方法代码块扩展
>  —— 这包括生成器表达式，因为它们使用了函数作用域来实现。

有了这几条规则，我们可以静态地推出在任意一个代码块被哪些名字空间的作用域所覆盖，有如下几种情况：

1. 代码块自身的局部名字空间，该空间包含了在本代码块中绑定的所有名字
2. 所有包含该代码块的函数代码块的名字空间，它们的作用域按代码块的分布层层嵌套

除了这两种可能的作用域之外，Python 还规定了以下两个所有代码块均可见的名字空间:

3. 代码块所在模块的全局名字空间，它包含了模块中的所有全局变量
4. 内建名字空间，它其实是 `__builtin__` 模块的名字空间

可以把上面的这两个名字空间合称『全局名字空间』，它们的作用域为模块内任何代码块。

当 Python 需要对一个变量解析其对应的对象时它会以上面列出的 1， 2， 3， 4 的顺序依次
尝试，把在最里层名字空间内找到的名字绑定作为当前名字的引用。
有一个例外是如果在代码块中用 `global` 语句对一个变量进行了声明，那么在该代码块中所有
对此名字进行解析时将跳过 1, 2 直接从 3 开始。比如下面这段代码：

{% highlight python %}
x = 2
def f():
    x = 10
    global x
print x
{% endhighlight python %}

将会输出 10。函数 `f` 的字节码如下：

    2           0 LOAD_CONST               1 (10)
                3 STORE_GLOBAL             0 (x)
    ...         ...

从中可以看到，`x` 在赋值时即被当作是全局变量。
事实上，定义上面这个函数时 Python 编译器[^3]给出一个*语法警告*，告诉你应该 `x` 在全局声明前
就被赋值。


   [Python]: http://www.python.org
   [pydoc]: http://docs.python.org
   [pyglossary]: http://docs.python.org/glossary.html

[^1]: 常见的绑定操作包括函数定义，类定义，赋值语句等，一个特别的绑定操作是 `del`
语句，出现在 `del` 后面的变量讲被认为是局部变量。
关于绑定操作更多的说明请参考: [Naming and
binding](http://docs.python.org/reference/executionmodel.html#naming-and-binding)
[^2]: 这里所指的类型并不是变量值的类型 (int, float...)，而是指它是局部、自由异或全局变量。下同。
[^3]: 虽说 Python 不是一个编译语言，但还是有一个编译器前端的，
它对源文件进行语法分析，生成句法树和符号表，并最终产生字节码供虚拟机执行。这些步骤属于
Python 的静态地带，变量的类型就是在其中确定的。
深入的信息请参考[Eli](http://eli.thegreenplace.net) 的系列文章[Python Internals: Symbol
tables](http://eli.thegreenplace.net/2010/09/18/python-internals-symbol-tables-part-1/)
