---
title: "基于SSH的员工管理系统（四）——项目流程"
date: 2016-11-02T13:55:11+00:00
tags: ["Java"]
---

```mermaid
flowchat
st=>start: localhost:8080/项目名
e=>end: 访问结束
fir=>: web.xml
op=>operation: 我的操作
cond=>condition: 确认？

st->op->cond
cond(yes)->e
cond(no)->op
```
