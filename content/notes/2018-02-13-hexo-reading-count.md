---
title: 关于hexo阅读次数统计及访问次数插件使用
date: 2018-02-13 14:46:31
lastmod: 2018-02-13 14:46:31
tags: ["Web"]
---

前一段时间换域名，发现博客的访问次数不显示了。一开始没时间关注，最近闲了，研究一下到底是怎么回事，发现原来当初这里不是基于本地配置，而是使用 leancloud 进行统计，查阅到一篇很易懂的教程，顺便整理了一下 PV 和 UV 的统计

<!--more-->

## 核心原理

hexo 很多配置基于配置文件和第三方插件，而配置文件的格式又是类似大多数模板语言的格式，比如 javaweb 中的 jsp 标签，还有 basic 中的 if 和 endif，仔细研究其中的一些配置，其实都可以很好的定制化和 hackable

## 参考链接

- <a href="http://www.jeyzhang.com/hexo-next-add-post-views.html">http://www.jeyzhang.com/hexo-next-add-post-views.html</a>

- <a href="https://leancloud.cn/">https://leancloud.cn/</a>
