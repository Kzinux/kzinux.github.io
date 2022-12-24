---
layout: post_layout
title: Firefox标签索引数字
time: 2022年12月24日 星期六
location: 中国
pulished: true
excerpt_separator: "<!--more-->"
---
Linux下Firefox的alt·1～9切换标签页很喜欢，特别是标签页多的时候alt·1切换到第一个标签页，alt·9切换到最后一个标签页。有时候标签多了不好数一般大概估计下alt·number跳过去然后再切换到对应标签页，如果给标签页都加上数字索引序号就方便多了。
<!--more-->
标签页数字索引很多扩展都提供这个功能，扩展中心搜索“tab number”好多此类扩展，不过我记得有css可以实现类似功能。搜索了下果然有，利用::before在标签页标题前面加数字索引。

不过直接加数字在前面很难看，参考了下此类扩展的实现方式，自己改了改样式，固定的标签页不显示序号但是计入序列，这样融入原有界面不会显得突兀。

<img src="/assets/img/tabindex.png" width="837px" />

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

