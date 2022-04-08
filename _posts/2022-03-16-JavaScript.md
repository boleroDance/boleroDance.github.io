---
layout:     post
title:      JavaScript对象与函数
subtitle:   入门基础 归纳笔记
date:       2022-03-16
author:     bolero
header-img: img/post-bg-ios9-web.jpg
catalog:    true
tags:
    - 前端开发
    - JavaScript
    - 对象与函数
---

## 对象

1. 什么是对象

- 多个数据的封装体
- 用来保存多个数据的容器
- 一个对象代表现实中的一个事物

2. 为什么要用对象

- 统一管理多个数据

3. 对象的组成

- 属性: 属性名(字符串)与属性值(任意类型)
- 方法: 一种特别的属性(属性值是一个函数)

4. 如何访问对象内部数据

- .属性名 编码简单, 有时无法使用
- ['属性名'] 编码复杂, 但能随意使用

5. 什么时候必须使用['属性名']?

- 属性名包含特殊字符：- 空格

示例

```javascript
var p = {}
p.content-type = "text/json" //报错
p['content-type'] = "text/json"
console.log(p)
```

- 变量名不确定（属性名不确定时，放入变量）

示例

```javascript
var a = "myage"
var value = 23
p.a = value //报错 变量a并不确定
p[a] = value 
console.log(p[a])
```

###### 创建对象的基础方法

- new所调用的函数是一个构造函数`constructor()`，构造函数是专门用来创建对象的函数

- 使用typeof语句会返回object

  ```javascript
    var obj = new Object();
    console.log(typeof obj);    //"object"
  ```

- 对象字面量

  - 可以使用对象字面量来新建对象，效果与new Object()相同

  - 语法：

    {属性名: 属性值, 属性名: 属性值…};

    `var obj = {name: "a", age: "16", gender: "男"};`

###### 基本和引用数据类型

在js中，内存分为栈内存和堆内存

1. 当调用new新建对象时会在堆内存中创建新的内存空间来新建一个对象
2. 给变量赋值为基本数据类型时，会直接修改栈内存中变量对应的变量值为相应的变量值
3. 给变量赋值为引用数据类型时，会直接修改栈内存中变量对应的变量值为内存地址
4. 两个变量的内存地址指向同一个对象时，修改一个变量的对象的值，另一个变量的对象的值也会发生改变

```javascript
     var obj1 = new Object();
      obj.name = "111";
      var a = obj1,b = obj1;
      a.name = "222";
      console.log(b.name);
      //结果为222
```

#### 对象方法

作为对象属性的函数被称为 **方法**

```javascript
let user = {
  name: "John",
  age: 30
};
user.sayHi = function() { //使用函数表达式创建了一个函数，并将其指定给对象的 user.sayHi 属性
  alert("Hello!");
};
user.sayHi(); // Hello! 
```

###### 方法简写

在对象字面量中，有一种更短的（声明）方法的语法：

```javascript
let user = {
  sayHi() { // 与 "sayHi: function(){...}" 一样
    alert("Hello");
  }
};
```

## 函数

- 函数也是对象，具有普通对象所具有的功能

1. 如何定义函数

   - 函数声明

     ```
     function foo(param1, param2...){
     	语句...
     };
     ```

   - 表达式

     ```
     var foo = function (param1, param2...){
     	语句...
     };
     ```
     
   - 新建函数对象

     ```javascript
     var func = new Fuction("需要执行的代码块")
     // 在构造函数中可以加入字符串参数代码使得调用函数时可以直接执行
     var func = new Function("console.log('a')") // a
     ```

2. 如何调用函数

   - 直接调用 test();
   - 通过对象调用 obj.test();
   - new调用 new test();
   - test.call/apply(obj):将obj对象作为call的参数来调用test函数，临时让test函数变成obj的方法

#### 回调函数

1. 什么函数属于回调函数
   - 自己定义过的
   - 自己没有调用的
   - 能被执行的(某个时刻或者某种条件下)

2. 常见的回调函数
   - dom事件回调函数
   - 定时器回调函数
   - ajax请求回调函数
   - 生命周期回调函数

#### IIFE(Immediately-Invoked Function Expression)

形式：

```javascript
(function(){
    console.log("..");
    var a = 3;
    console.log(a);
})();
```

作用：

 - 隐藏实现
 - 防止污染全局作用域
 - 最终用它来编写js模块

示例：

```javascript
;(function(){
    var a = 1
    function test(){
        console.log(++a)
    }
    window.$ = function(){ //向外暴露一个全局函数
        return {
            test: test
        }
    }
})()
$().test()// 2
```

#### 作用域

- 全局作用域

  + 直接写在script标签中的代码都属于全局作用域，它在打开页面时创建，在关闭页面时销毁 全局作用域中所有的变量可以在页面的任意部分被访问到

  ```
   var a = 1;
        function func1(){
            console.log(a);
        }
        //结果为1
  ```

  - 全局作用域中所有声明的变量都会被创建成window对象的属性

  ```
        var a = 1;
        console.log(window.a);
  ```

  - 变量的提前声明

  当使用var来声明或者声明并赋值变量时，无论声明位置在何处，声明本身这个语句会在当前script标签中的最顶端被执行

  ```
  console.log(a);
    var a = 1;
    //结果为undefined而不是报错，因为var a;这条语句已经在代码最顶端被执行过了
    console.log(a);
    a = 1;
    //报错，a未被声明
  ```

  - 函数的提前声明

  无论函数在何处被声明，函数声明本身会在任何代码前被执行

  ```
    func1();
    function func1(){
        console.log("1");
    } // "1"
  ```

  但是对于函数表达式来说，由于使用var来声明函数，因此只符合变量提前声明的特性

  ```
    func1();
    var func1 = function(){
        console.log("1");
    }
    //结果报错，undefined不是一个函数
  ```

- 函数作用域

函数作用域在函数执行时创建，在执行完毕后销毁，在函数作用域内部与全局作用域相似

当在函数中使用变量时会先向当前作用域查找，没有则向上一级作用域查找，直到全局作用域，如果全局作用域中没有此变量时报错

```
  var a = 1;
  function func1(){
      function func2(){
          console.log(a);
      }
      func2();
  }
  func1();
  //结果为1       
```

在函数作用域中可以访问到全局作用域的变量，反之不成立

```
  function func1(){
      var a = 1;
  }
  console.log(a);
  //结果为报错，a未定义
```

## This

#### 方法中的this

为了访问该对象，方法中可以使用 `this` 关键字,`this` 的值就是在点之前的这个对象

```
let user = {
  name: "John",
  age: 30,

  sayHi() {
    // "this" 指的是“当前的对象”
    alert(this.name);
  }

};

user.sayHi(); // John
```

#### this可用于任何函数

1. ``this``是什么？

   解析器在调用函数时，都会向函数内部传递一个隐含参数this，this指向一个对象，成为函数执行上下文对象(context)

2. 如何确定``this``的值？

- 任何函数本质都是通过某个对象来调用的, 如果没有指定就是``window``
- 以方法调用时，``this``就是调用方法的那个对象
- 以构造函数形式``new test()``调用时，this是新创建的那个对象
- 以``call``和``apply``调用，this是指定的那个对象

示例：

```javascript
function Test() {
  console.log(this)
  this.getColor = function() {
    console.log(this)
  }
}

Test() // window
var obj = new Test() // Test{}
getColor() // window
obj.getColor() // Test {}

var obj2 = {} 
obj.getColor.apply(obj2) // obj2

setTimeout(function() {
    console.log(this)   //  window 定时器为window自带的方法
},1000)		

const obj = {
    aaa() {
        setTimeout(function() {
            console.log(this)    // window
        })
        console.log(this) // obj
        
        setTimeout(() => {
            console.log(this)    // obj
        })
    }
}
obj.aaa()
```

#### 创建函数的高级方法

###### 工厂方法

```javascript
function createPerson(name, age){
          var obj = new Object();
          obj.name = name;
          obj.age = age;
          retrun obj;
      }
var person = createPerson('jack', 20)
console.log(person instanceof Object) // true
```

缺点：无法得知所创建的是一个什么对象，所以出现了构造函数来解决这个问题

###### 构造函数

- 构造函数在技术上是常规函数。不过有两个约定：

1. 它们的命名以大写字母开头。
2. 它们只能由 `"new"` 操作符来执行。

```javascript
function User(name) {
  // this = {} （隐式创建）
    
  // 添加属性到 this
  this.name = name;
  this.isAdmin = false;
    
  // return this;（隐式返回） 
}

let user = new User("Jack");

alert(user.name); // Jack
alert(user.isAdmin); // false
```

- 当一个函数被使用 `new` 操作符执行时，它按照以下步骤：

1. 一个新的空对象被创建并分配给 `this`。
2. 函数体执行。通常它会修改 `this`，为其添加新的属性。
3. 返回 `this` 的值。

构造器比每次都使用字面量创建要短得多，而且更易于阅读——实现可重用的对象创建代码

- 构造函数改进:

  按照上述代码编写会在给对象创建方法时重复创建函数，当实例化类次数增加时会浪费大量内存，因此需要将重复创建的方法函数变成只创建一次

```javascript
 function Person(name, age){
      this.name = name;
      this.age = age;
      this.sayName = func;
  }
  function func(){
      console.log(this.name);
  var person1 = new Person("aaa", 16);
  var person2 = new Person("bbb", 18);
  console.log(person1.sayName == person2.sayName);
```

但是这种办法会污染全局命名空间并且不够安全，有可能会被其他函数覆盖, 想要解决这个问题需要引出一个概念—原型对象

## 原型对象

###### 函数的prototype属性

- 每个函数对象function都有一个``prototype``属性(在定义函数时自动添加)，它默认指向一个Object空对象(原型对象)
- 实例对象里面有一个``__proto__``属性，也指向同一个原型对象
- 原型对象中有一个属性constructor，它指向函数对象
- 函数中的所有实例对象自动拥有原型对象中的属性或方法

```
function foo(){
}
console.log(foo.prototype) // object{constructor: foo()} 
```

函数中自动拥有原型对象中的两个属性：constructor, \__proto__

+ 原型对象中有一个属性constructor，它指向函数对象

```
console.log(Date.prototype.constructor === Date) //ture
console.log(fun.prototype.constrtuctor === fun) //true
```

- 可以利用原型的特性为类开辟出一个新的公共空间让这个类的每一个实例都可以使用这个公共空间的值或者方法而不会污染全局命名空间

```javascript
function Person(name, age) {
    this.name = name
    this.age = age
}
Person.prototype = function () {
    console.log(this.name)
}
var person1 = new Person('jack', 21)
console.log(person1.__proto__ == Person.prototype) //true
```

+ 所有对象都有原型属性`__proto__`，由于原型对象也是对象，因此也具有原型属性`__proto__`

```
console.log(Person.prototype.__proto__) // Object
```

- Object的实例的原型没有原型

所有对象都是Object对象的实例，包括原型对象，因此原型的回溯最多到Object实例的原型为止，也就是原型对象的C:\Users\THINKPAD\Desktop\codC:\Users\THINKPAD\Desktop\coding\JSreview\函数和对象.htmling\JSreview\函数和对象.html原型为止

```
  var a = new Object();
  console.log(a.__proto__.__proto__);
  //结果为null
```

- 使用`in`语句来查找属性是否属于某个对象时会向它的原型中查找
- 如果不想查找原型中的属性，使用`hasOwnProperty`方法

```javascript
function Animal(){

   }
 Animal.prototype.a = 1
 var dog = new Animal()
 dog.b = 1
 console.log('a' in dog) // true
 console.log(console.log(dog.hasOwnProperty('a')) // false
```

- 当在页面中打印一个对象时，实际上输出的是这个对象的valueOf方法的返回值，因此可以通过修改对象的valueOf方法来修改打印对象时的结果

```javascript
  function Person(name){
      this.name = name;
  }
  Person.prototype.valueOf = function(){
      return "name = " + this.name;
  };
  var person = new Person("a");
  console.log(person);
  结果为name = "a"        //在实际测试中，结果与浏览器相关
```

#### 垃圾回收

- 当创建对象之后对所有这个对象的变量赋值为null时，这个对象就永远无法被操作，这个对象就称为垃圾
- js拥有自动的垃圾回收机制，不需要也不能手动地回收垃圾，能做的只有将不再使用的对象赋值为null
