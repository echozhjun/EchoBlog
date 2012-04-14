---
layout: post
title: "安装 GHC, Cabal/Cabal-install 及 Pandoc"
category: "Better Work In Linux"
data: 2012-03-31 19:01:00
tags: [ghc, cabal, cabal-install, pandoc]
---
{% include JB/setup %}

GHC 是 Glasgow Haskell Compiler 的简称，也是 Haskell Platform 的基础。
最新的版本可以从[这里][ghc]下载。

   [ghc]:http://www.haskell.org/ghc/

进入最新版本的下载页面后有两个选择，一是二进制包，二是纯源码包。
如果想从纯源码包下载，需要你本机上已装有某个版本的 ghc ，因为这个
源码的大部分就事用 Haskell 写的。

由于种种缘故我把之前 yum 安装的 ghc 给删了，所以这里我选择 ghc 的 linux\_x86 
平台二进制包。

下好安装包后即是通用的 make 过程：

    tar xjvf ghc-7.x.x-i386-linux-unkown.tar.bz2
    cd ghc-7.x.x
    ./configure
    make install

现在可以开始安装 Cabal/Cabal-install了。

Cabal 是 Haskell 的一个 package，提供了一个 Haskell 包安装框架，方便了 Haskell
包的管理。
而 Cabal-install 则是另一个 Haskell 包安装库，它提供了 `cabal` 命令行
程序，该程序在安装新包时会自动解决包依赖关系，大大方便了 Haskell 包的安装。

官方的建议是安装 Haskekll Platform，它整合了 GHC，Cabal，Cabal-install
等常用工具。
由于我觉得 fedora repo 中的 HP 版本过老，编译 HP
又需要其他很多东西，所以就没有选取这个方案，而是自己安装 Cabal。

其实 Cabal 和 Cabal-install 只要安装后者就可以了，因为安装 Cabal-install
时用的 `bootstrap` 程序会自动下载 Cabal-install 所依赖的包，其中当然包括
Cabal。但如果你只安装前者那么后者还需要手动安装。

虽说可以从 [hackage][hackage] 中下载 Cabal-install，但版本实在太老了。
我选择从[开发库][cabal]中下载最新的稳定版本安装。
这里有一个需要注意的是一定要保证你所用的 Cabal 版本与 Cabal-install 版本一致，
也就是说从开发库中同一个版本文件夹中下载 Cabal 和
Cabal-install，否则安装时会出现麻烦。
所以最好的办法就是不安装 Cabal 而直接安装
Cabal-install，这样永远不会出现冲突问题。

   [hackage]:http://hackage.haskell.org/package/cabal-install
   [cabal]:http://darcs.haskell.org/cabal-branches/
   
我选择的是
[Cabal-1.14](http://darcs.haskell.org/cabal-branches/cabal-1.14/)
版本，把这个链接里所有的文件都用 `wget` 下载到本地。命令如下：

    wget -c -r -np -k -L -p http://darc.haskell.org/cabal-branches/cabal-1.14

当然这是非常土的方法，正确的方法是使用 `git`。

进入Cabal-install目录后执行`sh bootstrap.sh`，即可完成安装。

安装完成Cabal-install 后，将cabal程序所在目录加入PATH中，再执行：

    cabal update
    cabal install pandoc

好了，现在可以享受pandoc给我们带来的便利了！

pandoc的使用请参考下面的*Links*。


## Links

* [Pandoc Demos](http://johnmacfarlane.net/pandoc/demos.html)

* [Pandoc User's Guide](http://johnmacfarlane.net/pandoc/README.html)

* [一份中文简介](http://chen.yanping.me/cn/blog/2012/03/13/pandoc/)

