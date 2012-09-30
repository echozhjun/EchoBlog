---
layout: post
title: "Arch Linux 折腾后记：双屏显示器"
category: "Better Work In Linux"
date: 2012-09-30 22:30
tags: [arch, linux, radeon, ati, installation]
---
{% include JB/setup %}

几天前，实验室来了一批新显示器，这样，我的显示空间在原先的 593mm x 371mm 基础上又增加了 376mm x 301mm。 
按理说，这是一件好事，因为可以在新增的小号显示器上做一些隐秘的事情。
只是 Linux 总会抓住一切可能的机会考验你一把，这种幸福的烦恼更是不容错过了。

整个风波的始终如下：

新机器的 ATI 显卡配置了两个输出端口，一个为 DisplayPort， 一个为 DVI。
而现有的两台显示器，一个有 HDMI 输入口，另一个有 VGA 输入口。
这样，利用一条 DisplayPort 转 HDMI 的转接线及一个 DVI 转 VGA 转接口，两台显示器便应该可以正常工作了。
可实际情况是，接受 DisplayPort 输出的大屏显示器工作得很好，而 DVI 的那个却不停地闪屏。

因为双屏在安装了 ATI 私有驱动 Catalyst 的 Windows 下可以正常工作，所以觉得在 Arch 下安好 Catalyst 驱动应该就能解决问题。
根据官方的 [Wiki](https://wiki.archlinux.org/index.php/ATI_Catalyst) ，摸索出来的驱动过程安装如下。

1. 添加非官方 Catalyst 源。将下面的几行添加至 `/etc/pacman.conf` 里面位于 [core] 和 [extra] 之前。

~~~~~~~~ {.bash}
[catalyst]
Server = http://catalyst.apocalypsus.net/repo/catalyst/$arch
~~~~~~~~
    
2. 更新整个系统。这一步非常重要！不然后面编译驱动模块时会很麻烦。

~~~~~~~~ {.bash}
pacman -Syu
~~~~~~~~

3. 安装 catalyst 相关软件包。

~~~~~~~~ {.bash}
pacman -S catalyst-generator catalyst-util lib32-catalyst-utils
~~~~~~~~~~

4. 编译内核模块。
    
~~~~~~~~~ {.bash}
catalyst_build_module
~~~~~~~~~

5. 这个时候千万不能重启系统，还需生成初 Xorg 的配置文件。

~~~~~~~~~~ {.bash}
aticonfig --initial or aticonfig --initial=dual-head
~~~~~~~~~~

6. 修改内核启动参数。

~~~~~~~~~~~ {.bash}
/etc/default/grub
-----------------
GRUB_CMDLINE_LINUX="nomodeset"
grub-mkconfig -o /boot/grub/grub.cfg
~~~~~~~~~~~

7. 重启吧，剩下的交给 amdcccle 来处理。最后需要注意的就是 amdcccle 生成的 Xorg.conf 文件放在 /etc/X11 里，最好将其重命名为 20-monitor.conf 后放置 /etc/X11/Xorg.conf.d/ 中。

配置完后，双屏果然可以正常显示。就这样，我打开 xscreenvasor，痛痛快快地游泳去了。
结果游了一小时回来后，就发现悲剧了。
屏幕僵在了一个画面上，鼠标键盘全无反应，最后只好硬关机，重启，看着那台新 Optiplex 受此委屈，真让人心疼啊。

上网查了查，发现是 Xorg 挂了，据传是 ATI Catalyst 固有的一个问题。试了一个解决方案，发现还是会死机。
最后忍无可忍，下狠心删了 Catalyst 重新安装开源驱动，决定再难也要跨过 Xorg.conf 配置这一关。

查了一些 Xorg 的设置资料，但不管是利用 Xorg.conf 还是 xrandr，都没法解决闪屏的问题。
期间试了无数屏幕刷新率，均于事无补。
实在没办法，另找一个显示器插上，发现还是会闪屏。
之前一直觉得问题出在 Xorg 的配置，最后突然想到，会不会是两个接口出了问题。
上网一搜，果然有人提到说 DisplayPort 接口加 DVI 转 VGA 接口会导致 VGA 显示器闪屏，而这个问题也没有什么好的解决方案。
卸下转接头，直接用 DVI 接口接 DVI 显示器后，屏幕立刻就不闪了。
之后只要在 .xinitrc 中加入以下命令就能顺利双屏啦：

    xrandr --output DVI-0 --auto --pos 0x0 --output DisplayPort-0 --auto --right-of DVI-0

