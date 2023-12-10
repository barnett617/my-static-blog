---
title: javascript常见问题整理
date: 2018-02-15 15:16:31
lastmod: 2018-02-15 15:16:31
tags: ["javascript"]
---

25 个最基本的 javascript 问题整理

<!--more-->

1. 使用 typeof bar === "object"判定 bar 是否是对象的**潜在**陷阱是什么？**如何避免**该陷阱？

> 虽然 typeof bar === "object"是检查 bar 是否为对象的可靠方法，但是 javascript 中**null**也被认为是**对象**

要注意类似以下的 typeof 使用场景

```javascript
var bar2;
typeof bar2;
("undefined");
typeof undefined;
("undefined");
typeof "undefined";
("string");
typeof null;
("object");
```

要避免此类问题，可以同时检查 bar 是否为 null，如下：

```javascript
var bar = null;
console.log((bar !== null) && (typeof bar === "object"));
VM489:2 false
```

当 bar 为函数时，大多数情况上述值返回 false 是期望行为（因为函数不同于对象），但如果想要对函数返回 true 的话，可以修改为：

```javascript
function bar() {};
console.log((typeof bar !== null) && (typeof bar === "object") || (typeof bar === "function"));
VM757:2 true
```

当 bar 为一个数组时，返回值为 true 为期望行为（因为数组也是对象）

```JavaScript
var bar = [];
console.log((typeof bar !== null) && (typeof bar === "object") || (typeof bar === "function"));
VM926:2 true
```

但如果想让数组返回 false，可修改为：

```javascript
var bar = [];
console.log((typeof bar !== null) && (typeof bar === "object") && (toString.call(bar) !== "[object Array]"));
VM1133:2 false
```

如果使用 jQuery 可为：

```JavaScript
var bar = [];
console.log((typeof bar !== null) && (typeof bar === "object") && (! $.isArray(bar)));
```

拓展：call 方法

2. 以下输出什么？为什么？

```javascript
(function () {
  var a = (b = 3);
})();

console.log("a defined? " + (typeof a !== "undefined"));
console.log("b defined? " + (typeof b !== "undefined"));
```

> 由于 a 和 b 都定义在函数的封闭范围内，并且都始于 var 关键字，大多数情况期望上面结果都是 undefined，大多数情况上述的 var a = b = 3；等同于 var b = 3; var a = b；但**实际上**是 b = 3; var a = b；所以如果不使用严格模式，会输出如下：

```javascript
(function() {
	var a = b = 3;
})();

console.log("a defined? " + (typeof a !== 'undefined'));
console.log("b defined? " + (typeof b !== 'undefined'));
VM1296:6 a defined? false
VM1296:7 b defined? true
```

> 因为 var a = b = 3;相当于 b = 3; var a = b，b 没有使用 var 声明，所以变成全局变量

3. 以下输出什么？为什么

```javascript
var myObject = {
  foo: "bar",
  func: function () {
    var self = this;
    console.log("outer func: this.foo = " + this.foo);
    console.log("outer func: self.foo = " + self.foo);
    (function () {
      console.log("inner func: this.foo = " + this.foo);
      console.log("inner func: self.foo = " + self.foo);
    })();
  },
};
myObject.func();
```

```javascript
outer func: this.foo = bar
VM1353:6 outer func: self.foo = bar
VM1353:8 inner func: this.foo = undefined
VM1353:9 inner func: self.foo = bar
```

> 在外部函数中，this 和 self 都指向了 myObject，所以两者都能正确地引用和访问 foo；内部函数中，this 不再指向 myObject，所以此时 this.foo 未在内部函数中定义，相反指向到本地的变量 self 保持在范围内，且可以访问（**ES5 以前**，内部函数中的 this 将指向全局的**window**对象，**ES5**内部函数中的**this**是**未定义**的）

4. 封装 javascript 源文件的全部内容到一个函数块有什么意义及理由？

> 这是越来越普遍的做法，被许多流行 js 库所采用（例如 jQuery、Node.js）。这种技术创建了一个围绕**文件全部内容**的闭包，并且很重要的是创建了一个**私有**的**命名空间**，从而避免不同 javascript 模块和库之间潜在的**名称冲突**。

> 这种技术的另一个特点是允许一个**易于引用**的**别名**用于**全局变量**

> 例如 jQuery 插件中允许使用 jQuery.noConflict()来禁用$引用到jQuery命名空间，在此之后仍可使用$利用这种闭包技术，如下：

```javascript
(function ($) {
  /* jQuery plugin code referencing $ */
})(jQuery);
```

5. javascript 源文件开头包含 use strict 有什么意义和好处？

严格模式的一些优点：

- 使调试更加容易——那些被忽略或默默失败的代码错误会产生错误或抛出异常

- 防止意外的全局变量——非严格模式下将值分配一个未声明的变量会自动创建该名称的全局变量，但严格模式下会报错

- 消除 this 强制——非严格模式下，引用**null**或**未定义的值**到**this 值**会**自动强制**到**全局变量**

- 不允许重复的属性名称或参数值——当检测到**对象**中**重复命名属性**（var object = {foo: 'bar', foo: 'baz'};）或函数中重复命名参数（function foo(val1, val2, val1) {}）会报错，因此可捕获代码中的 bug 以避免大量的跟踪时间

- 使用 eval()更安全——严格模式下，**变量**和**声明在 eval()语句内部的函数**不会在**包含范围内**创建，而会在非严格模式下创建

- 在 delete 使用无效时报错——delete 不能用在对象不可配置的属性上，非严格模式时会静默失败，严格模式下会报错

6. 以下两个函数会返回相同的东西吗？为什么相同或不同？

```javascript
function foo1() {
  return {
    bar: "hello",
  };
}

function foo2() {
  return;
  {
    bar: "hello";
  }
}
```

```javascript
function foo1() { return {
	bar: "hello"
	};
}
console.log(foo1());
VM1405:5 {bar: "hello"}
undefined
function foo2() { return
	{
		bar: "hello"
	};
}
console.log(foo2());
VM1441:6 undefined
```

> 原因在于 javascript 中的分号是一个可选项，但省略其将会是非常糟糕的形式。当碰到 foo2()中包含 return 语句的代码行（代码行上没有任何代码），分号会立即**自动插入**到**返回语句**后。但不会报错，因为后面的代码是有效的，相当于是一个未使用的代码块，定义了等同于字符串"hello"的属性 bar

7. NaN 是什么？它的类型是什么？如何可靠地测试一个值是否等于 NaN？

> NaN 表示一个“不是数字”的值，因为运算不能执行而得。运算不能执行的原因有：其中的运算对象之一不是数字或运算结果不是数字。

> 虽然 NaN 表示“不是数字”，但是其类型是 Number

```javascript
typeof NaN;
("number");
```

> NaN 和**任何东西**（包括其自身）都是 false

```javascript
NaN == NaN;
false;
NaN === NaN;
false;
NaN === "hello";
false;
NaN == "hello";
false;
```

一种比较好的测试一个数字是否等于 NaN 的方式是使用内建函数 isNaN()，但不是最好的方案

更好的解决方案是使用 value !== value

```javascript
4 !== 4;
false;
4 !== "4";
true;
```

但如果 value 就是 NaN，那么仍然遵守“NaN 和任何东西比较都是 false”

```javascript
1 / "s" !== 1 / "s";
true;
```

ES6 提供一个新的 Number.isNaN()函数，比 isNaN()更可靠

8. 下列输出什么？为什么？

```javascript
console.log(0.1 + 0.2);
console.log(0.1 + 0.2 == 0.3);
```

```javascript
console.log(0.1 + 0.2);
console.log(0.1 + 0.2 == 0.3);
VM1624:1 0.30000000000000004
VM1624:2 false
```

JavaScript 中的数字和浮点精度的处理相同，因此可能不会总是产生预期的结果

9. 讨论写函数 isInteger(x)的可能方法，用于确定 x 是否是整数

ES6 中提供了解决方案：Number.isInteger()

但 ES 规格说明中，整数只是概念上存在，即：数字值**总是**存储为**浮点值**

ES6 之前的做法：

```javascript
function isInteger(x) {
  return (x ^ 0) === x;
}
```

^为位运算符中的 XOR 异或运算，二者（二进制形式）不同则为真，二者相同则为假，任何值和 0 求异或都是其本身，但浮点数例外，浮点数只会求其整数部分

```javascript
0 ^ 0;
0;
12 ^ 0;
12;
0 ^ 12;
12;
```

```javascript
1.1 ^ 0;
1;
1.5 ^ 0;
1;
1.9 ^ 0;
1;
```

这种方式可以当输入非数字值，例如 null 或字符串时稳健地返回 false

或者没有上面方法优雅的方案：

```javascript
function isInteger(x) {
  return Math.round(x) === x;
}
```

Math.round()为四舍五入，相当于 Math.ceil()进一法，Math.floor()退一法的集合

```javascript
Math.round(3.2);
3;
Math.round(3.5);
4;
Math.round(3.9);
4;
Math.ceil(3.2);
4;
Math.floor(3.9);
3;
```

或者

```javascript
function isInteger(x) {
  return typeof x === "number" && x % 1 === 0;
}
```

是数字的同时模 1 为 0

```javascript
3 % 1;
0;
3.1 % 1;
0.10000000000000009;
NaN % 1;
NaN;
```

不正确的一个方案是：

```javascript
function isInteger(x) {
  return parseInt(x, 10) === x;
}
```

> 这种方案在大多数情况不暴露问题，但当 x 相当大的时候无法正常工作，因为 parseInt 在解析数字之前强制其第一个参数到字符串，所以当数字十分大时，其字符串表达形式为指数形式（1e+21），此时 parseInt 去解析 1e+21，当达到 e 时则停止，会只返回 1

```javascript
String(10000000000000000000000000);
parseInt(10000000000000000000000000);
1;
```
