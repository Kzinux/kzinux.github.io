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
<!--more-->

----------------------2022/11/05-------------------------

#### **Gpg密码加密**

把gpg密码加密记录下，有段时间了都快忘了。这里分为两个部分，mutt和offlineimap的密码加密，mutt需要密码发邮件，offlineimap要同步下载新邮件。

##### **offlineimap**

1. 首先在~/.offlineimap下新建个GPGdecrypt.py文件，内容如下，pass.gpg是事先加密好的邮箱密码或者授权码

   ```python
   from subprocess import check_output
   def get_pass():
       return check_output("gpg -dq ~/.offlineimap/pass.gpg", shell=True).splitlines()[0]
   ```

   

2. 编辑.offlineimaprc将pythonfile=项设置为上面py文件的路径地址

3. 编辑.offlineimaprc设置`remotepasseval = get_pass()`

##### **mutt**

1. 在.mutt下新建一个文件mypass.txt，内容如下，双引号中间填入邮箱密码或者授权码`set my_pass = ""`

2. gpg加密生成加密文件，重命名为mypass.gpg

3. 编辑muttrc，加入这一行`source "gpg -dq $HOME/.mutt/mypass.gpg |"`

4. 编辑muttrc，设置 `set smtp_pass="$my_pass"`

#### **offlineimap定期启动**

默认offlineimap是后台静默运行的，可是容易遇到卡死。wiki里推荐了用systemd服务定期运行，而且可以设置卡死后自动杀死进程。

*新建编辑下面文件设置定期启动间隔，下面设置的是5分钟。*

```
~/.config/systemd/user/offlineimap-oneshot.timer.d/timer.conf

[Timer]
OnUnitInactiveSec=5m
```

*这里设置的卡顿300秒后杀死，wiki里没加NotifyAccess行会导致此功能失效。*

```
~/.config/systemd/user/offlineimap-oneshot.service.d/service.conf

[Service]
WatchdogSec=300
NotifyAccess=all
```

--------------2020/02/09------------------

用systemd启动一段时间，有次发现offlineimap又卡住了，而且watchdog也无法杀死进程。我用kill命令也无法杀死offlineimap进程，要用kill -9才能强制杀死。

于是搜索了下能不能在offlineimap启动后一段时间后强制结束进程，不过似乎没有这种程序。倒是找到个博客文章解决了这个需求：[Reliable IMAP synchronization with IDLE support](https://blog.mister-muffin.de/2013/06/05/reliable-imap-synchronization-with-idle-support) 。

用脚本定期每隔5分钟启动offlineimap并且一分钟后结束offlineimap，用了几天挺稳定的没遇到问题。

把下面的脚本放到开机启动里就行了，我没用notmuch所以notmuch那行就不需要了。

```sh
#!/bin/sh
#!/bin/bash
#!/bin/zsh

while true; do
    timeout --signal=KILL 1m offlineimap
    sleep 5m
done
```

------2020/02/28------

已经稳定运行20多天了，简单一个脚本比systemd的复杂方式可靠多了。

#### **mutt新邮件提醒**

~~新邮件提醒没有很不方便，网上也找到很多实现方式，可是太复杂且依赖外部程序太多。后来在网上找到了一个用内置mutt功能实现的新邮件提醒实现方式：[利用mutt的filter实现新邮件提醒](http://adam8157.info/blog/2010/05/mutt-filter-notify/)。~~

~~链接里的博文介绍得很清楚了，下面搬运过来。~~

~~首先在bin下「比如/usr/local/bin」新建一个脚本，将下面内容复制进去~~

```sh
#!/bin/sh

if [[ "$1" =~ "Inbox" && "$1" =~ "New:" ]];then
	notify-send -a mutt -i gmail 'Mutt' 'New mail arrived.                 '
else
	printf "\ekmutt\e\\" > /dev/tty
fi

echo "$1"
```

~~跟原作者的有所不同，可能因为邮箱服务器不同导致状态栏文字也不同，根据自己情况改下。就是检测有新邮件状态栏的文字特征变化然后发送一个notification。~~

~~然后在muttrc里加入下面的配置项，mutt-filter就是检测脚本的文件名。~~

```sh
set status_format="mutt-filter '-%r-Mutt: %f [Msgs:%?M?%M/?%m%?n? New:%n?%?o? Old:%o?%?d? Del:%d?%?F? Flag:%F?%?t? Tag:%t?%?p? Post:%p?%?b? Inc:%b?%?l? %l?]----%>-(%P)---'|"
```

<img src="/assets/img/muttnotification.png" width="273px" />

----------------------2020-11-03------------------------

邮件提醒系统已换——[Offlineimap实现后台邮件提醒](https://kzinux.github.io/2020/11/04/Offlineimap-Mail-Notification.html)。

<img src="/assets/img/MailNotification.png" width="588px" />

---------------2020-09-24---------------------

发现邮件多了每个notification都要手动取消很麻烦，但是如果让它自动消失，因为我没装状态栏所以会错过邮件提醒。记得conky里有邮件的相关变量，打开manpage果然有，而且很多，邮件总数、新邮件、垃圾邮件、已发送邮件等等。这里我只需要新邮件数变量，根据manpage加入如下变量即可，还可以设置更新频率。

```sh
${new_mails ${HOME}/.mutt/cache/mail/Inbox}
```

<img src="/assets/img/conky-mail.png" width="820px" />

#### **已读邮件自动同步**

~~mutt默认打开新邮件后并不会将已读标记同步至本地邮件，这样offlineimap同步后服务器上的邮件还是未读标记。用$快捷键绑定的sync-mailbox可以手动同步，可是有点麻烦。可以将打开邮件的命令display-message和同步命令绑定在一起，这样打开邮件的同时就同步一次。~~

~~下面是绑定的打开邮件的新命令配置。~~

```xml
macro index,pager    <Return>  "<display-message><sync-mailbox>"                                 "open mail and sync"
```

----------------------2020-11-03------------------------

绑定同步本地邮件命令到回车键导致每次打开邮件都卡一下，还是换回默认的，退出mutt会自动同步或者按$手动同步。
