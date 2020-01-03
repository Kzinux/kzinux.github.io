---
layout: post_layout
title: Sdcv使用和词典排序
time: 2017年06月08日 星期四
location: 中国
pulished: true
excerpt_separator: "```"
---
Sdcv是星际译王的命令行版。用过图形界面的翻译软件，为了快速调用就要让其常驻后台，否则每次重新打开等待受不了。即使这样从后台切到前台还是有点迟滞，就找到了小巧强大的sdcv。

+ #### 简单使用技巧

从源里安装sdcv后就可以在终端调用了，字典放在/usr/share/stardict/dic文件夹下。
1. 最简单的查询：
```bash
    sdcv 字词
```
2. 连续查询可以输入sdcv后再输入字词，查询完毕后ctrl c退出。
3. 如果不确定完整的待查字词，可以用？（匹配一个字符）\*（匹配多个字符）模糊查询：
```bash
    sdcv 'cultu?e'
```
```bash
    sdcv 'cult*e'
```
4. 还有一种全文查询，在词典里全文搜索，而不是只匹配词条：
```bash
    sdcv '|字词'
```
<!--more-->
<img src="/assets/img/sdcv1.png" width="267px" />

可以看到全文查询清漪园，由于没有词条匹配，所以显示的是颐和园的释义，因为颐和园里有清漪园匹配到了。

+ #### 词典排序

为了字典排序当时颇费了一番心思，因为当时没有此功能。最近的版本作者加入了排序功能，新建~/.sdcv_ordering，按照sdcv -l里的词典名称排序，每行一个词典。查询结果就会按照此顺序显示。

<img src="/assets/img/sdcv2.png" width="195px" />
