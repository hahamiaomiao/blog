---
title: ES系列主要新特性（待补充）
categories: [学习笔记]
comments: true
---

## ES12 新特性

### 1. String.prototype.replaceAll

在此之前只能使用正则替换，先可以直接使用该方法：replaceAll

```
// prev
'hello, world'.replace(/hello/g, 'hi');

// now
'hello, world'.replaceAll('hello', 'hi');
```

### 2. Promise.any

Promose.any()接收一个 promise 可迭代对象，只要其中的一个 promise 成功，就返回那个已经成功的 promise。

如果可迭代对象中没有一个 promise 成功，即所有的 promise 都失败/拒绝，就返回一个失败的 promise

```
const promise1 = new Promise((resolve, reject) => reject('我是失败的Promise_1'));
const promise2 = new Promise((resolve, reject) => reject('我是失败的Promise_2'));
const promise1 = new Promise((resolve, reject) => resolve('我是成功的Promise_3'));
const promise2 = new Promise((resolve, reject) => resolve('我是成功的Promise_4'));
const promiseList = [promise1, promise2];
const promiseList1 = [promise3, promise4];
const promiseList2 = [[promise1, promise2, promise4, promise3];

Promise.any(promiseList).then(values=>{
  console.log(values);
})
.catch(e=>{
  console.log(e); // AggregateError: All promises were rejected
});

Promise.any(promiseList1).then(values=>{
  console.log(values); // Promise {<fulfilled>: '我是成功的Promise_3'}
})
.catch(e=>{
  console.log(e);
});

Promise.any(promiseList2).then(values=>{
  console.log(values); // Promise {<fulfilled>: '我是成功的Promise_4'}
})
.catch(e=>{
  console.log(e);
});
```

### WeakRefs

使用 WeakRefs 的 Class 类创建对于对象的弱引用（对于对象若饮用是指当该对象应该被 GC 回收时，不会阻止 GC 的回收行为）。

### 运算符（&&=, ||=, ??=）

```
a = 1;
b = 2;
a &&= b; // a = 2
/* 相当于 a && a = b */

a = 1
b = 2
a ??= b; // a = 1
/ *
相当于
if(a == null || a == undefined) {
  a = b
}
*/

```

### 数字分隔符

数字分隔符，可以使用\_分割数字，方便阅读较大的数字

```
// prev syntax before es12
const number = 987654321

// new syntax coming with es12
const number = 987_654_321
console.log(number) // 987654321

// 对国人来说习惯万，亿为单位，可以这样
const number = 1_0000
console.log(number) // 10000
// chrome已支持
```
