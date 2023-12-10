---
title: 关于centos的iptables以及firewalld的总结
date: 2017-11-16 15:57:50
lastmod: 2017-11-16 17:51:26
tags: ["OS"]
---

centos 内置一个非常强劲的防火墙，统称为 iptables，实际原理是 iptables 是用户空间的模块，在内核中存在一个 netfilter 核心模块用于实现 iptables 中设置的规则，进行底层的实际过滤。

<!-- more -->

## iptables

### 关键字

- IP 地址
- 协议（TCP、UDP、ICMP）
- 端口

### 原理

iptables 将规则放入缺省规则链（INPUT、FORWARD、OUTPUT），所有流量（IP 封包）会被相关的规则链检查，根据规则处理每个封包（ACCEPT/REJECT），这些动作称为目标（target）

INPUT：以主机为目的地的封包
FORWARD：封包的目的地和来源地都不是主机，途经主机（由其选路），比如主机是路由器，这条规则链将被应用
OUTPUT：源自主机的封包

每个封包会逐一匹配每条规则，若符合某一条规则，响应动作将被执行（ACCEPT/REJECT），一旦吻合一条规则，则不再被其他规则检查。若所有规则都不匹配，将会执行这条规则链的缺省动作（即括号内的缺省策略）

### 使用方式

白名单方式：即规则链缺省规则设为 REJECT，仅对符合某些条件的封包进行放行，例如：bittorrent、FTP 服务器、网页服务器、Samba 文件服务器

黑名单方式：规则链缺省策略设置为 ACCEPT，对需要拦截的封包做拦截 REJECT

一般白名单用于 INPUT 规则链，用于控制目的地为主机的封包，黑名单用于 OUTPUT 规则链，用于控制由主机流出的封包

### 使用

iptables 需要 root 用户操作

iptables 是缺省安装在 centos5.x 及 6.x 上，而 centos7.x 使用 firewalld 取代之

先使用 rpm -q iptables 查看 iptables 是否安装在系统上

lsmod | grep ip_table 检查 iptables 模块是否被装入，

当 iptables 模块被装入后可通过 iptables -L 查看活动规则

更多使用策略参考链接：

- <a href="https://wiki.centos.org/zh/HowTos/Network/IPTables">https://wiki.centos.org/zh/HowTos/Network/IPTables</a>

## firewalld

### 简介

firewalld 是 centos7 预装的动态防火墙**后台**程序，用以支持网络“zones”，以分配对一个网络及其相关链接和界面的一定程度的信任。

### 特性

- 具备对 IPV4 和 IPV6 防火墙设置的支持
- 支持以太网桥
- 有运行时（runtime）和永久性（permanent）两种配置方式
- 具备一个通向服务或者应用程序的接口，以直接增加防火墙规则
- 动态生效，不需要保存或执行配置改变，会随时执行

### 对比 firewalld 和 iptables

- 配置存储

iptables service 存储在/etc/sysconfig/iptables

firewalld 配置存储在/usr/lib/firewalld 和/etc/firewalld/中的各种 XML 文件中

> 注意！当 firewalld 在 RHEL(Red Hat Enterprise Linux)上安装失败时，/etc/sysconfig/iptables 文件将不存在

- 规则修改

iptables service 每一个单独修改意味着清除所有原有规则，重新从/etc/sysconfig/iptables 中中读取规则

firewalld 不会创建新的规则，仅运行规则中的不同之处（因为可以在运行时修改而不丢失现有连接）

### 使用

上图可看出对于 iptables 防火墙 centos 有两种实现方式，即 iptables service(service)或 firewalld(daemon&service)

实际场景分别如下：

centos5.x 或 centos6.x 默认使用 iptables-services

centos7 默认使用 firewalld

> Tip：若安装了 systemctl，使用"service 服务名 status"查看服务状态会被重定向为"systemctl status 服务名"

### 将 firewalld 切换至 iptables.service

1.以 root 身份，先禁用并停止 firewalld 服务

```
systemctl disabled firewalld
systemctl stop firewalld
```

2.查看 iptables 是否安装在系统上，若不存在则安装 iptables-services 程序包

```
rpm -q iptables
yum install iptables-services
```

iptables-services 包含 iptables 和 ip6tables 服务

3.启动服务

```
systemctl start iptables
systemctl start ip6tables
systemctl enable iptables
systemctl enable ip6tables
```

### 使用 firewalld 取代 iptables.service

1.查看 firewalld 服务运行情况

```
systemctl status firewalld
firewall-cmd --state
```

2.安装 firewalld（可选：图形化用户接口工具 firewall-config）

```
yum install firewalld
yum install firewall-config
```

3.配置防火墙三种方式

- 命令行接口工具 firewall-cmd
- 图形化接口工具 firewall-config
- 编辑相应 XML 文件

> 注意：在 Runtime 模式下更改防火墙设置会立即生效，应注意本系统上的其他用户使用情况；Permanent 模式下更改设置，仅在重新加载防火墙或重启系统后生效

重启防火墙命令

```
firewall-cmd --reload
```

参考链接：

- [https://access.redhat.com/documentation/zh-cn/red_hat_enterprise_linux/7/html/security_guide/sec-using_firewalls](https://access.redhat.com/documentation/zh-cn/red_hat_enterprise_linux/7/html/security_guide/sec-using_firewalls)
- [http://havee.me/linux/2015-01/using-firewalls-on-centos-7.html](http://havee.me/linux/2015-01/using-firewalls-on-centos-7.html)
