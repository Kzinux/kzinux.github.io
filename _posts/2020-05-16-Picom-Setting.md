---
layout: post_layout
title: Picom「compton」设置相关
time: 2020年05月16日 星期六
location: 中国
pulished: true
excerpt_separator: "<!--more-->"
---
Picom版本号跳到8了，过去很多compton时代的设置也不兼容，干脆彻底迁移到新设置去。

新设置文件范例在/etc/xdg下，注意picom.conf和picom.conf.example不一样，后者才是官方的范例，各项设置都解释得很清楚，前者可能是他人贡献的推荐设置。而且前者的设置项也不全，例如opcity-rule里面就没有。

把旧设置迁移过来，Picom最大的改变就是跟conky一样实时加载配置变化，只要修改了就会重新加载配置。不用像以前一样要重启桌面才能看到效果。

<!--more-->

另外说下经常遇到的一个问题，就是firefox的tootip式菜单，包括网站信息、三道杠按钮还有很多扩展菜单都用了这种菜单。不过它是自带菜单阴影的，所以跟picom的阴影重复了。要在picom里排除这种菜单，简单粗暴的方法很多，要精确定位此种菜单把下面的配置项加到shadow-exclude下。

```
"class_g = 'firefox' && window_type = 'utility'",
```

另外还有个小问题纠缠了两天才弄清楚，notification通知在升级到8后变成半透明，不透明度大概90%。可是xfce4-notifyd-config里还有picom设置里都设置了100%的不透明度，弄懵比了，还怀疑是不是picom内置的设置。

今天突然想到会不会是xfce4-notifyd的主题问题，打开xfce-notify-4.0下的gtk.css，果然里面设置了背景透明，改成不透明解决了。