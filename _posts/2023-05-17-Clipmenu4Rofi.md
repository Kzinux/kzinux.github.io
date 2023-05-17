---
layout: post_layout
title: 剪贴板工具Clipmenu
time: 2023年05月17日 星期三
location: 中国
pulished: true
excerpt_separator: "<!--more-->"
---
Copyq用了挺长时间的，然后开始出现复制失败和粘贴失效的情况，快捷键和菜单都不管用。时灵时不灵的，copyq官网faq也有这个问题介绍，作者给出了方法但不保证能解决。试了下似乎缓解了一点，失灵几率下降但还是没解决。复制粘贴出问题影响太大了，遂打算换掉copyq。
<!--more-->

之前找剪贴板工具时折腾了一番，过去这么久了发现还是一些老面孔，最后无奈凑活用parcellite。其实要求很简单，快捷键菜单式弹出界面、快捷键选择条目和搜索筛选条目。

clipmenu以前也试用过，依托于dmenu的，界面和功能一般。现在也支持rofi，rofi界面和功能比dmenu强大美观多了，就想着把clipmenu折腾成想要的样子。

**1) 安装**

clipmenu安装直接pacman，依赖dmenu和可选的rofi。

**2) 配置**

clipmenu配置参数通过环境变量实现，clipmenu --help和clipmenud --help分别对应各自的定制变量。

$CM_LAUNCHER	对应依赖的呼出前端

$CM_HISTLENGTH	一次显示的剪贴板条目数量

$CM_SELECTIONS	同步到剪贴板里的内容，默认为clipboard「通过ctrl c和菜单复制」和primary「鼠标选择的内容」

$CM_MAX_CLIPS	保存的剪贴板条目总数，默认1000

把上面需要配置的变量复制到/etc/enviroment里重新登录就生效了。

clipmenu端配置好了就可以启动后台进程clipmenud，启动后会实时同步剪贴板。

接下来是前端rofi这边的配置。

- [ ] **首先是快捷键呼出**

在对应桌面的快捷键设置里给clipmenu设置一个快捷键，默认prompt显示dmenu，可以加-p定制prompt。
`clipmenu -p clipmenu`

- [ ] **条目增加序号**

rofi支持菜单序号但是默认没启用，在rofi主题里增加下面内容启用序号。

```
/*菜单条目增加序号*/
element { 
    children: [element-index, element-text, element-icon ]; 
}
/*序号颜色位置调整*/
element-index {
    background-color: inherit;
    color:  #f0544c;
    width:  15px;
    margin:    2px 6px 0px 0px;
}
```

- [ ] **快捷键选择条目**

rofi默认支持，win键+序号就可以选择对应条目，更习惯用alt 1在rofi配置文件里改下就可以了。


<img src="/assets/img/clipmenu.png" width="768px" />



