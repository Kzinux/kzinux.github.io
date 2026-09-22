---
layout: post_layout
title: Feh屏保
time: 2026年09月22日 星期二
location: 中国
pulished: true
excerpt_separator: "<!--more-->"
---
用窗口管理器很久了，最近看10分钟空闲就黑屏的屏幕腻了，就想改改延时顺便弄个屏保。桌面背景也是用的feh，每隔6小时换一个指定文件夹的随机图片，就想弄个feh版的屏保。
<!--more-->


## **屏幕空闲黑屏** ##
屏幕黑屏状态信息可以用xset q查询，由screen saver下的timeout和dpms下的Standby，Suspend，Off四个值决定。四个值似乎是取最小的一个数来决定黑屏延时，试过timeout最小则timeout优先，timeout设为0（禁用黑屏）则取Standby，Suspend，Off的数值。其他情况懒得测了，总之四个值设成一样就好。
参考arhchwiki的[显示电源管理信号 (DPMS) - ArchWiki - Arch Linux 教程](https://wiki.archlinux.org.cn/title/Display_Power_Management_Signaling)，在/etc/X11/xorg.conf.d下建立10-serverflags.conf文件，把四个值都设置成一样的延时，单位分钟。
```
Section "ServerFlags"
    Option "BlankTime" "176"
    Option "StandbyTime" "176"
    Option "SuspendTime" "176"
    Option "OffTime"     "176"
EndSection
```



## **feh屏保** ##
屏保的关键是触发，空闲状态下延时触发屏保，这一步就卡住了。
### **xssstate屏幕状态查询** ###
用“screensaver”关键词在pacman里查询找到一个命令xssstate，找到主页居然是dmenu的作者开发的，他写了很多有趣实用的小工具。
```
     -i     show the idle time of X11, in milliseconds.

     -s     show the current state of the X screensaver. There is on for the screensaver being active at the moment, off for the screensaver being off and
            disabled for the screensaver being disabled.

     -t     will show the time that needs to be waited until the screensaver should be activated, in milliseconds.
```

-i显示屏幕空闲时间，-s显示屏保状态，-t显示的上面设置的BlankTime也就是xset q里screen saver下的timeout值，单位都是毫秒。
### **feh显示随机图片定期切换** ###
解决了关键的屏幕空闲时间获取，接下来用feh调用随机图片幻灯片式自动切换。到feh的manpage里翻了翻需要的选项功能都找到了，--fullscreen全屏显示，--slideshow-delay 72延时72秒切换下一张图片，--randomize随机顺序图片，--zoom fill图片铺满屏幕。
``` 
   feh --fullscreen --slideshow-delay 72 --randomize --zoom fill '壁纸文件夹路径'
```
### **sh脚本** ###
```
#!/bin/sh
#!/bin/bash
#!/bin/zsh

while true
do
	#屏幕空闲大于10分钟，600000是毫秒
    if [[ $(xssstate -i) -gt 600000 ]]; 
    then
    feh --fullscreen --slideshow-delay 72 --randomize --zoom fill '壁纸文件夹路径'
    else
        echo "Not a long time."
        #查询xssstate时间间隔
        sleep 1m
    fi
done

```

最后把上面脚本加个执行权限放到开机启动里就完成了。