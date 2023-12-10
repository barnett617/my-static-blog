---
title: webpack学习笔记
date: 2018-01-17 11:41:10
lastmod: 2018-01-17 11:41:10
tags: ["Webpack"]
---

关于 Webpack 基础使用的整理

<!-- more -->

## 背景

- 网站由网页模式进化成 Webapp 模式

- 网站运行在高级浏览器中，使用 HTML5、CSS3、ES6 等新技术

- webapp 通常是单页面应用（每一个视图通过异步方式加载，导致页面初始化和使用过程会加载更多的 js 代码）

- 前端开发基于多语言、多层次编码和组织工作，交付基于浏览器，需要保证代码和资源在浏览器端快速优雅的加载和更新，亟需**模块化系统**

## 传统方式

```
<script src="module1.js"></scrpti>
<script src="module2.js"></scrpti>
<script src="module3.js"></scrpti>
...
```

### 弊端

- 全局作用域（定义在 window 对象下）下易造成变量冲突

- 文件只能按照脚本引入的顺序加载

- 需要主观解决模块和代码库的依赖关系

- 大型项目中资源难以管理，长期积累导致代码库混乱不堪

## CommonJS

服务端的 Node.js 遵循 CommonJS 规范

### 核心思想

1. 允许模块通过 require 方法来**同步加载**要依赖的其他模块
2. 通过 exports 或 module.exports 导出需要暴露的接口

```
require("module");
require("../file.js");
exports.doStuff = function() {};
module.exports = someValue;
```

### 优势

- 服务端模块便于重用
- NPM 中已有大量可用模块包（20w）
- 简单易用

### 缺陷

- 同步的模块加载方式不适合在浏览器环境中，同步意味着阻塞加载，浏览器资源是异步加载的
- 不能非阻塞的并行加载多个模块

## ES6 模块

ES6 标准增加了 js 语言层面的模块体系定义

ES6 的设计思想是尽量的**静态化**，使得编译时就能确定模块的依赖关系，以及输入和输出的变量。

### 优势

- 易于进行静态分析
- 面向未来 ES 标准

### 缺陷

- 原生浏览器未实现该标准
- 全新命令字，新版 Node.js 才支持

## 静态分析

编译时对整个代码进行静态分析，分析出各模块的**类型**和**依赖关系**，将不同类型的模块交由相应的加载器处理。

## Webpack 概念

Webpack 是一个模块打包器。

根据模块的依赖关系进行静态分析，然后将模块按照指定的规则生成对应的静态资源

## 现有模块化工具无法实现

- 将依赖树拆分成按需加载的块
- 初始化加载的耗时尽量少
- 各种静态资源都可视作模块
- 将第三方库整合成模块
- 自定义打包逻辑
- 适合无论单页或多页的 Web 应用大项目

## Webpack 特点

- 代码拆分
- Loader
- 智能解析
- 插件系统
- 快速运行

### 代码拆分

- 两种组织模块依赖的方式：同步、异步、
- 异步依赖作为分割点，形成新的块
- 优化了依赖树后，每一个异步区块都作为一个文件被打包

### Loader

- Webpack 本身只能处理原生 js 模块
- loader 转换器可将各种类型的资源转换成 js 模块

### 智能解析

Webpack 有一个智能解析器，几乎可以处理任何第三方库

无论模块形式是 CommonJS 或是普通 js 文件

加载依赖时甚至允许动态表达式

```
require("./templates/" + name + ".jade")
```

### 插件系统

Webpack 有一个功能丰富的插件系统

可开发和使用开源插件满足各式需求

### 快速运行

Webpack 使用异步 I/O 和多级缓存提高运行效率

## 安装

Webpack 需要 Node.js v0.6 以上支持

使用 npm 全局安装

```
npm install webpack -g
```

将 Webpack 安装到项目的依赖中

```
npm install webpack --save-dev
```

## 使用

index.html

```html
<html>
<head>
  <meta charset="utf-8">
</head>
<body>
  <scritp src="bundle.js"></script>
</body>
</html>
```

entry.js

```
document.write('Hello world')
```

编译 entry.js 并打包到 bundle.js

```
webpack entry.js bundle.js
```

访问 index.html 即可

### 添加模块

module.js

```
module.exports = 'It works from module.js.'
```

entry.js

```
document.write('Hello world')
document.write(require('./module.js'))
```

打包

```
webpack entry.js bundle.js
```

### 分析

webpack 分析入口文件，解析包含依赖关系的各个文件

这些文件（模块）都打包到 bundle.js

webpack 给每个模块分配一个唯一 ID 并通过 ID 索引和访问模块

页面启动时先执行 entry.js，其他模块在运行 require 时再执行

## loader

### 背景

webpack 本身只能处理 js 模块，其他类型文件需要 loader 进行转换

loader 本身一个函数，接受源文件为参数，返回转换结果

### loader 特性

- 可通过管道方式链式调用，每个 loader 可将资源转换成任意格式并传递给下一个 loader，最后一个 loader 必须返回 js
- 可同步或异步执行
- 运行在 node.js 环境中，可做任何可能的事情
- 可接受参数，以此传递配置给 loader
- 可通过文件拓展名（或正则表达式）绑定给不同类型的文件
- 可通过 npm 发布和安装
- 除了通过 package.json 和 main 指定，通常的模块也可导出一个 loader 来使用
- 可访问配置
- 支持插件
- 可分发出附件的任意文件

### loader 命名

一般为 xxx-loader，xxx 为转换的功能，例如 json-loader

引用 loader 时可通过全名（json-loader）或短名(json)

命名规则和搜索优先级顺序在 resolveLoader.moduleTemplates API 中定义

```
Default: ["*-webpack-loader", "*-web-loader", "*-loader", "*"]
```

### 添加方式

- 在 require()引用模块时添加
- webpack 全局配置中绑定
- 命令行方式

### 场景

在页面引入一个 CSS 文件 style.css

```
body { background: yellow; }
```

entry.js

```js
// 载入style.css
require("!style-loader!css-loader!./style.css")
document.write('Hello world.')
document.write(require('./module.js'))
```

### 处理

将 style.css 看作一个模块

用 css-loader 读取

用 style-loader 把它插入页面

### 安装 loader

```
npm install css-loader style-loader
```

即可

### 改进

根据模块类型（拓展名）自动绑定需要的 loader，避免每次 require CSS 文件时都写 loader 前缀

entry.js

```
require("!style!css!./style.css")
```

修改为

```
require("./style.css")
```

### 打包

```
webpack entry.js bundle.js --module-bind "css=style-loader!css-loader"
```

Bingo! 效果一样

只不过是在打包环节根据模块类型绑定需要的 loader，不需要在 require 中写 loader 前缀

## 配置文件

上面通过在命令行在执行 webpack 时传入参数，可通过指定配置文件来执行

默认搜索当前目录 webpack.config.js

该文件是一个 node.js 模块，返回一个 json 格式的配置信息对象

可通过--config 选项指定配置文件

### 具体操作

创建 package.json 添加 webpack 需要的依赖

```json
{
  "name": "my-webpack",
  "version": "1.0.0",
  "description": "A simple webpack example",
  "main": "bundle.js",
  "scripts": {
    "test": "echo \"Error: no test specified\" && exit 1"
  },
  "keywords": [
    "webpack"
  ],
  "author": "igam",
  "license": "MIT",
  "devDependencies": {
    "css-loader": "^0.21.0",
    "style-loader": "^0.13.0",
    "webpack": "^1.12.2"
  }
}
```

根据 package.json 下载依赖模块

```
npm install
```

创建配置文件 webpack.config.js

```js
var webpack = require('webpack')

module.exports = {
  entry: './entry.js',
  output: {
    path: __dirname,
    filename: 'bundle.js'
  },
  module: {
    loaders: [
      {test: /\.css$/, loader: 'style-loader!css-loader'}
    ]
  }
}
```

Bingo again!

## 插件

一般在配置文件 plugins 选项中指定

webpack 内置一些常用插件，还可通过 npm 安装第三方插件

### 实例

内置插件 BannerPlugin：给输出的文件头部添加注释

webpack.config.js

```js
var webpack = require('webpack')

module.exports = {
  entry: './entry.js',
  output: {
    path: __dirname,
    filename: 'bundle.js'
  },
  module: {
    loaders: [
      {test: /\.css$/, loader: 'style-loader!css-loader'}
    ]
  },
  plugins: [
    new webpack.BannerPlugin('This file is created by igam')
  ]
}
```

在 bundle.js 文件头可看见效果

## 开发环境

编译时间长，可以通过参数让编译输出内容带有**进度**和**颜色**

```
webpack --progress --colors
```

若不想每次修改模块后重新编译，可启动**监听模式**

开启后，没有变化的模块会在编译后缓存到内存中，而非每次重新编译

```
webpack --progress --colors --watch
```

### 更好选择

使用 webpack-dev-server 开发服务

将在 localhost:8080 启动一个 express 静态资源服务器

以监听模式自动运行 webpack

访问 http://localhost:8080/或 http://localhost:8080/webpack-dev-server/可浏览项目中的页面和编译后的资源输出，并通过一个 socket.io 服务实时监听变化并自动刷新

### 安装

```
npm install webpack-dev-server -g
```

### 运行

```
webpack -dev-server --progress --colors
```

## 故障处理

可通过参数--display-error-deatils 打印错误详情

webpack 的配置提供了 resovle 和 resolveLoader 参数设置模块解析的处理细节

resolve 配置应用层模块（被打包的模块）解析

resolveLoader 配置 loader 模块的解析

> 当引入通过 npm 安装的 node.js 模块时，可能出现找不到依赖的错误

Node.js 模块的依赖解析算法是通过查看模块的每一层父目录中的 node-modules 文件夹来查询依赖

当出现 Node.js 模块依赖查找失败时可尝试设置 resolve.fallback 和 resolveLoader.fallback 解决问题

```js
module.exports = {
  resolve: { fallback: path.join(__dirname, "node_modules") },
  resolveLoader: { fallback: path.join(__dirname, "node_modules") }
}
```

webpack 中设计路径配置最好使用绝对路径，建议使用 path.resolve(**dirname, "app/folder")或 path.join(**dirname, "app", "folder")方式配置，以兼容 Windows 环境

参考： [http://zhaoda.net/webpack-handbook/index.html](http://zhaoda.net/webpack-handbook/index.html)
