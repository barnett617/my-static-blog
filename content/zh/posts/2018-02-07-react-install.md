---
title: React教程——安装篇
date: 2018-02-07 14:59:31
lastmod: 2018-02-07 14:59:31
categories: 前端
tags: ["react"]
---

React 官网教程系列之前期准备

<!--more-->

## 初尝 React

在线尝试 React 或在本地开发环境安装 React

### 在线版

如果你对 React 的乐趣在于只是随便玩玩，那你可以使用在线编程环境。在<a href="https://codepen.io/pen?&editors=0010">CodePen</a>或<a href="https://codesandbox.io/s/new">CodeSandbox</a>尝试 Hello World 模板

### 最小 HTML 模板

如果你更想使用你自己的文本编辑器，你也可以用下载<a href="https://raw.githubusercontent.com/reactjs/reactjs.org/master/static/html/single-file-example.html">这个</a>HTML 文件，编辑它并从你本地浏览器中的文件系统打开它。它会有一个缓慢的代码转换，所以不要在生产中使用它。

### 接下来

#### 快速上手

- 前往<a href="https://reactjs.org/docs/hello-world.html">快速上手</a>部分来一步一步按照指引学习 React 的概念

- 尝试<a href="https://reactjs.org/tutorial/tutorial.html">教程</a>作为一个动手实践机会

#### 完整开发环境

如果你初次接触 React 或者仅仅用于实验，上面的轻量解决方案很适合你

当你准备好使用 React 去构建你第一个程序时，查阅下面的安装指引。这些安装被设计用来帮助你并获得良好的开发体验，并且可用于生产。它们包括代码检验（linting）、测试和最佳构建（optimizations built-in)；然而它们需要更多的时间和空间来安装

- 使用 React<a href="https://reactjs.org/docs/add-react-to-a-new-app.html">创建一个新的应用</a>：使用一个包含完整特性的开始工具套件创建一个新的应用

- 将 React<a href="https://reactjs.org/docs/add-react-to-an-existing-app.html">添加进一个已存在的应用</a>：将 React 添加进一个构建系统或一个更大的应用

##### 将 React 添加一个新的应用

在一个新的应用上使用 React 启动的最简单方式就是使用一个开始工具套件（strater kit）

> 这页描述了创建一个你所需要的单页应用，以提供一个舒服的工作流，包含代码检查（linting）、测试和最佳生产以及更多。

> 全特性工具需要一些时间和空间来安装。如果你想要一个轻量环境来用 React 做实验，查看上面的初尝 React 方式，一个单一的 HTML 文件足够开始。

> 最后，如果你不是构建单页应用，你可以将 React<a href="https://reactjs.org/docs/add-react-to-an-existing-app.html">添加进已存在的构建管道</a>或者从<a href="https://reactjs.org/docs/cdn-links.html">CDN</a>使用它而<a href="https://reactjs.org/docs/react-without-jsx.html">无需构建</a>

###### 创建 React 应用

<a href="https://github.com/facebook/create-react-app">Create React App</a>是开始构建一个新的 React 单页应用最好的方式。它建立了你的开发环境，这样你就可以使用到最新的 JavaScript 特性，提供你一个良好的开发体验，为你最佳化你的生产应用。你将需要机器上安装大于版本 6 的 Node

```
npm install -g create-react-app时
create-react-app my-app

cd my-app
npm start
```

如果你安装了 5.2.0 以上版本的 npm，你可能使用 m<a href="https://www.npmjs.com/package/npx">npx</a>代替

```
npx create-react-app my-app

cd my-app
npm start
```

创建 React 应用不会处理后端逻辑和数据库；它只是创建了一个前端构建管道，所以你可以把它用作任何后端。它底层使用比如<a href="http://babeljs.io/">Babel</a>和<a href="https://webpack.js.org/">Webpack</a>的构建工具，但是只需要零配置就能工作。

当你准备好部署你的产品，运行 npm run build 将会在 build 目录为你的应用创建一个最佳构建。你可以通过它的<a href="https://github.com/facebook/create-react-app#create-react-app-">README</a>和<a href="https://github.com/facebook/create-react-app#create-react-app-">用户手册</a>学习更多关于 Create React App

###### 其他启动工具

我们创建了一份官方推荐的<a href="https://reactjs.org/community/starter-kits.html">第三方启动工具列表</a>

它们在各自侧重点有些细微不同，但都是生产就绪的、良好维护的并且不需要任何配置就能开始的。

你也可以查看一个由社区贡献的<a href="https://reactjs.org/community/starter-kits.html#other-starter-kits">其他工具列表</a>

###### 高级使用

如果你倾向于手动配置一个工程，查阅下一部分的<a href="https://reactjs.org/docs/add-react-to-an-existing-app.html#installing-react">安装 React</a>

##### 将 React 添加进一个已存在的应用

你无需重写你的应用以开始使用 React

我们推荐添加 React 进你应用的一块小的部分，比如一个独立的组件，这样你可以在你的用例中看到它是否工作

虽然 React 可以被使用于<a href="https://reactjs.org/docs/react-without-es6.html">没有构建管道的情况</a>，但我们推荐建立构建管道，这样你可以提高生产。一个现代的构建管道通常组成：

- 一个包管理器，比如<a href="https://yarnpkg.com">Yarn</a>或者<a href="https://www.npmjs.com/">npm</a>。这使你能够利用第三方包的浩瀚生态，并轻易地安装或者更新它们

- 一个打包器，比如<a href="https://webpack.js.org/">webpack</a>或者<a href="http://browserify.org/">Browserify</a>。这让你书写模块化的代码并把它们打包到一起成小的包，以使得加载时最优

- 一个编译器，例如<a href="http://babeljs.io/">Babel</a>。它让你书写现代化化 JavaScript 代码，却仍能工作在旧的浏览器

###### 安装 React

> 一旦安装，我们强烈推荐建立一个<a href="https://reactjs.org/docs/optimizing-performance.html#use-the-production-build">生产构建进程</a>以确保你在生产中使用 React 的快速版本

我们推荐使用 Yarn 或者 npm 来管理前端依赖。如果你初次接触包管理器，<a href="https://yarnpkg.com/en/docs/getting-started">Yarn 文档</a>是一个良好开始的地方。

使用 Yarn 安装 React 并运行：

```
yarn init
yarn add react react-dom
```

使用 npm 安装 React 并运行：

```
npm init
npm install --save react react-dom
```

Yarn 和 npm 都是从 npm 注册中心下载

> 为避免潜在的不兼容问题，所有的 react 包应该使用相同的版本。（这包括：react、react-dom、react-test-renderer 等等）

###### 使 ES6 和 JSX 生效

我们推荐使用伴随 Babel 的 React 以使得你使用 ES6 和 JSX 在你的 JavaScript 代码中。ES6 是一个现代 JavaScript 代码的集合，使得开发更简单，JSX 是一个使得 JavaScript 语言和 React 一起良好工作的拓展

<a href="https://babeljs.io/docs/setup/">Babel 安装指引</a>解释了如何配置 Babel 在许多不同的构建环境。确保你安装<a href="http://babeljs.io/docs/plugins/preset-react/#basic-setup-with-the-cli-">babel-preset-react</a>和<a href="http://babeljs.io/docs/plugins/preset-env/">babel-preset-env</a>并确保它们在你的<a href="http://babeljs.io/docs/usage/babelrc/">.babelrc</a>配置中，你就可以良好工作了。

###### 使用 ES6 和 JSX 的 Hello World

我们推荐使用像 webpack 或 Browserify 这样的打包器，这样你可以书写模块化代码并它们打包到一起进小的包裹以在加载时最优化

最小的 React 例子：

```JavaScript
import React from 'react';
import ReactDOM from 'react-dom';

ReactDOM.render(
	<h1>Hello, world!</h1>,
	document.getElementById('root')
);
```

这份代码将 id 为 root 的元素渲染成一个 DOM 元素，所以你需要在你的 HTML 文件的某处有<div id="root"></div>

类似的，你可以将你使用任何其他 JavaScriptUI 库写的已存在的应用中某处的 DOM 元素内渲染一个 React 组件

了解更多关于<a href="https://reactjs.org/docs/integrating-with-other-libraries.html#integrating-with-other-view-libraries">集成 React 和已存在代码</a>

###### 开发和生产版本

默认的，React 包含许多有帮助的警告。这些警告在开发中非常有用。

然而，它使得开发版本变得更大更慢，所以你应该当你部署你的应用时使用生产版本。

学习<a href=""https://reactjs.org/docs/optimizing-performance.html#use-the-production-build>如何辨别你的网站是否正在服务正确版本的React</a>，还有如何最高效地配置生产构建进程：

- <a href="https://reactjs.org/docs/optimizing-performance.html#create-react-app">使用 Create React App 创建一个生产构建</a>

- 使用单文件构建创建一个生产构建

- 使用 Brunch 创建一个生产构建

- 使用 Browserify 创建一个生产构建

- 使用 Rollup 创建一个生产构建

- 使用 webpack 创建一个生产构建

###### 使用 CDN

如果你不想用 npm 管理客户端包，react 和 react-dom 的 npm 包也提供了单文件版本在 umd 目录。详情见<a href="https://reactjs.org/docs/cdn-links.html">CDN 页</a>

##### CDN 链接

React 的 UMD 构建和 ReactDOM 在 CDN 之间可用

```javascript
<script crossorigin src="https://unpkg.com/react@16/umd/react.development.js"></script>
<script crossorigin src="https://unpkg.com/react-dom@16/umd/react-dom.development.js"></script>
```

上面的版本仅对开发而言，对于生产不适用。简化和优化生产版本如下：

```javascript
<script crossorigin src="https://unpkg.com/react@16/umd/react.production.min.js"></script>
<script crossorigin src="https://unpkg.com/react-dom@16/umd/react-dom.production.min.js"></script>
```

加载 react 和 react-dom 一个特定的版本，用版本号替换 16 即可

###### 为什么有 crossorigin 这个属性

如果你从 CDN 提供 React 服务，我们推荐你保持 crossorigin 这个属性：

```JavaScript
<script crossorigin src="..."></script>
```

我们也推荐去确认你正在使用的 CDN 集的 Access-Control-Allow-Origin: \*的 HTTP 头（在 HTTP 相应头包含）。这使得一个更好的<a href="https://reactjs.org/blog/2017/07/26/error-handling-in-react-16.html">错误处理体验</a>在 React16 和以后的版本

参考链接：<a href="https://reactjs.org/docs/try-react.html">https://reactjs.org/docs/try-react.html</a>
