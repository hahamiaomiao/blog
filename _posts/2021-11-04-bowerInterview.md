---
title: 浏览器面试题
categories: [学习笔记]
comments: true
---

## 1. 常见浏览器内核

浏览器内核可分为两部分：渲染引擎和 JS 引擎

由于 JS 引擎越来越独立，内核就指的是渲染引擎了，渲染引擎主要用来请求网络页面资源解析排版后呈现给用户。

浏览器/Runtime - 内核 - JS 引擎

Chrome - Blink/webkit - V8

FireFox - Gecko - SpiderMonkey

Safari - Webkit - JavaScriptCore

Edge - EdgeHTML - Chakra(For JavaScript)

IE - Trident - Chakra(For JScript)

Opera - Presto -> blink - Linear A（4.0-6.1）/ Linear B（7.0-9.2）/ Futhark（9.5-10.2）/ Carakan（10.5-）

Node.js - none - V8
