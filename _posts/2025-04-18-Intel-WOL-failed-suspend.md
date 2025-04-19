---
layout: post_layout
title: Intel网卡(e1000e)网络唤醒WOL(wake on lan)导致睡眠失败
time: 2025年04月18日 星期五
location: 中国
pulished: true
excerpt_separator: "<!--more-->"
---
睡眠失败不定期出现，一般都是长期不关机只睡眠使用一段时间后出现的，有时几个月有时几个星期。以前遇到都是重启了事，今天打开journalctl看了下日志才发现是wol的锅。
<!--more-->

症状就是运行'systemctl suspend'后黑屏又亮屏，日志里定位到错误如下。
```
kernel: e1000e 0000:00:19.0 enp0s25: Hardware Error
kernel: e1000e 0000:00:19.0 enp0s25: Hardware Error
kernel: PM: Some devices failed to suspend, or early wake event detected
kernel: e1000e 0000:00:19.0: PM: failed to suspend async: error -2
kernel: e1000e 0000:00:19.0: PM: dpm_run_callback(): pci_pm_suspend returns -2
kernel: e1000e 0000:00:19.0: PM: pci_pm_suspend(): e1000e_pm_suspend [e1000e] returns -2
```
查了下e1000e是intel有线网卡的驱动，挺多人遇到这个问题的，有说新内核解决了这个bug但是好像一直都在。有的人还会遇到睡眠失败后内核进程kworker占用大量cpu，有线网络失效的问题，我这里kworker只是一直占用1%-2%的cpu。


## **关闭有线网卡** ##
既然定位到问题，用'ifconfig enpxxx down'关闭有线网卡，kworker的cpu占用马上消失了，但是睡眠还是老样子失败。而且有线网卡又被重新唤醒了，kworker又开始占用cpu。有说一劳永逸禁用e1000e驱动，因为用不到有线网络，倒是干净不过太暴力了。

## **定位wol（wake on lan）**  ##
接着翻了翻网页原来是wol（网络唤醒）的问题，用'ethtool enpxxx|rg "Wake-on"'看到wake-on:后面是g就是启用，d就是禁用。用'ethtool -s enpxxx wol d'命令禁用wol，kworker占用没了，睡眠也正常了。

## **开机执行**  ##
接下来把禁用wol的命令加到开机启动项里就行了。
