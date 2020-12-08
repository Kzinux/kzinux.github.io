---
layout: post_layout
title: Rofi启动器
time: 2020年12月08日 星期二
location: 中国
pulished: true
excerpt_separator: "<!--more-->"

---

Linux下原来用的是dmenu，优点快速、简洁，缺点也是太简洁了，外观就是传统的命令行界面，而且不支持.desktop启动。后来[rofi](https://github.com/davatorium/rofi)出现，开始取代dmenu，功能上强化，支持command启动、desktop启动、窗口切换还有ssh之类的其他功能；界面支持主题自定义，也积累了很多的主题。

Windows下启动器用的是[Listary](https://www.listary.com/)，反应快速、外观简洁，支持程序启动和文件搜索，后者于我倒不是必要。

<!--more-->

<img src="/assets/img/listary.png" width="500px" />

rofi内置了很多主题，可以用rofi-theme-selector选择，作者也收集了一些[用户贡献的主题](https://github.com/davatorium/rofi-themes/tree/master/User%20Themes)，目前用的就是里面的flat-orange修改的。因为前面的版本只能统一设置一个字体和字号，导致外观主题主次不明。像上面的listary用户输入和结果输出使用不同的字号，层次就分明多了。不过rofi最新的版本可以让各部分使用不同的字体和字号，把主题文件里的prompt和entry字号修改后顺眼多了。

<img src="/assets/img/rofi.png" width="672px" />