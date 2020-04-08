---
layout: post_layout
title: Ext4丢失的空间
time: 2020年04月08日 星期三
location: 中国
pulished: true
excerpt_separator: "<!--more-->"
---
linux装在ssd上，还有块1t机械盘挂载了用来放数据，但是用df查看空间时发现1t盘总空间只有916GB。按理说1t盘1024GB按厂商算法应该有1024GB/1.024/1.024/1.024/1.024=930.66GB，分区的保留空间Reserved blocks也是算进去的，而且这块数据盘的保留空间已经设置成0了。

`931-916=15GB`

这少了的15GB哪去了？

不弄清楚心里痒痒的，搜索了半天终于找到了线索——ext4文件系统的索引节点（inode）。

"<!--more-->"

首先介绍下inode

> inode包含文件的元信息，具体来说有以下内容：
> 　　* 文件的字节数
> 　　* 文件拥有者的User ID
> 　　* 文件的Group ID
> 　　* 文件的读、写、执行权限
> 　　* 文件的时间戳，共有三个：ctime指inode上一次变动的时间，mtime指文件内容上一次变动的时间，atime指文件上一次打开的时间。
> 　　* 链接数，即有多少文件名指向这个inode
>   * 文件数据block的位置
>
> 参考：https://blog.csdn.net/jaryle/java/article/details/78189427

一个文件需要一个inode，如果一个磁盘文件太多把inode用完了，即使磁盘空间还有剩余也无法写入新文件。查看inode可以用df -i。

```
文件系统         Inodes 已用(I)  可用(I) 已用(I)% 挂载点
/dev/sdb1      61054976   67384 60987592       1% /mnt/Media
```

可以看到这块磁盘的inode才用了千分之一，不到1%算作1%，总共6千1百万个。实在是预留的太多了，不过要重新设置需要mkfs重新格式化，还是算了。

而且inode也要占用磁盘空间的，一般分区格式化时就会预留出空间给inode。

下面是1tb盘的inode信息：

```
Inode count:              61054976
Inode size:               256
```

可以据此计算占用空间为：((61054976×256/1024)/1024)/1024=14.556640625GB

总算水落石出了。