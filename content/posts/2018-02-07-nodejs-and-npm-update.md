---
title: 工具篇——如何管理node和npm的更新
date: 2018-02-07 14:27:19
lastmod: 2018-02-07 14:27:19
categories: 前端
tags: ["node"]
---

管理 node 和 npm 的更新

<!--more-->

今天安装 react 开发工具，在使用 npm 安装 create-react-app 时，被提示更新 npm 和 node 的版本以获得更好的体验，瞬间感觉惨遭嫌弃，遂整理一波 node 和 npm 管理更新的方式

先查看 node 和 npm 的版本

```
node --version
```

```
npm --version
```

安装 nvm

```
curl -o- https://raw.githubusercontent.com/creationix/nvm/v0.25.4/install.sh | bash
```

这种方式用于类 Unix 系统

不过要注意安装后要重新打开终端，否则识别不到 nvm，而且其实在安装结束的时候，是有提示文案的（这其实和 Windows 下在 cmd 中安装东西后需要重新打开 cmd 一样）

nvm 主要用于安装 node 的不同版本，使用如下：

```
nvm install 8.5.0
```

这样直接安装指定的 node 版本，还可以

```
nvm install latest
```

安装最新版本（但不是最稳定版本，会有新的测试特性），还可以安装稳定版本

```
nvm install stable
```

Windows 的 nvm 还可以下载 node 多个版本并进行选择使用哪个版本（和 linux 不同，安装新的版本必须替换掉旧的版本，同时只能存在一个版本）

windows-nvm<a href="https://github.com/coreybutler/nvm-windows/releases">下载地址</a>

同样，下载某个你需要的版本

```
nvm install 8.5.0
```

结束后会提示你可以通过命来切换刚下载的版本，此时你也可以在 nvm 的目录下看到不同版本的 node 文件夹

如果不切换，仍会是当前安装的 node 版本

可查看当前本地有哪些版本

切换命令如提示：

```
nvm use 8.5.0
```

相应，删除某个版本

```
nvm uninstall 4.4.3
```

另外对于 Windows 下更新 npm 到最新版本，如下：

```
npm install npm@latest -g
```

参考链接：<a href="https://segmentfault.com/a/1190000007612011">https://segmentfault.com/a/1190000007612011</a>
