---
layout: post
title: "BUAA Story : slides using pandoc and dzslides"
date: 2012-05-10 22:11
category: "Freewheeling Mind"
tags: [BUAA, story]
---
{% include JB/setup %}

今天下午学校搞德育答辩，要求每个人做三分钟左右的展示。之前在[益辉][yihui]的博客上看到过非常炫的
html5
[幻灯片][introR]，所以趁这个机会自己也尝试了一把。实际证明效果还是不错的，打算以后就放弃
bearmer 转投 html5+css+js 的门下了。

我的这个展示用 [Markdown][] 编写，之后借助 [Pandoc][] 转换成
[dzslides][]。源文件可以从[这里][src]获取，也可以[在线][online]观看最终效果[^1]。

这里顺便介绍一下目前我知道的 html 幻灯片制作方法。

* 一种就是我使用的 [dzslides][]，它其实是一个 html 模板，利用 CSS3
实现了每张展示间的切换，它支持在幻灯片中直接使用 html5 元素。

* 第二种比较方便的方法是使用 Google 提供的 html5 [模板][google]。用法和
dzslides 类似。

* 第三种是绚丽的 [deck.js][]。这玩意儿真是太炫了，刚才提到的 R 介绍就是用它做的。
可惜目前我还不懂 JavaScript，但有这么漂亮的应用，JavaScript
已经排在我的学习列表里了。

[^1]: 注意一定*不要*用 IE 观看这个展示，推荐火狐或者
Chrome。另外由于这个展示里有很多图片，而我又是直接把图片放在 [Github][src]
上的，所以加载速度会很慢。我比较推荐把源码库克隆到本地后观看，如果你执意在线查看，那好，请打开链接后泡杯茶，待三十分钟后再回来 :-)

   [yihui]: http://yihui.name/en/2012/04/fancy-html5-slides-with-knitr-and-pandoc/
   [introR]: http://inundata.org/R_talks/meetup/
   [Markdown]: http://markdown.tw/
   [Pandoc]: http://johnmacfarlane.net/pandoc/
   [dzslides]: http://paulrouget.com/dzslides/
   [google]: http://code.google.com/p/html5slides/ 
   [deck.js]: http://imakewebthings.com/deck.js/
   [src]: http://github.com/yangzetian/BUAA-Story
   [online]: http://yangzetian.github.com/BUAA-Story
