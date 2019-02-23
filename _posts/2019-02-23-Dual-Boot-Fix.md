---
layout: post_layout
title: Win下双系统Grub启动修复
time: 2019年02月23日 星期六
location: 中国
pulished: true
excerpt_separator: "```"
---



事情起因得从正版说起，昨天忽然想到win下玩玩游戏，切到win7下结果发现右下角的“此windows副本不是正版”字眼。

搜了几个序列号都无法激活，过了12点直接给黑屏了，据说越往后正版弹窗系统限制越多。没法百度找了个排最前下载最多的激活工具，一键激活后显示成功让重启，一重启经过grub菜单选择win7后又跳到grub的命令行界面。了解下原来是激活工具有用到grub，激活失败了。

事前不仔细，事后穷忙活。win7进不去了，进arch重新生成grub.cfg然后重启win7依旧grub界面。这种情况多是让修复mbr，但是我双系统引导的grub就装在mbr，修复了双系统grub会受影响。后来又找到个方法可以用命令进win7，那就先进去。

`find --set-root /bootmgr`

`chainloader /bootmgr`

`boot`

每行一个命令回车执行后输入下一行，倒是进去了但是没法修复，不能每次启动都这样。

只能修复mbr，下载easybcd，里面有个修复mbr的选项。修复后重启果然进win7了，但是双系统引导grub又没了。常规方法是用arch的安装包做成启动盘修复，但是麻烦。后来发现easybcd里还可以添加linux引导，用默认的grub(legacy)试了没用，用grub2的自动搜索linux试了下竟然成功了。

进了arch就简单了，重新安装grub到mbr，然后重新生成grub.cfg就成了。

`grub-install --target=i386-pc /dev/sdx`

`grub-mkconfig -o /boot/grub/grub.cfg`

第一行x就是双系统所在盘，比如我的是sda，第二行重新生成grub.cfg，重启就恢复grub了。

最后就是win7的激活，这回了解了下没用山寨工具，本来整怕了想用序列号激活不是无效就是激活错误。搜索错误代码用了几个办法都没用还是无法激活，最后下载了小马的Oem7F7激活成功。之前那个山寨的大概是把小马的重新打包了下，界面类似，图标是个龙卷风叫暴风什么的。