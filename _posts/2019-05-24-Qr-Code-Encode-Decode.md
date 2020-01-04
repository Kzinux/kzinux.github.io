---
layout: post_layout
title: Arch二维码转换
time: 2019年05月24日 星期五
location: 中国
pulished: true
excerpt_separator: "<!--more-->"
---



手机和电脑的之间的数据分享连接有很多方式，软件和硬件都有解决方案。今天说下二维码的，有时想发送些链接文字到手机上，用二维码就挺方便的。

#### 二维码编码

安装qrencode来生成二维码图片，有复杂需求的可以看manpage，简单的直接在命令行用如下命令生成。

`qrencode '待编码文字' -o qrcode.png`
<!--more-->

#### 二维码解码

可以使用zbar来解码二维码图片，支持图片(zbarimg)和摄像头解码(zbarcam)，也有图形界面的前端，zbar-gtk和zbar-qt。

`zbarimg qrcode.png`

都是很简洁的命令和小程序，高效不臃肿。