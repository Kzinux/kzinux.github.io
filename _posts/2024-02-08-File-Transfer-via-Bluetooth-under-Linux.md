---
layout: post_layout
title: Linux下和手机通过蓝牙互传文件
time: 2024年02月08日 星期四
location: 中国
pulished: true
excerpt_separator: "<!--more-->"
---
手机和arch互传文件一直挺麻烦，插typec线挂载麻烦，网盘上传再下载麻烦，微信互传会压缩画质也麻烦。最后还是用蓝牙方便快捷，蓝牙一直没整，照着[wiki](https://wiki.archlinuxcn.org/wiki/%E8%93%9D%E7%89%99)弄下来还算简单方便。<!--more-->

### **安装支持**  ###
安装bluez包，用'modinfo btusb'检查蓝牙驱动是否已加载。

### **启用服务**  ###
'systemctl start bluetooth.service'启用服务，顺便再让其自启动'systemctl enable bluetooth.service'

### **添加用户组**  ###
连接蓝牙设备需要加入lp组，用'gpasswd -a user lp'命令将当前用户加入lp组。

### **前端界面**  ###
不想用命令行，blueberry不支持发送文件，选择blueman依赖还少跟gnome没关系。

### **连接应用**  ###
打开安装好的blueman，就可以看到打开蓝牙的手机，直接连接就行了。但是试了下发送文件却失败，提示‘name is not activatable’，搜索了下原来发送文件需要安装bluez-obex包，wiki上没提(原来是中文版翻译没跟上，英文页面有提到obex从bluez分离出来需要单独安装)。 安装后试了下从手机往arch和arch往手机发送都成功了。另外blueman-视图-本地服务-传输里面勾选'接受由受信设备发送的文件'就不用每次手动确认传入文件了。

### **后台优化**  ###
发现blueman原来是python程序，还带了applet，tray之类的用不上的几个拖油瓶。如果只是随用随关倒无所谓，但是彻底关闭blueman后传文件就失败了，连接是没问题的。

研究了下传文件是obexd服务管的，为什么一定要开blueman才能传。应该是文件接收默认需要确认，而确认是由blueman管的，那只要让obxed不要询问默认静默接收就没问题了。方法就是添加-a参数给obxed，另外还有-r自定义接收文件保存路径。在/etc/systemd/user下新建obex.service.d文件夹，再新建个fileReceive.conf的文件，内容如下，覆盖service默认execstart需要先清空否则会报错：

```
[Service]
ExecStart=
ExecStart=/usr/lib/bluetooth/obexd -r /home/user/Downloads -a
```
这样只需要enable一个obex服务就可以接收手机文件了，需要发送文件再打开臃肿的blueman。blueman关闭只是关闭前端，后台还有几个拖油瓶需要手动关闭，自启动也可以取消了，在/etc/xdg/autostart/下。

<img src="/assets/img/bluetooth.png" width="435px" />

-------------------------------------2024/02/21---------------------------------------

难得重启一次居然发现obex没有自动启动，明明enable了服务。打开service文件是dbus什么的，猜测应该是按需启动由blueman之类的通过dbus唤起。搜索一番，enable了服务却不自启动原因五花八门，终于看到个说是因为没有wantedby所以不会被启动。在上面的自定义文件fileReceive里添加wantedby再加个after保险，重启系统obex自启动了。

```
[Unit]
After=bluetooth.service

[Service]
ExecStart=
ExecStart=/usr/lib/bluetooth/obexd -r /home/user/Downloads -a

[Install]
WantedBy=default.target
```


### **命令行选择**  ###
blueman虽然简单好用，但是每次启动带一堆拖油瓶和android流氓一样让人不爽。而且默认自启动，每次更新都得删那个desktop，还是得找个轻巧的替换。

gui前端除了gnome和kde深度绑定的，其他要么功能不全，要么也是python还不如blueman。github上搜了下发现好多蓝牙管理的程序，看到bluez-tools支持发送文件还有连接之类的，extra源里的直接安装。自带的bt-obex命令试了下发送文件成功，还可以作为obex服务端替代bluez-obex。感觉这个全能啊可以替代blueman和bluez-obex，有这个其他的都不需要。

看到bluez-tools页面下方有提到有些问题没解决，看来还是用来当给手机发送文件的工具，留着bluez-utils做连接管理工具，继续用bluez-obex做文件接收后台。
