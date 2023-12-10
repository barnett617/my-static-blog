---
title: "JavaScript的防抖和节流"
date: 2023-06-11 00:42:00
summary: "关于JavaScript防抖节流的学习"
tags: ["javascript"]
---

> 本文为历史博客迁移

## 防抖和节流

### 开门见山（直观图解）

![71862515-1a26ce80-3135-11ea-8e2e-01203e17379a](https://user-images.githubusercontent.com/23159565/80471372-e38bf280-8975-11ea-96c8-28179086507f.gif)

### 本质解读

首先要弄清楚什么是防抖节流，这二者本质上都是性能优化的一种手段。

`防抖`字面意思可解读为`防止抖动`，是要防止什么的抖动呢，一般是事件响应，最常见的就是输入框`input`事件的监听。如果不加防抖会怎么样，你给一个输入框绑定了`input`事件监听，则你在输入的整个过程，都在频繁触发`input`回调。如果回调中存在复杂的`dom`操作或是网络请求，都会导致很大的资源浪费或造成页面震动，所以才有了`防抖`这种性能优化策略。

### 防抖

具体防抖策略是如何进行性能优化的，分两种：

1. 当发生被判定为抖动的操作后，只响应抖动结束前的最后一次操作。
2. 立即处理某种操作，但如果在某个时间区间内再次出现相同操作被判定为抖动而不进行响应。

分别举例：

1. 在谷歌输入框输入内容，在输入的同时对已输入的内容做联想回显，试想这样一个事情，用户要输入一个词语或句子进行搜索，用户的输入动作理应是连续的，如果中间有停顿，说明用户在思考要怎么描述要搜索的内容，所以这个时候才是联想提示的最佳时机。那么怎么样我们认为是可以进行联想的时机呢，也就是输入停顿有个时间判定，这里我们为了效果明显以 2s 内没有继续输入判定为停顿。

我们先看一下不加防抖的效果：

![normal](https://user-images.githubusercontent.com/23159565/80454372-859ee100-895c-11ea-896e-4d9c3f769061.gif)

代码如下

```html
<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="UTF-8" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0" />
    <title>Document</title>
  </head>
  <body>
    <input id="input" type="text" />
    <span id="content"></span>
  </body>
  <script src="1-normal.js"></script>
</html>
```

```js
function inputListener() {
  var inputDom = document.getElementById("input");
  inputDom.addEventListener("input", handleInput);
}

function handleInput(e) {
  var spanDom = document.getElementById("content");
  spanDom.innerHTML = e.target.value;
  autoTip(e.target.value);
}

function autoTip(content) {
  // 这里可能有网络请求/接口请求/DOM操作等操作
  console.log("自动联想");
}

inputListener();
```

每下输入都做响应是很糟糕的，所以我们需要防抖，策略如下：

持续监听键盘输入事件，也就是每次输入都会触发`input`事件，判定`2s`内发生了 1 次以上的事件响应暂不做处理，直到`2s`都没再有`input`事件被触发，则进行联想。代码如下：

```html
<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="UTF-8" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0" />
    <title>Document</title>
  </head>
  <body>
    <input id="input" type="text" />
    <span id="content"></span>
  </body>
  <script src="2-debounce.js"></script>
</html>
```

```js
function inputListener() {
  var inputDom = document.getElementById("input");
  inputDom.addEventListener("input", handleInput);
}

function handleInput(e) {
  var spanDom = document.getElementById("content");
  spanDom.innerHTML = e.target.value;
  debounced(e.target.value);
}

function autoTip(content) {
  // 这里可能有网络请求/接口请求/DOM操作等操作
  console.log("对" + content + "进行自动联想");
}

// 对复杂函数做防抖策略（可以理解为装饰）
var debounced = debounce(autoTip, 2000);

function debounce(fn, interval) {
  var timer;
  // 返回添加防抖后的函数
  return function (args) {
    // 每次触发都会清除掉现有的函数执行定时器，直到没有再次触发，等待定时器到时间后执行函数
    clearTimeout(timer);
    timer = setTimeout(() => {
      fn(args);
    }, interval);
  };
}

inputListener();
```

效果展示：

![debounce2](https://user-images.githubusercontent.com/23159565/80454146-2b058500-895c-11ea-880a-c8cb92c2e0b0.gif)

### 节流

`节流`字面意思可理解为`节制流量`，目的在于控制资源地索取，比如用户一秒十按你的提交按钮，但目的只在于提交表单，那么有没有必要真的一秒内去做十次请求呢，实际上是没必要的。当然如果是这种场景，我们一般的处理可能是，当用户第一次点击提交按钮后，发起请求的同时置灰按钮不可点并按钮显示处理中的动态状，那么后面的点击都会被阻拦，而不进行真正的请求，当上一次请求得到明确的结果后再放开按钮的可点击性。那我们要节流干嘛呢，先看不加节流的效果：

![throttle-normal](https://user-images.githubusercontent.com/23159565/80464532-863f7380-896c-11ea-8fac-1d74b0f486b5.gif)

```html
<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="UTF-8" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0" />
    <title>Document</title>
  </head>
  <body>
    <button id="btn">提交</button>
    <span>你点击了</span>
    <span id="count"></span>
    <span>次</span>
  </body>
  <script src="7-throttle-normal.js"></script>
</html>
```

```js
var count = 0;
var btn = document.getElementById("btn");
document.getElementById("count").innerHTML = count;
btn.addEventListener("click", handleClick);
function handleClick() {
  // 这里可能进行网络请求或DOM操作
  count++;
  console.log("点击按钮");
  document.getElementById("count").innerHTML = count;
}
```

为什么要节流呢，比如页面上放着一个按钮，这个按钮的功能是请求后台的接口，操作十分复杂，如果前端不加以限制，用户的点击是不可控的，可以疯狂点击，那么后台堆积的请求将会短时骤增，这样的情况是应进行控制的，而优化的手段就是进行节流。别管你的操作有多快，我要节制访问流量，那我就可以限制对你的操作响应为时间周期性地反馈，比如不管你 1 秒内能点击多少下，我都只会处理一次，这就达到了节流的定义。效果如下：

![throttle](https://user-images.githubusercontent.com/23159565/80470324-6dd35700-8974-11ea-8d79-d11ad69a2bd5.gif)

```html
<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="UTF-8" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0" />
    <title>Document</title>
  </head>
  <body>
    <button id="btn">提交</button>
    <span>你点击了</span>
    <span id="count"></span>
    <span>次</span>
  </body>
  <script src="8-throttle.js"></script>
</html>
```

```js
var count = 0;
document.getElementById("count").innerHTML = count;

var btn = document.getElementById("btn");

var throttled = throttle(handleClick, 2000);
btn.addEventListener("click", throttled);

function handleClick() {
  // 这里可能进行网络请求或DOM操作
  count++;
  console.log(
    new Date().getMinutes() + "分" + new Date().getSeconds() + "秒执行点击处理"
  );
  document.getElementById("count").innerHTML = count;
}

// 1s内点击3次，应该对第一次点击进行响应
function throttle(fn, interval) {
  var timer;
  var last;
  var lock = false;
  return function () {
    console.log(
      new Date().getMinutes() + "分" + new Date().getSeconds() + "秒发生点击"
    );
    if (lock) return;
    lock = true;
    setTimeout(() => {
      fn();
      lock = false;
    }, interval);
  };
}
```

但如果是用户对操作实时性要求高的，不能对节流周期中的最后一次操作响应，而应是第一次操作就响应，因为分秒必争。所以改进为锁的开放放到定时器内，但响应函数要移出，如下：

```html
<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="UTF-8" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0" />
    <title>Document</title>
  </head>
  <body>
    <button id="btn">提交</button>
    <span>你点击了</span>
    <span id="count"></span>
    <span>次</span>
  </body>
  <script src="9-throttle-optimize.js"></script>
</html>
```

```js
var count = 0;
document.getElementById("count").innerHTML = count;

var btn = document.getElementById("btn");

var throttled = throttle(handleClick, 2000);
btn.addEventListener("click", throttled);

function handleClick() {
  // 这里可能进行网络请求或DOM操作
  count++;
  console.log(
    new Date().getMinutes() + "分" + new Date().getSeconds() + "秒执行点击处理"
  );
  document.getElementById("count").innerHTML = count;
}

// 1s内点击3次，应该对第一次点击进行响应
function throttle(fn, interval) {
  var timer;
  var last;
  var lock = false;
  return function () {
    console.log(
      new Date().getMinutes() + "分" + new Date().getSeconds() + "秒发生点击"
    );
    if (lock) return;
    lock = true;
    fn();
    // 在限制时间延迟后释放锁，允许再次响应
    setTimeout(() => {
      lock = false;
    }, interval);
  };
}
```

![throttle-optimize2](https://user-images.githubusercontent.com/23159565/80470884-31542b00-8975-11ea-83e9-14f28dc8a745.gif)

代码仓库见: https://github.com/barnett617/codehub/blob/main/front-end/src/7-debouce-throttle/index.md
