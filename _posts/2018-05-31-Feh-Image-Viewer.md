---
layout: post_layout
title: Feh图片查看器
time: 2018年05月31日 星期四
location: 中国
pulished: true
excerpt_separator: "<!--more-->"
---



用了几年linux图片查看器也用了不少了，但是一直没有合适满意的。最早把主流的图片查看器都试用了一遍，包括eog、gpicviewer、gthumb、gwenview、ristretto之类的。对图片查看器有几点要求，奈何做得到的不多。

1. #### 首先打开速度加载要快

   作为一个图片查看器这是基础之一，臃肿缓慢是无法容忍的，毕竟不是图片编辑器。

2. #### 其次是丰富可自定义键盘快捷键

   平常用惯了vim式的键盘浏览，对快捷键有特殊需求。
<!--more-->

3. #### 最后是简洁明了的界面
无论是自带或者是自定义的，一般我只留下状态栏，工具栏、菜单栏还有乱七八糟的什么其它界面都隐藏掉。但是又要显示当前图片的简单信息：图片分辨率、大小、缩放信息、当前图片序号/所有图片数等等。

这三点基本要求就把上面全淘汰掉了，后来开始用geeqie，虽然满足2，3勉强满足，1就不行了。又换了sxiv，满足1、3，可惜2不满足，蛮像feh的，自定义程度不及。还试过nomacs，2、3都满足，1也达标，但是可惜的是反锯齿功能有缺陷，反锯齿下的图片相比其它图片查看器显得模糊，不知道作者用了什么特殊的算法。

最后开始使用feh，之前一直以为feh只满足1，看了下manpage才发现另有洞天，feh自定义文件在~/.config/feh/或者/etc/feh下，样例可以从/usr/share/doc/feh/examples复制过来，包含三个设置文件。

- **themes**

  设置界面功能，可以添加各种功能选项启动feh。而且可以设置多种模式调用，命名为feh的模式就会被默认调用。

  ```bash
  feh -g 1280x775 --scroll-step 180 --title 'feh@%u/%l %S %t %m %wx%h zoom<%z> - %n'
  ```

  ![](/assets/img/fehtitle.png)

- **keys**

  自定义快捷键，所需功能在manpage里查找修改。需要注意的是feh不会处理快捷键冲突问题，修改某被占用的快捷键时需要将对应的功能清空。

  ```bash
  # Fix key conflict
  save_filelist
  # Custom shortcuts
  toggle_fullscreen f
  
  ```

- **buttons**

  自定义鼠标功能，包括滚轮滚动、左键、右键和中键点击。

  ```bash
  # Switch menu and zoom keys, useful for two-button touchpads
  menu 2
  zoom 3
  
  # make scroll wheel (mousewheel up and down) zoom, instead of flipping images
  zoom_in 4
  zoom_out 5
  ```


作者拒绝加入gif动图功能，这个倒无所谓，很少本地看gif动图，推荐用mpv查看。

  ```bash
  mpv -loop xx.gif
  ```

最后就是pcmanfm文件管理器启动问题，默认情况下feh只会打开当前图片不会加载文件夹内的其它图片。之前用的sxiv也是这样，后来用脚本解决的，feh也google找了段脚本，可能太旧了，运行后吃光资源卡死系统。后来还是在feh的github页issue里找到了作者的回复。把下面代码保存为fehbrowser，移动到/usr/local/bin下，改可执行权限，然后文件管理器里右键打开方式调用「fehbrowser %f」。第二个$1也要用双引号，作者没加导致在复杂路径下会失效。

```bash
#!/bin/sh
feh --sort filename --start-at "$1" "$(dirname "$1")"

```

------ 2020/02/23 ------

最近发现在一个包括视频和图片的文件夹里，调用上面的fehbrowser脚本会加载读取完整的视频文件判断是不是图片。这样实在太蠢了，用filetype之类的来判断效率高上无数倍。

feh内部实现如此，只能从外部解决，改成只加载jpg、jpeg、gif和png这样扩展名的文件。

了解了下脚本内的正则实现方式，下面的脚本成功加载指定扩展名的文件。

```sh
#!/bin/sh
shopt -s extglob
feh --sort filename --start-at "$1" "$(dirname "$1")"/*(*.jpg|*.jpeg|*.png|*.gif|*.bmp)
```



  > Do One Thing and Do It Well.

unix软件哲学大道至简，核心简洁，效率，自定义程度丰富，linux下的优秀软件也大抵如此。
