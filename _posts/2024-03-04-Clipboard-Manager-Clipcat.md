---
layout: post_layout
title: 剪贴板管理Clipcat
time: 2024年03月04日 星期一
location: 中国
pulished: true
excerpt_separator: "<!--more-->"
---
剪贴板反复折腾没什么新面孔，之前的clipmenu配合rofi用的还不错。看到有人推荐clipcat打开看了下挺合心意，而且源里也有，试用后马上扔掉clipmenu。<!--more-->

### **1.性能**  ###
clipcat是rust语言写的，clipmenu是shell脚本，性能不可同日而语。

### **2.功能配置**  ###
clipcat提供后台clipcatd，命令行前端clipcatctl和gui前端clipcat-menu，gui需要配合rofi和dmenu之类的。而且clipcat支持图片文件文本，功能全wayland和x11都可以用。

配置方面更是完胜clipmenu，三个功能都有各自的toml配置文件，clipmenu只有有限的选项还得写到/etc/enviroment里。

具体配置clipcat的github页面介绍都有还有注解。

### **3.界面**  ###
同样都是用rofi，dmenu做界面，和rofi整合得也很好，很轻松地过渡配置过来了。

因为剪贴板的界面和rofi其他模式需求不同，需要用-theme-str传递一些定制界面参数，在clipcat-menu配置文件里rofi栏下面的extra_arguments后面添加，格式有点奇怪，属性和参数要用引号逗号隔开。

```
extra_arguments = ["-theme-str", "listview {require-input: false;} element {children: [element-index,element-text]; }"]
```

<img src="/assets/img/clipcat.png" width="432px" />
