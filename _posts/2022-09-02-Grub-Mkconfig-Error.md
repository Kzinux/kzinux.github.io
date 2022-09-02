---
layout: post_layout
title: grub-mkconfig在中文locale下报错
time: 2022年09月02日 星期五
location: 中国
pulished: true
excerpt_separator: "<!--more-->"
---
Arch的更新提示还是很重要的，时不时要手动干预下。今天看到最新的提示说grub更新最好重新安装grub并生成新的配置文件，谁知安装grub后生成配置文件出错，提示“/usr/share/grub/grub-mkconfig_lib: 第 237 行：printf: “$”：无效格式字符”。

搜索了下是语言环境locale的问题，新版的grub中文locale下grub-mkconfig会报错，需要将locale改成en_US之类的。一般都是到/etc/locale.conf里改全局，不过这样系统和软件的语言都变成英文了。到系统设置里将系统语言改成中文可以改回来，不使用桌面环境的没地方设置。
<!--more-->
想到以前conky也遇到这个locale问题，试了下同样的方法给命令单独指定locale，成功在中文环境下运行grub-mkconfig。

`env LANG=en_US.UTF-8 sudo grub-mkconfig -o /boot/grub/grub.cfg`

<img src="/assets/img/grub-error.png" width="467px" />
