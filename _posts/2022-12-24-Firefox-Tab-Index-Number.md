---
layout: post_layout
title: Firefox标签索引数字
time: 2022年12月24日 星期六
location: 中国
pulished: true
excerpt_separator: "<!--more-->"
---
Linux下Firefox的alt·1～9切换标签页很喜欢，特别是标签页多的时候alt·1切换到第一个标签页，alt·9切换到最后一个标签页。有时候标签多了不好数，一般大概估计下，alt·number跳过去然后再切换到对应标签页，如果给标签页都加上数字索引序号就方便多了。
<!--more-->

标签页数字索引很多扩展都提供这个功能，扩展中心搜索“tab number”好多此类扩展，不过我记得有css可以实现类似功能。搜索了下果然有，利用::before在标签页标题前面加数字索引。

不过直接加数字在前面很难看，参考了下此类扩展的实现方式，自己改了改样式，固定的标签页不显示序号但是计入序列，这样融入原有界面不会显得突兀。

<img src="/assets/img/tabindex.png" width="781px" />

```
.tabbrowser-tab:first-child{
  counter-reset: nth-tab 0;
}
.tab-text:before{
  font-size: 0.75em;
  position: relative;
  top: -6px;
  content: counter(nth-tab)"/";
  counter-increment: nth-tab;
}

```
-----------------------2024/1/5----------------------------

用了一阵感觉标签多了时要跳到最后看标签总数麻烦，刚好看到[有人](https://np.reddit.com/r/FirefoxCSS/comments/s4wsww/show_the_number_of_open_tabs_on_the_list_all_tabs/)分享在所有标签页菜单按钮显示标签总数的css，改了改样式拿来用。另外把标签序号改成只有当前标签页显示，全部显示乱糟糟的。
<img src="/assets/img/tabcount.png" width="563px" />
```
/* 所有标签菜单按钮显示标签总数 */  
#TabsToolbar-customization-target {  
    counter-reset: tabCount;  
}  
.tabbrowser-tab {  
    counter-increment: tabCount;  
}  
#alltabs-button > .toolbarbutton-badge-stack > .toolbarbutton-icon {  
    visibility: collapse !important;  
}  
#alltabs-button > .toolbarbutton-badge-stack {  
    position: relative !important;  
}  
#alltabs-button > .toolbarbutton-badge-stack::before {  
    content: "/"counter(tabCount);  
    font-weight:bold !important;
    opacity: var(--toolbarbutton-icon-fill-opacity);  
    position: absolute;  
    bottom: 0%;  
    left: 50%;  
    transform: translateX(-50%);  
}
/*当前标签序号*/
.tabbrowser-tab:first-child{
  counter-reset: nth-tab 0;
}
.tab-text:before{
    font-size:0.85em  !important;
    position: relative;
    top: -6px;
    content: counter(nth-tab)"/";
    counter-increment: nth-tab;
}
.tab-text:not([selected]):before{
    display:table-row !important;
    visibility:collapse !important;    
}
```
