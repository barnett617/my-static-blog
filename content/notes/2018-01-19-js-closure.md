---
title: js学习笔记——闭包
date: 2018-01-19 15:33:28
lastmod: 2018-1-20 09:30:04
tags: ["JavaScript"]
---

JS 闭包探析。What exactly the Closure is?

<!-- more -->

## 计算机术语

闭包：引用了自由变量的函数。这个被引用的自由变量和这个函数一同存在，即使已经离开了创造它的环境。

## 变量作用域

- 全局
- 局部

### 函数内部可直接读取全局变量

```javascript
var n1 = 9;

function f1() {
  alert(n1);
}

f1();
```

```
9
```

因为变量声明在 Global，全局可见，它的子当然可以访问到

### 函数外部无法直接读取函数内部的局部变量

```javascript
function f2() {
  var n2 = 99;
}

alert(n2);
```

```
n2 is not defined
```

因为函数是子，函数外部是父，父无法直接访问到子的局部变量

### 函数内部变量如果未用 var 声明则为全局变量

```javascript
function f3() {
  n3 = 999;
}

f3();

alert(n3);
```

999

不管变量是在函数内还是函数外声明，如果没有使用 var 声明变量，就会是全局可见的

于是此时的 n3 是 Global.n3

## 从外部读取局部变量

~想起了 Java 的反射~

方法：在函数内部再定义一个函数

```javascript
function f4() {
  var n4 = 9999;

  function f5() {
    alert(n4);
  }
}
```

解析：

f5 在 f4 内部，f4（父）的所有局部变量对 f5（子）可见
反之，f5（子）的局部变量对 f4（父）不可见

即“链式作用域”结构（chain scope）：子对象会一级一级向上寻找所有父对象的变量

将 f5 作为**返回值**，即可在 f4 外部读取其内部变量

```javascript
function f4() {
  var n4 = 9999;
  function f5() {
    alert(n4);
  }
  return f5;
}

var result = f4();
result(); // Bingo! 9999
```

引用了自由变量的函数（f5）和被引用的自由变量（n4）一同存在，即使自由变量离开了创造它的环境（f4）。

这个函数（f5）称为闭包

## 闭包

上面的 f5 函数就是闭包（能够读取其他函数内部变量的函数——函数内部的子函数）

闭包是连接函数外部和函数内部的桥梁

## 用途

- 读取函数内部的变量
- 让这些变量的值始终保持在内存中

```javascript
function f6() {
  var n6 = 999999;
  nAdd6 = function () {
    n6 += 1;
  };
  function f7() {
    alert(n6);
  }
  return f7;
}
var result6 = f6();
result6();
nAdd6();
result6();
```

```
999999
1000000
```

相当于 f6 的局部变量 n6 一直保存在内存中，并没有在 f6 调用后被自动清除

nAdd 的值是一个匿名函数，也相当于一个闭包，使得在函数外部可以对函数内部的局部变量进行操作

## 注意点

- 闭包导致函数中的变量保存在内存中，消耗内存大，易造成性能问题，IE 中可能导致内存泄露（解决：退出函数前将不使用的局部变量删除）
- 会在父函数外部改变内部变量的值，若把父函数当作对象（object）使用，把闭包当作其公用方法，把内部变量当作私有属性，切忌随意改变父函数内部变量的值

## 实践

```javascript
var name = "The Window";

var object = {
  name: "My Object",

  getNameFunc: function () {
    return function () {
      console.log(this);
      return this.name;
    };
  },
};

alert(object.getNameFunc()());
```

```
Window
The Window
```

没取到对象内部的变量，则 this 指向 window 中的变量 name（当前匿名函数的 this 即 Window）

```javascript
var name = "The Window";

var object = {
  name: "My Object",

  getNameFunc: function () {
    var that = this;
    return function () {
      return that.name;
    };
  },
};

alert(object.getNameFunc()());
```

// My Object

剖析：

> 通过函数 getNameFunc 内部的匿名函数取得 object 内部变量 name

> object.getNameFunc()()

> this 即是调用 getNameFunc 的 object

> 把 object 赋值给 that

> that.name 即是 My Object

> that.name，因为当前匿名函数没有 that 变量，会去其父寻找，找到，发现 that 的值是 this，this 又表示当前正在调用函数的对象，即 object

参考： [http://www.ruanyifeng.com/blog/2009/08/learning_javascript_closures.html](http://www.ruanyifeng.com/blog/2009/08/learning_javascript_closures.html)
