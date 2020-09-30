---
layout: post_layout
title: U盘多iso启动盘-ventoy
time: 2020年09月03日 星期四
location: 中国
pulished: true
excerpt_separator: "<!--more-->"
---
U盘启动盘是现在最常见的系统安装维护方式，usb3设备的速度、容量和便携不是老旧的光盘能比的。

一般安装win都是用pe，arch用的是自家的iso。之前不是没找过支持linux的pe工具，虽然也能挂载读写，功能还是不如arch的iso。最好能整合win的pe和arch的iso到一个u盘里，没想到真有这样的软件——[ventoy](https://www.ventoy.net/cn/index.html)。

<!--more-->

> Ventoy是一个制作可启动U盘的开源工具。有了Ventoy你就无需反复地格式化U盘，你只需要把ISO/WIM/IMG/EFI文件拷贝到U盘里面就可以启动了，无需其他操作。    你可以一次性拷贝很多个不同类型的ISO文件，在启动时Ventoy会显示一个菜单来选择([截图](https://www.ventoy.net/cn/screenshot.html))。 
>
> 无差异支持Legacy BIOS和UEFI模式。支持大部分常见操作系统 （Windows/WinPE/Linux/Unix/VMware/Xen ...）
>
> 目前已经测试了各类超过**550+** 个ISO文件([列表](https://www.ventoy.net/cn/isolist.html)).    支持 distrowatch.com 网站上收录的 **90%+** 的操作系统([列表](https://www.ventoy.net/cn/distrowatch.html))。

看上面介绍一目了然，插入u盘后打开ventoy安装到对应u盘，安装成功后将对应的iso复制到u盘里，根目录或者新建个文件夹安放都可以。注意文件名不要有中文和空格，重启选择u盘启动就看到iso在启动列表，选择打开对应的iso就跟iso单独启动盘一模一样。

真的是非常方便的软件，只要你u盘够大，想要放多少iso进去都行，而且支持很多系统和各种pe工具「作者做了很多测试，通过测试的都放在网站兼容性列表里」。
