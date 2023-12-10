---
title: mysql远端数据库与本地数据库间导入导出
tags: ["Database"]
date: 2017-08-12 16:35:16
lastmod: 2017-08-12 16:35:16
---

mysql 远端数据库与本地数据库间导入导出

<!-- mor e-->

## 远程数据库导出

1. mysqldump -hxxx -uxxx -pxxx 数据库名 > 脚本名.sql
2. sz 脚本名.sql（SecureCRT 将文件下载到本地）

## 本地数据库导入

1. 若直接用 navicat 运行本脚本，失败
2. 打开 cmd，进入本地数据库，mysql -uxxxx -pxxxx，use 创建好的数据库
3. source 脚本名.sql，可以将 2MB 以上的 sql 脚本导入
4. 成功执行，完成远端数据库到本地的克隆
