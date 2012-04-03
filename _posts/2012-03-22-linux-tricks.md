---
layout: post
title: "Linux 小窍门札记"
tagline: "Linux Tricks"
category: "Better Work In Linux"
tags: [linux]
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

--------------------------------

## screenshot
 
    scrot -s

-------------------------------

## Gnome

### 快捷键

* 缩放

        CRTL-_  || CRTL-+

-------------------------------
