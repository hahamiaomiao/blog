---
title: ES系列主要新特性（待补充）
categories: [学习笔记]
comments: true
---

雅迪电动车解除限制时速的方法：
1、首先转动钥匙关闭车子电源，然后将车子的车架撑起来，不要染后轮着地；
2、将油门转把转到底，然后捏紧刹车，再把转动钥匙打开电源，等待 10 秒钟左右再同时松开油门转把和刹车即可，此时电动车也就解除限速了。

## ES13 新特性

### 1. 类成员声明

在之前，我们只能在构造函数里面声明类的成员，而不能像其他大多数语音一样在类的最外层作用域里面声明成员

```
class Car {
  constructor() {
    this.color = 'blue';
    this.year = '2022';
  }
}

const car = new Car();
console.log(car.color); // blue
console.log(car.year); // 2022
```

这么写起来不是很方便，ES13 出来后，可以突破限制，写下如下代码

```
class Car {
  color = 'blue';
  year = '2022';
}

const car = new Car();
console.log(car.color); // blue
console.log(car.year); // 2022
```

### 2. 给类定义私有方法和成员变量

在此之前，是不可能给类定义私有成员的。所以为了表示某个成员变量是私有属性，会给该属性名增加一个下划线作为后缀。可是这只能作为程序员之间的君子之约来使用，因为这些变量其实还是可以被外界访问到。

```
class Person {
  _firstName = 'Jone';
  _lastName = 'Stevens';

  get name() {
    return `${this._firstName} ${this._lastName}`;
  }
}

const person = new Person();
console.log(person.name); // Jone Stevens

// 这些所谓的私有属性其实还是可以被外界访问到的
console.log(person._firstName); // Jone
console.log(person._lastName); // Stevens

// 而且还可以被修改
person._firstName = 'Robert';
person._lastName = 'Black';

console.log(person.name); // Robert Black
```

不过 ES13 出来后，私有属性不会被别人访问和修改啦。我们只需要给我们的属性名添加一个 hashtag(#)前缀，这个属性就变成私有的了。当属性私有后，任何外界对其的访问都会报错

```
class Person {
  #firstName = 'Jone';
  #lastName = 'Stevens';

  get name() {
    return `${this.#firstName} ${this.#lastName}`;
  }
}

const person = new Person();
console.log(person.name);

// SyntaxError: Private field '#firstName' must be
// declared in an enclosing class
console.log(person.#firstName);
console.log(person.#lastName);
```

这里的 SyntaxError 是一个编译时抛出的错误，所以你不会等你的代码运行后才知道这个属性被非法访问了

### 3. 支持在最外层写 await

await 操作符的作用是当我们碰到一个 promise 的时候，我们可以使用 await 来暂停当前代码的执行，等待 promise 被 settled（fulfilled 或者 rejected）了，再继续执行当前的代码。

可是之前使用 await 时就是一定要在一个 async 的函数里面使用而不能在全局作用域里面使用。

```
function setTimeoutAsync(timeout) {
  return new Promise(resolve => {
    setTimeout(() => {
      resolve()
    }, timeout)
  })
}

await setTimeoutAsync(3000);
// SyntaxError: await is only valid in async functions
```

ES13 后，我们就可以想上面那么写啦。

### 4. 类支持定义静态成员和静态私有方法

在 ES13 中，我们可以给类定义静态成员和静态私有函数。类的静态方法可以使用 this 关键字访问其他的私有或者公有静态成员，而对于类的实例方法可以通过 this.contructor 来访问这些静态属性

```
class Person {
  static #count = 0;

  static getCount() {
    return this.#count;
  }

  constructor() {
    this.constructor.#incrementCount();
  }

  static #incrementCount() {
    this.#count++;
  }
}

const person1 = new Person();
const person2 = new Person();

console.log(Person.getCount()); // 2
```

### 5. 类致辞定义静态代码块

ES13 允许在类中通过 static 关键字定义一系列静态代码块，这些代码块只会在类被创造的时候执行一次。这其实有点像其他面向对象的编程语言的静态构造函数的用法。

一个类可以定义任意多的的静态代码块，这些代码块会和穿插在它们之间的静态成员变量一起按照定义的顺序在类初始化的时候执行一次。我们还可以使用 super 关键字来访问父类的属性。

```
class Vehicle {
  static defaultColor = 'blue';
}

class Car extends Vehicle {
  static colors = [];

  static {
    this.colors.push(super.defaultColor, 'red')
  }

  static {
    this.colors.push('green');
  }

  console.log(Car.colors); // ['blue', 'red', 'green']
}
```

### 6.使用 in 来判断某个对象是否拥有某个私有属性

Ergonomic brand checks for Private Fields 私有字段检查，通过 in 操作符来判断。

```
class Car {
  #color;

  hasColor() {
    return #color in this;
  }
}

const car = new Car();
console.log(car.hasColor()); // true
```

这个操作符还可以区分不同类的同名私有属性

```
calss Car {
  #color;

  hasColor() {
    return #color in this;
  }
}

class House {
  #color;

  hasColor() {
    return #color in this;
  }
}

const car = new Car();
const house = new House();

console.log(car.hasColor()); // true
console.log(car.hasColor.call(house)); // false
console.log(house.hasColor()); // true
console.log(house.hasColor.call(car)); // false
```

### 7. at 函数来索引元素

此前当我们需要访问数组倒数第 N 个元素时，会使用方括号[数组的长度 - N]，ES13 的 at()函数可以很简便的写出来啦，是需要传入-N 给 at()函数即可。

```
const arr = ['a', 'b', 'c', 'd'];
// 倒数第一个元素
console.log(arr[arr.length - 1]); // d
// 倒数第二个元素
console.log(arr[arr.length - 2]); // c

// ES13的at函数方式
// 倒数第一个元素
console.log(arr.at(-1)); // d
// 倒数第二个元素
console.log(arr.at(-2)); // c
```

除了数组，string 和 TypedArray 对象也支持 at 函数

```
const str = 'Coding Beauty';
console.log(str.at(-1)); // y
console.log(str.at(-2)); // t

const typedArray = new Uint8Array([16, 32, 48, 64]);
console.log(typedArray.at(-1)); // 64
console.log(typedArray.at(-2)); // 48
```

### 8. 正则表达式匹配字符串的时候支持返回开始和结束索引

这个新属性允许我们告诉 RegExp 在返回 match 对象的时候，给我们返回匹配到的子字符串的开始和结束索引。此前，我们只能获取正则表达式匹配到的子字符串的开始索引，ES13 后，我们可以给正则表达式添加一个 d 的标记来让它在匹配的时候给我们既返回匹配到的子字符串的起始位置也返回其结束位置。

```

const str = 'sun and moon';
const regexBefore = /and/;
const regexNow = /and/d;
const matchObj1 = regexBefore.exec(str);
const matchObj2 = regexNow.exec(str);

console.log(matchObj1); // [ 'and', index: 4, input: 'sun and moon', groups: undefined ]
console.log(matchObj2);
/**
[
  'and',
  index: 4,
  input: 'sun and moon',
  groups: undefined,
  indices: [ [ 4, 7 ], groups: undefined ]
]
 */
```

设置完 d 标记后，多了一个 indices 的数组，里面就是匹配到的子字符串的范围。

### 9. Object.hasOwn()方法

我们可以使用 Object.prototype.hasOwnProperty()来检查某个对象自身是否拥有某个属性，但这个方法是存在问题的，首先它可以被某个类自定义的 hasOwnProperty 方法覆盖掉。其次是当一个对象是通过 Object.create(null)创建出来的具有 null 原型的对象时，该对象调用 hasOwnProperty 方法时会报错

问题 1

```
class Car {
  color = 'green';
  age = 2;

  // 你看这个方法就没有告诉我们这个类的对象是不是有某个属性
  hasOwnProperty() {
    return false;
  }
}

const car = new Car();

console.log(car.hasOwnProperty('age')); // false
console.log(car.hasOwnProperty('name')); // false
```

问题 2

```
const obj = Object.create(null);
obj.color = 'green';
obj.age = 2;

// TypeError: obj.hasOwnProperty is not a function
console.log(obj.hasOwnProperty('color'));
```

解决上述问题的一种方法就是调用 Object.prototype.hasOwnProperty 这个 Function 的 call 方法

```
const obj = Object.create(null);
obj.color = 'green';
obj.age = 2;
obj.hasOwnProperty = () => false;

Object.prototype.hasOwnProperty.call(obj, 'color'); // true
```

当 hasOwnProperty 需要被多次调用的时候，可以通过将这部分逻辑抽象成一个方法来减少重复的代码

```
function objHasOwnProp(obj, propertyKey) {
  return Object.prototype.hasOwnProperty.call(obj, propertyKey);
}

const obj = Object.create(null);
obj.color = 'green';
obj.age = 2;
obj.hasOwnProperty = () => false;

console.log(objHasOwnProp(obj, 'color')); // true
console.log(objHasOwnProp(obj, 'name')); // false
```

尽管这样还是看着好麻烦，所以 ES13 推出了全新的 Object.hasOwn()函数。这个新的内置函数接收 2 个参数，参数一是对象，参数二是属性，如果对象本身就有这个属性的话，这个函数会返回 true，否则返回 false。

```
const obj = Object.create(null);
obj.color = 'green';
obj.age = 2;
obj.hasOwnProperty = () => false;

console.log(Object.hasOwn(obj, 'color')); // true
console.log(Object.hasOwn(obj, 'name')); // false
```

### 10. Error 对象的 Cause 属性

ES13 后，Error 对象多了一个 cause 属性来指明错误出现的原因。该属性可帮助我们为错误添加更多的上下文信息，从而帮助使用者更好地定位错误。该属性是我们在创建 error 对象时传进去的第二个参数对象的 cause 属性。

```
function userAction() {
  try {
    apiCallThatCanThrow();
  } catch (err) {
    console.log(err);
    console.log(`Cause by: ${err.cause}`);
  }
}
```

### 11. 数组支持逆序查找

findLast()和 findLastIndex()出来后，数组就支持后序查找元素。

应用场景就是我们本身就是想要寻找最后一个满足某个条件的元素，例如找到数组里面最后一个偶数。

```
const nums = [7, 14, 3, 8, 10, 9];

const lastEven = nums.findLast((num) => num % 2 === 0);
const lastEvenIndex = nums.findLastIndex((num) => num % 2 === 0);

console.log(lastEven); // 10
console.log(lastEvenIndex); // 4
```

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

### 3. WeakRefs

使用 WeakRefs 的 Class 类创建对于对象的弱引用（对于对象若饮用是指当该对象应该被 GC 回收时，不会阻止 GC 的回收行为）。

### 4. 运算符（&&=, ||=, ??=）

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

### 5. 数字分隔符

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

## ES11

### BigInt

在 JavaScript 中，数值类型 Number 是 64 位浮点数，所以计算精度和表示范围都有一定限制。新增的 BigInt 数据类型，这也是 JavaScript 引入的第八种基本类型。BigInt 可以表示任意大的整数。

```
BigInt(value);
```

其中 value 是创建对象的数值。可以是字符串或者整数。

在 Javascript 中，Number 基本类型可以精确表示的最大整数是 253.因此早期会有这样的问题：

```
let max = Number.MAX_SAFE_INTEGER;
let max1 = max + 1
let max2 = max + 2

max1 === max2  // true
```

有了该类型，这个问题就不复存在了

```
let max = BigInt(Number.MAX_SAFE_INTEGER);

let max1 = max + 1n
let max2 = max + 2n

max1 === max2   // false
```

可以通过 typeof 操作符来判断变量是否为 BigInt 类型

```
typeof 1n === 'bigint'; // true
typeof BigInt('1') === 'bigint'; // true
```

还可以通过 Object.prototype.toString 方法来判断变量是否为 BigInt 类型

```
Object.prototype.toString.call(10n) === '[object BigInt]';    // true
```

注意，BigInt 和 Number 不是严格相等的，但是宽松相等：

```
10n === 10 // false
10n == 10  // true
```

Number 和 BigInt 可以进行比较：

```
1n < 2;    // true
2n > 1;    // true
2 > 2;     // false
2n > 2;    // false
2n >= 2;   // true
```

### 空值合算运算符（??）

在编写代码时，如果某个属性不为 null 和 undefined，那么就获取该属性，如果该属性为 null 或 undefined，则取一个默认值：

```
const name = dogName ? dogName : 'default';

// 通过||来简化
const name =  dogName || 'default';
```

但是||的写法存在一定的缺陷，当 dogName 为 0 或者 false 的时候也会走到 default 的逻辑。??运算符只有左边为 null 或 undefined 时才返回右边的值

```
const dogName = false;
const name = dogName ?? 'default'; // name = false
```

### 可选链操作符（?.）

在开发过程中，我们经常需要获取深层次属性，例如 system.user.addr.province.name。但在获取 name 这个属性前需要一步步的判断前面的属性是否存在，否则会报错：

```
const name = (system&&system.user&&system.user.addr&&system.user.province&&system.user.province.name) || 'default';
```

为了简化上述过程，可选链操作符（?.），允许读取位于连接对象链深处的属性的值，而不必明确验证链中的每一个引用是否有效。（?.）操作符功能类似于（.）链式操作符，不同之处在于，在引用为 null 或 undefined 的情况下不会引起错误，该表达式短路返回值是 undefined。在函数调用一起使用时，如果给定的函数不存在，则返回 undefined。

```
const name = system?.user?.addr?.province?.name || 'default';
```

当尝试访问可能不存在的对象属性时，可选链操作符将会使表达式更短、更简明。在探索一个对象的内容时，如果不能确定哪些属性必定存在，可选链操作符也是很有帮助的。

可选链有以下三种形式：

```
a?.[x]
// 等同于
a == null? undefined : a[x]

a?.b()
// 等同于
a == null ? undefined : a.b()

a?.()
// 等同于
a == null ? undefined : a()
```

在使用 TypeScript 开发时，这个操作符可以解决很多问题。

## ES10

### flat()和 flatMap()

(1) flat()

flat 方法用于创建并返回一个新数组，这个新数组包含与它调用 flat()的数组相同的元素，只不过其中任何本身也是数组的元素会被打平填充到返回的数组中：

```
[1, [2, 3]].flat()        // [1, 2, 3]
[1, [2, [3, 4]]].flat()   // [1, 2, [3, 4]]
```

在不传参数时，flat 默认只会打平一级嵌套，如果想要打平更多的层级，就需要传给 flat 一个数值参数，这个参数表示要打平的层级数

```
[1, [2, [3, 4]]].flat(2)  // [1, 2, 3, 4]
```

如果数组中存在空项，会直接跳过

```
[1, [2, , 3]].flat());    //  [1, 2, 3]
```

如果传入的参数小雨等于 0，就会返回原数组：

```
[1, [2, [3, [4, 5]]]].flat(0);    //  [1, [2, [3, [4, 5]]]]
[1, [2, [3, [4, 5]]]].flat(-10);  //  [1, [2, [3, [4, 5]]]]
```

（2）flatMap()

flatMap 方法使用映射函数映射每个元素，然后将结果压缩成一个新数组。它与 map 和连着深度值为 1 的 flat 几乎相同，但 flatMap 通常在合并成一种方法的效率稍微高一些。该方法会返回一个新的数组，其中每个元素都是回调函数的结果，并且结构深度 depth 值为 1.

```
[1, 2, 3, 4].flatMap(x => x * 2);      //  [2, 4, 6, 8]
[1, 2, 3, 4].flatMap(x => [x * 2]);    //  [2, 4, 6, 8]

[1, 2, 3, 4].flatMap(x => [[x * 2]]);  //  [[2], [4], [6], [8]]
[1, 2, 3, 4].map(x => [x * 2]);        //  [[2], [4], [6], [8]]

```

### Object.fromEntries()

Object.fromEntries 方法可以把键值对列表转换成对象。该方法相当于 Object.entries()方法的逆过程。Object.entries()方法返回一个给定对象自身可枚举属性的键值对数组，而 Object.fromEntries() 方法把键值对列表转换为一个对象。

```
const object = { key1: 'value1', key2: 'value2' }
const array = Object.entries(object)  // [ ["key1", "value1"], ["key2", "value2"] ]

Object.fromEntries(array)             // { key1: 'value1', key2: 'value2' }
```

使用该方法主要有以下两个用途：

（1）将数组转成对象

```
const entries = [
  ['foo', 'bar'],
  ['baz', 42]
]
Object.fromEntries(entries)  //  { foo: "bar", baz: 42 }
```

（2）将 Map 转成对象

```
const entries = new Map([
  ['foo', 'bar'],
  ['baz', 42]
])
Object.fromEntries(entries)  //  { foo: "bar", baz: 42 }
```

## ES9

### for await...of

for await...of 方法被称为异步迭代器，该方法是主要用来遍历异步对象。

for await...of 语句会在异步或者同步可迭代对象上创建一个迭代循环，包括 String, Array, 类数组, Map, Set 和自定义的异步或者同步可迭代对象。这个语句只能在 async function 内使用：

```
function Gen(time) {
  return new Promise((resolve, reject) => {
    setTimeout(function() {
      resolve(time)
    }, time)
  })
}

async function test () {
  let arr = [Gen(1000),Gen(2000), Gen(3000)]
  for await (let item of arr) {
    console.log(Date.now(), item)
    /*
    1632987682922 1000
    1632987682922 2000
    1632987683924 3000
    */
  }
}

test()
```

## ES8

### Object.values 和 Object.entries()

在 ES5 中就引入了 Object.keys 方法，在 ES8 中引入了跟 Object.keys 配套的 Object.values 和 Object.entries，作为遍历一个对戏那个的补充手段，供 for...of 循环使用。它们都用来遍历对象，它会返回一个由给定对象的自身可枚举属性（不含继承的和 Symbol 属性）组成的数组，数组元素的排列顺序和正常循环该对象时返回的顺序一致，这个三个元素返回的值分别如下：

- Object.keys(): 返回包含对象键名的数组

- Object.values(): 返回包含对象键值的数组

- Object.keys(): 返回包含对象键名和键值的数组

```
let obj = {
  id: 1,
  name: 'hello',
  age: 18
};
console.log(Object.keys(obj));   // 输出结果: ['id', 'name', 'age']
console.log(Object.values(obj)); // 输出结果: [1, 'hello', 18]
console.log(Object.entries(obj));   // 输出结果: [['id', 1], ['name', 'hello'], ['age', 18]

```

**注意**

- Object.keys()方法返回的数组中的值都是字符串，也就是说不是字符串的 key 值会转化为字符串。

- 结果数组中的属性值都是对象本身可枚举的属性，不包括继承来的属性

## ES7

### Array.prototype.includes

includes()方法用来判断一个数组是否包含一个指定的值，如果包含就返回 true，否则返回 false。该方法不会改变原数组。

```
arr.includes(searchElement, fromIndex)
```

该方法有两个参数：

- searchElement: 必须，需要查找的元素值

- fromIndex: 可选，从 fromIndex 索引处开始查找目标值。如果为负值，则按升序从 array.length + fromIndex 的索引开始搜索（即是从末尾开始往前跳 fromIndex 的绝对值个索引，然后往后搜索）。默认为 0。

```
[1, 2, 3].includes(2);  //  true
[1, 2, 3].includes(4);  //  false
[1, 2, 3].includes(3, 3);  // false
[1, 2, 3].includes(3, -1); // true
```

之前通常使用 indexOf 来判断数组中是否包含某个指定值。但 indexOf 在语义上不够明确直观，同事 indexOf 内部使用===来判等，所以存在对 NaN 的误判，includes 则修复了这个问题。

```
[1, 2, NaN].indexOf(NaN);   // -1
[1, 2, NaN].includes(NaN);  //  true
```

### 指数操作符

指数操作符用来更方便的进行指数计算，它与 Math.pow()等效

```
Math.pow(2,10); // 1024
2**10; // 1024
```

## ES6

### 函数默认参数

只有不传入参数时才会触发默认值

```
function getPoint(x = 0, y = 0) {
  console.log(x, y)
}

getPoint(1, 2) // 1 2
getPoint() // 0 0
getPoint(1) // 1 0
```

当使用函数默认值时，需要注意以下几点：

#### 1. 函数 length 属性值

函数 length 属性通常用来表示函数参数的个数，当引入函数默认值之后，length 表示的就是第一个有默认值参数之前的普通参数的个数。

```
const funcA = function(x, y) {}
console.log(funcA.length) // 2

const funcB = function(x, y = 1) {}
console.log(funcB.length) // 1

const funcC = function(x = 1, y) {}
console.log(funcC.length) // 0
```

#### 2. 参数作用域

当给函数的参数设置了默认值之后，参数在被初始化时将形成一个独立作用域，初始化完成后作用域消解。

```
let x = 1;
function func(x, y = x) {
  console.log(y); // 2
}
func(2);
```

在函数调用时，参数 x, y 将形成一个独立的作用域，所以参数中的 y 会等于第一个参数中的 x，而不是上面定义的 1。

### Symbol

新增基本数据类型 Symbol，表示独一无二的值。它是一种类似于字符串的数据类型，它的特点如下：

- Symbol 的值是唯一的，用来解决命名冲突的问题

- Symbol 值不能与其他类型数据进行运算

- Symbol 定义的对象属性不能使用 for...in 遍历循环，但是可以使用 Reflect.ownKeys 来获取对象的所有键名

```
let s1 = Symbol();
console.log(typeof s1); // symbol

let s2 = Symbol('hello');
let s3 = Symbol('hello');
console.log(s2 === s3); // false
```

基于以上特性，Symbol 属性类型比较适合用于两类场景:常量值和对象属性

（1）避免常量值重复

getValue 函数会根据传入字符串参数 key 执行对应代码逻辑

```
function getValue(key) {
  swith(key) {
    case 'A':
    ...
    case 'B':
    ...
  }
}
getValue('B')
```

这段代码对调用者而言非常不友好，因为代码中使用了魔术字符串（Magic string，指的是在代码之中多次出现、与代码形成强耦合的某一个具体的字符串或者数值），导致调用 getValue 函数时需要查看函数代码才能会找到参数 key 的可选值。所以可以将参数 key 的值以常量的方式声明：

```
const KEY = {
  alibaba: 'A',
  baidu: 'B',
}
function getValue(key) {
  switch(key){
    case KEY.alibaba:
      ...
    case KEY.baidu:
      ...
  }
}
getValue(KEY.baidu);
```

但这样也并非完美，假设现在要在 KEY 常量中加一个 key，根据对应的规则，很有可能会出现值重复的情况：

```
const KEY = {
  alibaba: 'A',
  baidu: 'B',
  tencent: 'B'
}

// getValue(KEY.baidu) 等同于 getValue(KEY.tencent)
```

所以这种场景更适合使用 Symbol，不需要关心值本身，只关心值的唯一性：

```
const KEY = {
  alibaba: Symbol(),
  baidu: Symbol(),,
  tencent: Symbol(),
}
```

（2）避免对象属性覆盖

函数 fn 需要对传入的对象参数添加一个临时属性 user，但可能该对象参数中已经有这个属性了，如果直接复制就会覆盖之前的值。此时就可以使用 Symbol 来避免这个问题。创建一个 Symbol 数据类型的变量，然后将该变量作为对象参数的属性进行赋值和读取，这样就能避免覆盖的情况：

```
function fn(o) {
  const s = Symbol()
  o[s] = 'zzz'
}
```
