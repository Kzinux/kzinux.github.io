---
layout: post_layout
title: Fontforge修改点阵字体的经验和教训
time: 2024年05月07日 星期二
location: 中国
pulished: true
excerpt_separator: "<!--more-->"
---
点阵字体的修改之前写了好几篇文章，最近又学会了补全点阵字库。踩了一些坑，把方法和教训记录一下。讲一下点阵的提取，点阵字体的合并，点阵字体之间的复制粘贴，都是关于补全方面的内容。
<!--more-->

Zpix的点阵包括简繁中文和日文，平时使用除了生僻字很少见缺字符的，偶尔看到韩文矢量夹杂在点阵里。虽然韩文几乎用不到，还是想试试能不能补全韩文的点阵。

### **点阵提取** ###
韩文点阵宋体也没有，要用专用的字体才有，Gulim字体在韩文系统里类似宋体的地位，可以到[**Cooltext**](https://cooltext.com/Download-Font-%EA%B5%B4%EB%A6%BC+Gulim)下载。

下载下来是个gulim.ttc的文件，用fontforge打开,出现一个选择框，Gulim偏圆体风格，Dotum棱角更尖锐，Che代表等宽版，四个版本都包含11-25px的点阵。这里选择用Gulimche，然后会让你选择加载的点阵，因为zpix只有12px点阵所以选择12，然后点击yes就行了。

点击菜单栏File-Generate Fonts，左边的下拉菜单选择'No Outline Font'，右边的下拉菜单选择'BDF'，点击Generate就可以导出Gulimche的12px点阵了。

### **点阵字体的合并**  ###
之前合并点阵字体用的是菜单栏File-Import，Zpix原版不是等宽字体，Import了Terminus后就变成等宽了，而且字体名也变成了Terminus。所以Import是以import选择的字体覆盖原来的字体，连字体名和属性都被覆盖。

这次本来也是打算用import，把上面导出的点阵打开，菜单栏File-import选择Zpix就会用Zpix覆盖Gulimche。用之前的java程序转换为ttf后alacritty和xterm试了下都正常，以为完事大吉了用新版覆盖了旧版的bdf。谁知道wezterm下新ttf的中文缩成了一团，英文和韩文正常，试了下bdf版系统直接无法识别。

损失惨重啊，全部修改重来一遍烦人，试了再次import了terminus还有反着来terminus字体import新生成的Zpix都没用，生成的bdf字体系统不识别。把之前提取的点阵复制到fonts里也无法识别，复制gulim.ttc到fonts里是可以识别的，看来是提取的bdf点阵有问题。

import不仅仅是覆盖原字体，原字体的属性也会保留一部分影响新生成的字体，所以提取的点阵有问题导致新生成的bdf也坏掉了。

### **点阵字体之间的复制粘贴**  ###
Import不管用只能试试传统的复制粘贴，刚开始分别打开Zpix和GUlimche在两个字体之间无法复制粘贴，想了想应该打开一个字体后再从菜单栏File-Open打开另一个字体，这样两个打开的fontforge窗口之间就是从属联系了。果然这样打开两个字体后就可以在两个fontforge窗口之间复制粘贴了。

先选择Gulimche的韩文字符，范围是U+AC00至U+D7A3。fontforge居然没有shift选择点击字符之间的范围这个功能，网上说用脚本我这里也用不了不知道是不是编译时没开选项。只能用左键选中第一个不放然后往下慢慢拖，选好之后再在Zpix那边同样选择韩文字符范围。然后切换到Gulimche窗口点击Edit-Copy Width，切换到Zpix窗口点击Edit-Paste Into，切换回Gulimche窗口Edit-Copy,切换回Zpix窗口Edit-Paste，再点击菜单栏File-Generate保存，生成新的Zpix.bdf。

通过这种方式合并补全的Zpix的bdf版系统正常识别，ttf版也和以前一样都正常了。

### **点阵字体位置修改**  ###
新生成的字体韩文和中文的水平基线不平，韩文的点阵位置偏高。打开Gulimche看了下Gulimche的点阵比Zpix高一格也偏右一格，fontforge菜单栏Element-Transformations-Transform-Move功能不知道点阵能不能用，刚开始试了下1，5，10之类的没反应，发狠试了下500居然真的移动了。多试了几次原来100代表点阵编辑窗口里的一格，那只要Move-X -100 Y -100就行了。

方法先跟之前一样选择韩文字符范围，然后点击菜单栏Element-Transformations-Transform-Move，X,Y都填-100代表向左和向下各移动一格。改好后Generate保存，然后用上面复制粘贴的方法把修改好的韩文字符粘贴到Zpix里，最后生成新Bdf就大功告成了。

<img src="/assets/img/Zpixunicode.png" width="564px" />

