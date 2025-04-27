---
layout: post_layout
title: 桌面悬浮歌词音乐播放
time: 2025年04月27日 星期日
location: 中国
pulished: true
excerpt_separator: "<!--more-->"
---
之前一直用的mpd后台播放，mpc快捷键控制，想找个显示本地歌词的办法，osdlyric和waylyric都没弄明白或者太花哨不喜欢。
<!--more-->

其实mpv就支持lrc歌词显示，终端里把lrc歌词命名为对应音频文件名就会自动识别显示和字幕一样。不过总不能开着个终端窗口悬浮在前面看歌词吧，不美观也影响视野。

今天看着mpv歌词显示突然想能不能把终端播放mpv改造成支持悬浮歌词的音乐播放器，就打算试试。
## **透明背景** ##
首先要把背景色去掉，不能盯着黑乎乎白乎乎的终端背景色显示歌词，支持透明的终端都可以，这里选择的是alacritty，后面的实现也都是基于alacritty配置。

alacritty支持多配置文件，可以用专门的配置来启动mpv而不影响alacritty日常使用，配置文件在~/.config/alacritty下，复制主配置文件重命名为alacritty4mpv.toml，然后就可以用这个配置文件修改实现对应想法了。

透明实现比较简单，alacritty和系统窗口管理器里都可以设置，alacritty配置文件里如下配置即可。
```
[window]
opacity = 0.0
```
## **单独歌词显示** ##
mpv终端播放输出里一堆信息，歌词只是其中一行，要让mpv只显示歌词屏蔽其他输出。其实这次尝试的开头就是用鼠标调整拉动alacritty窗口大小，发现输出信息会随着高度减小慢慢变少，最后显示优先级最高的是播放时戳和歌词。

这样可实现性就高了，但是麻烦的是时戳显示优先级比歌词还高，当调整窗口大小只剩一行时最后显示的是时戳。
### **mpv输出信息调整** ###
既然是mpv输出的信息那就看看能不能通过选项更改输出，mpv说实在的太复杂强大了，manpage选项茫茫多。找了下还真有，能调整终端播放输出信息，可以定制不过更简单的就是--quiet选项，去除其他输出只剩下歌词。
### **窗口大小调整** ###
输出信息搞定了再把窗口大小固定成歌词显示所需的条状，也是一样alacritty和窗口管理器都可以实现。alacritty配置里如下配置。columns是歌词显示长度，lines是窗口高度，只需要歌词所以设为一行。
```
[window.dimensions]
columns = 58
lines = 1
```
### **窗口位置调整** ###
接下来设置窗口启动的位置，不能每次启动歌词随机乱飘，alacritty里如下配置，x，y是离左上角的距离。
```
[window.position]
x = 000
y = 000
```
### **悬浮显示** ###
歌词要悬浮显示不能切换窗口就被覆盖了看不到，设置对应窗口始终在最上面就可以。alacritty里如下配置。
```
[window]
level = "AlwaysOnTop"
```
不过在我这里不管用，刚启动时悬浮在前，一切换窗口就被覆盖了，所以用窗口管理器实现的，设置一个专门的window class然后让它始终在前。
```
[window.class]
instance = "xxx"
general = "xxx"
```
### **去除窗口边框** ###
背景透明了窗口标题栏和边框还在，之前留着是方便调整配置，最后把它也去掉就完全透明只剩歌词了。alacritty里如下配置。
```
[window]
decorations = "None"
```
### **窗口管理优化** ###
最后就是让它在后台默默生效，不要显示在alt tab窗口列表或者任务栏里，还有就是启动时不需要聚焦到窗口。这些都可以在窗口管理器里实现，根据个人环境设置。

## **界面美化** ##
大框架搭起来了接下来调整下界面细节。
### **字体选择和颜色** ###
如果想要粗体歌词就把font.normal下的Normal也改成Bold。
```
[font]
size = 9.0

[font.normal]
family = "xxx"
style = "Normal"

[font.bold]
family = "xxx"
style = "Bold"
```
字体颜色
```
[colors.primary]
foreground = "#445057"
```
### **光标** ###
纯音乐或者歌词未显示时会显示一个光标，可以调整下没那么起眼。
```
[cursor]
unfocused_hollow = false
thickness = 0.12

[cursor.style]
shape = "Underline"

[colors.cursor]
cursor = "#3a3a3a"

```
## **mpv播放管理** ##
### **播放列表** ###
本地歌曲播放可以用mpv的播放列表实现，用find来筛选对应的本地音乐输出到playlist.m3u文件，mpv就可以直接播放了。
### **乱序播放** ###
加上--shuffle选项。
### **快捷键控制** ###
mpv窗口就剩下一行歌词，如果每次都要用鼠标点下歌词再用mpv快捷键控制太麻烦了。mpd用的是mpc设置快捷键控制，mpd默认是个服务器后台，mpc连接到对应地址实现控制。

找了下mpv也有类似实现，mpv的manpage里搜索--input-ipc-server就有具体，用--input-ipc-server选项启动后就可以将控制的命令绑定到对应快捷键。
### **mpvc** ###
还有个选择就是用mpvc，一个类似mpc的远程控制mpv的ipc后台的软件，功能也很多，播放列表管理、乱序播放、播放控制等等的。

mpvc的socket文件默认是～/.config/mpvc/mpvsocket，如果跟你之前设置的不同，可以软链接一个到mpvc配置文件夹下。
## **打包** ##
最后将上面的实现打包起来实现一个类似程序的启动，首先在/usr/local/bin下新建个可执行文件，如下。
```
#!/bin/sh
#!/bin/bash
#!/bin/zsh

alacritty --config-file=/home/xxx/.config/alacritty/alacritty4mpv.toml -e mpv --quiet --input-ipc-server=/tmp/mpvsocket --shuffle /home/xxx/musiclist.m3u
```
再新建个desktop方便启动，在~/.local/share/applications/下复制一个播放器的desktop过来改改就完成了。


<img src="/assets/img/lyrics.gif" width="509px" />
