---
title: python零碎知识点整理——注释
tags:
  - python
date: 2017-02-07 22:05:20
lastmod: 2017-02-07 22:05:20
categories: 笔记
---

python 零碎知识点整理——注释

<!--more-->

## 单行注释

> python 的单行注释用#，可在任意位置添加（单独一行或语句/表达式后面，python 是解释型语言，逐行按序解释代码）

## 多行注释

> 多行注释使用三个单引号或双引号
> 这实际上是**多行字符串**的书写方式，并非 python 本身提倡的多行注释

## 编码声明注释

> 出现在 python 脚本第一行或第二行（其他行则无效）的含有 coding:xxx 的注释被认为是对编码方式的声明，详见[python 官方文档](https://docs.python.org/3/reference/lexical_analysis.html#encoding-declarations)
>
> 从 python3 开始，python 默认使用 utf-8 编码（python3 以前使用 ascii 编码）

## 平台注释

> 使 python 程序运行在 windows 平台上，需要在 python 文件的最前面加上#!/usr/bin/python，这说明了程序用的环境的路径
