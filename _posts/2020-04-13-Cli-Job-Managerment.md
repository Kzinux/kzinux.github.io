---
layout: post_layout
title: 命令行程序控制
time: 2020年04月13日 星期一
location: 中国
pulished: true
excerpt_separator: "<!--more-->"
---
终端的命令行程序是日常打交道的，简洁、高效。但是也有不方便的地方，比如一般都不提供暂停、继续之类的控制功能。

bash之类的shell默认有提供ctrl s和ctrl z这样的程序暂停和休眠的快捷键，按下之后程序自动休眠到后台，然后就可以用jobs命令管理控制。

今天遇到一个程序上面的快捷键不管用，按下之后是暂停了，可是jobs里没有，而且也没办法恢复继续，只能杀掉进程。

<!--more-->

后来找到一个方便的控制方法，对想要暂停的终端程序发送停止信号

```
kill -STOP pid
```

需要重启恢复程序再发送重启信号

```
kill -CONT pid
```

一直用kill，只知道简单的kill和killall杀进程，没想到还暗藏玄机。