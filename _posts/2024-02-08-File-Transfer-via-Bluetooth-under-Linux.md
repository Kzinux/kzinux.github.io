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
