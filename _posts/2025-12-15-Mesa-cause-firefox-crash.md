---
layout: post_layout
title: Mesa1:25.3.1-2导致firefox崩溃
time: 2025年12月15日 星期一
location: 中国
pulished: true
excerpt_separator: "<!--more-->"
---
firefox在隔断时间进行的系统升级中由141-146，没啥大变化，修复了几处userchrome的bug就好了。而且之前137开始后台网页无法使用内置修改的快捷键滚动网页的bug也修复了，标签页组继续完善，都挺好的。
<!--more-->

但是没用多久就遇到一次崩溃，一天下来要四五次崩溃，崩溃有时是看视频，有时只是滚动网页，有时什么都没做放后台也崩溃，之前firefox都是一开几个月的。崩溃报告里崩溃原因大部分是“SIGSEGV / SI_KERNEL”，就一次是“render”，首先怀疑是不是配置文件用太久了不兼容导致的。
## **配置文件迁移** ##
把旧配置文件复制出来备份，然后用自带的about:profiles新建个配置删除旧配置，然后打开firefox就是全新的配置文件了。
### **设置迁移** ###
重要的prefs.js设置都是放在user.js里，所以迁移很简单把备份的user.js复制过来就行。
### **扩展迁移** ###
就5个扩展，没有用复制扩展文件夹的方式，而是将每个扩展的设置导出为文件，然后在新配置下重新安装扩展导入设置。
### **脚本迁移** ###
脚本userchrome.css和usercontent.css都在chrome文件里，一样复制，把代码里涉及到旧配置文件路径的改成新的就好了。
### **历史记录迁移** ###
历史记录是firefox强大好用的重要因素，把places.sqlite文件复制过来覆盖新的，记得要关闭firefox操作。
### **登录状态迁移** ###
把cookies.sqlite和cookies.sqlite-wal复制过来。

新配置迁移完成，用了一段时间又遇到崩溃。

## **版本降级** ##
接下来怀疑是新版本的问题，到https://archive.archlinux.org/ 龟速下载了旧版145.0.2，用pacman -U手动降级，记得把配置文件里compatibility.ini的lastversion改得比降级版本小，这里改成144就行，否则会提示降级不兼容啥的。

降级后照样崩溃，干脆直接降级回上次使用的141，这次真有兼容性问题，改了compatibility.ini也启动不了firefox，只能新建配置再迁移一次。

还是崩溃，那就排除了firefox问题了。
## **Mesa** ##
打开pacman.log看了下升级列表，怀疑是mesa升级导致的，这次直接回退到上次使用的mesa版本1:25.2.7-1，firefox重新升级到最新146，配置文件已经迁移就继续使用。现在已经开着用了3天了都没崩溃，确定就是mesa新版导致的firefox崩溃。

