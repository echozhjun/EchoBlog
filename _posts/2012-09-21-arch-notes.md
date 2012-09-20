---
layout: post
title: "Arch Linux 折腾记"
category: "Better Work In Linux"
date: 2012-09-21 00:02
tags: [arch, linux, installaltion]
---
{% include JB/setup %}

还记得不久前看到一个博主说自己在某天「破天荒」地写了两篇日志，没想到这事这么快就临到了自己头上。

时间宝贵，不容浪费，所以还是直接切入主题吧。

趁着老板给大家升级机器的良机，本人终于尝了一口火到爆的 Arch 之滋味。
其中真是有苦有甜有刺激，为了下次再尝时少走几步弯路，也为给那些怀揣 Arch 夙愿的 Linux 同仁做点心理准备，特将此次的折腾全过程记录如下。 

1. 老老实实地按着 [Beginner's Guide](https://wiki.archlinux.org/index.php/Beginners%27_Guide) 一步步配好基本系统

2. 安装配置 Awesome 桌面管理器。直接利用 .xinitrc 脚本启动，此外设置自动登录

~~~~~~~~~~~~~~~ {.bash}
.bash_profile:
--------------
vt=$(fgconsole 2>/dev/null)
(( vt == 1 )) && startx -- vt7 &> ~/.xlog
unset vt

/etc/inittab:
-------------
id:5:initdefault
c1:2345:respawn:/sbin/agetty -a user -o "-- \u" -8 -s 38400 tty1 linux

.xinitrc
--------
# 关闭系统鸣叫
xset -b
# 运行 awesome
exec awesome
~~~~~~~~~~~~~~~~~~~


3. 安装 Firefox, 配置好账户同步，Vimpertor 与 Tab Utilities

4. 安装字体：Dejavu, wqy_zenhei, wqy_microhei, bitstream vera. 之后利用文泉驿字体[设置工具](http://wenq.org/cloud/fcdesigner.html)配置 ~/.fonts.conf

5. 安装 pkgfile (提供包文件查询)，yaourt (方便安装AUR 内的包)，net-tools (提供 ifconfig 等网络设置命令)，wget

~~~~~~~~~~~~~~~~ {.bash}
pacman -S pkgfile, net-tools, wget

/etc/pacman.conf
---------------
# 添加 yaourt 源
[archlinuxfr]
Server = http://repo.archlinux.fr/$arch

pacman -S yaourt
~~~~~~~~~~~~~~~~~~~

其中 pkgfile 的常用方法为：

    pkgfile -r '.*ifconfig.*'

6. 安装小企鹅输入法 fcitx

~~~~~~~~~~~ {.bash}
yaourt -S fcitx-git

.xinitrc
--------
export XMODIFIERS=@im=fcitx
export GTK_IM_MODULE=fcitx
export QT_IM_MODULE=fcitx
~~~~~~~~~~~~~~~~~~

这里遇到一个恶心的问题，即当 yaourt 调用 wget 从 googlecode 上下载 fcitx 的 table 文件时，由于众所周知的原因始终没法正常下载，导致编译失败。
解决方案在编译失败的间隙想办法手工下好 table 文件，并放入 /tmp 里对应当前 yaourt 的目录中，然后重新编译即可。

之后将 dbus 添加至系统 Daemons 中

~~~~~~ {.bash}
/etc/rc.conf
DAEMONS=(... dbus @network ...)
~~~~~~~~~~~~~

7. 安装配置 Urxvt, Vim, Vimperator

8. 添加常用命令别名

~~~~~~~ {.bash}
.bashrc
-------
alias ls='ls --color=auto'
alias grep='grep --color=auto'
alias ll='ls -l'
alias vi='vim'
~~~~~~~~~~~~~~~

9. 设置 ls 命令的输出颜色

~~~~~~~~ {.bash}
.bashrc
-------
eval `dircolors`
~~~~~~~~~~~

10. 安装 openssh 与自动添加 ssh key

~~~~~~~ {.bash}
pacman -S openssh keychain

.bashrc
-------
alias ssh='eval $(/usr/bin/keychain --eval --agents ssh -Q --quiet ~/.ssh/id_rsa) && ssh'
~~~~~~~~~~~~~

11. 为用户添加关机、重启权限

~~~~~~ {.bash}
visudo
------
user hostname =NOPASSWD: /sbin/shutdown -h now,/sbin/halt,/sbin/poweroff,/sbin/reboot

.bashrc
------
alias poweroff='sudo poweroff'
alias reboot='sudo reboot'
alias halt='sudo halt'
~~~~~~~~

12. 安装 ntfs-3g ，并设置 ntfs 设备的加载操作

~~~~~~ {.bash}
pacman -S ntfs-3g

/etc/fstab
----------
# SATA 盘设置
/dev/sda2		/home/yang/ntfs		ntfs-3g		guid=users,uid=xxx,umask=0022	0 0

# 移动硬盘设置
/dev/disk/by-id/ata-Hitachi_xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx-part1	/media/Hitachi	ntfs-3g		noauto,gid=users,uid=xxxx,umask=0022,	0 0
~~~~~~~~~~~~~~~~~

配置好后，接入移动硬盘时直接

    # mount /media/Hitachi

即可加载移动硬盘。
此外，涉及用户信息时可通过 id 命令查看。

13. 安装应用程序

~~~~~ {.bash}
pacman -S xscreensaver(锁屏) scrot(截屏) pidgin(即时通讯) stardict(字典) nautilus(文件管理) flash libreoffice-writer(字处理) libreoffice-calc(表格) gwenview(图片浏览)
yaourt -S dropbox dropbox-cli dropbox-nautilus
~~~~~~~~~~

14. 修正双系统[时间问题](http://yangzetian.github.com/Yang-Tech-Notes/linux/arch_time.html)与[关机慢问题](http://yangzetian.github.com/Yang-Tech-Notes/linux/arch_sending_sigterm.html)


---------------------------

下面是待折腾项目，估计近期不会去碰

1. 安装配置 AMD Catalyst 显卡驱动
2. 选一个好看的 Gtk 与 Awesome 主题
3. zsh
4. mpd
