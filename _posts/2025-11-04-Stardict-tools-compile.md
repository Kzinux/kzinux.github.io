---
layout: post_layout
title: Stardict工具软件stardict-editor编译
time: 2025年11月04日 星期二
location: 中国
pulished: true
excerpt_separator: "<!--more-->"
---
由于sdcv使用的词典都是用stardict的工具stardict-editor转换编辑的，之前一直用aur里的stardict-tools没升级。这次升级发现gtk2居然被移出官方仓库了，而stardict-editor是系统里目前唯一还依赖gtk2的。
<!--more-->

因为上次升级aur的stardict-tools反而坏掉所以就固定了版本，现在连gtk2都淘汰了没办法用老版本了。aur里的两个都没人维护而且安装都失效了，干脆自己编译解决。
## **源码** ##
这个简单，到https://github.com/huzheng001/stardict-3 ，clone下来。页面有介绍目录结构和怎么安装编译，dict目录下是主程序，tools目录下是辅助工具，stardict-editor就在里面。
## **编译** ##
由于只需要stardict-editor所以进入tools文件夹，里面有个INSTALL文本介绍。
首先运行./configure，然后就报错了。
```./configure: line 20128: AM_ICONV: 未找到命令```
根据关键词搜索找到个链接https://gitlab.archlinux.org/archlinux/packaging/packages/dvdauthor/-/merge_requests/2 ，说是由于gettext包的m4文件移动导致的，下面有修复命令，看不懂照样在tools目录下运行下。
```autoreconf -fvi -I /usr/share/gettext/m4```
然后重新./configure再通过了，接着make编译等待，完成后在tools/src下就有很多编译成功的辅助程序，包括新版不依赖gtk2的stardict-editor。
还有一些不依赖gui的命令行版本程序，这样就不担心工具失效了。
