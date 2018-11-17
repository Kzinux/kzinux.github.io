---
layout: post_layout
title: Linux下Firefox广告过滤之Adm
time: 2018年11月17日 星期六
location: 中国
pulished: true
excerpt_separator: "```"
---



Adbyby用了段时间总体还是很不错的，后台运行稳定，内存占用就3M多，广告过滤效果也还可以，但我决定换掉用[Adm-阿呆喵](http://doc.admflt.com/)。

Adbyby有个极大的缺点：linux平台下无法过滤https广告。

win平台的3.x已经支持https过滤，linux平台还停留在2.x，好像开发也不活跃，不知道哪里找作者反馈。https过滤如果放在路由上是有很大争议的，毕竟有风险。可是现在很多广告都是https的，不支持漏网之鱼挺碍眼的。

跟Adbyby不同，Adm是主pc平台的，主要是win，linux平台没有界面，设置依靠配置文件，也只有主要的几个基本功能。Adm的文档很丰富，按照文档来就行了。

官网提供下载的启动和停止脚本不要用，运行后内存瞬间爆满。iptables命令跟adbyby一样还是没效果，不过类似adbyby可以当作代理主机转发过去。

每次运行后会自动更新规则，默认规则效果就很不错了。https过滤采用黑名单制，先添加域名进去才能生效。

内存占用是adbyby的十倍，其实就是3M和30多M的区别。

支持全局代理和智能代理，不过我试了下ssr不行，提示说不是http数据。

对比adbyby的内存消耗极致优化，adm也是很优秀的，而且有维护更新，功能完善不至于有瘸腿走路的感觉。