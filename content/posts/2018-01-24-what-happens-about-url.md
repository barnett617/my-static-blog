---
title: 从输入网址到浏览器呈现内容期间发生的事情
date: 2018-01-24 09:49:03
lastmod: 2018-01-24 09:49:03
tags: ["Web"]
---

探析浏览器访问站点这一行为背后的具体行为。

<!-- more -->

1. 浏览器地址栏输入网址www.coder.com
2. 浏览器发送一个 UDP 包给 DNS 服务器
3. DNS 服务器返回 coder.com 的 IP
4. （optional）浏览器将该 IP 缓存起来，以提高下次访问速度（Chrome 通过 chrome://net-internals/#dns 查看）
5. 浏览器利用获取到的 IP 发起 HTTP 请求，但是 HTTP Request/Response 必须在 TCP 这个“虚拟的连接”上发送和接收
6. 建立“虚拟的”TCP 连接需要**本机 IP**、**本机端口**、**服务器 IP**、**服务器端口**
7. 本机端口由操作系统给浏览器随机分配
8. 服务器端口使用相应服务的端口，例如 HTTP 服务：80
9. 三次握手后，客户端与服务器建立 TCP 连接
10. 一个 HTTP GET 请求经过多个路由器转发，达到服务器端（HTTP 数据包可能被下层分片传输）
11. web 服务器处理请求（三种方式）

    - 用一个线程处理所有请求，但同一时刻只能处理一个，性能问题严重

    - 为每个请求分配一个进程/线程，但当连接太多时，服务器端的进程/线程耗费大量内存资源，进程/线程切换导致 CPU 不堪重负

    - 复用 I/O：众多 Web 服务器采用复用结构，例如通过 epoll 方式监视所有连接，当连接状态发生变化（如有数据可读），才用一个进程/线程对该连接进行处理，处理完继续监视，等待下次变化。该方式可用少量进程/线程应对大量的连接请求

12. 以 Nginx 为例，对于 HTTP GET 请求，Nginx 利用 epoll 方式读取出来，Nginx 判断该请求是静态 or 动态
13. 若为静态（HTML、JavaScript、CSS、图片等），依赖于 Nginx 配置，可能转发到其他缓存服务器，可能读取本机硬盘上相关文件直接返回
14. 若为动态，需要后端服务器（如 Tomcat）处理后返回，则转发到 Tomcat，若后端 Tomcat 不止一个，则按策略选取一个，Nginx 选取方式

    - 轮询：按照次序依次向服务器转发

    - 权重：每个后端服务器指定一个权重，决定向每个服务器转发的几率

    - ip_hash：根据 IP 进行 hash，找到要转发的服务器，则同一个客户端 IP 总会被转发到同一个后端服务器

    - fair：根据后端服务器相应时间分配请求，响应时间短的优先分配

15. 无论哪种策略，Nginx 需要将 HTTPRequest 转发给后端 Tomcat，并把 Tomcat 输出的 HttpResponse 转发给浏览器
16. Http Request 到达 Tomcat（由 Java 编写，可处理 Servlet/JSP 的容器），Tomcat 可能为每个请求分配一个线程进行处理，即 BIO 模式（Blocking I/O）或 I/O 多路复用模式或仅使用若干线程处理所有请求（NIO 模式）
17. Http Request 被交给某个 Servlet 处理，Servlet 将请求转换，变成后端框架所用参数格式，分发给某个 Controller(Spring)或 Action(Struts)
18. 后端处理，包括和缓存、数据库等组件交互，最终返回 Http Response，本例即为一个 HTML 页面
19. Nginx 将 Http Response 发送给浏览器，若使用 HTTP1.1，该 TCP 连接默认为 keep-alive，即不能关闭
20. 若 HTTP1.0，需根据 HTTP Request 中是否有 Connection:keep-alive 判断该 TCP 连接是否能关闭
21. 浏览器收到 Http Response，读取 HTML 页面
22. 该页面引用大量其他资源，例如 js、css、图片等，这些资源位于服务器端，并且在另一个域名 static.coder.com 下
23. 浏览器需要一一下载，从 DNS 获取 IP 开始，重复上述操作，除去 Tomcat 处理
24. 由于要下载的资源众多，浏览器会建立多个 TCP 连接，并行下载
25. 但同一时间对同一域名的请求数量不能太多，否则服务器访问量太大无法承受
26. 因此浏览器做限制，例如 Chrome 在 HTTP1.1 只能并行下载 6 个资源
27. 当服务器给浏览器发送静态文件，会声明过期时间（Cache-Control 或 Expire），浏览器可将文件缓存到本地，第二次请求相同文件时，若不过期则直接从本地读取
28. 若过期，浏览器询问服务器端，文件是否变更（根据上一次服务器回传的 Last-Modified 和 ETag），若未修改（304 Not Modified），则可使用本次缓存，否则服务器将最新的文件发回浏览器
29. 若按 Ctrl+F5 则强制发出 GET 请求，无视缓存（Chrome 使用 chrome://view-http-cache 查看缓存）
30. 浏览器得到 HTML（浏览器将其变成 DOM Tree）、CSS（浏览器将其变成 CSS Rule Tree）、JavaScript（可修改 DOM Tree）
31. 浏览器通过 DOM Tree 和 CSS Rule Tree 生成“Render Tree”，计算每个元素的位置和大小，进行布局，然后调用操作系统的 API 进行绘制

参考：<a href="http://mp.weixin.qq.com/s/V1fUjSP3BwJ1CbEM0MC6pw">http://mp.weixin.qq.com/s/V1fUjSP3BwJ1CbEM0MC6pw</a>
