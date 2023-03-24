---
layout: post_layout
title: Firefox气泡菜单
time: 2023年3月24日 星期五
location: 中国
pulished: true
excerpt_separator: "<!--more-->"
---
Firefox之前的photon主题的气泡菜单挺精致的，后面改成了平平无奇的圆角普通菜单。花时间用css把气泡菜单弄回来了，气泡箭头自适应变换方向。
<!--more-->

正常情况下菜单

<img src="/assets/img/Appmenu-down.png" width="410px" />

下方空间不足是菜单朝上

<img src="/assets/img/Appmenu-up.png" width="410px" />

```
/*菜单圆角改直角*/
:is(menupopup, panel)[type="arrow"] {
    --panel-border-radius: 0px !important;
}
/*三道杠菜单改气泡式*/
#appMenu-popup::before {
    content: url("chrome://global/skin/arrow/panelarrow-vertical.svg") !important;
    -moz-context-properties: fill, stroke !important;
    /*svg内部颜色*/
    fill: var(--arrowpanel-background) !important;
    /*svg边框颜色*/
    stroke: var(--arrowpanel-border-color) !important;
    /*svg右对齐*/
    text-align: end !important;
    /*去掉svg和下方菜单间行距*/
    line-height: 0 !important;
    /*调整svg位置*/
    margin:9px 10px calc(-1 * (var(--panel-shadow-margin) + 1px)) 0 !important;
}
#appMenu-popup[side="bottom"] {
    /*菜单朝上时垂直翻转使箭头朝下*/
    transform: rotateX(180deg) !important;
}
#appMenu-popup[side="bottom"] > #appMenu-multiView {
    /*菜单内容翻转使文字方向恢复正常*/
    transform: rotateX(180deg) !important;
}

```

