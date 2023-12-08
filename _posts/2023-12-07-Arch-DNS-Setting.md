---
layout: post_layout
title: Arch设置自定义dns及dot设置验证
time: 2023年12月07日 星期四
location: 中国
pulished: true
excerpt_separator: "<!--more-->"
---
原来一直用着运营商的dns，被墙的站点翻墙就行了。最近换成了v2raya用上了透明代理，有个站点加到黑名单列表里还是无法访问。nslookup一下ip是127.0.0.1，但是设置成全局或者用firefox的pac设置用socks5却又可以访问。不懂其中的道道想来是dns污染的问题，v2raya自带防污染设置，本来觉得是件挺简单的事情，没想到花了不少功夫才解决。
<!--more-->

## v2raya自带防污染设置
里面的三个选项都没用，只有最后一个高级设置起作用不再是127.0.0.1可以访问了。看到配置界面下面有个“国外域名查询服务器”，就试着照着上面格式填了个“8.8.8.8 -> proxy”，谁知道生效后内存狂涨把系统都卡死了，几个月没关机给破功了按电源键强制关机，重启时又是一样内存狂涨，打开htop看了下确认是v2raya赶紧又重启了，然后这次登录后马上systemctl停了v2raya服务。总算进了桌面，手忙脚乱的把8888给删掉就正常了。到issues里看了下好几个反映这个问题的可能精力问题还没修复。
不过有用就行，用了一天都挺好结果突然又是内存狂涨，还好人在电脑前赶紧停了v2raya，看来这个dns防污染的高级设置有bug，老这样谁受得了啊。这个选项作用就是设置了个dns列表比如阿里opendns之类的，那自己把dns改成阿里的不就行了。
## archlinux系统dns设置
### systemd-resolved
dns印象中应该归systemd-resolved管，打开对应的archwiki页面看了下，自定义也简单，新建个/etc/systemd/resolved.conf.d/dns_servers.conf里面写上dns就行。重启systemd-resolved服务后再用resolvectl status看Global已经变成阿里dns了，不过下面的wlan0还是原来的，而且nslookup结果没变也是原来的。
### systemd-networkd
要改wlan0的dns也简单，systemd-networkd的archwiki也有介绍，不过单独改这里的dns没用。即使resolvectl status下wlan0已经变成阿里dns，nslookup还是原来的dns和结果。
### /etc/resolv.conf
又看了下wiki，这个文件才是浏览器读取的dns，里面一直显示192.168.1.1，即使强行更改也会变回去。文件注释里说是wlan0.dhcp生成的，但是systemd-networkd设置固定ip关闭dhcp还是会生成。难道是iwd这个wifi管理软件搞鬼，看了下iwd的wiki页面说默认是用的systemd-resolved的dns设置。又搜索了下原来是dhcpcd搞鬼，dhcpcd有自己的一套dns设置，会覆盖/etc/resolv.conf。archwiki页面有介绍禁用dhcpcd覆盖resolv.conf和设置自定义dns的方法。~~更简单的就是新建个/etc/resolv.conf.head，里面写上nameserver 223.5.5.5就会优先排在/etc/resolv.conf的顶部不会被覆盖。设置后重启systemd-networkd再nslookup结果就没被污染了dns也变成阿里的了。~~

resolv.conf.head的方法不管用，因为虽然添加了阿里dns但是dhcpcd添加的192.168.1.1也在resolv.conf里，而使用dns并不是由先后位置决定的。resolvectl status和nslookup里可以看到当前dns是随机在几个候选里变化的，轮到192.168.1.1时就完蛋。还是要设置dhcpcd的固定dns，方法archwiki也有加个设置项static domain_name_servers=223.5.5.5到/etc/dhcpcd.conf里。重启dhcpcd和systemd-resolved,systemd-networkd服务后resolv.conf里就只有阿里dns了。

resolv.conf解决了还要将wlan的dns也改过来，上面systemd-networkd有提到对应archwiki里有方法。否则一段时间后可能解析又变成192.168.1.1了。

折腾了这么多前面方向就错了，关键就是resolv.conf，兵家必争之地，很多网络管理程序都会直接覆盖resolv.conf。~~设置resolv.conf.head就是高优先级不会被覆盖~~而且systemd-resolved也是遵循resolv.conf的dns设置。之前以为resolv.conf会服从于systemd-resolved谁知道是反过来的，本末倒置瞎折腾了一番。

## DNs over tls设置
上面通过dhcpcd设置dns的方法还是不够妥当，还是让systemd-resolved接管dns相关的设置。顺便介绍下dot设置以及验证是否启用。
### dncpcd
要接管dns设置先排除掉dncpcd对resolv.conf的覆盖写入，编辑/etc/dhcpcd.conf最后面加入下面内容。重启dhcpcd服务就生效了，这样dhcpcd就不会再对resolv.conf写入了。
`nohook resolv.conf`
### systemd-resolved启用dot
新建/etc/systemd/resolved.conf.d/文件夹，然后在新文件夹下新建个文件dns_over_tls.conf，内容如下，重启systemd-resolved服务使之生效。
```
[Resolve]
DNS=223.5.5.5
DNSOverTLS=yes
```
接下来要让systemd-resolved接管/etc/resolv.conf，按wiki里说的运行以下命令。`ln -sf ../run/systemd/resolve/stub-resolv.conf /etc/resolv.conf`
将systemd-resolved的配置软链接到resolv.conf接管设置。

这样就设置完成，要验证wiki里也有提到方法，因为普通dns走53端口，dot的dns走853端口，所以监控853和53端口，在ping域名解析的时候是否有数据包，如果853端口没有53端口有那就是普通dns，如果853端口有流量而53端口没有那就是启用了dot。通过ngrep或者tcpdump命令都可以。
```
ngrep port 853
tcpdump -ni wlan0 -p 853
```