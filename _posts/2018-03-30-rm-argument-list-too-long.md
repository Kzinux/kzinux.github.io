---
layout: post_layout
title: rm参数列表过长无法执行
time: 2018年03月30日 星期五
location: 中国
pulished: true
excerpt_separator: "<!--more-->"
---


今天无意中看了下/var/cache/pacman/pkg，长久不清理竟然已经有29G了。想用rm *命令清理掉缓存软件包结果提示“无法执行 /usr/bin/rm: 参数列表过长”。  

第一次遇到这个问题搜了下，简单点直接rm -r删除父目录然后再重建此目录即可，其它方法都是用脚本循环之类的。我用笨方法，用find命令循环删除，慢了点不过简单。`find . -type f -exec sudo rm {} \;`

