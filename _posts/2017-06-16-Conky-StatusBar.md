---
layout: post_layout
title: Conky状态栏
time: 2017年06月16日 星期五
location: 中国
pulished: true
excerpt_separator: "```"
---
目前使用openbox作为窗口管理器，没有装任务栏，所以想找个能显示系统状态的软件。以前都是内嵌到任务栏的，现在没任务栏了，就只能放到标题栏上。找来找去还是conky了，功能最强大，占用很低，配置灵活。  

因为需要随时查看，肯定不能用常见的conky桌面风格，花里胡哨的我也不喜欢。还要切回到桌面才能看到，我开机后就看不到桌面了。

+ ### 配置

1. 想要的是状态栏风格，就要满足悬浮顶层，背景透明，不可移动等特性，一项项实现。把下面的代码加入到conkyrc里，就可以实现上面所说的几个要求了。
```xml

	own_window_transparent = true,
	own_window_type = 'normal',
	own_window_hints = 'undecorated,above,sticky,skip_taskbar,skip_pager',
```
2. 状态栏信息用一行文字呈现。这样就只需在conky.text里鼓捣，内容根据自己的需求配置，参考conky的manpage，很详细。

+ ### 美化

#### 颜文字

单单文字组成的状态栏有点简陋，找到了专门的颜文字[siji](https://github.com/stark/siji)来美化它。

1. 参考官网步骤安装好字体后，找到clone下来的siji镜像,用./view.sh打开脚本，然后就会显示此字体支持的所有颜文字。
2. 找到需要的颜文字后点击它，在顶部中间character后面的编码信息就会更新成选中的文字。
3. 在终端运行下面的命令就可以显示此颜文字，\u后面就是最后四位编码。
```bash
echo '\ue19a'
```
4. 复制此颜文字到conkyrc里使用，格式如下。
```xml
${font Wuncon Siji:size=10}${font}
```

#### 字体设置

需要注意conkyrc里的主字体设置和conky.text里的字体设置需要调整下，否则文字倾斜，我这里主字体Fira是8px，siji是10px。  

#### 文字偏移

使用中发现cpu使用率100%时整行文字会上下跳动，参照下面代码更改conkyrc即可。就是给百分数预留3个字符空位。
```xml
	pad_percents = 3,
```

+ ### 性能表现

刚开始使用时发现conky的cpu吃得越来越多，从1%到后来的%8左右，排查发现是$exec运行外部程序导致的。调用外部程序虽然大大增强了conky的功能，资源消耗也成倍增加。把慢慢积累起来的$exec调用全都换成conky内部接口后cpu又恢复到原来的1%了。

最后成果。

<img src="/assets/img/conky.png" width="572px" />
