---
layout: post_layout
title: Linux下阻止Firefox不停创建“下载”“Desktop”等文件夹
time: 2023年10月14日 星期六
location: 中国
pulished: true
excerpt_separator: "<!--more-->"
---
Firefox默认会在当前用户目录下新建很多文件夹，比如“下载”、“Desktop”、“Documents”之类的，即使删掉只要开着firefox过会儿又给重建。之前有搜索过说改变默认下载文件夹之类的根本不管用，而且还有其他几个跟下载没关系的文件夹。
<!--more-->

今天认真找了找，发现有人提到firefox是根据系统提供的XDG user directories新建文件夹的，只要将文件夹自定义为已有的文件夹就不会再新建了。

自定义这些变量需要安装xdg-user-dirs包，具体设置使用参考archkwi这里就不赘述了。

https://wiki.archlinux.org/title/XDG_user_directories

将几个变量都设置成已有文件夹后，果然不会再新建烦人的文件夹了。



