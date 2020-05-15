---
layout: post_layout
title: Gvim底部空白
time: 2020年05月15日 星期五
location: 中国
pulished: true
excerpt_separator: "<!--more-->"
---
Gvim底部空白问题以前解决过一次，wiki解释说是colorscheme未能覆盖全部区域，后面背景是gtk3的主题颜色。可以用下面的设置将空白去掉。

```
set guiheadroom=0
```

不过底部最近又出现了一条白线，跟以前的空白比起来小多了，不过还是挺碍眼。

<!--more-->

找了找解决方案，有用插件的，还有用gtk.css的。到vim的github页的issue看了下[相关讨论](https://github.com/vim/vim/issues/349)，下面有解决办法。不过最后那位老兄说得在理，gvim应该自己解决这个问题，而不是让使用者动手。

不说了，gtk.css的方案最方便，将下面的css放到~/.config/gtk-3.0/gtk.css「没有就新建一个」，重新打开gvim白线就消失了。颜色是你所使用的主题的窗体颜色，用取色器查探改下。

```css
#vim-main-window {
    background-color: #1B2B34;
}
```

gtk3可以用css定制界面，用下面的命令启动inspector和程序就可以查看部件修改，然后复制到gtk.css里使用。

```
GTK_DEBUG=interactive gvim
```

刚开始还兴奋了下，不过到[gtk3官网](https://developer.gnome.org/gtk3/stable/chap-css-properties.html)看了看，可以使用的css属性被大大限制。

虽然失望，不过也是因为期望，还以为可以随意修改了。
