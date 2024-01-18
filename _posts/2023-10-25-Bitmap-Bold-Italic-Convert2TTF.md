---
layout: post_layout
title: bdf点阵制作独立粗体和斜体以及转换格式「ttf」
time: 2023年10月25日 星期三
location: 中国
pulished: true
excerpt_separator: "<!--more-->"
---
Zpix只有12px常规点阵，alacritty虽然有设置粗斜体的选项但是没有效果，不像firefox可以自动调用freetype生成的假粗体。最近看到有人推荐wezterm，和alacritty一样都是用rust写的也支持gpu渲染，alacritty默认的图标丑丑的就想试试这个。
<!--more-->

wezterm官网上设置选项繁多，没那个需求就设置了字体和colorscheme这些就可以了。打开发现竟然有粗体，应该和firefox一样调用的假粗体。有了粗体以后终端显示的层次重点分明，比单纯的常规体看起来漂亮可读性也高多了。不过使用了一天发现也有些缺点，比较臃肿功能繁多，多标签、窗口多面板分割还有脚本什么的，不像alacritty就是单纯的终端。其实不喜欢大而全的软件，小而精更好，多标签面板之类的用tmux实现效率更高更好。开一个窗口用久了内存占用大概120多M，对比alacritty大概40M，而且打开时窗口加载明显比alacritty卡顿一下。另外发现wezterm识别不到bdf的粗体,wezterm ls-fonts --list-system识别出来的粗体被标记为常规体（regular）。所以即使有真粗体，wezterm也全当作常规体转而使用freetype生成的假粗体，明明fc-list里显示的是粗体的。

想换回alacritty，但是看过wezterm更漂亮的多字重显示心痒难耐，alacritty又不支持freetype的假粗体，那最后的办法就是用上真粗体。zpix是没粗体的，只能想办法找一个搭配的粗体。

#### **制作独立粗斜体** ####

- 用其他字体的粗体搭配，西文还好找，中文点阵都没有粗体而且不同字体字符支持和字形不同
- 最靠谱的就是用fontforge基于zpix常规点阵制作粗体点阵，但是elements-style这类都是面向矢量字体不支持点阵
- 既然freetype能生成假粗体能否导出保存下来，搜索了下没找到方法，可行性挺高的

接下来用"bitmap bold font"之类的关键字搜索无意中打开一个支持粗体的自制点阵西文字体[**fatty-bitmap-font**](https://github.com/domo141/fatty-bitmap-font)，没想到居然是个宝库，作者在里面打包了很多bdf点阵字体工具脚本，最关键的是竟然有**mkbold**、**mkitalic**这两个转换制作点阵粗体和斜体的perl脚本。代码具体不懂，打开看了下没有调用fontforge和freetype完全就是一段小脚本才150行，不懂为什么fontforge竟然不支持点阵的粗体放大只支持矢量字体的加粗。

接下来就简单了，clone下来加上x权限在终端里对zpix执行下就会生成对应粗体，连字体命名都处理好了。脚本内容直接输出到终端的加个>就可以导出生成的字体文件。`./Git/fatty-bitmap-font/src/mkbold-1.0 /usr/share/fonts/misc/ZpixP.bdf >zpixpb.bdf`

生成粗体后还可以继续用mkitalic生成对应的常规斜体和常规粗斜体，效果比freetype生成的假粗体好多了。freetype是无脑加粗，粗体看上去就是常规体的肥胖版，因为12px点阵本来就小直接加粗很多都糊成一团了。而perl脚本生成的粗体则是有粗有细，有的笔画加粗明显有的笔画轻微加粗，中文加粗的效果特别好，有种类似古人毛笔小楷的感觉。

自动生成的粗体有的字形不理想，比如1和8，可以在fontforge里双击字符打开编辑窗口绘制修改字形。和拼积木差不多，调整完成后generate font就可以了。

#### **bdf点阵转换为ttf/otb**  ####
bdf用了段时间，后来看到有人说harfbuzz停止支持bdf再加上wezterm也识别不到bdf的粗体，就想要换一个更现代支持更广泛的字体格式。

##### **fontforge**  #####
直接用fontforge是可以转换成otb的，ttf就不行了。file-gengrate生成字体对话框里选择otbs生成就可以，生成的otb小了很多猜测跟压缩差不多，bdf应该是元数据未压缩，在alacritty里正常使用，xterm下字体全挤成一堆了。搜索了下fontforge的[issue](https://github.com/fontforge/fontforge/issues/3853 "Generating an OTB from GohuFont BDF seems to lose its width · Issue #3853 · fontforge/fontforge")有提到转换bdf为otb会丢失宽度而且没解决。

看到最后面有回复写了个[转换脚本](https://github.com/ctrlcctrlv/bitmapfont2otb "ctrlcctrlv/bitmapfont2otb: Convert a bitmap font in BDF, PCF, or SFD format to an OpenType Bitmap font using FontForge's API and bdfreader")解决了此问题，clone下来安装了俩依赖试了下卡半天内存不停涨还以为坏了，等了几分钟终于好了。生成的otb有十几M大了好几倍，这下alacritty和xterm都正常不过在firefox里中文都挤成了一团，应该是作者没考虑到cjk字符，都当作英文处理cjk字符宽度错了。

##### **bitsnpicas**  #####
忽然想起来zpix作者提到过找到了新工具转换bdf为ttf，到github页面一看tools/bin下有个jar的文件就是了。按照名字找到此工具的[github主页](https://github.com/kreativekorp/bitsnpicas/tree/master "kreativekorp/bitsnpicas: Bits'N'Picas - Bitmap & Emoji Font Creation & Conversion Tools")，到release页面把最新的BitsNPicas.jar下载下来。因为是java的程序还要安装java环境直接安装jdk，然后按照作者给出的命令转换就行了。`java -jar BitsNPicas.jar convertbitmap -f ttf -o Zpix.ttf Zpix.bdf`
如果需要改名最好先在bdf格式上修改好，然后再用BitsNPicas.jar转换，因为ttf格式的点阵fontforge编辑修改会破坏掉点阵。

alacritty用ttf完美切换，另外firefox下也正常使用，xterm用ttf格式的字体中英文要分开设置，bdf可以都设置成zpix。

这样子以后就不当心以后bdf格式失去支持了，

### 纯中文点阵的4个字型对比
<img src="/assets/img/zpixfamily.png" width="775px" />

### alacritty真粗体和wezterm假粗体的对比
<img src="/assets/img/zpixbold4term.png" width="750px" />

