---
layout: post_layout
title: 禁用Acessibility解决at-spi报错
time: 2022年11月25日 星期五
location: 中国
pulished: true
excerpt_separator: "<!--more-->"
---
Acessibility是盲人无障碍服务，用不上却不停跳出来就很困扰。最近跟无障碍服务杠上了，记录下解决方法。
<!--more-->
1. 首先是淘宝网页的无障碍工具条，你弄个复杂的快捷键唤起就行了，设置了一堆快捷键都可以唤起工具条。特别alt '1-9'是我常用的切换firefox标签页的快捷键，在淘宝页面按下之后就弹出老大的工具条还开始自我介绍，系统音量大时吓人一跳。
找了一圈没有网页禁用设置，其实如果安装ublock和adblock之类的找到对应脚本禁用它挺简单。不过不用广告过滤很久了，现在网页广告不像十几年前那么丧心病狂，而且过滤类扩展资源消耗和占用反而拖慢了浏览速度。后来看到一直装着的redirector就想能不能让其起到类似的过滤作用，用firefox自带网页工具定位了无障碍服务的脚本和资源都在g.alicdn.com/dinamic/barrier-free/下，用下面规则并把高级选项里的apply to全勾选上。
```
 Redirect:	^https://g\.alicdn\.com/dinamic/barrier-free/.*
 to:		http://127.0.0.1/ 
 ```
刷新网页再用alt 1快捷键就不会唤起无障碍工具条了。
redirecotr还挺厉害的，本来想把reddit网页快捷键冲突也解决了，但是reddit网页太垃圾了，一堆js导致网页卡顿臃肿缓慢。 用redirector把reddit都转到np.reddit.com去，功能上没区别但是流畅很多，就是界面难看排版太烂，自己写了点css改成紧凑舒服的样式。

2. 然后就是最近遇到的终端启动图形界面刷刷一堆at-spi报错，类似`AT-SPI: Could not obtain desktop path or name`这种错误信息刷屏。解决方法很简单，禁用无障碍服务，把下面代码加到/etc/enviroment里`NO_AT_BRIDGE=1`，重新登录系统使设置生效，再打开图形程序就没错误信息了。