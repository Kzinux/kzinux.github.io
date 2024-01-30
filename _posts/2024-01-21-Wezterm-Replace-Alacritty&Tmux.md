---
layout: post_layout
title: Wezterm平替Alacritty和Tmux
time: 2024年01月21日 星期天
location: 中国
pulished: true
excerpt_separator: "<!--more-->"
---
Alacritty也用了几年了，小巧精干速度快资源占用低，对字体的支持也很好，开发维护积极。唯一的缺点就是没有标签页功能，作者很固执坚持认为应该由窗口管理器提供此功能。坚持原则理念无话可说也很欣赏软件专精而不是大包大揽。但是大部分窗口管理器都不提供此类功能所以很多alacritty用户都是用tmux来实现标签页。

Tmux很早就开始用了，最早用的是screen，后来tmux流行开了就换了。主要因为以前的xterm,urxvt之类的都不支持标签，平时又喜欢固定多开几个终端分别做不同的任务。还有就是虽然xfce-terminal之类的支持标签但是标签栏太丑啦，特别是只开一两个标签时标签也会铺满整个标签栏不像firefox那样标签有最大宽度限制。<!--more-->

### **Alacritty&Tmux配合的不足**  ###

Tmux小巧功能丰富，标签管理跳转也算方便，弥补alacritty功能上的缺失，但是有些小痛点让人耿耿于怀。

#### **1.tmux自身功能缺陷**  ####

tmux的窗口标题刷新问题，tmux可以自定义窗口标题，pane_current_path可以显示当前标签路径。不过显示刷新有延迟，不像直接在alacritty这样的终端上时实时显示。这个困扰了挺久的后来在issue里有人提到说是跟status-interval状态栏刷新挂钩的，默认是5s所以有延迟，可以设置为1s，看上去和正常终端一样但是实现方式太丑陋了。

#### **2.隔离浮置于alacritty**  ####

Tmux是套了一层壳运行在alacritty之上，所以在用户和alacritty之间多了一层tmux，有些alacritty的功能就无法实现。比如alacritty的copy mode只能复制当前页内容不能朝前翻页滚动。虽然tmux也提供copy mode但是有缺陷复制长行会断行丢失格式。

### **寻找平替**  ###

其实一直想找个不用套壳的terminal，kitty号称对位alacritty且功能完全，但是居然不支持点阵字体。issue里翻了下和alacritty的标签功能一样挺多人提的，但是作者坚持认为bitmap过时落后不符合kitty的先进理念。这个比alacritty的不支持标签离谱多了。

wezterm试用后虽然打开速度资源占用不如alacritty，但是最大的问题是无法正确识别bdf和pcf的粗体。前面的那篇[博文](https://kzinux.github.io/2023/10/25/Bitmap-Bold-Italic-Convert2TTF.html "bdf点阵制作独立粗体和斜体以及转换格式「ttf」")里提到解决了点阵转换ttf格式后问题也算解决了。而且之后发现wezterm的git版已经解决了这个bug「[fonts: improve categorization, selection of pcf fonts · wez/wezterm@70931f5](https://github.com/wez/wezterm/commit/70931f58f1c7a57fd377f9862597e7b1fe070b52)」，只要等下个release就行了。(终于发新版了，20240128.202157.1e552d76-1测试bdf粗体已经正常识别了)

### **Wezterm**  ###

#### **1.wezterm.lua**  ####

wezterm用lua设置文件，比alacritty和xterm之类的都复杂，原理上也不是linux常见的一行一个配置项的思路，更类似于编程语言。对于不懂代码的有点复杂，好在作者有个[官网](https://wezfurlong.org/wezterm/config/files.html)专门介绍配置，搜索也方便。
配置文件.wezterm.lua放在~/下，基本结构参照作者给的模板，每行都有注释，自定义配置放在config choices后面一行。

``` lua
-- Pull in the wezterm API
local wezterm = require 'wezterm'

-- This table will hold the configuration.
local config = {}

-- In newer versions of wezterm, use the config_builder which will
-- help provide clearer error messages
if wezterm.config_builder then
  config = wezterm.config_builder()
end

-- This is where you actually apply your config choices

-- For example, changing the color scheme:
config.color_scheme = 'AdventureTime'

-- and finally, return the configuration to wezterm
return config
```
#### **2.字体**  ####

##### **1).字体链接和大小**  #####

终端到手先把字体改掉，wezterm支持字体链接，可以设置多个候选字体排列顺序。

``` lua
--设置字体
config.font = wezterm.font_with_fallback({
  {family="Zpix", weight="Medium"},
  "Twemoji",
  "XHei webOS Mono",
  {family="HanaMinA", weight="Medium"},
  {family="HanaMinB", weight="Medium"},

})
--设置字体大小
config.font_size = 9.0
```
#####  **2).禁用斜体**  #####

vim里注释之类的用调用斜体，但是点阵斜体可读性不好，干脆禁用掉，用font_rules实现。
--字体规则禁用斜体

``` lua
config.font_rules = {
  {
    intensity = 'Bold',
    italic = true,
    font = wezterm.font_with_fallback ({
  {family="Zpix", weight="Bold"},
  "Twemoji",
  {family="XHei webOS Mono", weight="Bold"},
  {family="HanaMinA", weight="Medium"},
  {family="HanaMinB", weight="Medium"},
    }),
  },
  {
    italic = true,
    intensity = 'Normal',
    font = wezterm.font_with_fallback ({
  {family="Zpix", weight="Medium"},
  "Twemoji",
  {family="XHei webOS Mono", weight="Regular"},
  {family="HanaMinA", weight="Medium"},
  {family="HanaMinB", weight="Medium"},
    }),
  },
}
```
#### **3.主题外观**  ####

#####  **1).颜色方案**  #####

终端配色不仅影响美观，长时间使用选择一个舒缓眼睛压力的方案很重要。wezterm内置了很多主题，可以到网站「[The Color Schemes Section](https://wezfurlong.org/wezterm/colorschemes/index.html)」自己挑。如果想要自定义微调就不能设置colorscheme否则自定义颜色设置就会失效。颜色倾向于暗色系的太亮眼的看久了眼睛累。

``` lua
config.colors = {
  -- The default text color
  foreground = '#d3c6aa',
  -- The default background color
  background = '#3a3a3a',

  -- Overrides the cell background color when the current cell is occupied by the
  -- cursor and the cursor style is set to Block
  cursor_bg = '#f6f7ec',
  -- Overrides the text color when the current cell is occupied by the cursor
  cursor_fg = '#292520',
  -- Specifies the border color of the cursor when the cursor style is set to Block,
  -- or the color of the vertical or horizontal bar when the cursor style is set to
  -- Bar or Underline.
  cursor_border = '#f6f7ec',

  -- the foreground color of selected text
  selection_fg = '#e5c7a9',
  -- the background color of selected text
  selection_bg = '#5c3f4f',

  ansi = {
--black
"#2b2727",
--red
"#d94b42",
--green
"#7da35a",
--yellow
"#cd9125",
--blue
"#1398b9",
--magenta
"#ae65b0",
--cyan
"#d98246",
--white
"#e5c6aa",
  },
  brights = {
--black
"#675f54",
--red
"#ff9269",
--green
"#98e036",
--yellow
"#e0d561",
--blue
"#5fdaff",
--magenta
"#fc7eff",
--cyan
"#ff8c3c",
--white
"#f6f7ec",
  },
}
```
#####  **2).粗体配色**  #####

终端的粗体一般默认用brights配色，可以设置让粗体用普通配色。

``` lua
--粗体用普通色系
config.bold_brightens_ansi_colors = "No"
```
#####  **3).窗口大小**  #####

``` lua
--设置默认窗口行列数
config.initial_rows = 36
config.initial_cols = 155
```
#####  **4).窗口边框空白**  #####

``` lua
--设置内容和边框之间的空白
config.window_padding = {
  left = 1,
  right = 1,
  top = 0,
  bottom = 2,
}
```

####  **4.标签栏**  ####

标签栏默认是常见的上置和丑丑的样子，可以设置禁用fancy_tab_bar就会变成简洁的外观，自己再改改就和tmux的标签栏差不多了。

``` lua
--标签栏
config.enable_tab_bar = true
--禁用系统主题标签样式
config.use_fancy_tab_bar = false
--标签栏位置
config.tab_bar_at_bottom  = true
```
修改标签栏样式需要把配置加到前面颜色方案里的config.colors框框里，放到色系设置后面。

``` lua
--标签栏样式
tab_bar = {
    background = "#282828",
    -- The active tab is the one that has focus in the window
    active_tab = {
        -- The color of the background area for the tab
        bg_color = "#c8c8c8",
        -- The color of the text for the tab
        fg_color = "#3f3c3c",

        intensity = "Bold",
        underline = "None",
        italic = false,
        strikethrough = false,
    },

    -- Inactive tabs are the tabs that do not have focus
    inactive_tab = {
        bg_color = "#282828",
        fg_color = "#c2c0bd",
        intensity = "Bold",
    },
    inactive_tab_hover = {
        bg_color = "#202020",
        fg_color = "#dddddd",
        intensity = "Bold",
    },

    new_tab = {
        bg_color = "#282828",
        fg_color = "#c2c0bd",
        intensity = "Bold",
    },
    new_tab_hover = {
        bg_color = "#18131e",
        fg_color = "#dddddd",
        intensity = "Bold",
    },
},
```
#### **5.快捷键**  ####

wezterm的快捷键也很丰富，键位设置很多都是共通的，copy mode之类也都是vim键位。一般不改默认键位，但是希望标签页管理的键位改成firefox的alt 1~9这样的。

设置快捷键运行下面命令就可以直接生成目前的配置。`wezterm show-keys --lua> key.lua`
注意需要在配置文件前面local wezterm后面添加一行local act = wezterm.action
才能自定义快捷键。

``` lua
config.keys = {
    { key = '1', mods = 'ALT', action = act.ActivateTab(0) },
    { key = '2', mods = 'ALT', action = act.ActivateTab(1) },
    { key = '3', mods = 'ALT', action = act.ActivateTab(2) },
    { key = '4', mods = 'ALT', action = act.ActivateTab(3) },
    { key = '5', mods = 'ALT', action = act.ActivateTab(4) },
    { key = '6', mods = 'ALT', action = act.ActivateTab(5) },
    { key = '7', mods = 'ALT', action = act.ActivateTab(6) },
    { key = '8', mods = 'ALT', action = act.ActivateTab(7) },
    { key = '9', mods = 'ALT', action = act.ActivateTab(-1) },
    { key = 'n', mods = 'CTRL', action = act.ActivateTabRelative(1) },
    { key = 'p', mods = 'CTRL', action = act.ActivateTabRelative(-1) },
    { key = 't', mods = 'CTRL', action = act.SpawnTab 'CurrentPaneDomain' },
    { key = 'd', mods = 'CTRL', action = act.CloseCurrentTab{ confirm = true } },
    { key = 'l', mods = 'CTRL', action = act.ActivateLastTab },
    { key = 'UpArrow', mods = 'SHIFT', action = act.ScrollByLine(-1) },
    { key = 'DownArrow', mods = 'SHIFT', action = act.ScrollByLine(1) },
  }
```

#### **6.预加载标签**  ####

类似tmux的session可以设置标签页数目和布局以及运行的命令，和快捷键一样需要在前面local wezterm后添加一行local mux = wezterm.mux依赖。cwd是标签自定义路径，args就是标签自动运行的程序。

``` lua
--启动预加载标签
wezterm.on('gui-startup', function(cmd)
  local tab, pane, window = mux.spawn_window(cmd or {})

local tab = window:spawn_tab {
cwd = "/home/xxx/Downloads"
}
local tab = window:spawn_tab {
cwd = "/home/xxx/file/"
}
local tab = window:spawn_tab {
args = {"mutt"}
}
end)
```

这样就完成了对alacritty和tmux的完美平替，而且不用套壳直接沟通terminal，启动慢一点以及内存多用几十M和体验比起来不值一提。

<img src="/assets/img/wezterm.png" width="600px" />
