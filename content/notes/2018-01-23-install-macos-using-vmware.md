---
title: 使用vmware安装macOS
date: 2018-01-23 12:04:14
lastmod: 2018-01-23 12:04:14
tags: ["OS"]
---

虽然类似这样的文章网上一搜一大把，但应 [youngwa](https://github.com/yanghua0311) 邀，还是决定重新整理一下个人安装的详细流程及相关资源链接，以备后用。

<!-- more -->

## 资源准备

macOS 系统镜像 8G 左右 cdr 后缀 **<a href="https://pan.baidu.com/s/1i60XCgd">点击下载</a>**

vmware 推荐版本 12（自行安装）

vmware 补丁（用于添加 macOS 类镜像）**<a href="https://pan.baidu.com/s/1mjoGoNQ">点击下载</a>**

## 操作步骤

1. 下载以上三个资源
2. 安装 VMware
3. 安装补丁，补充 VMware 关于镜像类型的选择（若不安装补丁，则没有 macOS 镜像的选项），安装 unlock，右击使用管理员权限打开 win-install.cmd
4. 新建虚拟机，选择 macOS 类型镜像，并做相应配置
5. 配置好后打开虚拟机的 vmx 文件，手动修改配置（搜索 smc）在 smc.present = "TRUE"后添加一行 smc.version = 0
6. 启动虚拟机进行系统安装
7. 完成即可（历时一个小时左右，视宿主机内存及硬盘配置而定）

## 注意事项

需要理解 linux 分区概念（不理解也可以将虚拟机分配的全部空间用作一个分区，在实用工具选项中选择磁盘工具，设置后关闭，进行系统安装）

## 注意点

安装 macOS 和安装其他系统镜像类型的虚拟机没什么大的区别，主要在于以下几点：

- 为 VMware 安装补丁，拓展镜像类型（实用 unlock）
- 修改 vmx，虚拟机配置文件，添加 smc.version = 0
