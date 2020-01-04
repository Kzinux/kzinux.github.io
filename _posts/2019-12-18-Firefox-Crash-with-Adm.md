---
layout: post_layout
title: Adm导致Firefox奔溃
time: 2019年12月18日 星期三
location: 中国
pulished: true
excerpt_separator: "<!--more-->"
---





Adm广告过滤软件用了一段时间了，发现Firefox打开使用很长时间没关闭再访问淘宝等特定网站很容易崩溃。当时还没怀疑adm，毕竟频率不高，不容易复现。后来遇到崩溃的网站越来越多，而且adm也会直接退出。

当时怀疑是规则，排查后清空规则还是如此。

一筹莫展之下都打算换到其它软件，adbyby不支持https，koolproxy不支持pc且作者放弃此项目了。

一番折腾下还是找到了解决办法，回滚到旧版的adm，linux下最新版是2.6，换用2.5后一切正常。再也没有遇到Firefox崩溃和adm自动退出的问题。
<!--more-->
