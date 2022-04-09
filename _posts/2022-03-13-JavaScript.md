---
layout:     post
title:      Javascript基本概念与运算操作符
subtitle:   JS核心语法梳理
date:       2022-03-13
author:     bolero
header-img: img/post-bg-re-vs-ng2.jpg
catalog:    true
tags:
    - JavaScript
---
# 前言

**Javascript最重要也是最基础的就是那些运算操作符了，这些内容是必须时刻掌握一点都不能忘**

### 输出

用于基本的调试

```javascript
document.write() // //向body中写字符串
console.log()
alert()
```

### 字面量和变量

- 字面量即为常量
- 变量可被字面量赋值
- 变量声明和赋值可分开或一起

```javascript
	var a
    a=123
// 声明和赋值同时进行
    var b = 789
```

### 标识符

- 在js中所有可以自主命名的都可以是标识符

- 例如:变量名,函数,属性名都属于标识符
- 只能以字母数字，下划线，$构成
- 不能以数字开头
- 不能使用ES的关键字和保留字
- 一般使用驼峰命名法

### 数据类型

###### 基本数据类型(原始数据类型)

- `string`

- `number`

  - 可以表示整数与浮点数

  - 2进制浮点数以分数表示，不准确 

    ```javascript
    var a = 0.2
    var b = 0.01
    console.log(a + b) // 0.21000000000000002
    ```

  - `NaN`与`Infinity`是数值的字面量，表示非数与无穷
    - `NaN` 是粘性的。任何对 `NaN` 的进一步数学运算都会返回 `NaN`
    - 只有一个例外：`NaN ** 0` 结果为 `1`）

- ``boolean``
  - 只有两个值：`true false`

- ``null``

```javascript
//表示一个空对象
       var a = null
       console.log(typeof a)
//结果为object
```

- ``undefined``
  - 表示声明但未赋值

- ``BigInt``

  - 表示很大的数字，例如用于加密或微秒精度的时间戳

  - 可以通过将 `n` 附加到整数字段的末尾来创建 `BigInt` 值

  ```javascript
  // 尾部的 "n" 表示这是一个 BigInt 类型
  const bigInt = 1234567890123456789012345678901234567890n
  ```

###### 复杂数据类型

- ``object``
  - 用于储存数据集合和更复杂的实体
- `symbol` 
  - 用于创建对象的唯一标识符

###### 强制类型转换

- ``string ``

  - ``a.toString()方法`` 或者调用 ``String()函数``

    - 对于null和undefined会直接进行转换

      ```javascript
      var a = null
      a = String(a)
      console.log(a) // null
      console.log(typeof a) // String
      ```

  - 任何值和字符串相加都会转换成字符串

    ```
    var c = 123
    c = c + ''
    console.log(c)
    ```

- ``number``

  - 方法一: ``Number()``

    - 对于字符串来说如果只包含数字，直接转换成数字，如果包含非数字转换成NaN，空字符串``""``转换成0, 转换出错则输出 `NaN`
    - ``true``转换成1，``false``转换成0
    - ``null``，转换成0
    - ``undefined``，转换成``NaN``

    ```javascript
    var a = null
    a = Number(a)
    console.log(a) // 0
    console.log(typeof a) // number
    
    var a = undefined
    a = Number(a)
    console.log(a) // NaN
    console.log(typeof a) // number
    ```

  - 方法二: ``parseInt()`` 

    - 首先将所有内容转换成字符串再开始解析。
    - 从左到右依次解析，需要非整数直接舍去，第一位非整数返回NaN

  - 方法三:  ``parseFloat()``
    - 与parseInt()相似，只是遇到第一位小数点不会忽略会转换成小数，其余与之相同

  - 方法四： 任何值做-*/运算时都会自动转换为Number,可以利用这一特点做隐式的类型转换

- ``boolean``
  - 一种方法：``Boolean()``
    - 对于数字：只有``0``跟``NaN``会转换成false
    - 对于字符串：只有``""``会转换成false (注意：有空格的空字符串``\" "``转换成true)
    - 对于``null``和``undefined``，只会转换成false

### 进制

- 16进制在数字前加``0x``

- 8进制在数字前加 ``0``

- 在某些浏览器中键入以下代码：

```javascript
var a = "070"
console.log(parseInt(a))
//结果会输出56。因为浏览器将其当做8进制，解决方法是输入第二个参数，强制以10进制输出
parseInt(a, 10)
```

### 运算符

###### ``typeof``运算符

- `typeof` 运算符返回参数的类型
- 对 `typeof x` 的调用会以字符串的形式返回数据类型：

```javascript
typeof undefined // "undefined"

typeof 0 // "number"

typeof 10n // "bigint"

typeof true // "boolean"

typeof "foo" // "string"

typeof Symbol("id") // "symbol"

typeof Math // "object" 

typeof null // "object"  

typeof alert // "function"
```

###### ``+``运算符

- 加号 `+` 被应用于字符串，它将合并（连接）各个字符串

- 只要任意一个运算元是字符串，那么另一个运算元也将被转化为字符串

  ```javascript
  alert('1' + 2 + 2); // "122"，不是 "14"
  ```

-  转化非数字,效果和 `Number()` 相同，但是更加简短（一元运算符优先级高于二元运算符）

  ```javascript
  alert( +true ); // 1
  alert( +"" );   // 0
  
  let a = "2";
  let b = "3";
  // 在二元运算符加号起作用之前，所有的值都被转化为了数字
  alert( +a + +b ); // 5 等同于 alert(Number(a) + Number(b))
  ```

- **自增** `++` 将变量与 1 相加, **自减** `--` 将变量与 1 相减：

  ```javascript
  let counter = 2;
  counter++;      // 和 counter = counter + 1 效果一样，但是更简洁
  alert(counter); // 3
  
  let counter = 2;
  counter--;      // 和 counter = counter - 1 效果一样，但是更简洁
  alert( counter ); // 1
  ```

- 运算符 `++`前置和后置

  - 两者都做同一件事：将变量 与 `1` 相加
  - 区别在于使用 `++/--` 的返回值不同

  ```javascript
  let counter = 1;
  let a = ++counter; // 返回的是新的值 2
  alert(a); // 2
  
  let counter = 1;
  let a = counter++; //  返回的是旧值（做加法之前的值）
  alert(a); // 1
  ```

  - 结论： 
    - 如果自增/自减的值不会被使用，那么两者形式没有区别
    - 如果想要对变量进行自增操作，并且需要立刻使用自增后的值，那么我们需要使用前置形式

  ```javascript
  let counter = 0;
  counter++;
  ++counter;
  alert( counter ); // 2，以上两行作用相同
  
  let counter = 0;
  alert( ++counter ); // 1
  ```

- 自增/和其他运算符的对比

  - 优先级比绝大部分的算数运算符要高

  ```javascript
  let counter = 1;
  alert( 2 * ++counter ); // 4
  
  let counter = 1;
  alert( 2 * counter++ ); // 2
  ```

###### 逻辑运算符``!``,``&&``,``||``

- `!`：两次取非会得到原值的布尔值,可以利用这个特性进行隐式布尔值转换

- 在JS中`&&`与`||`都是属于短路操作，即当一个值满足要求时才会继续执行第二个操作
- 当参数不是boolean值时先会将参数转换成boolean值后再按照以上规则输出原值

```javascript
// 与运算返回第一个假值,没有假值就返回最后一个值
alert( 1 && 0 ); // 0
alert( 1 && 5 ); // 5
// 或运算返回第一个真值, 没有真值就返回最后一个值
alert( null || 0 || 1 ); // 1
alert( undefined || null || 0 ); // 0
```

- 与运算 `&&` 的优先级比或运算 `||` 要高

### 值的比较

- 单等号 `a = b` 表示赋值

- 双等号 `==` 表示相等性检查

  - 对不同类型的值进行比较时，JavaScript 会首先将其转化为数字（number）再判定大小

  ```javascript
  let a = 0;
  alert( Boolean(a) ); // false
  
  let b = "0";
  alert( Boolean(b) ); // true
  
  alert(a == b); // true
  ```

  - `undefined` 和 `null` 在相等性检查 `==` 中不会进行任何的类型转换

  ```javascript
  alert( null == 0 ); // false
  ```

- 严格相等``===``

  - 普通的相等性检查 `==` 由于要做类型转换因此存在一些问题，如：

  ```
  alert( 0 == false ); // true
  
  alert( '' == false ); // true
  ```

  - 严格相等运算符 `===` 在进行比较时不会做任何的类型转换

- ``null``v.s. ``undefined``

```javascript
alert( null === undefined ); // false
alert( null == undefined ); // true 
```

- ``null`` vs ``0``

```
alert( null > 0 );  // false
alert( null == 0 ); // false 
alert( null >= 0 ); // true
```

- ``undefined``
  - `undefined` 不应该被与其他值进行比较,结果均为false
  -  `undefined` 只与 `null` 相等，不会与其他值相等

- ``NaN``与任何值进行运算结果都为false，包括自己

  ```javascript
  console.log(NaN == NaN) //false
  console.log(NaN === NaN) //false
  ```

### 条件语句

- ###### 条件判断语句

  语法1：满足条件只执行紧接着的第一条语句，后面语句与判断语句无关

  ```javascript
        if(表达式)
            语句1;
  ```

  语法2：满足条件执行代码块中的代码，代码块外的代码与判断语句无关

  ```javascript
        if(表达式){
            语句1;
            语句2;
        } 
  ```

  语法3：满足条件执行前一个代码块中的代码，否则执行后一个代码块中的代码

  ```javascript
        if(表达式){
            语句...
        }else{
            语句...
        }
  ```

  语法4：从上到下依次判断，当满足一个表达式后后面的代码不再执行

  ```javascript
        if(表达式){
            语句...
        }else if{
            语句...
        }else if{
            语句...
        }else if{
            语句...
        }else{
            语句...
        }
  ```

  语法5：条件判断语句可以嵌套

  ```javascript
        if(表达式){
            语句...
        }else{
            语句...
            if(表达式){
                语句...
            }
        }
  ```

  语法6：三元运算符

  ```javascript
        let result = condition ? value1 : value2
  ```

- ###### 条件分支语句

  ```javascript
    语法：
       switch(表达式){
           case 值1: 
               语句...
               break;
           case 值2: 
               语句...
               break;
           case 值3: 
               语句...
               break;
           default:
               语句...
               break;
       }
  ```

  `break`语句用来跳出`switch`语句，当没有此语句时，如果表达式的值满足某个case时，后面的语句都会执行

  ```javascript
    var a = 1;
    switch(a){
       case 1:
           console.log("1");
       case 2:
           console.log("2");
       default:
           console.log("其他");
    }
    //结果为1, 2, 其他
  ```

- ###### 循环语句

  - ``while``

    语句：

    - 语法1：

    ```javascript
         while(true){    //1.将表达式写死
             if(条件表达式){  //2.当满足某种条件时退出
                 break;
             }
             语句...
         }
    ```

    - 语法2：

    ```javascript
         var i = 0;     //1.初始化变量
         while(i < 10){  //2.当不满足条件时退出循环
             语句...
             i++;        //3.更新变量
         }
    ```

  - ``do while``

    语句：

    - 语法：

    ```javascript
         do{
             语句...
         }while(表达式)
    ```

    首先执行语句，然后执行条件，当条件为真时，重复执行循环体，也就是说，语句至少执行一次，这区别于while

  - for循环

    ```
    for (begin; condition; step) {
      // ……循环体……
    }
    ```

  
