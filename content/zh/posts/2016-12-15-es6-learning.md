---
title: ECMAScript6(ECMAScript2015)学习笔记
date: 2016-12-16 15:34:31
lastmod: 2017-06-23 20:20:20
categories: 笔记
tags:
  - es6
---

ECMAScript 6（以下简称 ES6）是 JavaScript 语言的下一代标准。因为当前版本的 ES6 是在**2015 年**发布的，所以又称 ECMAScript 2015。

<!--more-->

### 一、ES6 简介

> ECMAScript 6（以下简称 ES6）是 JavaScript 语言的下一代标准。因为当前版本的 ES6 是在**2015 年**发布的，所以又称 ECMAScript 2015。

> 即 ES6 === ES2015

### 二、ES6 转码器

> [Babel](https://babeljs.io/)是一个广泛使用的 ES6 转码器，可以将 ES6 代码转为 ES5 代码，从而在现有环境执行。（chrome 已支持 ES6 解释，亲测可用）大家可以选择自己习惯的工具来使用使用 Babel，具体过程可直接在[Babel 官网](https://babeljs.io/)查看：

### 三、常用特性

1.  let / const（与传统 var 对比）
2.  class / extends / super（面向对象）
3.  arrow functions（箭头函数）
4.  template string（模板字符串）
5.  destructing（解构）
6.  default（默认值）
7.  rest / arguments（函数参数）

### 四、特性详解

#### （1）let

> 与 var 类似，都是用来**声明变量**的，但在实际运用中他俩都有各自的特殊用途。

```js
var name = "tom";

while (true) {
  var name = "bar";
  console.log(name);
  break;
}

console.log(name);
```

> bar
> bar
>
> 使用 var 两次输出都是 bar，内层变量覆盖外层变量。这是因为 ES5 只有**全局作用域**和**函数作用域**，没有**块级作用域**，这带来很多不合理的场景。
>
> 而 let 则实际上为 JavaScript 新增了**块级作用域**。用它所声明的变量，只在 let 命令所在的代码块内有效。

```js
let name = "tom";

while (true) {
  let name = "bar";
  console.log(name);
  break;
}

console.log(name);
```

> bar
> tom
>
> 另外一个 var 带来的不合理场景就是用来计数的**循环变量**泄露为**全局变量**，如下

```
var a = [];

for(var i=0; i<10; i++) {
	a[i] = function() {
		console.log(i);
	};
}

a[5]();
```

> 10
>
> 上面代码中，**变量 i 是 var 声明的，在全局范围内都有效**。所以每一次循环，**新的 i 值**都会**覆盖旧值**，导致最后**输出**的是**最后一轮**的 i 的值。而使用 let 则不会出现这个问题。

```js
var a = [];

for (let i = 0; i < 10; i++) {
  a[i] = function () {
    console.log(i);
  };
}

a[5]();
```

> 5

#### （2）const

> const 也用来声明变量，但是声明的是**常量**。一旦声明，常量的值就不能改变。

```js
const PI = Math.PI
PI = 23
VM1600:2 Uncaught TypeError: Assignment to constant variable.(…)(anonymous function) @ VM1600:2
```

> 当我们尝试去改变用 const 声明的常量时，浏览器就会报错。const 有一个很好的应用场景，就是当我们引用**第三方库**时声明的变量，用 const 来声明可以避免未来不小心重命名而导致出现 bug：
>
> const monent = require('moment')

#### （3）class/extends/super

> ES6 提供了更接近**传统语言**的写法，引入了 Class（类）这个概念。新的 class 写法让**对象原型**的写法更加清晰、更像**面向对象编程**的语法，也更加通俗易懂。

```
class Animal {
	constructor() {
		this.type = 'animal'
	}
	says(say) {
		console.log(this.type + 'says' +say)
	}
}

let animal = new Animal()
animal.says('hello')
```

> animalsayshello

```
class Cat extends Animal {
	constructor() {
		super()
		this.type = 'cat'
	}
}

let cat = new Cat()
cat.says('hello')
```

> catsayshello
>
> 上面代码首先用**class**定义了一个“类”，可以看到里面有一个 constructor 方法，这就是构造方法。
>
> 而**this**关键字则代表**实例对象**。简单地说，constructor 内定义的方法和属性是实例对象自己的，而 constructor 外定义的方法和属性则是所有实例对象可以共享的。
>
> Class 之间可以通过**extends**关键字实现继承，这比 ES5 的通过修改原型链实现继承，要清晰和方便很多。上面定义了一个 Cat 类，该类通过 extends 关键字，继承了 Animal 类的所有属性和方法。
>
> **super**关键字，它指代父类的**实例**（即父类的**this 对象**）。子类必须在 constructor 方法中调用**super**方法，否则新建实例时会报错。这是因为**子类没有自己的 this 对象**，而是继承父类的 this 对象，然后对其进行加工。如果不调用 super 方法，子类就得不到 this 对象。
>
> ES6 的继承机制，实质是先创造父类的实例对象 this（所以必须先调用 super 方法），然后再用子类的构造函数修改 this。

#### （4）arrow function

> ES6 最最常用的一个新特性了，用它来写 function 比原来的写法要简洁清晰很多

```
var f = (i) => i+1
var result = f(3)
console.log(result)
```

> 4
>
> 其实声明函数只需要（i）=>i + 1 这一句
> ES6 的箭头函数功能强大，详情见[Arrow Functions](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Functions/Arrow_functions)

```
// ES5函数使用
function test(x, y) {
	x++;
	y--;
	return x + y;
}

var f1 = test(6, 7)
console.log(f1)

```

> 13
>
> 这里实际为 7+6，return 的时候 x 已经为 7，y 为 6，可自行调试观察

```
// ES6 arrow functions
var arrow = (x, y) => {x++;y--;return x+y}
var result2 = arrow(3, 9)
console.log(result2)
```

> Arrow functions 还可以解决一个 ES5 中关于 this 的问题，JavaScript 语言的 this 对象一直是一个令人头痛的问题，在**对象方法**中使用 this，例如：

```
class Animal {
	constructor() {
		this.type = 'animal'
	}
	says(say) {
		setTimeout(function() {
			console.log(this.type + ' says ' + say)
		}, 1000)
	}
}

var animal = new Animal()
animal.says('hi')
```

> undefined says hi
>
> 原因：setTimeout 中的 this 指向的是全局对象
>
> 传统解决方法 1：将 this 传给 self,再用 self 来指代 this

```
class Animal {
	constructor() {
		this.type = 'animal'
	}
	says(say) {
		var self = this
		setTimeout(function() {
			console.log(self.type + ' says ' + say)
		}, 1000)
	}
}

var animal = new Animal()
animal.says('hi')
```

> 传统解决方法 2：使用 bind(this)

```
class Animal {
	constructor() {
		this.type = 'animal'
	}
	says(say) {
		setTimeout(function() {
			console.log(this.type + ' says ' + say)
		}.bind(this), 1000)
	}
}

var animal = new Animal()
animal.says('hi')
```

> 使用 arrow functions 解决

```
class Animal {
	constructor() {
		this.type = 'animal'
	}
	says(say) {
		setTimeout( () => {
			console.log(this.type + ' says ' + say)
		}, 1000)
	}
}

var animal = new Animal()
animal.says('hi')
```

> 当我们使用箭头函数时，函数体内的 this 对象，就是**定义时**所在的对象，而不是**使用时**所在的对象。并不是因为箭头函数内部有绑定 this 的机制，实际原因是箭头函数根本**没有自己的 this**，它的 this 是**继承外面的**，因此内部的 this 就是外层代码块的 this。

#### （5）template string

> 当我们要插入**大段**的**html 内容**到文档中时，传统的写法非常麻烦，所以之前我们通常会引用一些**模板工具库**，比如**mustache**等等。

```html
<body>
  <div id="result">test</div>
</body>
```

```
$(function() {
	var count = 5
	var onSale = 3

	$("#result").append(
		"There are <b>" + count + "</b>" +
		" items in your basket," +
		"<em>" + onSale +
		"</em> are on sale!"
	);
})
```

> testThere are 5 items in your basket,3 are on sale!
>
> 要用一堆的'+'号来连接**文本**与**变量**，而使用 ES6 的新特性**模板字符串``**后，我们可以直接这么来写

```
$(function() {
	var count = 5
	var onSale = 3

	$("#result").append(`
		There are <b>${count}</b>
		items in your basket,
		<em>${onSale}</em> are on sale!
	`);
})
```

> test There are 5 items in your basket, 3 are on sale!
>
> 非一般的简洁
>
> 用**反引号**（\）（键盘 ESC 下方）来标识起始，用${}（类似于 JavaEE 的 EL 表达式）来引用变量，而且所有的**空格**和**缩进**都会被**保留**在输出之中

#### （6）destructing

> ES6 允许按照一定模式，从**数组**和**对象**中**提取值**，对**变量**进行**赋值**，这被称为**解构**（Destructuring）

```js
// ES5
let cat = "tom";
let mouse = "terry";
let zoo = { cat: cat, mouse: mouse };
console.log(zoo);
```

> Object {cat: "tom", mouse: "terry"}

```js
// ES6
let zoo2 = { cat, mouse };
console.log(zoo2);
```

> Object {cat: "tom", mouse: "terry"}

```js
let dog = { type: "animal", num: 6 };
let { type, num } = dog;
console.log(type, num);
```

> animal 6

#### （7）default

```js
// ES5
function animal(type) {
  type = type || "cat";
  console.log(type);
}

animal();
animal("dog");
```

> cat
> dog
>
> 调用方法时若不加参数则使用默认值

```js
// ES6
function animal(type = "cat") {
  console.log(type);
}

animal();
animal("dog");
```

> cat
> dog

#### （8）rest

```js
// ES6 rest
function animals(...types) {
  console.log(types);
}

animals("cat", "dog", "fish");
```

> ["cat", "dog", "fish"]
