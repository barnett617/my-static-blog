---
title: "JavaScript的Promise学习"
date: 2023-06-11 00:42:00
summary: "关于JavaScript Promise原理的学习"
tags: ["javascript"]
---

> 本文为历史博客迁移

首先我们应该承认，`promise`的确是一个大话题，因为毕竟是 es6 才出现的东西，融合了很多 js 既有特性，比如【函数作为参数传入另外的函数】、【使用 bind 强制绑定 this】、【使用原型对象 prototype 添加方法】等。

再到`promise`的应用层面，需要了解【promise 的实现原理】、【如何实现 A+标准的 promise】以及【promise 与其他异步方式混用】的变态面试题等等，我们能挖到几层算几层

首先，同`js`中大多成员一样，`promise`也是一个`对象`，是一个函数返回的对象。

那么`promise`是怎么得来的呢，是由`Promise`构造函数生成的。

为什么要有`promise`这东西呢，是为了处理`异步`行为。

如果你有一个异步操作，可以把它放到`Promise`构造器里来获得一个`promise实例`，拿着这个实例你就相当于拿到了一份“承诺”，当异步事务处理结束后，你手中的 promise 实例便有了结果，而且这个结果是确切的、不会变的，要么成功，要么失败，你可以根据拿到的结果做后续操作。

既然 Promise 本质上是对象，那我们看看它的属性和方法分别有什么。

## Promise 对象的属性和方法

### 属性

- length（永远为 1）
- prototype

### 方法

- all
- race
- reject
- resolve

## Promise 的原型

### 属性

- constructor

### 方法

- catch
- then
- finally

## 创建 Promise

创建 Promise 需要给构造函数传一个执行器，执行器中需要包括两个回调函数，一个是当异步操作成功后进行的回调，另一个是失败时接收的回调。

```js
var promise = new Promise((resolve, reject) => {
  var result;
  // result = 异步操作
  if (result === "success") {
    resolve();
  } else {
    reject();
  }
});

var executor = function (resolve, reject) {
  var result;
  // result = 异步操作
  if (result === "success") {
    resolve();
  } else {
    reject();
  }
};
var promise2 = new Promise(executor);
```

### 实际应用

```js
var promise = new Promise((resolve, reject) => {
  try {
    setTimeout(() => {
      console.log("done");
      var endTime = new Date();
      resolve(endTime);
    }, 0);
  } catch (e) {
    reject(e);
  }
});
promise.then(
  (data) => {
    console.log("success: " + data);
  },
  (err) => {
    console.log("fail: " + err);
  }
);

// done
// success: Sat Apr 25 2020 11:26:10 GMT+0800 (GMT+08:00)
```

```js
var executor = function (resolve, reject) {
  try {
    setTimeout(() => {
      console.log("done");
      var endTime = new Date();
      resolve(endTime);
    }, 0);
    throw new Error("oop");
  } catch (e) {
    reject(e);
  }
};
var promise = new Promise(executor);
promise.then(
  (data) => {
    console.log("success: " + data);
  },
  (err) => {
    console.log("fail: " + err);
  }
);

// fail: Error: oop
// done
```

### 链式调用

```js
// 这就叫链式调用(统一收敛异常捕获)
doSomething()
  .then((data) => {
    return doSomethingElse(data);
  })
  .then((data) => {
    return doThirdThing(data);
  })
  .then((data) => {
    console.log("All well done: " + data);
  })
  .catch(failureCallback);
function doSomething() {
  return new Promise((callback, error) => {
    try {
      setTimeout(() => {
        console.log("something done");
        callback("a");
      }, 0);
    } catch (e) {
      error(e);
    }
  });
}
function doSomethingElse(result) {
  console.log(result);
  return new Promise((callback, error) => {
    try {
      setTimeout(() => {
        console.log("something else done");
        callback("b");
      }, 0);
    } catch (e) {
      error(e);
    }
  });
}
function doThirdThing(result) {
  console.log(result);
  return new Promise((callback, error) => {
    try {
      setTimeout(() => {
        console.log("third thing done");
        callback("c");
      }, 0);
    } catch (e) {
      error(e);
    }
  });
}
function failureCallback(e) {
  console.log("error: " + e);
}
// something done
// a
// something else done
// b
// third thing done
// All well done: c
```

代码仓库见: https://github.com/barnett617/codehub/blob/main/front-end/src/5-promise/index.md
