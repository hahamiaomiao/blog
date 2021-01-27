---
title: 类型转换
categories: [学习笔记]
comments: true
---
## 一、数据类型
### 1.1 原始数据类型（值类型）
- Null
- Undefined
- Boolean
- String
- Number
- Symbol
- BigInt
> BigInt是一种新的数据类型，用于当整数值大于Number数据类型支持的范围时。这种数据类型允许我们安全地对大整数执行算术操作，表示高分辨率的时间戳，使用大整数id，等等，而不需使用库。重要的是，不能使用number和bigInt操作数的混合执行算术运算，需要通过显示转换其中的一种类型。此外，由于兼容性原因，不允许在bigIng上使用一元加号（+）运算符。
### 1.2 引用数据类型(Object)
javaScript中内置了很多对象
- Array
- ArrayBuffer
- AsyncFunction
- Atomics
- BigInt
- BigInt64Array
- BigUnit64Array
- Boolean
- DataView
- Date
- Error
- EvalError
- Float32Array
- Float64Array
- Function
- Generator
- GeneratorFunction
- Infinity
- Int16Array
- Int32Array
- Int8Array
- InternalError
- Intl
- Intl.Collator
- Intl.DateTimeFormat
- Intl.ListFormat
- Intl.Locale
- Intl.NumberFormat
- Intl.PluralRules
- Intl.RelativeTimeFormat
- JSON
- Map
- Math
- NaN
- Number
- Object
- Promise
- Proxy
- RangeError
- ReferenceError
- Reflect
- RegExp
- Set
- SharedArrayBuffer
- String
- Symbol
- SyntaxError
- TypeError
- TypedArray
- URIError
- Uint16Array
- Uint32Array
- Uint8Array
- Uint8ClampedArray
- WeakMap
- WeakSet
- WebAssembly
- decodeURI()
- decodeURIComponent()
- encodeURI()
- encodeURIComponent()
- escape()
- eval()
- globalThis
- isFinite()
- isNaN()
- null
- parseFloat
- parseInt
- undefined
- unescape()
- uneval()
> 大家不要看JavaScript的内置对象这么多，转换时只需要把这么统统当作一个类型（引用类型）进行转换就行，在JavaScript内部中转换也不会考虑这么多。
## 二、自动装箱
> 为了方便操作基本数据类型，ECMAScript还提供了三个特殊的引用类型，基本包装类型，String、Boolean、Number。有了这三个类型，在需要的时候，原始类型会自动转换成相应的包装对象（这个过程叫自动装箱）。自动装箱就是临时创建一个包装对象，将原始类型的值封装起来，以便调用包装对象的函数。但是原来那个变量的值不会有任何变化。

```
var s = 'hello'
var s1 = s.substring(2)
```
字符串是基本数据类型，为啥能调用方法呢，这其实在后台进行了一系列的操作
- 1. 创建String类型的一个实例
- 2. 在实例上调用指定的方法
- 3. 销毁这个实例

```
var s = new String('hello')
var s1 = substring(2)
s = null
```
当然，你可以将Boolean、Number、String这三个函数当作构造函数来使用，通过手动new包装类来装箱（得到包装对象）

```
var b = new Boolean(true)
typeof(b) // 'object'
```

## 三、类型转换的规则
number转化为布尔值，除了0，-0，NaN都为true<br>
number转化为字符串，加上""

string转化为布尔值，除了空串""都为true<br>
string转换为数字<br>
 - parseInt('2') // 2<br>
 - parseInt('2a') // 2<br>
 - parseInt('a2') // NaN<br>
 - parseFloat('2') // 2<br>
 - parseFloat('2a') // 2<br>
 - parseFloat('a2') // NaN<br>
 - Number('2') // 2<br>
 - Number('2a') // NaN<br>
 - Number('a2') // NaN

 undefined,null转化为布尔值为fasle<br>
 undefined,null转化为字符串为"null","undefined"<br>
 undefined,null转化为数字为NaN, 0

引用类型转化为布尔值为true<br>
 各类引用类型转化为字符串<br>
 - 函数转化为字符串，加上""<br>
 - 数组转化为字符串，去掉中括号，加上""。`[1,2] => '1,2'`<br>
 - 对象转化为字符串，'[object Object]'<br>
引用类型转化为数字，除了数组的引用类型，都为NaN<br>
 - 空数组为0，存在一个元素且为数字转数字，其它情况为NaN

 Symbol转字符串加""<br>
 Symbol转布尔值，为true<br>
 Symbol转数字抛错

 Boolean转字符串加""<br>
 Boolean转数字true=>1,false=>0

## 四、内部实现类型转换的4个函数

### 4.1 ToPrimitive(input[,PreferredType])
```
// ECMA-262, section 9.1, page 30. Use null/undefined for no hint,
// (1) for number hint, and (2) for string hint.
function ToPrimitive(x, hint) {
  // Fast case check.
  if (IS_STRING(x)) return x;
  // Normal behavior.
  if (!IS_SPEC_OBJECT(x)) return x;
  if (IS_SYMBOL_WRAPPER(x)) throw MakeTypeError(kSymbolToPrimitive);
  if (hint == NO_HINT) hint = (IS_DATE(x)) ? STRING_HINT : NUMBER_HINT;
  return (hint == NUMBER_HINT) ? DefaultNumber(x) : DefaultString(x);
}

// ECMA-262, section 8.6.2.6, page 28.
function DefaultNumber(x) {
  if (!IS_SYMBOL_WRAPPER(x)) {
    var valueOf = x.valueOf;
    if (IS_SPEC_FUNCTION(valueOf)) {
      var v = % _CallFunction(x, valueOf);
      if (IsPrimitive(v)) return v;
    }
    var toString = x.toString;
    if (IS_SPEC_FUNCTION(toString)) {
      var s = % _CallFunction(x, toString);
      if (IsPrimitive(s)) return s;
    }
  }
  throw MakeTypeError(kCannotConvertToPrimitive);
}

// ECMA-262, section 8.6.2.6, page 28.
function DefaultString(x) {
  if (!IS_SYMBOL_WRAPPER(x)) {
    var toString = x.toString;
    if (IS_SPEC_FUNCTION(toString)) {
      var s = % _CallFunction(x, toString);
      if (IsPrimitive(s)) return s;
    }
    var valueOf = x.valueOf;
    if (IS_SPEC_FUNCTION(valueOf)) {
      var v = % _CallFunction(x, valueOf);
      if (IsPrimitive(v)) return v;
    }
  }
  throw MakeTypeError(kCannotConvertToPrimitive);
}
```
ToPrimitive将input转换为基本数据类型，PreferredType要么不传，要么是number、string。

#### 4.1.1 PreferredType为number
- 1. 如果input本身就是原始类型，直接返回input。
- 2. 调用input.valueOf()，如果结果是原始类型，则返回这个结果。
- 3. 调用input.toString()，如果结果是原始类型，则返回这个结果。
- 4. 抛出TypeError异常。

#### 4.1.2 PreferredType为string
- 1. 如果input本身是原始类型，直接返回input。
- 2. 调用input.toString()，如果结果是原始类型，则返回这个结果。
- 3. 调用input.valueOf()，如果结果是原始类型，则返回这个结果。
- 4. 抛出TypeError异常。

#### 4.1.3 PreferredType不传
- 1. 如果input是内置的Date类型，PreferredType视为String
- 2. 否则PreferredType视为Number
```
// 面试题：
({}) + 1 // '[object Object]1'
```
+号操作符，只有当左右两边的类型相同（都为string或者number）是才进行操作。所以会经历如下步骤：<br>
- 1. {}和1都会调用ToPrimitive，1原始类型直接返回。
- 2. {}内部调用DefaultNumber，使用valueOf方法，返回object。
- 3. 在调用toString方法，返回[object, Object]。
- 4. 所以最后的结果就是[object, Object]1。

### 4.2 ToBoolean(argument)
- undefined,null ----false<br>
- boolean        ----argument<br>
- number         ----仅当argument为+0,-0,NaN时false，否则一律true<br>
- String         ----仅当argument为空串(长度为0）时false，否则一律true<br>
- Symbol,Object  ----true

### 4.3 ToNumber(argument)
- undefined  ----NaN<br>
- null       ----0<br>
- boolean    ----true=>1,false=0<br>
- number     ----argument<br>
- String     ----将字符串中的内容转化为数字('23'=>23)，如果失败则返回NaN('23a'=>NaN)<br>
- Symbol     ----抛出TypeError异常<br>
- Object     ----先**primValue = ToPrimitive(argument, Number)**，再对primValue使用ToNumber(primValue)

### 4.4 ToString(argument)
- undefined  ----"undefined"<br>
- null       ----"null"<br>
- boolean    ----"argument"<br>
- number     ----"argument"<br>
- string     ----argument<br>
- Symbol     ----抛出TypeError异常<br>
- Object     ----先**primValue = ToPrimitive(argument, String)**,再对primValue使用ToString(primValue)

## 五、隐式类型转换
在执行过程中，当JS内部期望得到某种类型的值，而实际在那里的值是其他类型，就会发生隐式类型转换。系统内部会自动调用我们前面讲过的ToBoolean(argument)、ToNumber(argument )、ToString(argument)，尝试转换成期望的数据类型。

### 5.1 期望得到boolean的值
```
if(!undefined && !null && !0 && !NaN && !'') {
  ... ...
}
```
因为在if括号中，js期望得到boolean的值，所以对括号中每一个值都使用ToBoolean(argument)，将它们转化成boolean。

### 5.2 期望得到number的值
```
3 * {valueOf: function() {return 5}}

// 要对右侧执行ToNumber，因为右侧为Object，所以对其进行ToPrimitive(argument,Number)，即先进行valueOf，已经得到原始类型5
```
因为在乘号的两端，js期望得到number类型的值，所以对右边的那个对象使用ToNumber(argument)，得到结果5，再与乘号左边的3相乘。

在内部发生的事情：<br>
- 1. {valueOf: function() {return 5}}不是原始类型，执行ToPrimitive({valueOf: function() {return 5}}, hint number)
- 2. 调用valueOf方法，返回5
- 3. 执行ToNumber(5)，返回5

### 5.3 家号有别于其他运算符
- 如果有一边是字符串，就把另一边也转换为字符串<br>
- 如果一方不是字符串或者数据，就转换为数据或者字符串

除了加号运算符，其他运算符，只要其中一方为数据，那么另一方就被转换为数字。

### 5.4 显示类型转换
手动调用Boolean(value)、Number(value)、String(value)完成的类型转换。
```
Boolean('hello')  //  true
Number("2020")   //  2020
String({a: 1})  //  "[object Object]"
```
前面两个类型转换没有什么好解释的，我们看看最后一个String({a: 1})。<br>
在内部发生的事情：<br>
- 1. 执行转换String({a: 1})
- 2. 执行内部的ToString({a: 1})
- 3. {a: 1}不是原始类型，执行ToPrimitive({a: 1}, hint string)
- 4. 调用toString方法，返回"[object, object]"
- 5. 执行ToString("[object, object]")，返回"[object, object]"