---
layout:     post
title:      javascript专题系列(一)
subtitle:  	数组去重？数组扁平？防抖节流？深浅拷贝？一文搞定！
date:       2022-05-18
author:    	bolero
header-img: img/post-bg-github-cup.jpg
catalog: 	true
tags:
    - Javascript
    - Javascript专题系列
---

> 本篇是Javascript专题系列的第一篇，以后将长期更新该专题，大致是对JavaScript手写主要功能的一些总结和拓展。一直觉得写代码的过程就是站在巨人的肩膀上眺望的过程，一些常规API我们早已无需自己手写。但尽管如此，手写这些功能绝对是能使自己更加深入了解JavaScript的！
>
> 本篇将介绍以下需求的JS实现，阅读时请务必打开编辑器边敲便测试呀
>
> - 数组去重
> - 数组扁平
> - 防抖函数、节流函数
> - 深拷贝、浅拷贝

### 数组去重

###### 双重循环

最原始的方法，内层循环和外层循环作对比，如果相等，就把内层循环的值去掉。看起来很笨蛋的方法，但仍然推荐，因为————兼容性赛高！

```javascript
function unique (arr) {
	for(let i = 0; i < arr.length; i++) {
        for(let j = i + 1; j < arr.length; j++) {
            if (arr[i] === arr[j]) {
                arr.splice(j, 1)
                j--
            }
        }
    }
    return arr
}
```

###### indexof 

我们可以用indexOf来简化内层的循环：

```javascript
function unique (arr) {
	let res = []
    for (let i = 0; i < arr.length; i++) {
        if (res.indexOf(arr[i]) === -1) {
            res.push(arr[i])
        }
    }
}
```

###### sort()排序后去重

**sort()方法**

`sort()`方法能够对数组进行原位排序，如果我们对有多个相同值的数组使用sort方法，能够将相同值排在一起，然后我们只需要判断当前元素与上一个元素是否相同：

```javascript
function unique (arr) {
    let res = []
    let sortedArr = arr.sort()
    let isSeen = null
    for (let i = 0; i < sortedArr.length; i++) {
        if(sortedArr[i] !== isSeen) {
            res.push(sortedArr[i])
        }
        isSeen = sortedArr[i]
    }
    return res
}
```

###### ES6提供的方法

随着es6的到来，去重的方法又有了进展。我们可以考虑使用以下几种方法

**map 结合 filter**

map作为es6中一种新数据结构，可以设置任何类型的值作为键：

```javascript
function unique (arr) {
    const res = new Map()
    return arr.filter((item) => !res.has(item) && res.set(item, 1))
}
```

**set结合Array.from()**

set只允许唯一的值， 我们也可以将数组转化为set结构，再通过`Array.from()`方法将set转为数组:

```javascript
function unique (arr) {
    const setArr = new Set(arr)
    // Array.from() 可以接受一个可迭代的或类数组的值，创建一个新数组，并将该对象的所有元素复制到这个新数组
    let res = Array.from(setArr)
    return res
}
```

**set结合...拓展运算符解构**

`...`spread能够将类数组转换为真数组

```javascript
function unique (arr) {
    return [...new Set(arr)]
}
```

### 数组扁平化

**定义**

数组的扁平化，就是将一个嵌套多层的数组 array (嵌套可以是任何层数)转换为只有一层的数组

实现效果：

```
var arr = [1, [2, [3, 4]]]
console.log(flatten(arr)) // [1, 2, 3, 4]
```

**实现方式一: 递归**

循环数组元素，如果还是一个数组，就递归调用该方法：

```javascript
function flatten (arr) {
      var res = []
      for(var i = 0; i < arr.length; i++) {
        if (Array.isArray(arr[i])) {
          res = res.concat(flatten(arr[i]))
        }
        else {
          res.push(arr[i])
        }
      }
      return res
    }
```

**实现方法二：toString**

如果数组的元素都是数字，那么我们可以考虑使用 toString 方法:

```javascript
function flatten (arr) {
  return arr.toString().split(',').map(item => +item)
}
```

缺点： 使用场景十分有限，例如如果数组是 [1, '1', 2, '2'] 的话，这种方法就会产生错误的结果

**实现方法三：扩展运算符**

ES6 增加了扩展运算符，用于取出参数对象的所有可遍历属性，拷贝到当前对象之中：

```javascript
let arr = [1, [2, [3, 4]]]
console.log([].concat(...arr)) // [1, 2, [3, 4]]
```

用这种方法只可以扁平一层, 顺着这个思考，可以继续遍历

```javascript
function flatten (arr) {
    
    while (arr.some(item => Array.isArray(item))) {
        arr = [].concat(...arr)
    }
    return arr
}
```

### 防抖函数和节流函数

前端开发中会遇到一些频繁的事件触发，比如windows 的`scroll`，`mousedown`, `mousemove, keyup`, `keydown`等

**通过示例代码来了解如何频繁触发**

```html
  <input type="text">
  <input type="submit" id="input">
  
  <script>
  var btn = document.getElementById('input')
  btn.addEventListener('click',submitClick)
  
  function submitClick() {
  console.log("触发")
}
  </script>
```

如果我们不停触发click事件，将会不停打印，目前浏览器暂且能反应。

但如果是复杂的回调函数或是 ajax 请求，会发生卡顿现象。

为了解决这个问题，一般有两种解决方案：

1. 防抖：等事件停止触发后n秒才执行函数
2. 节流：持续触发的时候，每 n 秒执行一次函数

#### 防抖函数

```javascript
function debounce (func, delay) {
    var timer = null
    if(timer) clearTimeout(timer)
    timer = setTimeout(function () {
        func()
    }, delay)
}
```

我们通过定时器的方式，将原本要执行的函数放在定时器中，原理很简单

**使用**

```
btn.addEventListener('click', debounce(submitClick, 500))
```

这样，无论点击多少次，始终在500ms后执行函数

**this**

我们先不使用刚才写的``debounce``函数，在``submitClick``中打印一下``this``:

```javascript
function submitClick() {
  console.log(this)
}    // <input type="submit" id="input">
```

this指向调用方法的对象btn

如果我们使用``debounce``函数，在``submitClick``中打印一下``this``:

```javascript
function submitClick() {
  console.log(this)
}    // Window
```

因此，我们需要将this指向正确的对象

**修改第二版代码**

```javascript
function debounce (func, delay) {
    var timer = null
    return function () {
        console.log(this) // <input type="submit" id="input">
        var context = this
        if(timer) clearTimeout(timer)
        timer = setTimeout(() => {
            func.apply(context)
        })
    }
}        
```

**event对象**

JavaScript 在事件处理函数中会提供事件对象 `event`，即window在调用submitClick函数时，会将事件对象作为参数传进去

我们先不使用刚才写的``debounce``函数，在``submitClick``中打印一下事件对象，会得到`PointerEvent`对象

```javascript
function submitClick(e) {
  console.log(e)
}    // PointerEvent {isTrusted: true, pointerId: 0, …}
```

如果在我们实现的debounce 函数中，会打印出``undefined``!

```
function submitClick(e) {
  console.log(e)
}    // undefined
```

**修改第三版代码**

```javascript
function debounce(func, delay) {
	var timer = null
    return function() {
        var context = this
        console.log(arguments) // 打印submitClick的参数列表Arguments [PointerEvent, callee: ƒ, Symbol(Symbol.iterator): ƒ] 
        var args = arguments
        if(timer) clearTimeout(timer)
        timer = setTimeout(function() {
            func.apply(context, args)   // 由于作用域不同，将submitClick的参数列表传过来
        }, delay)
    }
}
```

#### 节流函数

持续触发事件，每隔一段时间，只执行一次事件

实现函数方式：使用时间戳

```javascript
function throttle (func, delay) {
    var prev = 0
    return function () {
        var context = this
        var args = arguments
        var now = +new Date()
        if (now - prev > delay) {
            func.apply(context, args)
            prev = now
        }
    }
}
```

### 深浅拷贝

**拷贝数据**

对于原始数据类型的拷贝，拷贝后生成一份新的数据，修改新数据不会改变原数据的值；

对于对象/数组类型的拷贝，由于拷贝的是原数据的引用地址，修改新数据会改变原数据的值。

#### 浅拷贝方法

**针对对象的浅拷贝**

我们可以用 Object.assign 代替let key in… 循环来进行简单克隆

```javascript
let user1 = {
    name: 'sakuma',
    lastname: 'daisuke',
    age: 29
  }
let clone = Object.assign({}, user1) 
clone.age = 19
console.log(user1.age) // 29
```

但对于对象，仍然使用同一个引用地址:

```javascript
let user2 = {
	name: 'snowman',
    members: {
      leader: 'fukka',
      center: 'raul'
    }
}
let clone2 = Object.assign({}, user2)
clone2.members.leader = 'hikaru'
console.log(user5.members.leader) 
```

**针对数组的浅拷贝**

如果是数组，我们可以利用数组的一些方法比如：`slice`、`concat` 返回一个新数组的特性来实现拷贝

```javascript
var arr = ['old', 1, true, null, undefined]

var new_arr = arr.concat()
// var new_arr = arr.slice() 也能达到一样效果
console.log(new_arr) // ["new", 1, true, null, undefined]
```

但如果数组嵌套了对象或者数组的话, 这种克隆是不彻底的

```javascript
var arr = [{old: 'old'}, ['old']]
var new_arr = arr.concat()

arr[0].old = 'new'
arr[1][0] = 'new'

console.log(arr) // [{old: 'new'}, ['new']]
console.log(new_arr) // [{old: 'new'}, ['new']]
```

#### 深拷贝

**数组和对象的深拷贝**

使用 `JSON.parse(JSON.stringify())`

```javascript
let arr3 = ['old', 1, true, ['old1', 'old2'], {name: 'bolero'}]
let newArr = JSON.parse(JSON.stringify(arr3))

newArr[4].name = 'vivi'
console.log(newArr[4].name) // vivi
console.log(arr3[4].name) // bolero
```

但对于函数的拷贝，返回null

```javascript
let arr4 = [
  function () {
    console.log('a')
  },
  function () {
    console.log('b')
  }
]

let clone4 = JSON.parse(JSON.stringify(arr4))
console.log(clone4) // [null, null]
```

**实现深拷贝**

```javascript
let obj = {
    name: 'snowman',
    gender: 'male',
    mamber: ['hikaru', 'fukka', 'raul', 'sakuma', 'meme']
}

function deepClone (src) {
    // 1. 判断原始数据类型
    let type = Object.prototype.toString.call(src).slice(8, -1)
    let dist = null
    if (['Number', 'String', 'Boolean', 'Null', 'Undefined'].includes(type)) {
        return src
    } else if (type === 'Object') {
        // 非原始数据类型，创建相同结构，逐一复制结构体内容,存入dist
        dist = {}
    } else if (type === 'Array') {
        dist = []
    } else of (type === 'Function') {
        dist = src.bind(this)
    }
    // 2. 根据原数据结构创建出相同结构dist后，遍历src逐一复制
    for (let key in src) {
        // 3. 递归判断src中的值是否是引用数据
        if (src.hasOwnProperty(key)) {
            // 只克隆自有属性，排除继承而来的
            dist[key] = deepClone(src[key])
        }
    }
    return dist
}

let clone = deepClone(obj)
console.log(obj === objclone) // false
console.log(obj.member === objclone.member) // false
```

