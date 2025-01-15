---
layout: post_layout
title: Firefox配置缓存内存盘迁移
time: 2025年01月08日 星期三
location: 中国
pulished: true
excerpt_separator: "<!--more-->"
---
平常电脑都是睡眠，firefox也长期不关，有时连续用了十几天几个月就会发现硬盘写入会有几十G甚至上百G，读取相比之下反而少很多大概只有写入的1/6到1/7。用smartctl看ssd的写入也是一样，写入总量是读取的好多倍。
<!--more-->

虽然现在ssd速度瓶颈不明显，但是很多内存空着也是空着，不如用来改善firefox的读写。首先想到的就是网页缓存，平时上网缓存不断下载更新读取都是大量的读写操作。

## **缓存cache** ##
firefox默认的缓存就在配置文件夹下的cache2文件夹内，要将缓存移到内存有两种方法。
#### **关闭硬盘缓存** ####
firefox默认有内存缓存和硬盘缓存，关闭硬盘缓存就会只使用内存缓存，缺点是关闭firefox后缓存就会丢失，详见[archwiki](https://wiki.archlinuxcn.org/wiki/Firefox/%E5%BE%AE%E8%B0%83#%E5%85%B3%E9%97%AD%E7%A3%81%E7%9B%98%E7%BC%93%E5%AD%98)。
#### **更改硬盘缓存位置** ####
将缓存位置更改到/run/user/id下就可以利用系统的内存盘tempfs实现，优点是关闭firefox后缓存不会丢失，只有重启系统才会刷新缓存，详见[archwiki](https://wiki.archlinuxcn.org/wiki/Firefox/%E5%BE%AE%E8%B0%83#%E5%B0%86%E7%A3%81%E7%9B%98%E7%BC%93%E5%AD%98%E7%A7%BB%E5%8A%A8%E5%88%B0%E5%86%85%E5%AD%98%E4%B8%AD)。

## **配置文件**  ##
把缓存放到内存盘后，用了几个星期发现firefox写入还是很高有7，80G，看来不只是缓存，配置文件夹平时也会进行大量的写入。

查了下archwiki上有专门的页面介绍配置文件内存盘迁移，[Firefox/在 RAM 中存储配置](https://wiki.archlinuxcn.org/wiki/Firefox/%E5%9C%A8_RAM_%E4%B8%AD%E5%AD%98%E5%82%A8%E9%85%8D%E7%BD%AE)。

主要方法有两种，安装软件或者自己用脚本配置。有现成软件当然要，加上一个源里的包一个aur的自然选官方源的。
#### **Profile-sync-daemon**  ####
安装配置参考[Profile-sync-daemon的archwiki](https://wiki.archlinuxcn.org/wiki/Profile-sync-daemon)，配置项很简单，将firefox加入想要迁移内存的列表程序里，如果担心默认的tempfs不够可以参考archwiki里的方法扩大容量。

另外就是推荐打开Overlayfs,只挂载配置文件内更改的数据而不是将整个配置文件夹无脑复制过去，用psd parse命令可以看到实际占用的内存盘空间不到配置文件夹大小的一半。

Psd默认每隔一小时将内存盘中的文件更改同步回硬盘中，可以自定义时间间隔。

最后Psd默认也会将配置文件夹内的cache2缓存移到内存盘，优点就是即使关机重启缓存也不会丢失。需要这点的可以将上面配置的更改缓存的步骤还原，但是考虑到网页缓存的保留没必要还会增加Psd同步更改时的写入，就没有将缓存位置改回默认的配置文件夹cache2内。

这样硬盘缓存还在内存盘/run下，却不在Psd的同步文件夹下。


-----------------------------------------
使用一周后firefox的读写总量，800M多一点的读取，刚打开就是790多M，一周后增加了十几M，写入20多M也是差不多从打开时的几M增加了十几M。和过去动辄几十上百G的读取写入相比完全可以忽略不计。

<img src="/assets/img/firefoxIO1week.png" width="350px" />
