---
layout: post_layout
title: Mutt从零开始
time: 2020年02月06日 星期四
location: 中国
pulished: true
excerpt_separator: "<!--more-->"
---
闲来无事准备折腾下邮件客户端mutt，事先已有准备比较麻烦，还是估计错了折腾的难度。

安装配置基本按照archwiki的[Mutt](https://wiki.archlinux.org/index.php/Mutt)页，arch的mutt默认内置收邮件imap和发邮件smtp功能。内置的imap容易断线，建议换成[offlineimap](https://wiki.archlinux.org/index.php/OfflineIMAP)。内置的smtp把我坑了一下午，邮件发送地址默认弄成了系统host，结果一直发送失败，后来换了msmtp，编辑header改了发送地址才成功了。

wiki里的内容这里不再赘述，这里就做一下补充。mutt和offlineimap配置好接下来就需要配置gpg加密密码了，否则邮箱密码明文存储太危险了。如何配置上述两个wiki里都有详述。

#### **offlineimap定期启动**

默认offlineimap是后台静默运行的，可是容易遇到卡死。wiki里推荐了用systemd服务定期运行，而且可以设置卡死后自动杀死进程。

新建编辑下面文件设置定期启动间隔，下面设置的是5分钟。

```
~/.config/systemd/user/offlineimap-oneshot.timer.d/timer.conf

[Timer]
OnUnitInactiveSec=5m
```

这里设置的卡顿300秒后杀死，wiki里没加NotifyAccess行会导致此功能失效。

```
~/.config/systemd/user/offlineimap-oneshot.service.d/service.conf

[Service]
WatchdogSec=300
NotifyAccess=all
```

#### **mutt新邮件提醒**

新邮件提醒没有很不方便，网上也找到很多实现方式，可是太复杂且依赖外部程序太多。后来在网上找到了一个用内置mutt功能实现的新邮件提醒实现方式：[利用mutt的filter实现新邮件提醒](http://adam8157.info/blog/2010/05/mutt-filter-notify/)。

链接里的博文介绍得很清楚了，下面搬运过来。

首先在bin下「比如/usr/local/bin」新建一个脚本，将下面内容复制进去

```sh
#!/bin/sh

if [[ "$1" =~ "Inbox" && "$1" =~ "New:" ]];then
	notify-send -a mutt -i gmail -t 0 'Mutt' 'New mail arrived.                 '
else
	printf "\ekmutt\e\\" > /dev/tty
fi

echo "$1"

```

跟原作者的有所不同，可能因为邮箱服务器不同导致状态栏文字也不同，根据自己情况改下。就是检测有新邮件状态栏的文字特征变化然后发送一个notification。

然后在muttrc里加入下面的配置项，mutt-filter就是检测脚本的文件名。`set status_format="mutt-filter '-%r-Mutt: %f [Msgs:%?M?%M/?%m%?n? New:%n?%?o? Old:%o?%?d? Del:%d?%?F? Flag:%F?%?t? Tag:%t?%?p? Post:%p?%?b? Inc:%b?%?l? %l?]----%>-(%P)---'|"`



<img src="/assets/img/muttnotification.png" width="273px" />