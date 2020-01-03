---
layout: post_layout
title: Remind日程提醒及通知「Notification」
time: 2020年01月03日 星期五
location: 中国
pulished: true
excerpt_separator: "```"
---
日程提醒是一个常用又实用的功能，手机以及PC上都有很多实现方式。今天要介绍一下linux下一个强大的日程提醒软件Remind，以及openbox环境下系统通知的搭建和选择方案。

#### **Remind**

使用配置文件管理日程，主程序后台运行，一般加个-z选项指定配置文件扫描频率。下面命令指定remind每隔30分钟重新读取配置文件.reminders，可以自定义，范围在1-60分钟。`remind -z30 ~/.reminders`

重点就在remind的配置文件，功能强大、灵活又不失简洁，提供你能想到的各种日程提醒功能和组合搭配，具体参考remind的manpage，讲解得很详细。也可以参考[remind的Wiki](https://www.roaringpenguin.com/wiki/index.php/Remind)。

下面简单介绍几个实例用法。

在2020年1月8号早上6点45分播放一首铃声提醒`REM 2020-1-8@6:45 RUN mplayer -loop 0 /path2ringtone/alarm.mp3`

在周一至周五的早上六点半播放铃声提醒`REM Mon Tue Wed Thu Fri AT 6:30 RUN mplayer -loop 0 /path2ringtone/alarm.mp3` 

在每天的下午2点18分发送一条通知提醒`REM AT 14:18 RUN notify-send -a Remind -i calendar -t 0 "滚滚长江东逝水"`

在每月的1号上午九点半发送通知提醒`REM 1 AT 9:30 RUN notify-send -a Remind -i calendar -t 0 "浪花淘尽英雄"`

在每年的2月1号上午九点半发送通知提醒`REM 1 Feb AT 9:30 RUN notify-send -a Remind -i calendar -t 0 "是非成败转头空"`

在每月的第一个周六上午九点半发送通知提醒`REM Sat 1 AT 9:30 RUN notify-send -a Remind -i calendar -t 0 "青山依旧在"`

上面只是一些基本用法，可以将年月日以及星期组合成各种你想要的结果，就不一一介绍。

#### **Notification**

桌面环境默认组件，分为后台daemon和前台命令notify-send。Openbox是一个窗口管理器，当然没有通知组件，需要自己安装，一般安装后台daemon就行了，前台的notify-send「libnotify」作为很多软件的依赖已安装了。

可以脱离桌面独立运行的notification后台在archwiki的[Desktop Notification](https://wiki.archlinux.org/index.php/Desktop_notifications)页面看了下，排除aur打包的还有dunst，lxqt-notificationd，mate-notification-daemon，notification-daemon，notify-osd和xfce4-notifyd。

- xfce4-notifyd

  xfce桌面的通知服务，依赖不多，需要安装xfce4桌面的libxfce4ui包，安装后还有一个设置窗口可以设置通知行为和主题。

<img src="/assets/img/xfce4-notifyd.png" width="375px" />

- notify-osd

unity的通知服务，不依赖桌面，没有设置程序。

<img src="/assets/img/notify-osd.png" width="384px" />

- notification-daemon

gnome下的通知服务，不依赖桌面，没有设置程序。

<img src="/assets/img/notification-daemon.png" width="412px" />

- mate-notification-daemon

mate桌面的通知服务，不依赖桌面，可以设置主题。

<img src="/assets/img/mate-notification-daemon.png" width="375px" />

- lxqt-notificationd

lxqt桌面的通知服务，依赖不多，可以设置位置和大小。

<img src="/assets/img/lxqt-notificationd.png" width="312px" />

- dunst

独立开发的通知服务，自定义程度非常丰富，可以说从功能到主题都需要你自己重头来。而且主题资源很少，我就找到官网有一个推荐的主题。

<img src="/assets/img/dunst.png" width="312px" />

xfce4-notifyd，mate-notification-daemon，notify-osd和lxqt-notificationd都挺不错的，按外观来说xfce4-notifyd>lxqt-notificationd>notify-osd>mate-notification-daemon。最终选择了lxqt-notificationd，浅色背景的通知跟系统主题更搭而且醒目点，深色通知背景和深色系统主题混在一起不能一目了然。

lxqt-notificationd测试时正常，后来用dbus启动发现图标不见了，换成firefox、gvim有图标，但是很多程序都没图标了。仔细查看下发现原来图标主题变成了hicolor而不是默认的papirus，换个启动方式为何图标主题就变了？

折腾一番总算找到原因了，我这qt的主题是通过在.xinitrc里设置export QT_QPA_PLATFORMTHEME=gtk3来实现的，但是dbus启动的服务早于startx，所以变成了默认的hicolor主题。

解决方法是在dbus的通知服务文件org.freedesktop.Notifications.service加个选项「--platformtheme gtk3」。`Exec=/usr/bin/lxqt-notificationd --platformtheme gtk3`

重启桌面图标就正常了。