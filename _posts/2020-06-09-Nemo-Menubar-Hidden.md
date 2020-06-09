---
layout: post_layout
title: 隐藏Nemo菜单栏
time: 2020年06月09日 星期二
location: 中国
pulished: true
excerpt_separator: "<!--more-->"
---
Nemo是最近使用下来功能最全面、稳定的文件管理器，就是菜单栏一直有些问题。虽然有隐藏菜单栏选项，之前是只要按下alt就会重新显示菜单栏，即使你用「alt tab」切换窗口也会触发。

后来这个bug修复了，要按下alt并松开才会触发菜单栏，跟firefox的alt「Menu access key」一样，不过firefox的这个功能可以在about:config里禁用。nemo这个无法禁用，有时误按下alt就见菜单栏闪啊闪，而且焦点也转移到工具栏去了，无法再用键盘上、下、回车等键操作nemo。只有重新用鼠标点击nemo的文件显示区域让焦点回到下面才会恢复，这让平时习惯键盘操作的我很困扰。

<!--more-->

本来想去github反馈下，不过nemo的github页只接收功能建议，表示会转达给开发团队，不是直面开发者的。后来在前面「[Gvim底部空白](https://kzinux.github.io/2020/05/15/Gvim-Bottom-Line.html)」提到的gtk的css定制得到了启发。虽然属性限制很多，display:none、visibility:hidden这两个都没法用，不过还是可以用margin-top来实现相似的效果。

将nemo菜单栏设置成显示状态，将下面代码放到~/.config/gtk-3.0/gtk.css里，重新打开nemo「nemo直接关闭并不彻底，后台还有守护进程，需要用nemo -q或者直接杀掉进程」。

```css
.nemo-window #MenuBar {
    margin-top:-20px;
}
```

这样其实是将菜单栏高度减小至看不见的程度，这样alt键不会触发，菜单栏也隐藏了。另外需要调用菜单栏可以用alt f、alt e之类的快捷键打开对应菜单，然后用方向键切换至所需菜单项。