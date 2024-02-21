---
layout: post_layout
title: 终端各类型文件自定义颜色
time: 2024年02月21日 星期三
location: 中国
pulished: true
excerpt_separator: "<!--more-->"
---
终端下不同文件类型的颜色让输出易读性大大增加，不过如果搭配不好反而让人不忍直视。默认的颜色方案大部分情况下都不错，个别比如ow(other writable)文件绿底蓝字闪瞎了眼。自然颜色方案是可以修改的，这也是linux的魅力。<!--more-->

### **1.Dircolors**  ###
终端下最常见的颜色就是ls的输出，ls的颜色由ls_colors变量定义，此变量又可以用dircolors命令配置。

#### **(1)生成配置文件**  ####
命令dircolors -p > dir_colors就会生成当下的颜色方案，再把下面命令放到zshrc里，man dir_colors里说放到/etc/profile里试了没用。

```
eval `dircolors some_path/dir_colors`
```

#### **(2)自定义颜色方案**  ####
编辑上面生成的dir_colors，修改对应文件类型的色彩配置，比如ow文件就是OTHER_WRITABLE开头那行，数字代表的颜色和意义在man dir_colors里又解释。修改后重新打开终端就生效了。

### **2.Zsh补全**  ###
zsh自动补全时的颜色和ls的输出不一样，还是之前默认的颜色方案，难道zsh补全不遵循ls_colors变量？

#### **(1)zstyle**  ####
搜索下可以用下面命令设置补全的颜色方案为ls_colors的，很多表示有效的但是我这里不起作用。

```
zstyle ':completion:*' list-colors "${(@s.:.)LS_COLORS}"
```
#### **(2)ZLS_COLORS**  ####
怀疑是不是zsh用的自己的颜色方案，果然有个类似的变量zls_colors，echo变量结果和ls_colors不一样。在zshrc里设置ZLS_COLRS=$LS_COLRS，重新打开终端echo两个变量已经一样了。但是试了下补全还是默认的颜色，再次echo后发现zls_colors又重置变回去了。

#### **(3)grml-zsh-config**  ####
一补全就自动重置zls变量，肯定是其他设置干扰，最大的可能就是grml的zsh配置。pacman将grml卸载，再打开终端补全已经没有彩色了，自己在zshrc里添加补全色彩配置后颜色回来而且遵循的是ls_colors方案。

那问题就出在grml上，grep了下grml的文件在/etc/zsh/zshrc里有补全色彩方案的设置代码。这个代码和网络上的有些许不同，试了下删掉此行用上面的zstyle代码补全颜色就正常遵循ls_colors了。难道是冲突，把上面.zshrc里的zstyle代码换成grml里的zstyle代码就生效了，即使恢复/etc/zsh/zshrc里的zstyle代码颜色也会遵循ls_colors。

```
zstyle ':completion:*:default'         list-colors ${(s.:.)LS_COLORS}
```

问题还复杂，grml里有补全颜色配置但不知为何不生效而且还会和稍有不同的同样配置起冲突，只有将grml里zstyle配置复制到.zshrc里，让/etc/zsh/zshrc和.zshrc里同时存在一样的zstyle配置才会生效。真是诡异又别扭的bug，够折腾人的。

忽然灵光一闪想通了，上面eval dircolors设置ls_colors变量的命令是放在.zshrc里的，.zshrc优先级高于/etc/zsh/zshrc，所以grml里zstyle是在dircolors之前执行的。这样grml的zstyle就无法收到修改后的ls_colors，而我.zshrc里zstyle是在eval dircolors后面的，就可以接收到修改后的ls_colors。

为了验证将eval dircolors放到里zstyle后面，果然颜色又变回默认了。那问题就是要确保zstyle设置补全颜色方案的代码要在eval dircolors设置ls_colors变量的命令之后执行，而且还要和grml里的zstyle代码一样否则会冲突导致都失效。
