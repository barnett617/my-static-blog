---
title: vultr账单解析及使用ssr在vultr搭建代理
date: 2017-11-28 12:38:01
update: 2017-11-28 12:38:01
categories: 瞎鼓捣
tags: 
  - 其他
---

今天就之前疑惑不解的vultr账单做了一波分析，终于算是破案了。借此也记录一下服务器搭建ssr的详细步骤。

<!--more-->

先来看最终结果

![](http://trigolds.com/vultr0.png)

一开始打开vultr账单，我是懵比的

![](http://trigolds.com/vultr10.png)

这里面的加加减减使我很费解，不知道vultr的记账方式，也不知道它的扣款方式（原因是我从开通账户后就毫无规律的充值，但服务器的使用却一直未受影响而断过）

-----

首先我第一反应是看我总共实际花了多少钱。

我的vultr是绑定Paypal作为付款方式，而Paypal中绑定了两张银行卡，一张储蓄卡，一张信用卡，在查阅了银行卡关于paypal的消费记录后统计如下

![](http://trigolds.com/vultr7.png)

然后我试着从vultr账单中与之响应对账，得到如下

![](http://trigolds.com/vultr8.png)

的确找到四笔是通过paypal消费的，再来看paypal的消费记录

![](http://trigolds.com/vultr9.png)

现在证实了一点，我一共在vultr花费了17.65美元，折合人民币123.6元

----

那么接下来再通过这四笔明确的消费来反推其他账单记录的含义，发现下图这样一笔比较特殊的

![](http://trigolds.com/vultr5.png)

计算得到

![](http://trigolds.com/vultr6.png)

由此可见vultr是按小时计费，一开始承诺的每月5美元，指的是如果你用满一个月（744个小时，31天）为5美元，而使用就是指你的服务器开启着（记录中一开始有十几天没计费应该是我虽然开通了vultr并且也充了值，但是没建立服务器实例，或者说建立的实例没开启）

归根结底，当服务器开启，每个月还是相当于5美元的，这样再看账单可以清晰一点

![](http://trigolds.com/vultr4.png)

那么更进一步，我回想到之前按照首页提示的奖励金额去关注了vultr的twitter账号，所以有了11月这三笔记录

![](http://trigolds.com/vultr3.png)

这三笔很明显是系统审核我的行为后为我以信用账户（accout credit）的方式分别充值了3美元

再看本月总账单

![](http://trigolds.com/vultr1.png)

至此，整体应该算是破案了，我们可以用类比的思想（把陌生的事物类比成熟悉的事物，以便理解）可以理解为，vultr在开通账户的时候是需要先付款后消费，不存在试用（先免费用，再补交费用）的情况。

但是当开通账户并充值以后，它的收费方式是每月1号，结算上个月的消费实际情况，会给你账单记录中发送一枚发票（invoice），你使用paypal进行的主动充值也会有所记录。但是可以先消费，后缴费。具体体现就是你这个月使用的费用是下个月1号结算，如果到时候你的vultr账户余额不足，它就会从你绑定的支付方式（比如paypal或信用卡）中自动扣款。

最终再次整理账单分析

![](http://trigolds.com/vultr0.png)

-----

下面是在vultr上搭建ssr的步骤（在国外服务器搭建代理）：

首先，你得有台服务器，国外服务器，国内服务器也可以，不过达不到“代理”的效果

然后，你得有ssr的客户端 <a href="https://pan.baidu.com/s/1nvdzD37">下载</a>

接着你只需要在你的服务器上安装ssr服务，再通过ssr客户端访问，就可以实现让你电脑上的网络访问经由代理处理。

代理的作用就是，首先它是国外的服务器，它可以轻松访问很多国外的资源，比如谷歌，比如youtube。然后它作为你的代理帮你拿到这些资源后，你和代理有着亲密的关系（通过ssr），这时你访问谷歌或者youtube，实际是在访问代理替你拿到的资源。（whatever）最终你实现了访问你本来访问不到的资源这样的效果。

这么看来，关键在于搭建ssr服务这一步（和代理服务器搞好关系），那么其实这个操作也没什么难度，因为已经有大佬写了ssr这个东西，你只是需要安装而已。

有这样的一键安装脚本

不检查证书安装shadowsocksR
```
wget --no-check-certificate https://raw.githubusercontent.com/teddysun/shadowsocks_install/master/shadowsocksR.sh
```

将shadowsocksR设置为可执行
```
chmod +x shadowsocksR.sh
```

执行shadowsocksR并记录日志
```
./shadowsocksR.sh 2>&1 | tee shadowsocksR.log
```

安装过程中会提示你输入一些东西（当然，也会提示你如果不填会有默认项）

密码：设置你通过ssr客户端连接ssr服务的密码

服务端口：设置你的ssr服务在服务器上开放哪个端口提供外部访问

以及加密方式等balabala其他参数信息（都可以根据提示填好，提示很清晰，只需英语好）

最后，重启服务器

```
reboot
```

在你本地的ssr客户端填入刚才在服务器设置的一系列参数（密码、端口、加密方式及其他）即可愉快地科学上网啦

拓展：卸载shadowsocksR方法（当然，你要在安装shadowsocks的目录执行该命令）
```
./shadowsocks-go.sh uninstall
```


参考链接：

<a href="https://github.com/Alvin9999/new-pac/wiki/%E8%87%AA%E5%BB%BAss%E6%9C%8D%E5%8A%A1%E5%99%A8%E6%95%99%E7%A8%8B">https://github.com/Alvin9999/new-pac/wiki/%E8%87%AA%E5%BB%BAss%E6%9C%8D%E5%8A%A1%E5%99%A8%E6%95%99%E7%A8%8B</a>
