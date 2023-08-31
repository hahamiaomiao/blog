---
title: 简单代码
categories: [学习笔记]
comments: true
---

## 1.获取字符串中的字符数

```
const characterCount = (str, char) => str.split(char).length - 1
```

## 2.检查对象是否为空

```
const isEmptyObj = obj => Reflect.ownKeys(obj).length === 0 && obj.constructor === Object
```

## 3.等待一段时间再执行

```
const wait = async (waitseconds) => new Promise((resolve) => setTimeout(resolve, waitseconds))
```

## 4.获取两个日期之间的日差

```
const daysBetween = (date1, date2) => Math.ceil(Math.abs(date1 - date2) / 1000 * 60 * 60 * 24)
```

备注：Math 常用对象方法

- abs(x) 绝对值
- ceil(x) 向上舍入为最接近的整数
- floor(x) 向下舍入为最接近的整数
- max(x,y,...,n) 值最高的数字
- min(x,y,...,n) 值最低的数字
- pow(x, y) x 的 y 次幂
- random() 0 到 1 之间的随机数
- round(x) 舍入为最接近的整数
- sqrt(x) 平方根

## 5.重定向到另一个 URL

```
const redirect = url => location.href = url
```

## 6.检查设备上的触摸支持

```
const touchSupported = () => ('ontouchstart' in window || DocumentTouch && document instanceof DocumentTouch)
```

## 7.在元素后插入 HTML 字符串

```
const insertHTMLAfter = (html, el) => el.insertAdjacentHTML('afterend', html)
```

## 8.随机排列数组

```
const shuffle = arr.sort(() => 0.5 - Math.random())
```

## 9.在网页上获取选定的文本

```
const getSelectedText = () => window.getSelection().toString()
```

## 10.获取随机布尔值

```
const getRandomBoolean = () => Math.random() >= 0.5
```

## 11.计算数组的平均值

```
const arrAverage = (arr) => arr.reduce((a, b) => a + b) / arr.length
```
