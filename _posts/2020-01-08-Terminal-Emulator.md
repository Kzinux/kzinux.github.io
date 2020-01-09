---
layout: post_layout
title: 主力终端换为Alacritty
time: 2020年01月08日 星期三
location: 中国
pulished: true
excerpt_separator: "<!--more-->"
---
前面有说到命令行是Linux下的精髓，自然终端就是日常使用最多的程序之一。

xterm也用了好几年了，刚开始还用过Xfce-terminal，因为支持tab分页，后来接触到screen/tmux就转到xterm一直用到现在。xterm小巧、简洁、速度也快，配置也方便，还支持类似vim一样拉丁和汉字分开的字体设置。我英文用的Terminus，中文用的也是点阵字体「Zpix」。但是有个问题openbox窗口切换界面「alt+tab」不显示图标，每次看到白板图标就难受。

这些年推陈出新又有很多后辈，就想看看有没有更好的代替者。

#### **Termite**

本来想想试试前段时间找到的Termite，设置点阵字体后所有字都变成了框框。搜索了下原来是因为Termite依赖的pango从1.4.4版本起不再支持bdf和pcf点阵字体，因为pango的开发者把字体描绘依赖从fontconfig改成了harfbuzz，后者不支持上述点阵字体格式，只支持otb格式的点阵字体。对fontconfig还是挺有好感的，pango这样一弄对termite也没什么兴趣了，还是当作备用吧。

#### **Alacritty**

<!--more-->
无意中找到，还挺热门的，说是Gpu加速的，跨平台，号称最快的终端。

安装后从它的example里复制了配置文件范例出来，可是这个配置文件太难理解了，注释和设置行都混在一起没有明显的分界，而且只要你把哪个设置或者注释前面的注释符号去掉了或者去掉的设置行没有完整马上报错，都不知道哪里错了。至少用空行把注释和设置之间的界限分清楚。后来还是用下载别人的配置文件我再重新修改才搞定的。

使用下来的确是挺快的，用catimg显示gif文件跟xterm对比很流畅且cpu占用少，xterm卡卡的而且cpu占用是它的两倍。也支持bdf和pcf点阵，不过没有拉丁、中文分开的字体设置，我用fontforge把Terminus替换了Zpix的拉丁部分合并成一个新字体，这样就解决了字体的问题。

另外还要说下图标，alacritty倒是有图标显示，可是默认的图标挺难看的，跟终端一点不搭「见下图」，程序员的审美挺诡异的。

<img src="/assets/img/Alacritty.png" width="64px" />

用了几天下来感觉挺顺手的，开发维护也很积极，决定主用alacritty了。

