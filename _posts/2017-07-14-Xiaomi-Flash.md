---
layout: post_layout
title: 小米误删/system救砖记
time: 2017年07月14日 星期五
location: 中国
pulished: true
excerpt_separator: "```"
---
因为拨号与通讯录一直停止运行，先是说清除app缓存，再又看到说是要双清，结果系统恢复出厂设置了还是老样子。觉得是双清不彻底就点了第三方rec里清除下面的高级，傻傻的选上了/system，结果格式化后要重启时就提醒说无系统了。这才知道/system是系统分区，重启后就一个mi.com的logo，长按电源键关机还是会自动开机重回此界面。没法了，只能刷机，当时还意料不到接下来的一波三折。


+ #### 损坏情况

1. 首先是系统分区被格式化了，系统没了。 
2. 按电源和音量键上无反应无法进入recovery，猜测应该是rec在/system分区一起被格式化了。
3. 按电源和音量下能进入fastboot模式，这是个好消息。

+ #### 准备救砖

64位win7笔记本一台，数据线一根。

#### 前景不明——百般解数亦无用

1. 尝试用小米助手刷回系统，安装官网的[小米助手](http://zhushou.xiaomi.com/)，将手机连接数据线插入电脑，提示安装驱动但是失败，因为小米助手还不支持android 6…………
2. 无驱动就无法识别设备接下来一切空谈，网上下了个驱动包打算手动安装驱动。打开设备管理器看到一个打了问号的android设备，右键更新驱动定位到下载解压好的驱动包，提示无驱动，失败。
3. 又是一通搜索，说是要打开usb设备调试才好安驱动识别设备，但是我系统都删了怎么打开usb设备调试，失败。
4. 小米助手没维护了都推荐用miflash，下载了也提示安装未识别驱动但还是失败，照样是个问号设备。
5. 还有说64位系统要进入[测试模式](http://jingyan.baidu.com/article/acf728fd21c3e7f8e510a3ef.html)操作，进入后还是失败。

#### 峰回路转——刷机精灵显威风

6. 看到有推荐用[刷机精灵](http://www.shuame.com/)救砖的，本着死马作活马的心态安装上。手机进入fastboot模式，打开精灵提示连接结果还是失败，要我打开usb调试模式…………
7. 本来到这就要放弃了结果一次重启手机进入usb模式后设备管理器竟然出现了驱动上了的android设备，刷机精灵也识别出来手机型号。当时都想欢呼了，感谢刷机精灵一百遍。点击刷机，选择下载好的miui卡刷包，手机先是黑屏，然后进入了久违的recovery模式，看着靠谱啊。接下来提示复制刷机包到手机，等了段时间复制好后刷机精灵出现了绿字“恭喜你，现在进入了自动刷机模式”，让我等待完成。我一看界面就是平常用recovery卡刷的样子，有戏啊，满怀希望地等它完成自动重启进入单调的mi.com的logo界面，印象中要过段时间就会进入系统了，可是半个多小时过去了还是卡在这，不敢动直到希望的火焰冷成残渣。


#### 重回低谷——高通线刷挽颓势

8. 虽然刷机精灵失败了但还是有了大大的进展，至少设备管理器识别了android设备，之前没法线刷就是因为无驱动。
9. 重启手机后设备管理器没了android设备，连问号设备都没了，记得当时是重启手机进入fastboot模式刷机精灵才识别出了android设备，重试了下不行，把数据线拔了重启进入fastboot模式再插上数据线刷机精灵果然又识别出了android设备，这期间刷机精灵要打开。
10. 下载[Miflash](http://bigota.d.miui.com/tools/MiFlash20150601_win10.exe)，因为此版本自带adb工具，有的adb工具会提示不兼容64位。然后打开C:\Program Files (x86)\Xiaomi\MiPhone\Google\Android文件夹，按shift点右键，打开命令窗口，输入下面命令。
```cmd
fastboot oem edl
```
会提示finished什么的，设备管理器串口分类下多了个9008串口设备。

#### 再攀高峰

11. 打开Miflash，解压好线刷包，路径不要有中文，点浏览定位到解压好的线刷包文件夹，先点刷新出现com设备，勾选清除所有数据，点刷机，看到出现了绿色的进度条内心充满了喜悦。没过几秒钟就error了，提示空间不足无法完成操作，搜了下关键词还好是小问题，虚拟内存太小了，重新设置了虚拟内存。
12. 刷新再次点下刷机，这回又提示“到文件末尾”什么的，再搜下关键词说是卸载多余的串口设备，没事还是小问题。
13. 卸载串口设备，刷新再点刷机再次提示“can not receive hello packet”，搜了下这回五花八门什么说法都有。

#### 砸落山崖

14. 按说高通线刷是终极大法，怎么到我手就不灵了，真是无语问苍天啊。

#### 我劝天公重抖擞

15. 有说要用最新版Miflash的，下载后果然界面不一样了，右下角多了个清除所有数据并上锁的选项，选择线刷包解压路径刷新设备再点刷机，还是hello packet错误。
16. 说法太多每个人都有解决方案，干脆我重新试下，重启win7，卸载多余串口设备，重启手机进入fastboot模式，重新插入电脑，用adb工具进入9008模式，打开最新版Miflash，选择线刷包，刷新设备，点击刷机，闪亮的绿色进度条出现了！这回没有波折了，等了5分多钟进度条走完提示successed，不敢动过了段时间确认完成了才重启手机。
17. 重启手机还是厌人的mi.com界面，过了超过5分钟（也可能是我等得心焦误估时间），mi.com下面才出现....四个点的进度条闪动，又过了7，8分钟才出现了miui初始化界面。


+ #### 题后语

线刷包下载的是miui官网的最新稳定版8.1.5.0，发现广告比以前少了很多，而且内置的推广app都可以删除，工信部总算做了件好事。

折腾一晚上真心累，应该这段时间都懒得动刷机念头了。