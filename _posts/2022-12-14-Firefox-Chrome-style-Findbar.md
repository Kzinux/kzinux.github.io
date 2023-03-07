---
layout: post_layout
title: Firefox简洁chrome式查找栏
time: 2022年12月14日 星期三
location: 中国
pulished: true
excerpt_separator: "<!--more-->"
---
Firefox的查找栏外观还是Firefox3时代的老古董，虽然功能吸收拓展了很多流行的查找栏扩展的热门功能，奇怪为什么就是不学学chrome查找栏的样式。

本来用着一个简单的css把查找栏那一大条简化放到了右上角悬浮显示，也没弄什么外观修饰调整，谁知这次升级失效了。干脆借着修复的机会彻底整整查找栏。
<!--more-->
打开Firefox的浏览器工具箱，直接在里面定位到查找栏修改，实时显示更改效果，很强大好用的工具，比以前的dom inspector扩展好用多了。

对于要改成什么样是有底的，chromium那个查找栏简洁大方，照着改就成。不过firefox的查找栏功能比chrome的强大，没必要削足适履。成品还是很满意的，外观简洁又保留了firefox查找栏的全部功能。
* 默认打开状态

<img src="/assets/img/findbar1.png" width="363px" />

* 查找匹配状态

<img src="/assets/img/findbar2.png" width="523px" />

* 查找失败状态

<img src="/assets/img/findbar3.png" width="460px" />

* 快速查找栏

<img src="/assets/img/findbar4.png" width="298px" />

<img src="/assets/img/findbar5.png" width="458px" />

<img src="/assets/img/findbar6.png" width="299px" />


用了几天觉得“高亮”那4个设置项摆那太浪费空间了，把它们设置成隐藏式的，既保留了功能又不浪费空间。

鼠标点击查找栏不放2秒钟就会弹出设置项，这时可以更改查阅，2秒钟内如果没有动作就会恢复缩回去。

<img src="/assets/img/findbar-settings.gif" width="756px" />
