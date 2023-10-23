---
layout: post_layout
title: Zpix bdf点阵的修改和powerline破解
time: 2023年10月23日 星期一
location: 中国
pulished: true
excerpt_separator: "<!--more-->"
---
终端字体一直习惯用点阵，小字体下也保持着很高的可读性，对于大量的文字信息输出长时间查看可以减少对眼睛的负担。矢量字体小字体下的可读性和点阵无法相提并论，字号加大后如网页那样的矢量是有优势的但是这样又会导致终端一页输出的信息量大大减少，终端使用起来就少了掌控全局的感觉。
<!--more-->

[Zpix](https://github.com/SolidZORO/zpix-pixel-font/)是一款个人制作的点阵字体，除了商用其他都可以免费使用。用了几年最近发现作者又开始维护更新了，多了个ttf版本可以在字号高于12px时显示矢量模拟的点阵，还多了一些改进和纠错，bdf就是纯点阵只有12px。

本来想更新下，ttf用在网页上正常。bdf安装后firefox字体列表里有却无法使用，不管是内置的字体设置或是用css指定都没反应，alacritty设置成bdf版倒是有效但是字体宽度有问题。之前用的bdf版是修改过的alacritty和firefox都可以正常使用，连vim和zsh的prompt的powerline字体支持都有，但是时间太久我都忘了当初怎么改的。

都打算放弃了就用旧版的不升级了，忽然灵光一现想起来似乎是用fontfrge整合terminus的powerline版本实现的。一翻折腾下又走了不少弯路终于想起来全过程重新修改patch了zpix。这中间的问题弯路和解决方法都记录下来。

+ zpix的bdf版不知是什么问题无法在终端之外的程序使用
+ 在终端内使用字体之间的左右间隙比较大因为它不是等宽字体
+ 直接将patch过的terminus导入zpix字体间隙正常了但是箭头字符显示位置偏右有空隙
+ 在fontforge内用右键菜单transform的move并不能纠正箭头位置的偏差
+ 使用patch脚本破解zpix.bdf同样的字体间隙和箭头字符问题都存在


- **[ 1 ] 等宽问题**

首先要解决的将zpix改成等宽适应终端显示，用[Terminus Powerline版](https://github.com/powerline/fonts/tree/master/Terminus/BDF)的拉丁文点阵覆盖zpix，用等宽西文覆盖zpix后在alacritty下使用就没问题了。注意只需要下载12px的常规体就可以因为zpix没有其他字号点阵也没有粗体点阵。
1. 用fontforge打开zpix.bdf
2. 文件-导入-选择ter-powerline-x12n.bdf-确定overwrite

- **[ 2 ] 修改命名**

为了与zpix.ttf共存需要改下名字。
1. 菜单栏-元素-字体信息
2. 前三栏都改掉，比如“Zpix P”、“Zpix P”、Zpix Powerline
3. 确定
4. 生成新的bdf字体，菜单栏-文件-生成字体，弹出的窗口已经选好了“无轮廓字体”、“BDF”、“12”等
5. 点击“Generate”，分辨率默认75，确定就生成了

- **[ 3 ] powerline字符支持**

在fontforge里乱折腾了一阵都失败了箭头字符始终位置不对与左侧有空隙，后来在自己保存git clone文件夹内发现个fontpatch才想起来当初是用脚本自动patch的。但是几年前的python2版本现在arch都是python3了又去找了个新的[Font-patch脚本](https://github.com/alexandermckay/font-patcher)。

脚本下载下来之后对上面生成的bdf字体执行如下命令就可以生成patch字体了。

`fontforge -script 脚本的路径 字体的路径`

- **[ 4 ] 程序识别使用**

其实第一步导入terminus后firefox就可以正常识别新字体了。现在将patch脚本生成的新字体复制到/usr/share/fonts/misc下，再**fc-cache -vf**生成新缓存就可以使用了。

- **[ 5 ] 添加新字符**

进入git clone文件夹后powerline会显示状态符号，有个大加号✚**U+271A**调用的矢量字体显示和点阵不搭配。查看了下zpix和terminus都没有这个字符，就想把普通加号+复制过去，结果复制过去fontforge显示这个字符有了生成字体却又消失了。

搜索了下直接copy-paste是无效的

1. 先选中源字符普通加号+，菜单栏-编辑-复制宽度
2. 滚动到目标字符位置**U+271A**，选中它，菜单栏-编辑-粘贴到
3. 再回到源字符位置，选中+号，菜单栏-编辑-复制
4. 下拉到目标字符位置**U+271A**，选中，菜单栏-编辑-粘贴
5. 生成新字体

总结下就是要先粘贴宽度再粘贴字符才有效。