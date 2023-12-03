---
title: atom实现html实时预览
tags:
  - 其他
date: 2017-10-16 19:00:44
lastmod: 2017-10-16 19:00:44
categories: 其他
---

如何使用 atom 编辑器实现 html 实时预览

<!--more-->

### 背景

atom 自带 markdown 实时预览插件，但当我想用 atom 进行进端开发并取代 webstorm 这样的收费 IDE 时，我发现基于文件进行操作的编辑器 atom 只能高亮显示 html、js 这样的文件，但不能实时显示进行调试，很不方便。

于是上网查，结果都是很简略的方法，作为刚开始上手 atom 的新手，一时不理解，终于经过自己的折腾成功使用，如下

[外链图片转存失败,源站可能有防盗链机制,建议将图片保存下来直接上传(img-zVuzOSpf-1588576020854)(http://ourcia6f4.bkt.clouddn.com/atom-html-preview.png)]

特此记录，以便为新手提供方便，节省这些不必要的查询时间。

####1、 搜索插件

[外链图片转存失败,源站可能有防盗链机制,建议将图片保存下来直接上传(img-yzCW8kwd-1588576020857)(http://ourcia6f4.bkt.clouddn.com/package.png)]

####2、 安装插件

[外链图片转存失败,源站可能有防盗链机制,建议将图片保存下来直接上传(img-iZHbWYFJ-1588576020860)(http://ourcia6f4.bkt.clouddn.com/search.png)]

####3、 修改快捷键

atom-html-preview 初始快捷键为 ctrl+p，于 atom 已有快捷键冲突，修改为 ctrl+F12，如下
点击 File->Settings->KeyBindings->your keymap file 超链接->在末尾添加

```
'atom-text-editor':
　'ctrl-F12':'atom-html-preview:toggle'
```

####4、 完成
