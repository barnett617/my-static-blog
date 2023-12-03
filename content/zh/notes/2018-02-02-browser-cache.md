---
title: 关于浏览器缓存
date: 2018-02-02 14:38:49
lastmod: 2018-02-02 14:38:49
categories: 浏览器
tags: ["浏览器"]
---

探讨浏览器缓存相关

<!--more-->

### 相关概念

- cookie

- 浏览器缓存

- localStorage

- sessionStorage

### 使用场景

提高前端访问**性能**——优秀的**缓存策略**

好处：

- 缩短网页请求资源的距离

- 减少延迟

- 缓存文件重复使用

- 减少带宽

- 降低网络负荷

### web缓存分类

- 数据库缓存

- 代理服务器缓存

- CDN缓存

- 浏览器缓存

实例：

浏览器向代理服务器发起web请求，代理服务器将请求转发到源服务器，此处使用**共享缓存**，使得多处地方可以使用相同的缓存，节省流量

### 浏览器缓存

实例：

浏览器缓存是将文件保存在**客户端**，同一个会话过程中检查缓存副本是否足够新，在**后退**网页时，访问过的资源可从缓存中取。

性能提升点：通过**减少**服务器**请求**的数量，获得**更快**的访问体验

#### 缓存决定因素

页面的缓存状态由header决定，重要参数如下：

1. Cache-Control

2. Expires

3. Last-modified

4. ETag

##### 1.Cache-Control

可配置选项如下：

- max-age

- s-maxage

- public

- private

- no-cache

- no-store

- must-revalidate

- ...

###### 总体关联

- max-age

单位：s

含义：缓存最大有效时间

特点：会覆盖掉Expires，并且在缓存有效时间内，即使服务器上资源发生变更，浏览器也不会得到通知

- s-maxage

同max-age，但仅用于共享缓存（例如CDN缓存）

实例：s-maxage=60，则60s内即使CDN内容更新，浏览器也不会再次请求

对比max-age：

max-age用于普通缓存，s-maxage用于代理缓存

特点：s-maxage会覆盖掉max-age和Expires

- public

响应会被缓存，且在多用户间共享

若未指定public还是private，默认为public

- private

相应会被缓存，且不在用户间共享

实例：要求HTTP认证，相应会自动设置为private

- no-cache

指定响应资源不进行缓存

注意：仅设置no-cache不代表浏览器不缓存，而是在缓存前要向服务器确认资源是否变更

实例：若想防止缓存，可设置no-cache private 过期时间设置为已过去的时间（不会到达）

-no-store

绝对禁止缓存，每次请求资源都从服务器重新获取

- must-revalidate

若页面过期，则需重新去服务器获取

##### 2.Expires

缓存过期时间（指定资源到期时间，在此时间前浏览器可从浏览器缓存取资源，而无需再次请求），是**服务器端**具体时间点

Expires = max-age + 请求时间

需结合Last-modified结合使用

优先级：Cache-Control > Expires

##### 3.Last-Modified

**服务器端**文件最后修改时间，需和Cache-Control共同使用

可检查服务器端资源是否变更

浏览器再次发出请求时，会向服务器发送If-Modified-Since报头，以询问Last-Modified时间点后该资源是否变更，若未变更，则返回304，使用缓存；若变更，则重新向服务器请求资源，返回200

##### 4.ETag

根据资源文件**内容**生成**hash**，用于标识**资源状态**，**服务器端**产生

浏览器再次访问服务端时会带上ETag，以验证资源是否变更

<img src="http://trigolds.com/cache2.jpg" width="70%">

优势：解决Last-modified存在的问题

1. 某些服务器不能精确得到资源的最后修改时间，因此无法根据最后修改时间判断资源是否变更

2. 资源修改频繁，在秒级下的修改，无法被Last-modified识别到（Last-Modified为秒级）

3. 资源最后修改时间改变，但内容未变更，ETag识别为资源未变更（实际上资源的最后修改时间发生变更）




参考链接：

- <a href="https://segmentfault.com/a/1190000008377508">https://segmentfault.com/a/1190000008377508</a>

- <a href="http://www.linuxidc.com/Linux/2016-12/138800.htm">HTTP权威指南</a>
