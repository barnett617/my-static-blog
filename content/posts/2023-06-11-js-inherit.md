---
title: "JavaScript的原型和继承"
date: 2023-06-11 00:42:00
summary: "关于JavaScript继承原理的学习"
cover:
  image: "https://user-images.githubusercontent.com/23159565/79936336-c2b13200-8489-11ea-920b-7118cbd694db.jpg"
---

> 本文为历史博客迁移

<!-- ![v2-67544acf46e76d497bb250e37c3ebc2f_720w](https://user-images.githubusercontent.com/23159565/79936336-c2b13200-8489-11ea-920b-7118cbd694db.jpg#center) -->

首先应该阐明的一点是`js`中是没有真正的`继承`的，只是通过`原型`这个东西模拟了继承的效果。

## 原型和原型链

`js`的继承核心在于`对象`。

每个`实例对象`都有一个`私有属性`（[[prototype]]，部分浏览器可通过**proto**访问）指向其构造函数的原型对象（prototype）。所以当访问实例对象自身没有的东西时就会沿着其构造函数的原型对象去找，然后原型对象自身也有[[prototype]]（即**proto**）指向上一层，直到原型对象为 null，这一条长链即是`原型链`。

## 构造函数

上面提到了构造函数，构造函数是面向对象中的概念，是指一个类在实例化时被调用以初始化实例的函数，例如：

```js
var Person = function (name, age) {
  this.name = name;
  this.age = age;
};
Person.prototype.talk = function () {
  console.log("hello");
};
var tom = new Person("tom", 23);
```

`Person`就叫作`构造方法`，用于每次在`new Person`的时候，给返回的`实例`做一些准备工作。这些准备工作就是`Person类`希望其每一个`实例`都拥有的`初始状态`。可以在`实例化`时由传入的参数决定，也可以没有参数，其在内部自己决定初始行为。例如：

```js
function Shape() {
  this.x = 0;
  this.y = 0;
}
var square = new Shape();
```

我们很容易就发现，构造函数和类同名。

没错，因为存在`Person.prototype.constructor === Person`这样的关系（曾经看到这个关系觉得太神奇了，但这其实就是 js 原型模型中的一个固有设计），也就是`Person`本身是没有`构造函数`的，其构造函数只是其`原型`上的一个`constructor `属性，而`constructor `这个属性的作用是用来初始化`new Person`时的行为的，所以需要有一个函数来做具体实现，那么这个函数叫什么合适呢，叫什么都不合适，所以只能叫自己的名字，也就是 Person()

## prototype

原型是什么，原型是对象上的一个名叫`prototype`的属性，可以按照字面意思理解为一个对象的“模子”。因为可以理解`js`一切皆对象，所以`prototype`是很重要的概念。js 中都是对象，而原型又是用于描述对象的基础模子，所以原型有一个重要的也是唯一的标准属性叫作`constructor`，一般这个属性会指向一个函数，称作构造函数。

理论上来说，js 任意一个元素，如果其本质上是对象的话，就会拥有原型（prototype）。

## `__proto__`

ES6 中增加了访问实例对象原型的方法`Object.getPrototypeOf()`（相当于访问**proto**）。虽然方法的名字好像是在获取 prototype 属性，但实际上获取的只是原本的**proto**，其仍然只是指向了构造函数的 prototype 属性。只是官方因为先前不是所有浏览器都统一存在访问实例对象私有属性[[prototype]]（即**proto**）的途径而新增的访问方式。

## 继承方法

严格意义上说`js`是没有`继承方法`的，因为所有的方法都是以函数的形式存在于对象上的属性而已。在继承过程中可能会被任意覆盖，例如：

```js
function Tool() {
  this.name = "tool";
  this.created = function () {
    console.log(new Date());
  };
}
Tool.prototype.func = function () {
  console.log("Tool: I can be used");
};
var tool = new Tool();
console.log("tool's name: " + tool.name);
// tool's name: tool
tool.func();
// Tool: I can be use

function IronTool() {}

IronTool.prototype = Object.create(Tool.prototype);

var hammer = new IronTool();
hammer.func();
// Tool: I can be used
var shaped = function () {
  console.log("shaped: I can be shaped");
};
// 这里就相当于面向对象语言中继承过程中的方法重写了
// 但在js里只相当于修改了 IronTool.prototype 的 func 属性
IronTool.prototype.func = shaped;
IronTool.prototype.func();
// shaped: I can be shaped
```

例子中用到了`Object.create()`，为什么可以如面向对象中的 extends 连接两个类的关系一样建立两个函数的关联呢。

看一下 MDN 中对于 Ojbect.create()实现 js 类式单继承例子

```js
// 父类
function Shape() {
  this.x = 0;
  this.y = 0;
}
// 父类方法
Shape.prototype.move = function (x, y) {
  this.x += x;
  this.y += y;
  console.info("shape moved");
};
// 子类
function Rectangle() {
  // 关键步骤[1]:调用父类的构造方法,相当于super()
  Shape.call(this);
}
// 关键步骤[2]:使用现有的对象 (Shape.prototype) 提供新创建的对象 (Rectangle.prototype) 的 __proto__
// 效果即子类(函数)原型对象的 __proto__ 指向父类(函数)原型对象 (Rectangle.prototype.__proto === Shape.prototype)
// 但是 Object.create() 生成的新对象只有 __proto__，没有 constructor，导致无法被 new 调用
// 即：在没有给 Rectangle 重置 prototype 前，其只是一个普通的函数，没有和 Shape 形成原型链,其 prototype 指向 Object.prototype,结构如下:
// {
//     constructor:function Rectangle() { … }
//     __proto__:Object {constructor: , __defineGetter__: , __defineSetter__: , …}
// }
// 当重置了 Rectangle 的 prototype 属性后,其 prototype 指向 Shape.prototype,结构如下
// {
//     __proto__:Object {move: , constructor: }
// }
// 也就是原本每个函数是默认有自己同名的构造函数在 prototype 上的，但是 Object.create()生成的原型是没有构造函数的，需要自己指定
Rectangle.prototype = Object.create(Shape.prototype);
// 关键步骤[3]:给子类指定构造函数（建立构造函数和自身的绑定关系）
// 在 new Rectangle 时,调用的其实是 Rectangle.prototype 上的 constructor 方法
// 所以如果没有这一步, Rectangle.prototype 是不具备 constructor 属性的
Rectangle.prototype.constructor = Rectangle;

var shape = new Shape();
console.log("shape instanceof Shape: ", shape instanceof Shape);
shape.move(4, 5);

var rectangle = new Rectangle();
console.log("rectangle instanceof Rectangle: ", rectangle instanceof Rectangle);
console.log("rectangle instanceof Shape: ", rectangle instanceof Shape);
rectangle.move(2, 3);

// shape instanceof Shape:  true
// shape moved
// rectangle instanceof Rectangle:  true
// rectangle instanceof Shape:  true
// shape moved
```

代码仓库见: https://github.com/barnett617/codehub/blob/main/front-end/src/2-inheritance/index.md