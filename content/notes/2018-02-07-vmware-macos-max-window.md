---
title: VMware使用macOS如何全屏
date: 2018-02-07 14:10:45
lastmod: 2018-02-07 14:10:45
tags: ["OS"]
---

如何解决 VMware 安装 VMware Tools 后仍不能全屏显示的问题

<!--more-->

## 原因

macOS10.11 上启动了一个 SIP(System Integrity Protection，系统集成保护)

它防止/library/perferences/systemconfiguration/com.apple.Boot.plist 文件被修改

## 解决

启动 macOS 过程中进入 recovery console（启动系统时按住 command+R，windows 系统则按住开始键+R，直至看到苹果标志即可松手）

选择实用工具，打开终端

关闭 SIP，重启

Bingo！
