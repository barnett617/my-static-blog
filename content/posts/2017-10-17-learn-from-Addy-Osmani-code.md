---
title: 从谷歌"名猿"Addy Osmani一行代码中学到的东西
date: 2017-10-17 16:45:17
lastmod: 2017-10-17 21:09:22
tags: ["javascript"]
---

谷歌某大佬通过一行知识量包含极丰富的 js 代码实现了一个魔法小功能：给页面所有元素加一个彩色外边框

<!-- more -->

```javascript
[].forEach.call($$("*"), function (a) {
  a.style.outline =
    "1px solid #" + (~~(Math.random() * (1 << 24))).toString(16);
});
```

单行形式：

```javascript
[].forEach.call($$("*"), function (a) {
  a.style.outline =
    "1px solid #" + (~~(Math.random() * (1 << 24))).toString(16);
});
```

将其贴到 chrome 的 console 中即可看到效果

## 代码解析

```javascript
$$("*");
document.querySelectorAll("*");
document.all;
```

以上三种写法是相同效果，都相当于通过选择器的方式获取页面所有元素。第三种是较不规范的方式，不建议使用。$$是现代浏览器API的一部分，比如$$('a')可以获取页面所有的 a 标签元素

接下来，通过上面选择器获取到的是一个 NodeList，是一种类似于数组 Array，但它并未实现很多 Array 的接口，所以不能使用$$('\*').forEach 来遍历结果，类似的还有 arguments，也是类似于 Array，但并不是数组

这时需要通过 call()或者 apply()可以使得非数组对象来调用数组的方法

```javascript
[].forEach.call($$("*"), function (e) {});
```

以上即实现了遍历页面的每一个元素，并可以将获得的非数组元素使用数组的遍历方法来取到每一个元素 e

```javascript
a.style.outline = "1px solid #" + color;
```

outline 是 CSS 的一个属性，它是在 CSS 盒模型以外，所以它不会影响元素的 size 以及元素在 larout 中的 position

```javascript
color = (~~(Math.random() * (1 << 24))).toString(16);
```

```
1<<24 == 1左移24位 == 2^24 == 16777216
```

```
parseInt("ffffff", 16) == 16777215 = 16777216 - 1 = 2^24 - 1
```

Math.random()得到的是 0.0~1.0 之间的伪随机数

Math.random() \* (1<<24)得到 0.0~16777216.0 之间的随机浮点数

~操作符(tlide operator)可将一个变量按位取反，使用~操作符可以得到浮点数的整数部分，此处用~~达到 parseInt 的效果

toString(16)可将变量转换为 16 进制的数

综上：(~~(Math.random()\*(1<<24)).toString(16))可以获得(000000,ffffff)之间的一种随机颜色

## END

附上大佬的博客[Addy Osmani](https://addyosmani.com/blog)并献上双膝

以及本文学习源 [http://arqex.com/939/learning-much-javascript-one-line-code](http://arqex.com/939/learning-much-javascript-one-line-code)
