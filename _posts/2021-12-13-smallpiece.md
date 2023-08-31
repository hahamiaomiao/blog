---
title: 零碎知识点
categories: [随笔]
comments: true
---

## 1. ??

使用??操作符 ｜ 使用其他操作符

undefined??true // true ｜ undefined || true // true

undefined??false // false ｜ undefined || false // false

null??false // false ｜ null || false // false

null??true // true ｜ null || false // false

false??true // false ｜ false || true // true

false??'' // false ｜ false || "" // ""

""??true // "" ｜ "" || true // true

""??false // "" ｜ "" || false // false

""??null // "" ｜ "" || null // null

## 2. 0.1 + 0.2 === 0.3 // false

在 js 当中，Number 类型实际上是 double 类型，运算小数时存在精度问题。因为计算机只认识二进制，在进行运算时，需要将其他进制的数值转换成二进制，然后再进行计算，小数用二进制表达时是无穷的。

```
// 将0.1转换成二进制
console.log(0.1.toString(2)); // 0.0001100110011001100110011001100110011001100110011001101

// 将0.2转换成二进制
console.log(0.2.toString(2)); // 0.001100110011001100110011001100110011001100110011001101

```

双精度浮点数的小数部分最多支持 53 位二进制位，所以两者相加后，因浮点数小数位的限制而截断的二进制数字，在转换成十进制，就成了 0.30000000000000004，这样在进行算术计算时会产生误差。

ES6 在 Number 对象上面，新增一个极小的常量 Number.EPSILON。根据规格，它表示 1 与大于 1 的最小浮点数之间的差。对于 64 位浮点数来说，大于 1 的最小浮点数相当于二进制的 1.00..001，小数点后面有连续 51 个 0。这个值减去 1 之后，就等于 2 的-52 次方。

```
Number.EPSILON === Math.pow(2, -52)
// true
Number.EPSILON
// 2.220446049250313e-16
Number.EPSILON.toFixed(20)
// "0.00000000000000022204"
```

Number.EPSILON 实际上是 JavaScript 能够表示的最小精度。误差如果小于这个值，就可以认为已经没有意义了，即不存在误差了。

引入一个这么小的量的目的，在于为浮点数计算，设置一个误差范围。比如，误差范围设为 2 的-50 次方（即 Number.EPSILON \* Math.pow(2, 2)），即如果两个浮点数的差小于这个值，我们就认为这两个浮点数相等。

## 设计模式

设计模式是拿来主义在软件领域的贯彻实践。

### SOLID 设计原则

设计原则是设计模式的指导理论，它可以帮助我们规避不良的软件设计。

- 单一功能原则(Single Responsibility Principle)

- 开放封闭原则(Opened Closed Principle)

- 里式替换原则(LisKov Substitution Principle)

- 接口隔离原则(Interface Segregation Principle)

- 依赖反转原则(Dependency Inversion Principle)

## 构建型

### 工厂模式

工厂模式就是将创建对象的过程单独封装。将创建对象的过程单独封装，这样的操作就是工厂模式。

简单工厂

场景： 有构造函数的地方，我们就应该想到简单工厂；在写了大量构造函数、调用了大量的 new,我们就应该思考是不是可以掏出工厂模式重构我们的代码了。

抽象工厂

开放封闭原则： 对拓展开放，对修改封闭。软件实体（类、模块、函数）可以扩展，但是不可修改。

共同点：
在于都尝试去分离一个系统中变与不变的部分。
不同点：
场景的复杂度

抽象工厂本质上处理的其实也是类，但是是一帮非常棘手、复杂的类，这些类中不仅能划分门派，还能划分出等级，同时存在着千变万化的扩展可能性 - 这使得我们必须对共性作更特别的处理、使用抽象类去降低扩展的成本，同时需要对类的性质作划分，于是有这样的四个关键角色：

- 抽象工厂（抽象类，它不能被用于生成具体实例）：用于声明最终目标产品的共性。在一个系统里，抽象工厂可以有多个，每个抽象工厂对应的这一类的产品，被称为“产品族”

- 具体工厂（用于生成产品族里的一个具体产品）：继承自抽象工厂，实现了抽象工厂里声明的那些方法，用于创建具体的产品的类

- 抽象产品（抽象类，他不能被用于生成具体实例）：具体工厂里实现的接口，会依赖一些类，这些类对应到各种各样的具体的细粒度产品，这些具体产品类的共性各自抽离，便对应到了各自的抽象产品类。

- 具体产品（用于生成产品族里的一个具体的产品所依赖的更细粒度的产品）

抽象工厂模式的定义，是围绕一个超级工厂创建其他工厂。

### 单例模式

不管我们尝试去创建多少次，它凑只给你返回第一次所创建的那唯一的一个实例。

构造函数具备判断自己是否已经创建过一个实例的能力

```
class SingleDog {
  show() {
    console.log('我是一个单例对象')
  }
  static getInstance() {
    if(!SingleDog.instance) {
      SingleDog.instance = new SingleDog()
    }
    return SingleDog.instance
  }
}

const s1 = SingleDog.getInstance()
const s2 = SingleDog.getInstance()

s1 === s2 // true
```

闭包实现

```
SingleDog.getInstance = (function() {
  let instance = null
  return function() {
    if(!instance) {
      instance = new SingleDog()
    }
    return instance
  }
})()
```

### 原型模式

### 装饰器模式

装饰器模式，又名装饰者模式。它的定义是“在不改变原对象的基础上，通过对其进行包装拓展，使原有对象可以满足用户的更复杂需求”。
