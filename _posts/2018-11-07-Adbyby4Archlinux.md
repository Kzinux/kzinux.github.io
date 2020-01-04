---
layout: post_layout
title: Linux下Firefox广告过滤之Adbyby
time: 2018年11月07日 星期三
location: 中国
pulished: true
excerpt_separator: "<!--more-->"
---



广告过滤一直是我必装的浏览器扩展，从早期一统江湖的Adblock Plus到现在风光无线的ublock，我也没想过有一天会考虑卸载它们。

起因是升级Firefox63后linux下的webextension也变成独立进程了，这样就可以很直观的看出来扩展消耗的资源。一段时间后发现浏览器打开网站多了或者打开时间长了webextension进程内存会由原来的几十M增加到2，300甚至400多M，而且不会主动降下来释放内存。即使你把标签都关了只剩一个，只能重启。还有个取巧的方法就是打开ublock扩展设置页面(其它扩展设置页面也可以)就可以马上释放内存降回几十M，真的是很诡异。

排查了一段时间发现这类对网页有大量操作修改的扩展都是内存杀手，但是广告过滤又是不可或缺的。我试着禁用了广告过滤上了会网无法忍受，网页加载速度明显变慢还有大量各类影响浏览的广告。

广告过滤也是被逼出来的，如果广告量适度也不会费这劲。chrome听说内置了广告过滤对付这类恶意广告也维持广告业良性发展，不过chrome势大不代表firefox也可以这么做。

本来对除了扩展的广告过滤也不了解，看了下科普linux下可用的当然最好是路由器采用的广告过滤方案，[Adbyby](http://www.adbyby.com/index.htm)和[Koolproxy](https://koolproxy.io/)。前者是老牌的路由广告过滤软件，koolproxy则是这两年的后起之秀。
<!--more-->
本来想都试试的，结果昨天adbyby官网挂了我就先下载了koolproxy试用，下载x64包解压，就一个可执行文件，直接运行启动很简单，未见配置文件，不知如何生效。到官方论坛看了下结果作者说未对linux适配，要用iptables命令手动设置，六七条命令看得我迷迷糊糊的，不懂也不想折腾iptables我就放弃了。

Adbyby还好还有个github镜像站，顺利下载了x64包解压这回文件多了些，有adbyby主程序还有配置文件规则数据之类的，先打开配置文件adhook.ini做如下改动

```xml
listen-address=127.0.0.1:8118
```

规则同步似乎没动静，我就用了别人维护的规则并且关了同步

```xml
rule=none
```

里面有个startadbyby脚本，看了下是后台运行adbyby并且设置iptables透明代理，需要root权限运行否则iptables命令会提示权限不够。试了下发现命令在archlinux下执行后无效，我也懒得查了，直接启动adbyby作为一个本地代理，地址就是上面的127.0.0.1:8118，然后将系统代理设置成这个地址就可以将网页转发给adbyby过滤广告了。

对于需要科学上网的网友，可以将默认地址设置成adbyby的监听地址，这个有个缺点就是代理访问的网站就无法广告过滤了。我用的是pac管理代理，做如下设置即可。

```javascript
function FindProxyForURL(url, host) {
//ShadowSocks
    var PROXY = "SOCKS5 127.0.0.1:9999";
//Adbyby
      var DEFAULT = "PROXY 127.0.0.1:8118";

```

data文件夹下还有个user.txt自定义规则，规则兼容[ABP语法](https://adblockplus.org/zh_CN/filters)。

浏览器扩展效率毕竟不如软件，adbyby内存消耗才3M多，cpu也几乎不吃，路由上运行的软件对资源消耗控制是重中之重。网页表现也很好，过滤了一堆广告网站加载又流畅了，也没有卡网之类的问题。跟扩展比有个缺点就是无法实时编辑增删规则，需要重启。

adbyby是全平台开发的，性能优异、资源消耗低，以后就用这个了。
