---
title: vuex对比pinia
date: 2023-06-28 21:38:46
tags: ["Vue"]
---

vuex@3 对比 pinia@v2

<!--more-->

## 版本信息

| 包名  | 版本号 |
| ----- | ------ |
| vuex  | 3.6.2  |
| pinia | 2.1.3  |

## API 解惑

为什么 mutations 内的对象第一个参数是当前模块的 state，第二个是 payload，并且 mutation 内部的 this 可以访问到 store 实例

因为对于 mutations 的注册实现如下，mutations 是一个数组，每个元素是一个函数，也就是 mutation，当 mutation 被触发时，执行的即是`handler.call(store, local.state, payload);`

这里 call 的第一个参数是执行 handler 的上下文，也就是其内部访问的 this。后面的参数会是 handler 执行时的入参，local.state 对应一个 mutation 方法的第一个参数，payload 是第二个参数

```js
function registerMutation(store, type, handler, local) {
  var entry = store._mutations[type] || (store._mutations[type] = []);
  entry.push(function wrappedMutationHandler(payload) {
    handler.call(store, local.state, payload);
  });
}
```

为什么 actions 内的对象第一个参数可以解构出 dispatch 方法用于调用其他 actions，可以解构出 commit 用于调用 mutation，可以解构出 getters 和 state 用于访问当前模块的状态，可以解构出 rootGetters 和 rootState 用于访问 store 根模块下的 getters 和 state

为什么 action 一定返回 Promise

```js
function registerAction(store, type, handler, local) {
  var entry = store._actions[type] || (store._actions[type] = []);
  entry.push(function wrappedActionHandler(payload) {
    var res = handler.call(
      store,
      {
        dispatch: local.dispatch,
        commit: local.commit,
        getters: local.getters,
        state: local.state,
        rootGetters: store.getters,
        rootState: store.state,
      },
      payload
    );
    if (!isPromise(res)) {
      res = Promise.resolve(res);
    }
    if (store._devtoolHook) {
      return res.catch(function (err) {
        store._devtoolHook.emit("vuex:error", err);
        throw err;
      });
    } else {
      return res;
    }
  });
}
```

如上，actions 一个是数组，而且每个元素是一个方法，方法执行时的上下文是 store 实例，第一个参数是一个对象，里面包括 dispatch、commit、getters、state、rootGetters 和 rootState，可按照需要解构出其中的内容进行调用。如果需要调用其他 action，则使用 dispatch，如果调用 mutation，则使用 commit，如果需要访问当前模块的状态则使用 getters 或 state，如果需要访问根模块的状态则使用 rootGetters 或 rootState

最终 action 的执行结果如果不是 Promise，也会经过 Promise.resolve 来生成一个 Promise 用作返回，保持返回值是 Promise 的一致性
