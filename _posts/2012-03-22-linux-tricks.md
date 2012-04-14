---
layout: post
title: "Linux 小窍门札记"
category: "Better Work In Linux"
tags: [linux]
date: 2012-03-22 20:33
---
{% include JB/setup %}

这里用来记录Linux使用过程中得到的一些零碎却非常实用的小技巧。

Here I record bits of useful tricks for daily working with Linux.

This will be keep updating...

-------------------------------

## yum

查找哪个包提供了`makeinfo`程序：

    yum provides *bin/makeinfo

-------------------------------

## rpm

查找某个包的相关文件：

    rpm -qs libgfortran

--------------------------------

## zip

    unzip xxx.zip -d destdir

## tar

将foo1，foo2打包至目标target

    tar czf dir/to/target.tar.gz foo1 foo2

将target.tar.gz内容抽取至foo目录
 
    mkdir foo; tar xzf target.tar.gz --directory=foo

## 7z

    7z e xxx.7z -o{DIR}

--------------------------------

## screenshot
 
    scrot -s

-------------------------------

## Gnome 快捷键

* Terminal缩放

        Crtl-_  || Crtl-+

* 插入系统剪切板的内容

        Shift-Ins

-------------------------------

## Bash 快捷键

### 命令行编辑

* 移动至行首或行尾 `Crtl-a || Crtl-e`
* 按单词前移或后移 `Alt-f || Alt-b`
* 删除至行首或行尾 `Ctrl-u || Ctrl-k`
* 回删一个词 `Ctrl-w || Alt-Backspace || Esc-Backspace`
* 后删一个词 `Alt-d`
* 粘贴至光标后 `Ctrl-y`

### 历史命令

*    Ctrl-r Ctrl-g Ctrl-p Ctrl-n Alt-.


-------------------------------

## wget

从<URL>下载整个文件夹

    wget -c -r -np -k -L -p <URL>

指定下载目录

    wget -P <dir>

以30秒为间隔不断重新请求下载

    wget -t 0 -w 31 -c <URL>

---------------------------------
