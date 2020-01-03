---
layout: post_layout
title: 合并分区及更改分区顺序
time: 2019年11月27日 星期三
location: 中国
pulished: true
excerpt_separator: "```"
---





### 合并分区

更换ssd之前的老机械盘被当作了数据盘使用，但是因为之前分区的原因所以留下了两个分区。其实数据盘一个分区使用最方便，两个看着碍眼，使用上还要分配空间容量。一直想要合并分区，担心数据安全没敢，今天决定合并掉。

合并用gparted，之前分区为/dev/sdb1和/dev/sdb2，sdb1容量150多G，1t盘剩下的容量都给了sdb2。因为sdb1数据只有几G，干脆复制出来然后删除掉sdb1然后再调整sdb2的容量至全盘。

选择好了点应用操作，经过漫长的4个小时的等待，终于合并成功了。机械盘就是慢，ssd十几分钟大概就够了。

挂载了下新sdb2，数据都在，看来成功了。

### 更改分区顺序

但是现在有个小问题，sdb只有一个分区就是sdb2，为什么不自动顺移至sdb1？

重启了下也没变，还是sdb2，虽然不影响使用，总是个瑕疵。搜索了下说可以用fdisk里的f「修复分区顺序」搞定，试了下提示顺序无错，大概是因为只有一个分区所以顺序没问题。fdisk不太聪明的样子。

接下来总算发现了其它办法。「注意：分区表操作有风险」

1. 首先卸载分区sdb2

2. 用sfdisk导出分区表命令为sdb.bkp`sfdisk -d /dev/sdb > sdb.bkp`
<!--more-->
3. 然后备份一下`cp sdb.bkp sdb.new`

4. 现在开始编辑分区表sdb.new，我的是这样

   ```
   label: dos
   label-id: 0xa3cbc176
   device: /dev/sdb
   unit: sectors
   
   /dev/sdb2 : start=        2048, size=  1953521664, type=83
   ```

   

5. 把sdb2改成sdb1然后保存退出

6. 再导入新的分区表`sfdisk /dev/sdb < sdb.new`

7. lsblk看了下更改成功

   ```
      sdb      8:16   0 931.5G  0 disk
      └─sdb1   8:17   0 931.5G  0 part 
   ```

挂载新的sdb1看了下也没问题。
