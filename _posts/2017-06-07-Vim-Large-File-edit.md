---
layout: post_layout
title: Vim大文件编辑性能
time: 2017年06月07日 星期三
location: 中国
pulished: true
excerpt_separator: "```"
---
自用的一个sdcv词典有225M，之前都是用vim编辑整理格式的。最近又打算继续完善下，结果用vim打开后发现可视模式和插入模式输入时都很卡。网上搜了下，类似情况的一般都是代码高亮的问题，我这个几乎没有高亮。不过也提供了解决的方法思路。

### 首先排除配置文件问题
刚开始还傻傻的用二分法慢慢排除，最后发现把vimrc直接清空还是卡。
### 检查vim启动耗时情况
用下面的命令启动vim编辑文件，timefile是记录启动过程耗时的文件，file是待编辑文件，这里就是sdcv词典。
```bash
vim --startuptime timefile file
```
结果发现vim-airline自带的wordcount插件严重拖慢了启动速度，这也不意外，文件越大计算时间越长。
### 禁用wordcount插件
将下面的代码加到vimrc禁用wordcount功能。
```vim
let g:airline#extensions#wordcount#enabled = 0
```
再次打开sdcv词典，输入和可视模式都正常了。应该是输入是wordcount不停计算字词数占用大量cpu导致卡顿的，但是可视模式为何也卡我还想不通。
