---
layout: post_layout
title: Firefox Quantum全局快捷键
time: 2018年03月29日 星期四
location: 中国
pulished: true
excerpt_separator: "<!--more-->"
---
Firefox57后全面更换webextension扩展，其它扩展也就罢了，webextension版快捷键扩展实在无法忍受。不支持自家的网站、firefox内置页面，最难受的是页面加载完毕才可以使用，有种用油猴脚本的感觉。

Firefox自宫练《葵花宝典》，原来流畅的全键盘浏览也灰飞烟灭。试了几个webextension扩展也都差不多，firefox不给权限，限制多多导致体验很差。苦苦寻觅之下终于在论坛找到了解决方案，[修改默认快捷键](https://www.firefox.net.cn/read-60546-1)。

#### 自定义常用快捷键

1. 复制/usr/lib/firefox/browser/omni.ja到空目录下，然后解压到当前文件夹，我用7z有错误信息所以换成unzip，mozilla也是用zip格式压缩omni.ja的。

2. 把omni.ja剪切到别的地方，以免等下把它打包重复打包。

3. 编辑器打开chrome/browser/content/browser/browser.xul文件，在keyset id="mainKeyset"代码下面添加自定义的快捷键代码，没有特殊需求推荐添加新快捷键不要修改默认快捷键。
4. 命令代码都在上面有的，可以自行选择设置。但是browser.xul里没有滚动的代码，所以要编辑另一个文件实现。
<!--more-->

   <img src="/assets/img/firefox-shortcut1.png" width="369px" />

5. 编辑完成保存退出，然后把所有文件用zip格式重新压缩，压缩等级为存储。`zip -Z store -r omni.zip *`

6. 关闭firefox，把生成的omni.zip复制替换/usr/lib/firefox/browser/omni.ja，删除配置文件下的startupcache文件夹，linux是`rm -r ~/.cache/mozilla/firefox/xxxxxx.default/startupCache`，再打开firefox就可以体验久违的全局快捷键了。

***Firefox65取消了platformHTMLBindings.xml文件，搜索代码也没有另外修改的地方了。***

#### ~~自定义滚屏快捷键~~

1. ~~滚屏命令browser.xul里没有，这里另外添加，复制/usr/lib/firefox/omni.ja到空目录，解压至当前文件。~~

2. ~~照样剪切omni.ja到其它文件夹。~~

3. ~~编辑器打开chrome/toolkit/content/global/platformHTMLBindings.xml，找到binding id="browser"下面的滚屏命令，复制需要的命令到新行，我直接在原代码下面复制的。~~

   ~~<img src="/assets/img/firefox-shortcut2.png" width="481px" />~~

4. ~~保存退出，照样压缩，注意压缩等级为存储。`zip -Z store -r omni.zip *`~~

5. ~~关闭firefox，复制omni.zip替换/usr/lib/firefox/omni.ja，删除startupcache文件夹，重新打开firefox即可。~~

**2019-3-3更新**

#### 自定义滚屏快捷键

Firefox65删除了platformHTMLBindings.xml，用了两天没有jk滚屏好难受。想将就下用webextension版的快捷键扩展，一看到一条条的限制又不想用。本来想从系统软件方面解决的，win下倒是有个好替代方案autohotkey(win下试了下无法jk输入打字)，linux下只有个类似的autokey，有点臃肿。还有用xbindkeys和xdotool的，不过还是不完美。

最后还是从firefox自带的browser.xul上找到了解决办法。

1. 复制/usr/lib/firefox/browser/omni.ja到空文件夹用unzip解压。

2. 移走omni.ja以免重复打包。

3. 编辑chrome/browser/content/browser/browser.xul，在 commandset id="mainCommandSet">下添加所需的滚屏命令，如下格式：

   ```xml
     <command id="cmd_moveUp" oncommand="goDoCommand('cmd_moveUp')"/>
     <command id="cmd_moveDown" oncommand="goDoCommand('cmd_moveDown')"/>
     <command id="cmd_movePageUp" oncommand="goDoCommand('cmd_movePageUp')"/>
     <command id="cmd_movePageDown" oncommand="goDoCommand('cmd_movePageDown')"/>
     <command id="cmd_moveTop" oncommand="goDoCommand('cmd_moveTop')"/>
     <command id="cmd_moveBottom" oncommand="goDoCommand('cmd_moveBottom')"/>
   ```

4. 再在keyset id="mainKeyset">下添加上面定义的滚屏命令，如下格式:

   ```xml
       <key key="j" command="cmd_moveDown"/>
       <key key="k" command="cmd_moveUp"/>
       <key key="n" command="cmd_movePageDown"/>
       <key key="p" command="cmd_movePageUp"/>
       <key key="g" command="cmd_moveTop"/>
       <key key="b" command="cmd_moveBottom"/>
   ```

5. 编辑完成保存退出，然后把所有文件用zip格式重新压缩，压缩等级为存储。`zip -Z store -r omni.zip *`

6. 关闭firefox，把生成的omni.zip复制替换/usr/lib/firefox/browser/omni.ja，删除配置文件下的startupcache文件夹，linux是`rm -r ~/.cache/mozilla/firefox/xxxxxx.default/startupCache`，然后打开firefox快捷键就生效了。

##### **2019-10-23更新**

Firefox69开始browser.xul被舍弃了，改成了browser.xhtml，修改方法没变，对象改成browser.xhtml就没问题了。

##### **2025-04-30更新**

Firefox133升级至137后发现后台加载的网页（包括前台打开时焦点切换到其他程序窗口）需要先按下tab或双击网页才能滚动，up、down、pageup之类的键不受影响，再升级至138后发现滚动快捷键都失效，其它正常。折腾一番后发现除了上面的修改cmd_move*还需要在chrome/browser/content/browser/browser-sets.js下定义，模仿其它的命令格式定义。

也就是说需要定义两次，一次在browser-sets.js下定义cmd_move..命令，一次在browser.xhtml里的mainCommandSet下定义cmd_move..的id，只定义一个不管用。
```
          case "cmd_moveUp":
            goDoCommand('cmd_moveUp');
            break;
          case "cmd_moveDown":
            goDoCommand('cmd_moveDown');
            break;
          case "cmd_movePageUp":
            goDoCommand('cmd_movePageUp');
            break;
          case "cmd_movePageDown":
            goDoCommand('cmd_movePageDown');
            break;
          case "cmd_moveTop":
            goDoCommand('cmd_moveTop');
            break;
          case "cmd_moveBottom":
            goDoCommand('cmd_moveBottom');
            break;
```
