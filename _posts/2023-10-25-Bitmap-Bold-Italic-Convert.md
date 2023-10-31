---
layout: post_layout
title: 转换点阵字体制作独立粗体和斜体
time: 2023年10月25日 星期三
location: 中国
pulished: true
excerpt_separator: "<!--more-->"
---
Zpix只有12px常规点阵，alacritty虽然有设置粗斜体的选项但是没有效果，不像firefox可以自动调用freetype生成的假粗体。最近看到有人推荐wezterm，和alacritty一样都是用rust写的也支持gpu渲染，alacritty默认的图标丑丑的就想试试这个。
<!--more-->

wezterm官网上设置选项繁多，没那个需求就设置了字体和colorscheme这些就可以了。打开发现竟然有粗体，应该和firefox一样调用的假粗体。有了粗体以后终端显示的层次重点分明，比单纯的常规体看起来漂亮可读性也高多了。不过使用了一天发现也有些缺点，比较臃肿功能繁多，多标签、窗口多面板分割还有脚本什么的，不像alacritty就是单纯的终端。其实不喜欢大而全的软件，小而精更好，多标签面板之类的用tmux实现效率更高更好。开一个窗口用久了内存占用大概120多M，对比alacritty大概40M，而且打开时窗口加载明显比alacritty卡顿一下。

想换回alacritty，但是看过wezterm更漂亮的多字重显示心痒难耐，alacritty又不支持freetype的假粗体，那最后的办法就是用上真粗体。zpix是没粗体的，只能想办法找一个搭配的粗体。

- 用其他字体的粗体搭配，西文还好找，中文点阵都没有粗体而且不同字体字符支持和字形不同
- 最靠谱的就是用fontforge基于zpix常规点阵制作粗体点阵，但是elements-style这类都是面向矢量字体不支持点阵
- 既然freetype能生成假粗体能否导出保存下来，搜索了下没找到方法，可行性挺高的

接下来用"bitmap bold font"之类的关键字搜索无意中打开一个支持粗体的自制点阵西文字体[**fatty-bitmap-font**](https://github.com/domo141/fatty-bitmap-font)，没想到居然是个宝库，作者在里面打包了很多bdf点阵字体工具脚本，最关键的是竟然有**mkbold**、**mkitalic**这两个转换制作点阵粗体和斜体的perl脚本。代码具体不懂，打开看了下没有调用fontforge和freetype完全就是一段小脚本才150行，不懂为什么fontforge竟然不支持点阵的粗体放大只支持矢量字体的加粗。

接下来就简单了，clone下来加上x权限在终端里对zpix执行下就会生成对应粗体，连字体命名都处理好了。脚本内容直接输出到终端的加个>就可以导出生成的字体文件。`./Git/fatty-bitmap-font/src/mkbold-1.0 /usr/share/fonts/misc/ZpixP.bdf >zpixpb.bdf`

生成粗体后还可以继续用mkitalic生成对应的常规斜体和常规粗斜体，效果比freetype生成的假粗体好多了。freetype是无脑加粗，粗体看上去就是常规体的肥胖版，因为12px点阵本来就小直接加粗很多都糊成一团了。而perl脚本生成的粗体则是有粗有细，有的笔画加粗明显有的笔画轻微加粗，中文加粗的效果特别好，有种类似古人毛笔小楷的感觉。

### 纯中文点阵的4个字型对比
<img src="/assets/img/zpixfamily.png" width="775px" />

### alacritty真粗体和wezterm假粗体的对比
<img src="/assets/img/zpixbold4term.png" width="750px" />

