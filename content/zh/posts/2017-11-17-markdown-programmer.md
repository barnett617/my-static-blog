---
title: Markdown语法学习整理
date: 2017-11-17 10:09:16
categories: 其他
tags:
  - 其他
---

虽说 markdown 用了一段时间，但还未曾专门地阅读文档对其进行过相对系统的学习。刚才使用分割线时发现分割线下面的文字变成标题般字体，专门查了一波，发现还是有必要系统学习一下 md 语法，以节省日后使用时的查阅时间。

<!--more-->

### 宗旨

易读易写

一份使用 markdown 格式撰写的文档理应可以直接以纯文本发布，也就意味着 markdown 的一系列标签语法对于纯文本来说应该是**低浸入**、无感知的，用户阅读不会因为增加了 md 标签而变得晦涩。

这点区别于 HTML 语言至少还需要掌握 HTML 编程语法，才能理解部分标签的使用规则。

### 灵感

markdown 最大灵感源自纯文本电子邮件的格式（可能人们对于纯文本的电子邮件格式觉得太过单调，于是通过添加一些小标记，来增加丰富的格式）

### 目标

- 成为一种适用于网络的书写语言

> markdown 并非要取代 HTML，甚至也没有要和它相近。

> 相比 HTML，markdown 语法种类少，仅对应 HTML 标记的一小部分

- 让文档更容易读、写、随意改

> 易读的前提就是 md 的标记需要尽量的没有侵入感，比如一篇纯文本文档增加 md 标记后不会影响原始阅读感受

> 易写易改的要求则是 md 的语法标记需要同 HTML 一样“语义化”，能够见标知意

### 兼容 HTML

#### 区块标签

一些 HTML 区块元素比如<code>&lt;div&gt;</code>、<code>&lt;table&gt;</code>、<code>&lt;pre&gt;</code>、<code>&lt;p&gt;</code>等，**必须**在其前后加上空行与其他内容隔开，**并且开始标签与结束标签不能用制表符（Tab）或空格来进行缩进**。
（下面实例的缩进实现方式是通过 HTML 的 pre 标签，使其内部的 markdown 语法失效）

> 比如这里的 HTML 标签如果我想要以标签的样子显示在屏幕上，那我其实需要在文本里通过 md 标记来编辑，类似使用 code 标签、转义字符（通过转义字符来“画出”尖括号，code 标签可以使其成为块状文本）

另外，markdown 生成器不会在 HTML 区块标签外加上不必要的<code>&lt;p&gt;</code>标签，区别于对于 HTML 文本进行编辑时，若你写了大段内容而未加任何标签的话，HTML 生成器会为你在内容前后加上<code>&lt;p&gt;</code>标签

实例如下：

<pre><code>段落A

&lt;table&gt;
		&lt;tr&gt;
				&lt;td&gt;Foo&lt;/td&gt;
		&lt;/tr&gt;
&lt;/table&gt;

段落B
</code></pre>

> 注意！在 HTML 区块标签区间的 Markdown 语法将不会被处理

#### 区段（行内）标签

HTML 行内标签比如&lt;span&gt;、&lt;cite&gt;、&lt;del&gt;可以在 Markdown 的段落、列表或是标题内随意使用，比如直接使用&lt;a&gt;标签撰写超链接、使用&lt;img&gt;标签取代 md 的

<pre><code>
![](图片地址)
</code></pre>

（当想要 md 标签直接显示在屏幕上，而不被 markdown 生成器处理，可以将其写在 HTML 区块标签内）

总结：HTML 区块内的 md 标签不生效，HTML 区段内的 md 标签生效

### 特殊字符自动转换

<a href="https://balabala&balabala">balabala</a>

上面这个 a 标签超链接 href 中的 https://balabala&balabala 会被解析成 https://balabala%26balabala/

（这一点我看到的时候，瞬间感觉到头皮发麻，因为我之前博客里的参考链接都是使用 HTML 的 a 标签，也就意味着如果连接内包含&字符的话，其实应该要改写成<code>&amp;amp;</code>才能被正确的解析）转义还真是门学问-\_-||

如果你下在页面显示出 AT&amp;T，其实需要在 md 文件中写成 AT&amp;amp;T，而想要显示 AT&amp;amp;T，则要在 md 中写成 AT**&amp;amp;**apm;T，别绕晕，总之需要通过<code>&amp;amp;</code>这个转义符来“渲染”&符，即遇到想要显示<code>&amp;</code>的地方使用<code>&amp;amp;</code>来转义，理清之后会觉得转义很有意思 : ）

那么究竟 markdown 对于<code>&amp;</code>的处理方式是如何的呢？答案是：视情况而定

- 不做转换

> 当<code>&amp;</code>是 HTML 字符实体的一部分时，它会不做干涉

> 比如你在 md 内写到&amp;copy;

> 会被解析成&copy;

（注意：当想你的 md 某段内容包含 md 标签时，请不要在外部套上 HTML 区块标签，因为那会使 md 标签失效）

- 转换

> 而如果你在 md 中写的<code>&amp;</code>不是 HTML 字符实体的一部分的话，它就会被转换成&amp;amp;

markdown 这么做充分体现其兼容 HTML 的做法

（当多行引用标记连续使用时，若其中某一行进行缩进，会在引用的基础上增加层次）

4 < 5

4 &lt; 5

在 code 范围内，不论是行内还是区块，<和&两个符号都**一定**会被转换成 HTML 实体，这项特性可以使你轻易地使用 md 写 HTML code，相比在 HTML 文档中需要把所有的<和&转换为 HTML 实体才能在 HTML 文件内写 HTML code

上面这段话的意思就是在 html 文件里，遇到&或是<，需要以&amp;amp;或是&amp;lt;这样的 HTML 实体来表示之后，才能正常地编写 html code，但是在 markdown 里不需要这一步，

`<span><code>4 &</code></span>`

`<code>4 < 5</code>`

`<code><code>`

参考链接：

- <a href="http://www.appinn.com/markdown/">http://www.appinn.com/markdown/</a>
