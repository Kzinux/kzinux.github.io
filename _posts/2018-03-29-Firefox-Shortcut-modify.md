---
layout: post_layout
title: Firefox Quantum全局快捷键
time: 2018年03月29日 星期四
location: 中国
pulished: true
excerpt_separator: "```"
---
Firefox57后全面更换webextension扩展，其它扩展也就罢了，webextension版快捷键扩展实在无法忍受。不支持自家的网站、firefox内置页面，最难受的是页面加载完毕才可以使用，有种用油猴脚本的感觉。

Firefox自宫练《葵花宝典》，原来流畅的全键盘浏览也灰飞烟灭。试了几个webextension扩展也都差不多，firefox不给权限，限制多多导致体验很差。苦苦寻觅之下终于在论坛找到了解决方案，[修改默认快捷键](https://www.firefox.net.cn/read-60546-1)。

#### 自定义常用快捷键

1. 复制/usr/lib/firefox/browser/omni.ja到空目录下，然后解压到当前文件夹，我用7z有错误信息所以换成unzip，mozilla也是用zip格式压缩omni.ja的。

2. 把omni.ja剪切到别的地方，以免等下把它打包重复打包。

3. 编辑器打开chrome/browser/content/browser/browser.xul文件，在keyset id="mainKeyset"代码下面添加自定义的快捷键代码，没有特殊需求推荐添加新快捷键不要修改默认快捷键。

4. 命令代码都在上面有的，可以自行选择设置。但是browser.xul里没有滚动的代码，所以要编辑另一个文件实现。

   <img src="/assets/img/firefox-shortcut1.png" width="369px" />

5. 编辑完成保存退出，然后把所有文件用zip格式重新压缩，压缩等级为存储。`zip -Z store -r omni.zip *`

6. 关闭firefox，把生成的omni.zip复制替换/usr/lib/firefox/browser/omni.ja，删除配置文件下的startupcache文件夹，linux是`rm -r ~/.cache/mozilla/firefox/gdop66vs.default/startupCache`，再打开firefox就可以体验久违的全局快捷键了。

#### 自定义滚屏快捷键

1. 滚屏命令browser.xul里没有，这里另外添加，复制/usr/lib/firefox/omni.ja到空目录，解压至当前文件。

2. 照样剪切omni.ja到其它文件夹。

3. 编辑器打开chrome/toolkit/content/global/platformHTMLBindings.xml，找到binding id="browser"下面的滚屏命令，复制需要的命令到新行，我直接在原代码下面复制的。

   <img src="/assets/img/firefox-shortcut2.png" width="481px" />

4. 保存退出，照样压缩，注意压缩等级为存储。`zip -Z store -r omni.zip *`

5. 关闭firefox，复制omni.zip替换/usr/lib/firefox/omni.ja，删除startupcache文件夹，重新打开firefox即可。

