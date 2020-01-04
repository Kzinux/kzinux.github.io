---
layout: post_layout
title: Gtk3主题修改
time: 2019年11月28日 星期四
location: 中国
pulished: true
excerpt_separator: "<!--more-->"
---







最近在使用arc的gtk主题，archlinux源里的arc-solid-gtk-theme，跟普通版相比禁用了透明。此主题简单大方不失精致，用了段时间挺满意的，不过有个问题就是字体颜色太淡了，看起来很累眼。

字体颜色归gtk主题管，gtk又分为gtk2和gtk3，现在就分别修改下。

#### Gtk2
<!--more-->

2的修改很简单，就是很方便的文本配置，编辑/usr/share/themes/Arc-Darker-solid/gtk-2.0下的gtkrc，把text_color和fg_color都改成心仪的字体颜色就可以了。

#### Gtk3

3就麻烦了，设置项保存在/usr/share/themes/Arc-Darker-solid/gtk-3.0的gtk.gresource文件里，这是个特殊的打包文件，没法直接修改。搜索了下用gresource命令可以查看导出对应的文件但是没法导入，只能放到~/.config/gtk-3.0下自定义配置。不喜欢这种方式，感觉有点零碎。

还有种方法就是源码重编译，干净利落。

1. 首先下载github站上的[arc源码](https://github.com/arc-design/arc-theme)，git clone下来。

2. 然后根据下面的介绍安装编译所需的依赖。

3. 接下来修改gtk3主题的字体颜色，在源码文件夹下的/arc-theme/common/gtk-3.0/3.xx/sass的_colors.scss里。
   ```css
   $base_color: if($variant =='light', #ffffff, #404552);
   $text_color: if($variant == 'light', #263238, #D3DAE3);
   $bg_color: if($variant =='light', #F5F6F7, #383C4A);
   $fg_color: if($variant =='light', #263238, #D3DAE3);
   ```

   把text_color和fg_color对应行的第一个颜色改成中意的字体颜色。

4. 用下面的命令开始编译，prefix后面跟着的是生成主题的路径。`./autogen.sh --prefix=/home/user/theme` `make install`

5. 最后把生成的新主题里的gtk.gresource文件覆盖原来主题的对应此文件。

6. 重新选择此主题应用就可以了。
