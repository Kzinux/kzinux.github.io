---
layout: post_layout
title: Feh图片查看器
time: 2018年05月31日 星期四
location: 中国
pulished: true
excerpt_separator: "```"
---



用了几年linux图片查看器也用了不少了，但是一直没有合适满意的。最早把主流的图片查看器都试用了一遍，包括eog、gpicviewer、gthumb、gwenview、ristretto之类的。对图片查看器有几点要求，奈何做得到的不多。

1. #### 首先打开速度加载要快

   作为一个图片查看器这是基础之一，臃肿缓慢是无法容忍的，毕竟不是图片编辑器。

2. #### 其次是丰富可自定义键盘快捷键

   平常用惯了vim式的键盘浏览，对快捷键有特殊需求。

3. #### 最后是简洁明了的界面

   无论是自带或者是自定义的，一般我只留下状态栏，工具栏、菜单栏还有乱七八糟的什么其它界面都隐藏掉。但是又要显示当前图片的简单信息：图片分辨率、大小、缩放信息、当前图片序号/所有图片数等等。

这三点基本要求就把上面全淘汰掉了，后来开始用geeqie，虽然满足2，3勉强满足，1就不行了。又换了sxiv，满足1、3，可惜2不满足，蛮像feh的，自定义程度不及。

最后开始使用feh，之前一直以为feh只满足1，看了下manpage才发现另有洞天，feh自定义文件在~/.config/feh/或者/etc/feh下，样例可以从/usr/share/doc/feh/examples复制过来，包含三个设置文件。

- ##### themes

  设置界面功能，可以添加各种功能选项启动feh。而且可以设置多种模式调用，命令为feh的模式就会被默认调用。

  ```bash
  feh -g 1280x775 --scroll-step 180 --title 'feh@%u/%l %S %t %m %wx%h zoom<%z> - %n'
  ```

  ![](/assets/img/fehtitle.png)

- ##### keys

  自定义快捷键，所需功能在manpage里查找修改。需要注意的是feh不会处理快捷键冲突问题，修改某被占用的快捷键时需要将对应的功能清空。

  ```bash
  # Fix key conflict
  save_filelist
  # Custom shortcuts
  toggle_fullscreen f
  
  ```

- ##### buttons

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

  > Do One Thing and Do It Well.

核心简洁，效率，自定义程度丰富，linux下的优秀软件大抵如此。