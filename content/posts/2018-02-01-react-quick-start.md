---
title: React教程之快速上手篇
date: 2018-02-01 15:32:54
lastmod: 2018-02-01 15:32:54
tags: ["react"]
---

React 官方教程系列之快速上手篇

<!--more-->

## Hello World

React 官网的 Quick Start 提供了一个在线编辑器 CodePen，里面提供了一个 react 的最小配置示例，即

```JavaScript
ReactDOM.render(
  <h1>Hello, World</h1>,
  document.getElementById('root')
);
```

这个 js 文件将会把 html 文件渲染出 Hello，World 这个标题

html 文件结构如下

```html
<div id="root"></div>
```

接下来将探析 React 应用的构建块部分：元素、组件

一旦掌握，便可通过碎小的可复用块创造复杂的应用

## 关于 React 于 JavaScript 的关系

React 是一个 JavaScript 库（library）

官网给出的建议是在学习 React 前确保自己的 JavaScript 有所理解，参照另一篇博文<a href="https://www.h2mes.com/2018/02/01/js%E7%9F%A5%E8%AF%86%E5%B7%A9%E5%9B%BA/">Refresh your JavaScript Knowledge</a>

我们在例子里也用到了一些 ES6 语法。我们试图保守地用它，因为它还近乎崭新，但我们鼓励你熟悉一下箭头函数、类、模板字、let 和 const 语句。你可以使用<a href="https://babeljs.io/repl/#?presets=react&code_lz=MYewdgzgLgBApgGzgWzmWBeGAeAFgRgD4AJRBEAGhgHcQAnBAEwEJsB6AwgbgChRJY_KAEMAlmDh0YWRiGABXVOgB0AczhQAokiVQAQgE8AkowAUPGDADkdECChWeASl4AlOMOBQAIgHkAssp0aIySpogoaFBUQmISdC48QA">Babel REPL</a>来检查 ES6 代码编译成什么样

## JSX 介绍

考虑这个变量声明：

```
const element = <h1>Hello, world!</h1>;
```

这个搞笑的标签语法既不是一个字符串也不是 HTML

它叫做 JSX，并且它是 JavaScript 的一种语法拓展。我们推荐你在 React 使用它来描述 UI 看上去应该的样子。JSX 可能使你想起模板语言，但它完全来自 JavaScript 的强大

JSX 生产 React“元素”。我们将在下一部分探索把它们渲染成 DOM。在下面你会发现 JSX 必备基础来使你开始

### 为什么用 JSX

React 拥抱渲染逻辑本质上是加上其他的 UI 逻辑这样的事实：事件如何处理，状态如何改变，还有数据如何被准备好用作展示

不是通过把标记和逻辑放在不同的文件中这样人为的分离技术，取而代之 React 分离聚焦在两个称作组件的松耦合单元（原文：React separates concerns with loosely coupled units called “components” that contain both.）我们会在后面的部分回到组件，但如果你还对于把标记放进 JavaScript 感到不舒服，<a href="https://www.youtube.com/watch?v=x7cQ3mrcKaY">这段谈话</a>可能会使你信服。

React 并不一定要使用 JSX，但大多数人发现这在他们处理 JavaScript 代码中的 UI 时，JSX 像一个可视化目标一样帮到他们。它也允许 React 去展示更多有用的错误和警告信息

有了这些方法，让我们开始吧！

### 在 JSX 中嵌入表达式

你可以在 JSX 中嵌入任何 JavaScript 表达式，通过包裹在大括号内

例如：2 + 2、user.firstName 和 formatName(user)都是有效的表达式：

```javascript
function formatName(user) {
  return user.firstName + " " + user.lastName;
}

const user = {
  firstName: "Harper",
  lastName: "Perez",
};

const element = <h1>Hello, {formatName(user)}!</h1>;

ReactDOM.render(element, document.getElementById("root"));
```

我们为了可读性把 JSX 拆分成多行。虽然不是必须的，当这样做时，我们还是建议用括号包裹起来以避免<a href="https://stackoverflow.com/questions/2846283/what-are-the-rules-for-javascripts-automatic-semicolon-insertion-asi">自动分号</a>的陷阱

### JSX 也是表达式

在编译之后，JSX 表达式成为常规的 JavaScript 函数调用和评估

这意味着你可以在 if 语句和 for 循环内使用 JSX，把它赋值给变量，接受它作为参数，还有从函数返回它

```javascript
function getGreeting(user) {
  if (user) {
    return <h1>Hello, {formatName(user)}!</h1>;
  }
  return <h1>Hello, Stranger.</h1>;
}
```

### 为 JSX 指定属性

你可能使用引号来指定字符串文字作为属性：

```javascript
const element = <div tabIndex="0"></div>;
```

你也可能使用花括号来在属性内嵌入 JavaScript 表达式

```jJavaScript
const element = <img src={user.avatarUrl}></img>;
```

当向一个属性内嵌入 JavaScript 表达式时，不要在花括号外加引号。你应该使用引号（对字符串值）或花括号（对表达式），但不应该二者都用于属性

> 警告：因为 JSX 比起 HTML 更像 JavaScript，React DOM 使用驼峰属性命名法取代 HTML 属性名称。例如，在 JSX 中，class 变成 className，tabindex 变成 tabIndex

### 为 JSX 指定子

如果一个标签是空的，你可能会立即用一个/>关上它，像 XML：

```javascript
const element = <img src={user.avatarUrl} />;
```

JSX 标签可能包含子：

```jsx
const element = (
  <div>
    <h1>Hello!</h1>
    <h2>Good to see you here.</h2>
  </div>
);
```

### JSX 避免注入攻击

在 JSX 中嵌入用户输入是安全的

```JavaScript
const title = response.potentiallyMaliciousInput;
const element <h1>{title}</h1>;
```

默认的，React DOM 会在渲染之前转义嵌在 JSX 中的值。从而确保你永远不会注入任何写在你程序里不明确的东西。任何东西都会在渲染之前被转换为字符串。这避免了 XSS 攻击

### JSX 表示对象

Babel 编译 JSX 为 React.createElement()调用

下面两个例子完全相同：

```javascript
const element = <h1 className="greeting">Hello, world!</h1>;
```

```javascript
const element = React.createElement(
  "h1",
  { className: "greeting" },
  "Hello world"
);
```

React.createElement()实施了一些检查以帮助你写出无缺陷的代码，但会潜在创造一个如下的对象：

```javascript
const element = {
  type: "h1",
  props: {
    className: "greeting",
    children: "Hello, world",
  },
};
```

这些对象称为 React 元素。你可以把它们当作你想在屏幕上看到的东西的描述。React 读取这些对象并使用它们去构造 DOM 并保持它们为最新的

我们将会在下个部分探索渲染 React 元素为 DOM

> Tip: 我们推荐你为你的编辑器选择使用<a href="http://babeljs.io/docs/editors">Babel 语言定义</a>，这样 ES6 和 JSX 就可以正确地高亮显示。（原文此处使用<a href="https://labs.voronianski.com/oceanic-next-color-scheme/">Oceanic Next</a>颜色主题）

## 渲染元素

元素是 React 应用最小的构建块

元素描述了你想在屏幕上看到的东西：

```javascript
const element = <h1>Hello, world</h1>;
```

不像浏览器 DOM 元素，React 元素是纯文本对象，并且容易创建。React DOM 关心更新 DOM 以匹配 React 元素

> 有人可能会混淆元素一个更广为人知的概念——“组件”。我们将会介绍组件在下一部分。元素是组件的组成部分并且我们鼓励你跳读前阅读组件这个部分

### 把元素渲染为 DOM

让我们假设在你的 HTML 文件中的某个地方有一个<div>

```javascript
<div id="root"></div>
```

我们称其为 DOM“根”节点，因为所以其内的节点都受管于 React DOM

仅用 React 构建的程序经常只有一个唯一的根 DOM 节点。如果你将 React 集成进一个已存在的应用，你可以有尽可能多的孤立的根 DOM

渲染一个 React 元素为一个根 DOM 节点，通过 ReactDOM.render()：

```javascript
const element = <h1>Hello, world</h1>;
ReactDOM.render(element, document.getElementById("root"));
```

### 更新渲染的元素

React 元素是一成不变的。一旦你创建一个元素，你无法改变它的子或者属性。元素就像电影中的一帧：它展现了在以一个特定时间点的 UI

就我们现在所知，更新 UI 的唯一方式是创建一个新的元素，并把它传递给 ReactDOM.render()

考虑一下这个钟表滴答例子：

```jsx
function tick() {
  const element = (
    <div>
      <h1>Hello, world!</h1>
      <h2>
        It is{' '}
        {hew Date().toLocaleTimeString()}.
      </h2>
    </div>
  );
  ReactDOM.render(
    element,
    document.getElementById('root')
  );
}

setInterval(tick, 1000);
```

它从<a href="https://developer.mozilla.org/en-US/docs/Web/API/WindowOrWorkerGlobalScope/setInterval">setInterval()</a>的回调函数每秒调用一次 ReactDOM.render()

> 实际上，大多数 React 应用只调用一次 ReactDOM.render()。在下一个部分我们将学习怎样把这样的代码装进状态组件。我们推荐你不要跳过话题，因为它们依赖于彼此。

### React 只更新必要的东西

React DOM 会比较元素及其子元素与前一个状态的对比，只把必要的 DOM 更新为适用于所需的状态

你可通过使用浏览器工具检查上一个例子来确认

即使我们每一秒创建一个元素来描述整个 UI 树，但只有内容改变的文本节点通过 React DOM 获得更新

在我们的实验中，思考 UI 如何根据给出的时刻而不是随着时间的推移进行改变，消除整个类的错误

## 组件和 props

组件使你将 UI 分离成独立的部分、可重用的碎片并隔离的考虑每一个碎片

概念上讲，组件像是 JavaScript 的函数。它们接收任意的输入（称作 props）并返回 React 元素来描述屏幕上发生了什么

### 功能组件和类组件

定义一个组件最简单的方式就是写一个 JavaScript 函数：

```javascript
function Welcome(props) {
  return <h1>Hello, {props.name}</h1>;
}
```

这个函数是一个有效的 React 组件，因为它接收了一个伴有数据的单一的 props（表示属性）对象参数并返回了一个 React 元素。我们称这样的组件为函数组件，因为它们字面上是 JavaScript 函数

你也可以使用一个 ES6 的类来定义一个组件

```JavaScript
class Welcome extends React.Comoponent {
  render() {
    return <h1>Hello, {this.props.name}</h1>
  }
}
```

以上两个组件从 React 的角度等价

类有一些额外特性我们将在接下来的部分讨论。到那时，我们将因其简明性而使用函数组件

### 渲染一个组件

先前，我们只遇到 React 元素呈现 DOM 标签：

```javascript
const element = <div />;
```

然而，元素也可以呈现用户定义的组件：

```javascript
const element = <Welcome name="Sara" />;
```

当 React 看到一个元素展现一个用户定义的组件时，它作为一个单独的对象传递 JSX 参数给这个组件。我们称这个对象为“props”

比如，这个代码渲染“你好，Sara”在页面上：

```javascript
function Welcome(props) {
  return <h1>Hello, {props.name}</h1>;
}
```

参考链接：

- <a href="https://reactjs.org/docs/">https://reactjs.org/docs/</a>
- <a href="https://codepen.io/pen?&editors=0010">https://codepen.io/pen?&editors=0010</a>
