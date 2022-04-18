---
layout:     post
title:      深入认识Javascript
subtitle:   总结JS中进阶概念与深入理解对象与函数
date:       2022-04-18
author:     bolero
header-img: img/post-bg-2015.jpg
catalog: 	true
tags:
            - Javascript
            - 进阶知识
            - 对象与函数
---

## 前言

在[Javascript基本概念与运算操作符](https://bolerodance.github.io/2022/03/13/JavaScript/) 与 [JavaScript对象与函数](https://bolerodance.github.io/2022/03/16/JavaScript/) 中已经对JS的基本概念与主要数据类型有了较为全面的介绍，这里针对JS背后的逻辑还有对象与函数的深入理解再次进行介绍，以便对JS有更加全面的理解和认识

#### 回顾数据类型

**原始数据类型**

- `string`: 任意字符串
- `number`: 任意数字
- `boolean`: `true`/`false`
- `undefined`: `undefined`
- `null`: `null`

**对象数据类型**

- `object`: 基本对象类型
- `function`: 特殊对象, 可以执行
- `array`: 特殊对象, 可通过下标执行, 内部有序

**相关问题**

1. `undefined`与`null`的区别
   - `undefined`: 创建了变量未赋值
   - `null`: 创建了变量并赋值, 赋值为`null`
2. 赋值为`null`的场景
   - 初始赋值为`null`, 表明将要赋值为对象
   - 最后赋值为`null`, 让这个变量原来指向的对象被垃圾回收机制回收
3. 变量类型？ 数据类型？
   - 变量类型(内存值类型)
     - 基本类型
     - 引用类型
   - 数据类型
     - 原始数据类型(基本类型)
     - 对象类型

#### 数据类型的检查和判断

**`typeof`**

- 返回数据类型的字符串表达
- 可以判断数值, 字符串, `undefined`, `boolean`, `function`
- 不能判断`null`与`object`与`array`, 返回都是`Object`

```
console.log(typeof undefined) // undefined
console.log(typeof true) 	  // boolean
console.log(typeof null)	  // object
console.log(typeof {})        // object
console.log(typeof [])		  // object
```

**`instanceof`**

- 返回 `boolean`值
- 只能用于判断对象，判断实例是否属于某种类型
- 语法:  `obj instanceof Class`

```javascript
// 检查类
class Rabbit {}
let rabbit = new Rabbit()
console.log(rabbit instanceof Rabbit) // true
// 检查数组
let arr = [1, 2, 3]
console.log(arr instanceof Array) // ture
// 检查构造函数
function Animal() {}
let animal = new Animal()
console.log(animal instanceof Animal) // true
```

**`instanceof` 操作符的原理**

逻辑即使用 `obj instanceOf Class` 检查 `Class.prototype` 是否等于 `obj` 的原型链中的原型之一，也就是遍历左边obj的原型链，直到等于右边类的`prototype`, 返回`ture`

```javascript
// 以继承为例
class Animal {}
class Rabbit extends Animal {}
let rabbit = new Rabbit()

console.log(rabbit instanceof Animal) // true

// rabbit.__proto__.__proto__ === Animal.prototype（匹配！）
```

下图展示了 `rabbit instanceof Animal` 的执行过程中，`Animal.prototype` 是如何参与比较的：

![](https://zh.javascript.info/article/instanceof/instanceof.svg)

**`Object.prototype.toString.call()`**

在内部，`toString` 的算法会检查 `this`，并返回相应的结果

```
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
```

**总结**

| 类检查方法    | 用于                   | 返回值     |
| ------------- | ---------------------- | ---------- |
| `typeof`      | 原始数据类型           | string     |
| `{}.toString` | 原始数据类型，内建对象 | string     |
| `instanceof`  | 对象                   | true/false |

#### 函数绑定 `bind`

函数提供了一个内建方法 [bind](https://developer.mozilla.org/zh/docs/Web/JavaScript/Reference/Global_Objects/Function/bind)，它可以绑定 `this`, 确保在正确的上下文调用对象方法

基本的语法是：

> let boundFunc = func.bind(context);

将调用传递给 `func` 并设定 `this=context`

```javascript
    let user = {
      firstName: "john",
      sayHi() {
        console.log(`hello, ${this.firstName}!`)
      }
    }
    
    setTimeout(user.sayHi, 1000) // hello, undefined

	// 取了方法 user.sayHi 并将其绑定到 user, sayHi变为绑定后的方法
	let sayHi = user.sayHi.bind(user)
    
    // 在与对象分离的情况下运行
    sayHi() // hello, john!

	setTimeout(sayHi, 1000); // Hello, John!
```

#### 原型与原型链

**[[prototype]]**

- 每个函数都有一个`prototype`, 默认指向一个`object`空实例对象(原型对象)
- 但是`Object`除外, 它的`prototype`不是实例`Object.prototype.__proto__ ` 是 `null`

- 原型对象有个`constructor`,  指向函数对象

**原型链**

作用：查找对象属性(方法)

1. 查找函数自身内部的方法
2. 如果函数自身内部没有这个方法就去找这个函数\__proto__内的方法
3. 没有找到就沿着\__proto__向上查找
4. Object的显式原型的隐式原型为null, 即原型链的尽头
5. 原型对象也是Object对象的实例，原型的回溯最多到Object实例的原型为止，也就是原型对象的原型为止

#### 执行上下文与执行上下文栈

**回顾声明提升**

- 变量声明提升

  `var`来声明或声明并赋值时，无论位置，声明最先执行

- 函数声明提升

  - 无论函数在何处执行，函数声明首先发生

  - 函数表达式除外，因为是使用var来声明

**执行上下文**

- 全局执行上下文
  - 在执行全局代码前将`window`确定为全局执行上下文
  - 对全局数据进行预处理
    - `var`定义的全局变量处理为`undefined`, 添加为`window`的属性
    - 为`function`声明的全局函数赋值，添加为``window``的方法
    - `this`赋值为`window`
  - 开始执行全局代码
- 函数执行上下文
  - 在调用函数(只有调用才会产生执行上下文), 准备执行函数体之前, 创建对应的函数执行上下文对象(虚拟的，存在于栈中)
  - 对局部数据进行预处理
    - 为形参变量赋值，添加为执行上下文的属性
    - `arguments` 赋值为实参列表, 添加为执行上下文属性
    - `var`定义的局部变量赋值为`undefined`，添加为执行上下文的属性
    - 为`function`声明的函数赋值，添加为执行上下文的方法
    - `this`赋值为调用函数的对象
  - 开始执行函数体代码

**执行上下文栈**

1. 全局代码执行前, JS引擎会创建一个上下文栈来存储管理执行上下文
2. 在全局执行上下文(window)确定后, 将其添加到栈中
3. 在函数执行上下文创建后，将其压入栈中
4. 在当前函数执行完后，栈顶对象出栈
5. 当所有代码执行完毕，栈中只剩下`window`

#### 通过递归理解执行上下文和堆栈

递归(recursion)是一种编程模式。当一个函数解决一个任务时，在解决的过程中它可以调用很多其它函数。在部分情况下，函数会调用 **自身**。这就是所谓的 **递归**。

例如一个计算`x`的`n`次方的函数：

```javascript
function pow(x, n) {
    if(n == 1) {
        return x
    } else {
        return x * pow(x, n - 1)
    }
}
pow(2, 3)
// 在函数内部 pow 递归地调用自身 直到 n == 1
```

当一个函数进行嵌套调用时，将发生：

1. 当前函数暂停
2. 当前函数的执行上下文保存至执行上下文堆栈
3. 执行嵌套调用
4. 嵌套调用结束后，从堆栈中恢复之前的执行上下文，并从停止的位置恢复外部函数

- 调用 `pow(2, 3)` 时：

  1. 产生执行上下文，存储变量：`x = 2, n = 3`，执行流程在函数的第 `1` 行

  2. 条件 `n == 1` 结果为假，所以执行流程进入 `if` 的第二分支

  3. 变量相同，但是行改变了，因此现在的上下文是`context: {x: 2, n: 3, at line 5} call: pow(2, 3)`

  4. 计算 `x * pow(x, n - 1)`, 带有新参数的新的 `pow` 子调用 `pow(2, 2)`

- 为了执行嵌套调用，JavaScript 会在 执行上下文堆栈中记住当前的执行上下文

  1. 当前上下文被“记录”在堆栈的顶部
  2. 为子调用创建新的上下文
  3. 当子调用结束后 —— 前一个上下文被从堆栈中弹出，并继续执行

- 完成子调用后 —— 很容易恢复上一个上下文，因为它既保留了变量，也保留了当时所在代码的确切位置
- 执行 `pow(2, 1)` 时， `if` 的第一个分支生效, 不再有新的嵌套调用，所以函数结束，返回 `2`
- 函数完成后，就不再需要其执行上下文了，因此它被从内存中弹出。前一个上下文恢复到堆栈的顶部
- 恢复执行 `pow(2, 2)`。它拥有子调用 `pow(2, 1)` 的结果，可以完成 `x * pow(x, n - 1)` 的执行，并返回 `4`
- 前一个上下文被恢复， 得到了结果 `pow(2, 3) = 8`

**面试题：**

```javascript
console.log('global begin:' + i)
var i = 1
foo(1)
function foo(i) {
    if(i == 4) {
        return
    }
    console.log('foo() begin:' + i)
    foo(i + 1)
    console.log('foo() end:' + i)
}
console.log('global end:' + i)
// 打印顺序依次为:global begin:undefined -> foo() begin: 1 -> foo() begin: 2 -> foo() begin: 3 -> foo() end: 3 -> foo() end: 2 -> foo() end: 1 -> global end:1
```

#### 作用域与作用域链

**作用域**

作用：隔离变量

**作用域链**

当代码要访问一个变量时 —— 首先会搜索内部词法环境，然后搜索外部环境，然后搜索更外部的环境，以此类推，直到全局词法环境

**词法环境**

```javascript
// 一个两层嵌套的词法环境
function makeCounter() {
    let count = 0
    
    return function() { 	// counter.[[Environment]] -> count: 0
        return count++
    }
}

let counter = makeCounter()
console.log(counter()) // 0
console.log(counter()) // 1
```

- 所有的函数在创建时都会记住创建它们的词法环境——名为 `[[Environment]]` 的隐藏属性，该属性保存了对创建该函数的词法环境的引用
- 因此， `counter.[[Environment]]` 有对 `{count: 0}` 词法环境的引用，与函数被在哪儿调用无关
- 调用 `counter()` 时， 会为该调用创建一个新的词法环境
- 当 `counter()` 中的代码查找 `count` 变量时，它首先搜索自己的词法环境（为空，那里没有局部变量），然后是外部 `makeCounter()` 的词法环境
- 在变量**所在的词法环境**中更新变量

#### 闭包

**何为闭包?**

MDN对闭包的定义为:

> 闭包是那些能够访问自由变量(指在函数中使用的，但既不是函数参数也不是函数的局部变量的变量)的函数

**产生闭包的条件?**

- 函数嵌套
- 内部函数引用了外部函数的数据且已经被定义
- 外部函数执行

**理论上来说，所有的函数都是闭包?**

是的， 函数创建的时候就将上层上下文的数据保存起来了，哪怕是简单的全局变量也是如此，因为函数中访问全局变量就相当于是在访问自由变量，这个时候使用最外层的作用域

**从实践角度：以下函数才算是闭包**

- 即使创建它的上下文已经销毁，它仍然存在（比如，内部函数从父函数中返回），闭包的作用域包含它自己的作用域，以及父函数的作用域和全局作用域，通常函数的作用域及其所有所有变量在函数执行结束后被销毁，但在创建一个闭包后，这个函数的作用域就会一直保存到闭包不存在为止。
- 在代码中引用了自由变量

**闭包的作用**

- 外部函数在执行完毕后能使它的内部变量仍然存留在内存中, 即延长了变量的生命周期
- 未成为闭包中的变量会在外部函数执行完毕后被释放
- 让函数外部可以操作到函数内部的数据但并非将变量直接暴露给外部

**垃圾回收机制**

通常，函数调用完成后，会将词法环境和其中的所有变量从内存中删除，因为现在没有任何对它们的引用了

但是，如果有一个嵌套的函数在函数结束后仍可达，则它将具有引用词法环境的 `[[Environment]]` 属性:

```javascript
function f() {
    let value = 123
    return function() {
        console.log(value)
    }
}
	let g = f() // 外部函数执行
    g() // 123  g.[[Environment]] 存储了 f() 调用的词法环境
```

如果多次调用 `f()`，并且返回的函数被保存，那么所有相应的词法环境对象也会保留在内存中

以计数器为例：

```javascript
function makeCounter() {
    let count = 0
    return function() {
        count++
    }
}
let counter1 = makeCounter() // counter1.[[Environment]]保存了makeCounter()的环境变量,具有独立的外部词法环境
let counter2 = makeCounter() // counter2.[[Environment]]保存了makeCounter()的环境变量,具有独立的外部词法环境

console.log(counter1()) // 0
console.log(counter1()) // 1

console.log(counter2()) // 0
console.log(counter2()) // 1
```

**面试题1：**

```javascript
let name = "window"
const object = {
    name: "object",
    function() {
        console.log(this) // object
        return function() {
            console.log(this.name) // window
        }
    }
}
object.function()()

let name = "window"
const object = {
    name: "object",
    function() {
        let _this = this
        return function() {
            console.log(_this.name) // object
        }
    }
}
```

**面试题2：**

```javascript
  <button>one</button>
  <button>two</button>
  <button>three</button>
  <button>four</button>
  
  var btns = document.getElementsByTagName('button')
  for(var i = 0; i < btns.length; i++) {
  btns[i].addEventListener('click', function() {
       console.log("第" + i + "个按钮被点击")
     })
   }
// 由于var没有自己的作用域，i的变量无法保存，for遍历结束后，无论点击哪个按钮，都是循环结束后的值

// 解决方式一：闭包 for的每一次遍历都保存不同的环境来记录i的值
  for(var i = 0; i < btns.length; i++) {

    (function(i){
      btns[i].addEventListener('click', function() {
        console.log("第" + i + "个按钮被点击")
      })
    })(i)

  }
// 解决方式二：let
  for(let i = 0; i < btns.length; i++) {
  btns[i].addEventListener('click', function() {
       console.log("第" + i + "个按钮被点击")
     })
   }
```

#### 回顾对象创建模式

**`Object`构造函数模式**

- 先`new`来创建空对象, 再动态添加新的属性/方法
- 使用场景: 起始时不知道对象内部的数据
- 问题: 语句太多

```javascript
var obj = new Object()

obj.name = 'john'
obj.setName = function() {
	this.name = 'kavin'
}
```

**对象字面量模式**

- 套路: 使用{}创建空对象, 再动态添加新的属性/方法
- 使用场景: 起始时已知对象内部的数据
- 问题: 如果创建多个对象, 代码会重复

```javascript
var obj = {
    name: 'john',
    setName: function() {
        this.name = 'kevin'
    }
}
```

**工厂模式**

- 套路: 使用工厂函数动态创建对象并返回
- 使用场景: 需要创建多个对象
- 问题: 对象没有具体类型, 都是`Object`

```javascript
function createPerson(name, age) {
    var obj = new Object()
    obj.name = 'john',
    obj.age = 21,
    obj.setName = function() {
        this.name = 'kevin'
    }
    return obj
}
```

**构造函数模式**

- 自定义构造函数, 使用`new`创建
- 使用场景: 需要创建多个类型确定的对象
- 实例化多次会使同样的方法重复占用内存空间

```javascript
funcrion CreatePerson(name, age) {
   	this.name = name
    this.age = age
    this.setName = function() {
        this.name = 'kevin'
    }
}
let person = new CreatePerson('john', 31)
```

**构造函数+原型对象组合模式**

- 添加方法时使用原型对象来添加

```javascript
function CreatePerson(name, age) {
    this.name = name
    this.age = age
}
CreatePerson.prototype = {
    setName: function() {
        this.name = 'kevin'
    }
}
```

#### 继承的多种方式和优缺点

###### 1. 原型链继承

- 关键之处：让子函数的原型对象成为父函数的实例

- 缺点

  如果父函数有一个变量为引用类型，任何一个实例修改这一变量会导致所有实例的相关属性被修改

```javascript
function Parent () {
    this.names = ['kevin', 'daisy']
    this.age = 31
}

Parent.prototype.getAge = function () {
    console.log(this.age)
}

function Child() {}

// 关键步骤：让子函数的原型对象成为父函数的实例
Child.prototype = new Parent() // Child.prototype.__proto__ === Parent.prototype 

var child = new Child()
child.getAge() // 31

child.names.push('yayu')

console.log(child.names) // ['kevin', 'daisy', 'yayu']
console.log(child2.names) // ['kevin', 'daisy', 'yayu']
```

###### 2. 借用构造函数(经典继承/假继承)

- 创建父类型构造函数
- 创建子类型构造函数
- 在子类型构造函数中使用`call/apply` 调用父类型构造函数

```javascript
    function Parent() {
      this.names = ['kevin', 'daisy']
    }

	function Child() {
      Parent.call(this)
    }

    var child = new Child()
    child.names.push('yayu')
    console.log(child.names) // ['kevin', 'daisy', 'yayu']

    var child1 = new Child()
    console.log(child1.names) // ['kevin', 'daisy']
```

优点：

- 避免了引用类型的属性被所有实例共享
- 可以在 `Child` 中向 `Parent` 传参

```javascript
	function Parent(name) {
      this.name = name
    }

    function Child(name) {
      Parent.call(this, name)
    }

    var child1 = new Child('kevin')
    console.log(child1.name) // kevin

    var child2 = new Child('daisy')
    console.log(child2.name) // daisy
```

###### 3. 组合继承

原型链继承和经典继承双剑合璧

优点：融合原型链继承和构造函数的优点，是 JavaScript 中最常用的继承模式

缺点：调用了两次父构造函数

```javascript
    function Parent(name) {
      this.name = name
      this.colors = ['red', 'blue', 'green']
    }

    Parent.prototype.getName = function() {
      console.log(this.name)
    }

    function Child(name, age) {
      Parent.call(this, name)
      this.age = age
    }
	// 设置子类型实例的原型
    Child.prototype = new Parent()
    console.log(Child.prototype.constructor) // Parent
    Child.prototype.constructor = Child
    console.log(Child.prototype.constructor) // Child

    var child1 = new Child('kevin', 21)
    child1.colors.push('yellow')
    console.log(child1.name) // kevin
    console.log(child1.colors) // ['red', 'blue', 'green', 'yellow']

    var child2 = new Child('daisy', 12)
    console.log(child2.age) // 12
    console.log(child2.colors) // ['red', 'blue', 'green']
```

###### 4. 原型式继承

 就是ES5 `Object.create` 的模拟实现，将传入的对象作为创建的对象的原型

```javascript
function createObj(o) {
    function F(){}
    F.prototype = o;
    return new F();
}
```

缺点：包含引用类型的属性值始终都会共享相应的值，这点跟原型链继承一样

```javascript
    var person = {
      name: 'kevin',
      friends: ['daisy', 'kelly']
    }
    
    var person1 = createObj(person)
    var person2 = createObj(person)
    
    person1.name = 'yuya'
    person1.friends.push('jojo')
    console.log(person2.name) // kevin
    console.log(person2.friends) // ['daisy', 'kelly', 'jojo']
```

###### 5. 寄生式继承

- 创建父类型对象
- 创建子类型构造函数
- 在子类型构造函数中使用`Object.create` 创建隐式原型为父类型的对象

```javascript
function createObj(o) {
    var clone = Object.create(o) // 创建一个以o为原型的新对象clone
    clone.sayName = function() {
        console.log('hi!' + this.name)
    }
    return clone
}

var obj1 = {
    name: 'kevin'
}

var student1 = new createObj(obj1)
student1.sayName() // hi! kevin
```

缺点：跟借用构造函数模式一样，每次创建对象都会创建一遍方法

###### 6. 寄生组合式继承

借用组合继承+寄生继承

高效之处：只调用了一次 `Parent` 构造函数，并且因此避免了在 `Parent.prototype` 上面创建不必要的、多余的属性

寄生组合式继承是引用类型最理想的继承范式

```javascript
    function Parent(name) {
      this.name = name
      this.colors = ['red', 'blue', 'green']
    }

    Parent.prototype.getName = function() {
      console.log(this.name)
    }

    function Child(name, age) {
      Parent.call(this, name)
      this.age = age
    }
	// 关键的三步 不使用 Child.prototype = new Parent()而是间接的让 Child.prototype访问Parent.prototype 
    var F = function(){}

    F.prototype = Parent.prototype

    Child.prototype = new F()

    var child1 = new Child('kevin', 17)
    child1.getName()
```

#### 进程与线程

**进程**

程序的一次执行, 在内存中占用一片独立的内存空间

**线程**

- 是进程里的一个独立执行单元
- 是程序执行的一个完整的流程
- 是CPU的最小调度

**单线程与多线程**

- 多线程优点
  - CPU利用效率高
- 多线程缺点
  - 创建多线程开销
  - 线程间切换开销
  - 死锁与状态同步问题
- 单线程优点
  - 顺序编程简单易懂
- 单线程缺点
  - 效率低

**前端的单线程与多线程**

- 单线程是javascript这门语言的基石
  - 作为浏览器脚本语言主要用途在于与用户互动及操作DOM, 这决定必须为单线程执行, 否则有严重的同步问题
- 使用H5的web workers可以多线程运行
- 浏览器运行是多线程(chrome与新版IE)

#### JavaScript的事件循环机制

从上文的执行上下文与堆栈部分我们已经知道，当我们调用一个方法的时候，js会生成一个与这个方法对应的执行上下文（context），其中存在着这个方法的私有作用域，上层作用域的指向，方法的参数，这个作用域中定义的变量以及这个作用域的`this`对象。这个地方被称为执行栈。

但当一个异步代码（如发送ajax请求数据）执行后，情况有所不同。

**事件队列**

- js引擎遇到一个异步事件后并不会一直等待其返回结果，而是会将这个事件挂起，继续执行执行栈中的其他任务
- 当一个异步事件返回结果后，js会将这个事件加入与当前执行栈不同的另一个队列，我们称之为事件队列, 队列先进先出
- 执行栈中的所有任务都执行完毕， 主线程处于闲置状态时， 事件队列中取出排在第一位的事件，并把这个事件对应的回调放入执行栈中
- 形成了一个无限的循环， 称为“事件循环（Event Loop）”

**宏任务与微任务**

不同的异步任务被分为两类：微任务（micro task）和宏任务（macro task）

**宏任务**

- `setInterval()`
- `setTimeout()`

**微任务**

- `new Promise()`
  - `new Promise` 中传入的执行器函数是同步函数
  - `promise`的处理程序 `.then`, `.catch` 和`.finally` 是异步函数
  - 即便一个promise立即被`resovle`， `.then` 下面的代码也会在这些处理程序之前被执行

- `new MutationObserver()`

**执行顺序**

1. 异步事件返回结果后，根据异步事件的类型，放入对应的宏任务队列，或微任务队列
2. 执行栈执行完毕，先处理所有微任务队列中的全部事件，取出放入执行栈，直到微任务队列为空
3. 宏任务队列中查看，如果存在事件，将对应回调加入当前执行栈
4. 如此反复，进入循环

例：

```javascript
setTimeout(function() {
    console.log(1)
})

new Promise(function(resolve, reject) {
    console.log(2)
    reject(3)
}).catch(function(res) {
    console.log(res)
})

console.log(4)  // 执行顺序 2 -> 4 -> 3 -> 1
```

