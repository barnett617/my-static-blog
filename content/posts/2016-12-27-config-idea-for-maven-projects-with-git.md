---
title: IDEA2016配置运行基于Maven的Git项目
tags: ["Git"]
date: 2016-12-27 20:22:56
lastmod: 2017-05-19 20:20:20
---

IDEA2016 配置运行基于 Maven 的 Git 项目

<!-- more -->

## 一、IDEA&Maven&Git 作用

1.  IDEA（项目容器）
2.  Maven（管理 Jar 和项目打包）
3.  Git（版本控制）

## 二、从远程 clone git 项目

使用工具 clone 下 git 项目

> 可能问题： 直接通过 git bash 上 git clone 时可能失败
>
> 问题解决：使用 TortoiseGit（有时候 TortoiseGit 也可能失败，可能和 gitblit 服务器有关）。
>
> 可能项目由多个 module 相互依赖
>
> 从 Git 直接 clone 下的项目，未编译，即不含 target 或 out 文件夹（gitignore 中限制编译后的文件被 Git 管理）
>
> 但有 pom.xml 文件（Idea 导入基于 Maven 的项目依赖该文件，即导入项目时需找到 pom.xml 文件）

## 三、将 clone 到本地的 Maven 项目使用 IDEA 编辑（导入或打开）

> 如果一个项目有多个相互依赖的 module 组成（各自 module 分别有 pom.xml），则在 Idea 中依次导入已存在 module（不是新建 module）
>
> 由多个 module 相互依赖组成的项目，会有一个是主 module
>
> 或者通过 open 的方式（更快）找到 pom.xml（多个 module 使用同一个 module）
>
> ！！！对于 Maven 项目，使用 IDEA 导入可能出现的情况：
>
> 如果直接打开的 module 文件夹，而不是 pom.xml 文件，则不会生成 Maven 项目，这是 remove 了 module，会残余文件，若 delete 后，则从 git 上 clone 下的 module 文件夹中将没有了 pom.xml（因为刚才 delete 掉了），此时再看该 module 文件夹，git 已提示有文件被删除了（原因：IDEA 只是管理代码的 IDE，对于打开项目之后的操作都是对本地文件的操作，从 IDE 里删除了文件，也就是对本地文件进行删除）

**方式二**：直接使用 Idea，project from Git

## 四、IDEA 初始化配置

> 第零步：jdk&maven 配置
>
> 第一步：Project Structure

1.  project name
2.  SDK
3.  project compiler output

> 第二步：服务器配置
> 热部署（JRebel 可通过 VM 配置，给运行的容器 VM 参数 ）
> 第三部：fix artifacts，选择 war exploded

## 五、启动项目

1.  编译（build）
2.  启动服务器

## 六、问题情况

> 导入 module 后，提示 is registered as a Git root, but no Git repositories were found there.点击 config，将项目添加 git，之后正常配置，即为正常
>
> 启动 tomcat，debugger 端口被占用
> java.net.SocketException "socket closed"

## 七、问题情况

> 如果端口被占用情况反复出现，且已确认自己正确配置，那么重启电脑是最好的解决办法，其他的端口被占用情况类似（因为端口占用的问题浪费了几个小时都找不到原因，查了 stackoverflow，segementFault，看了各种博客，解决方法都不行，原来重启就解决了。。。俗话说：没有什么是重启电脑解决不了的，重启真是神技啊！）
