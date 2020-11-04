---
layout: post_layout
title: Offlineimap实现后台邮件提醒
time: 2020年11月04日 星期三
location: 中国
pulished: true
excerpt_separator: "<!--more-->"

---

之前用mutt自带设置弄了个邮件提醒，简单但是缺点也多。

1. 只能提醒新邮件，邮件标题和发件人不能显示在通知里;
2. 依据mutt状态栏实现的，只要状态栏有new即未读邮件，那么每次更新邮件状态都会弹出提醒;
3. mutt要开着，也算个小缺点，虽然mutt占用很小。

新邮件提醒系统依赖offlineimap同步的本地邮件实现，用inotify-tools的inotifywait命令监控邮件目录写入，从而实时弹出邮件提醒通知。通知功能网上找的几个脚本参考融合了下，邮件标题和发件人提取用notmuch实现。

```mermaid
graph LR
A[邮件服务器] -->|Offlineimap|B(本地文件)
    B --> C[ionotifywait监控写入]
    C -->|新的写入| D[通知Notification]
    C -->|新的写入| E[Notmuch更新数据库]
    E -->|提供邮件标题和发件人| D
```

<img src="/assets/img/MailNotification.png" width="536px" />

<!--more-->

本地的邮件使用特殊的编码写的，直接提取标题和发件人如果包含中文就会乱码。要找个能提取邮件标题和发件人的工具，现成的工具很多都是针对英文邮件不适用于中文。后来找到个python脚本可以，提取邮件标题脚本如下。

```python
#!/usr/bin/env python3
from email.parser import BytesParser
from email.header import Header, decode_header
from email.errors import HeaderParseError
from sys import argv

for filename in argv[1:]:
    with open(filename, 'rb') as handle:  # handle file not found etc?
        message = BytesParser().parse(handle)
    try:
        subj = ''.join([
            frag.decode(enc if enc else "utf-8")
                if isinstance(frag, bytes) else frag
                    for frag, enc in decode_header(message['subject'])])
    except (HeaderParseError, UnicodeDecodeError): # maybe warn about error?
        subj = message['subject'] 
        subj = subj.decode("utf-8") if isinstance(subj, bytes) else subj
    print(subj)
	
```

提取邮件发件人脚本如下。

```python
#!/usr/bin/env python3
from email.parser import BytesParser
from email.header import Header, decode_header
from email.errors import HeaderParseError
from sys import argv

for filename in argv[1:]:
    with open(filename, 'rb') as handle:  # handle file not found etc?
        message = BytesParser().parse(handle)
    try:
        subj = ''.join([
            frag.decode(enc if enc else "utf-8")
                if isinstance(frag, bytes) else frag
                    for frag, enc in decode_header(message['from'])])
    except (HeaderParseError, UnicodeDecodeError): # maybe warn about error?
        subj = message['from'] 
        subj = subj.decode("utf-8") if isinstance(subj, bytes) else subj
    print(subj)

```

用上面的python脚本读取邮件文件就可以提取邮件标题或者邮件发件人。不过还是觉得用软件工具好，毕竟有人维护，不当心失效。不懂python失效了还得重新找，所以考虑了下还是用notmuch。

```sh
#!/bin/sh

# WHAT IS THIS SHIT?
	# It's a script comprising of shitty regexes that notifies you,
	# when there has been a change in you mailbox dir. 
	# Works best with offlineimap.

# Author:https://gist.github.com/edison23/02aacf1361cea8fb49f8

# PREREQUISITIES
	# Depends on inotify-tools and libnotify

# THANKS
	# to Nicolas.Estibals (https://bbs.archlinux.org/viewtopic.php?pid=933429#p933429)
	# for inspiration.

# NOTE
	# inotifywait output usually looks like /home/user/.mail/myname/INBOX/new/ CREATE 1434095160_4.29513.my-Computer\,U\=13529\,FMD5\=7e33429f656f1e6e9d79b29c3f82c57e\:2\,

# SETUP
	MAILBOX="/home/user/mail" # what's you maildir name
	TIMOUT="10000" # notification timeout

# MAGIC WITH SHITTY REGEXES
while true; do
	# watch for changes in folder
	NOTIFY=`inotifywait -e create -e moved_to ${MAILBOX}/mail/INBOX/new ${MAILBOX}/mail/Junk/new 2> /dev/null`
	
	# sed out stuff (folder name and file path) from inotifywait output and create popup
    notmuch new
    FROM=`notmuch search --sort=newest-first --limit=1 --output=summary '*unread*' | cut -d';' -f1|cut -d']' -f 2`
    SUBJECT=`notmuch search --sort=newest-first --limit=1 --output=summary '*unread*' | cut -d';' -f2`
	notify-send -a Offlineimap -i trojita -t $TIMOUT "Offlineimap" "From: $FROM\n$SUBJECT" 
done

```

这样当有新邮件时就会弹窗提醒，而且不依赖邮件客户端，只要后台开着offlineimap同步邮件即可。

