---
layout:     post
title:      Javascript专题系列(二)
subtitle:  	类型判断？数组寻值？字符串反转？一文搞定！
date:       2022-05-23
author:    	bolero
header-img: img/post-bg-github-cup.jpg
catalog: 	true
tags:
    - Javascript
    - Javascript专题系列
---

>本篇继续更新Javascript专题系列，也是Javascript专题系列的第二篇，系列一可以点击往期文章[javascript专题系列(一):数组去重？数组扁平？防抖节流？深浅拷贝？一文搞定！ ](https://bolerodance.github.io/2022/05/18/JavaScript%E4%B8%93%E9%A2%98%E7%B3%BB%E5%88%97(%E4%B8%80)/) 阅读，本篇文章主要将介绍以下内容：
>
>- 类型判断
>- 数组寻值
>- 字符串反转

### 类型判断

#### typeof

- 返回数据类型的字符串表达
- 可以判断数值, 字符串, `undefined`, `boolean`, `function`
- 不能判断`null`与`object`与`array`, 返回都是`Object`

```javascript
    console.log(typeof('kjf')) //string
    console.log(typeof(undefined)) //undefined
    console.log(typeof(true))  // boolean
    console.log(typeof(2)) //number
    console.log(typeof(function fn(){})) // function
    console.log(typeof(new Date)) // object
    console.log(typeof([])) // object
    console.log(typeof(null)) // object
    console.log(typeof({})) // object
```

#### Object.prototype.toString

在介绍`Object.prototype.toString` 之前， 我们有必有先了解一下`toString`方法

默认情况下，每一个继承Object的对象都有 `toString`  方法

- 如果 `toString` 方法没有重写的话，会返回 `[Object type]`，其中 type 为对象的类型

- 但当除了 `Object` 类型的对象外，其他类型直接使用 `toString` 方法时，会直接返回都是内容的字符串

```javascript
 console.log('foo'.toString()) // 'foo'
 console.log(1.toString())     // Uncaught SyntaxError: Invalid or unexpected token
 console.log(false.toString())    // 'false'
 console.log(undefined.toString()) // Uncaught TypeError: Cannot read property 'toString' of undefined
 console.log(null.toString()) // // Uncaught TypeError: Cannot read property 'toString' of undefined
 console.log(String.toString()) // function String(){[native code]}
 console.log(Number.toString()) // "function Number() { [native code] }"
 console.log(Boolean.toString()) //"function Boolean() { [native code] }"
 console.log(Array.toString()) // "function Array() { [native code] }"
 console.log(Function.toString()) // "function Function() { [native code] }"
 console.log(Object.toString()) // "function Object() { [native code] }"
 console.log(Math.toString()) // "[object Math]"
```

请大家重点关注一下`Object.toString()`

其实Object对象和它的原型上各自有一个`toString`方法，第一个返回的是一个函数，第二个返回的是值类型，即：

```javascript
 console.log(Object.toString()) // "function Object() { [native code] }"
 console.log(Object.prototype.toString()) // [object Object]
```

[ES5规范中](https://es5.github.io/#x15.2.4.2)对`Object.prototype.toString`解释得很清楚(自己翻译，可能不是很准确，建议去看原文)：

当`toString` 方法通过call调用时，将执行以下几个步骤：

1. 如果 `this` 的值是 `undefined`, 该方法返回 `[Object Undefined]`
2. 如果  `this` 的值是 `null` , 该方法返回 `[object Null]`
3. 调用`ToObject` 来传递 `this` 的值作为参数，并让 *O* 作为调用`ToObject` 的结果
4. 让 *class* 作为*O* 的内部属性[[Class]] 的值
5. 返回字符串值，由 [Object, *class*] 三部分组成

由此可见，调用 `Object.prototype.toString.call` 会返回一个由 [Object, *class*] 组成的字符串，而 *class* 是要判断的对象的内部属性：

```javascript
Object.prototype.toString.call(1)            [object Number]
Object.prototype.toString.call('a')          [object String]
Object.prototype.toString.call(true)         [object Boolean]
Object.prototype.toString.call({})           [object Object]
Object.prototype.toString.call([])           [object Array]
Object.prototype.toString.call(null)         [object Null]
Object.prototype.toString.call(/a/)          [object RegExp]
Object.prototype.toString.call(undefined)    [object Undefined]
Object.prototype.toString.call(function(){}) [object Function]
Object.prototype.toString.call(new Date())   [object Date]
Object.prototype.toString.call(JSON)         [object JSON]
function fn () {
      console.log(Object.prototype.toString.call(arguments))
    }
    fn() // [object Arguments]
```

打印结果我们可以发现，*class* 是我们做类型检查的关键所在！这个方法十分强大，几乎所有数据类型都能被正确检查到！简单处理下，`slice(8, -1)`就能返回我们想要的结果

#### Array.isArray

由于数组基于对象，无法构成单独的语言类型。我们无法使用`typeof`从对象中区分出数组

```
alert(typeof {}) // object
alert(typeof []) // object
```

不过Array为我们提供了一种方法`Array.isArray(value)`，该方法返回一个布尔值，通过这种方式我们可以很方便的判断数组类型

```
alert(Array.isArray({})) // false
alert(Array.isArray([])) // true
```

#### instanceof

- `instanceof` 主要用于判断对象，判断实例是否属于某种类型

- 语法: `obj instanceof Class`
- 返回 `boolean`值

```javascript
// 检查类
class Rabbit {}
let rabbit = new Rabbit()
console.log(rabbit instanceof Rabbit) // true
// 检查数组
let arr = [1, 2, 3]
console.log(arr instance of Array) // true
// 检查构造函数
function Animal () {}
let animal = new Animal()
console.log(animal instanceof Animal) // true

```

**内部机制**

逻辑即使用 `obj instanceOf Class` 检查 `Class.prototype` 是否等于 `obj` 的原型链中的原型之一，也就是遍历左边obj的原型链，直到等于右边类的`prototype`, 返回`ture`

```JavaScript
// 以继承为例
class Animal {}
class Rabbit extends Animal {}
let rabbit = new Rabbit()

console.log(rabbit instanceof Animal) // ture

// rabbit.__proto__.proto__ === Animal.prototype (匹配！)
```

**手写instanceof**

当我们明白`instanceOf `操作符的内部机制之后，可以自己来尝试实现一个`instanceof` ，逻辑很简单，遍历左边obj的原型链，直到等于右边类的 `prototype`:

```javascript
function instanceof (obj, class) {
    if (typeof class !== 'function' || typeof obj !== 'obj') {
        return false
    }
    const prototype = class.prototype
    while (obj !== null) { 
        if (obj === prototype) {
            return true
        }
        obj = obj.__proto__
    }
    return false
}

function A() {}
const a = new A()

class Animal {}
class Rabbit extends Animal {}
let rabbit = new Rabbit()

console.log(instanceOf(a, A)) // true
console.log(instanceOf(rabbit, Animal)) // true
```

### 数组寻值

给定一个数组，需要取出合规的值，这个值可能是一个给定随机数，可能是数组中的最大值或者最小值，这是开发中常见的需求，以下有几种常用或者好用的方法可供我们选择

#### 寻最值

**Math.max**

JavaScript 提供了内建函数 `Math.max` 函数返回一组数中的最大值，语法是：

```
Math.max(value1[,value2, ...])
```

如：

```
alert( Math.max(3, 5, 1) )  // 5
```

注意：

- 如果参数不是数字类型，会被转换成数字，再进行比较
- 如果有任一参数不能被转换为数值，则结果为 `NaN`
- 直接把数组“原样”传入是不会奏效的，返回 `NaN`
- 如果没有参数，则结果为 `-Infinity`

```
Math.max(true, 0, null) // 1
Math.max(1, undefined) // NaN
Math.max([1, 2, 3]) // NaN
Math.max() // -Infinity
```

在Math.max 的基础上，我们结合其他方法，便可以实现寻最值的需求

**方法一：Math.max & 循环遍历**

```javascript
    let arr = [6, 4, 1, 8, 2, 11, 23]
	function arrMax (arr) {
      let res = arr[0]
      for (let key in arr) {
        res = Math.max(arr[0], arr[key])
      }
      return res
    }
```

**方法二：Math.max & apply**

ES5中 `apply` 会帮我们将一个数组转换为一个参数接一个参数传递给方法

```
console.log(Math.max.apply(null, arr))
```

**方法二：Math.max & ...拓展运算符**

ES6 中的拓展运算符，作为rest参数的逆运算，能够将一个数组转换为用逗号分隔的参数序列，完全能够代替 `apply`, 因为它更加方便，请看：

```
console.log(Math.max(...arr1))
```

**sort排序**

其实除了使用`Math.max`，我们也可以使用sort方法，一头一尾即为我们所需的最小和最大值：

```
console.log(arr.sort((a, b) => a - b)[arr.length - 1])
```

#### 生成给定范围随机数

JavaScript 提供了 `Math.random()` 方法，能够返回一个从 0 到 1 的随机数（不包括 1：

```
alert( Math.random() ); // ... (任何随机数)
```

以这个方法为基础，我们可以将区间0…1 中的所有值“映射”为范围在 `min` 到 `max` 中的值：

1. 我们将 0…1 的随机数乘以 `max-min`，则随机数的范围将从 0…1 增加到 `0..max-min`
2. 将随机数与 `min` 相加，则随机数的范围将为 `min` 到 `max`

函数实现：

```javascript
function random(min, max) {
  return min + Math.random() * (max - min);
}

console.log(random(2, 6)) // 4.877133887771004
```

在此基础上，如果我们需要生成随机整数：

```javascript
// 包括 min 和 max
function randomNum(min, max) {
  let rad = min + Math.random() * (max - min + 1)
  return Math.floor(rad)
}
console.log(randomNum(2, 6))
```

### 字符串反转

#### 使用内置函数

由于字符串没有反转函数，但是数组提供了reverse()函数，我们可以将字符串转成数组，然后再转成字符串

```
let str = 'helloword'
alert(str.split('').reverse().join(''))
```

#### 递归

```javascript
// 方式一
function reverse (str) {
    let i = str.length
    if (i <= 1) {
        return str
    }
    let left = str.substring(0, i/2)
    let right = str.substring(i/2, i)
    return reverse(right) + reverse(left)
}

// 方式二
function reverse (str) {
    if (str === '') {
        return ''
    }
    return reverse (str.slice(1) + str[0])
}
```



