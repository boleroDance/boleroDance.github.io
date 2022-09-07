---
layout:     post
title:      javascript模块
subtitle:   模块化的演变
date:       2022-09-07
author:     bolero
header-img: img/post-bg-github-cup.jpg
catalog: 	true
tags:
            - Javascript
            - 进阶知识
            - 模块化
---

### 前言

>javascript本身没有模块的概念, 但当项目足够庞大时, 变量很可能会互相污染导致不必要的后期维护, 模块化则是在一步步更轻松解决这个问题的环境下诞生的

### 引入模块化

**原始写法**

直接将需要声明的变量写在全局中

如：

```
function foo() {}
function bar() {}
foo()
bar()
```

缺点：

污染全局

**namespace方式**

将需要声明的变量封装到一个函数中

```
let module = {
	foo() {}
	bar() {}
}
module.foo()
module.bar()
```

特点：

本质是对象，可以随时获取内部数据，不安全

**IIFE**

通过闭包的方式，只暴露想要暴露的数据或方法

```javascript
let module = (function() {
  function private() {
    console.log('private')
  }
  function foo() {
    console.log('foo')
  }
  return {foo: foo}
})()
// module保存在全局
module.foo() // foo
module.private() // module.private is not a function
```

- 特点

  函数是唯一的local scope

  形成模块的原始雏形

### 模块化的理念

- 为什么要模块化
  1. 现代网页更像一个web app
  2. 代码复杂度逐渐上升
  3. 需要高解耦性的js文件
  4. 需要部署一种最大优化http数量的网站
- 模块化的好处
  1. 避免命名冲突
  2. 更好的分离，按需加载
  3. 更高复用性
  4. 高可维护性

- 通过页面引入 `script` 的方式
  1. 提高了http数量，降低网站性能
  2. 引入顺序改变会导致报错
  3. 依赖关系模糊
  4. 难以维护

### 模块化规范

#### commonJS

- 每个文件都可以当做一个模块, 反映为文件名就是模块名
- 常用于服务器端, 因为文件存储在本地, 同步加载不影响响应速度且nodeJS默认支持此规范
- 如果用于浏览器端需要提前编译打包
- 属于运行时加载的模块化, 本质还是对变量的一种赋值, 即值拷贝

**基于浏览器端**

- 文件目录

```
project
  -|dist
  	-|bundle.js
  -|src
    -|index.html
    -|mathUtils.js
    -|main.js
```

- 核心语法

  - 导出

    ```javascript
    var admmin = true
    function foo() {...}
    module.export = {
    	admin,
    	foo
    }
    ```

  - 导入

    ```
    const { admin, foo } = require('./..')
    ```

- 使用browserify打包
  -  npm install -g browserify
  - npm install -save-dev browserify
  - browerify src.js -o destination.js

#### ES6模块化

- 属于静态关系的模块化定义, 引入值时是引用地址的拷贝

- 依赖模块需要编译打包处理

- 文档结构

  ```
  JS
  	-| build
  		-|main.js
  		-|module1.js
  	-|dist
  		-|main.js
  	-|node_modules
  	-|src
  		-|main.js
  		-|module1.js
  	-| index.html
  	-| .babelrc
  	-|package-lock.json
  	-|package.json
  ```

- 核心语法

  - 导出(暴露)

    ```javascript
    // 常规暴露
    var admmin = true
    function foo() {...}
    
    export {
      admin,
      foo
    }
    
    // 默认暴露
    function foo() {...}
    
    export default foo // import bar from './.' 
    ```

  - 导入

    ```
    import { foo } from './..'
    ```


**es6模块化示例**

- 初始化项目

```
js
	|src
		|module1.js
		|main.js
	|index.html
```

- 在工程的根目录下，新建文件`package.json`

  ```
  {
   "name": "es6-babel-browserify",
   "version": "1.0.0"
  }
  ```

- 环境配置

  1. 安装babel 和 browserify

  ```
  npm install babel-cli -g
  
  npm install babel-preset-es2015 --save-dev
  
  npm install browserify -g
  ```

  2. 在根目录下新建文件`.babelrc`

  ```
  {
  	"presets": [
  		"es2015"
  	],
  	"plugins": []
  }
  ```

- 编写代码

  1. module1.js：

  ```javascript
  export function pow(x, n) {
    if(n == 1) {
      return x
    } else {
      return x * pow(x, n-1)
    }
  }
  
  export function add(n1, n2) {
    return n1 + n2
  }
  ```

  	2. main.js：

  ```
  import { pow, add} from './module1'
  ```

  3. index.html

  ```
  // 引入main.js
  <body>
      <script src="dist/main.js"></script>
  </body>
  ```

- 编译转换

  - 利用 babel 将 ES6 转换为 ES5：

  ```
  babel src -d build      //将src目录下的所有ES6文件转化为ES5文件，并放在build目录下（build目录会被自动创建）
  ```

  - 利用`Browserify`编译打包 `build`目录下的 ES5 文件：

  ```
  browserify build/main.js -o dist/main.js     //dist目录需要手动创建
  ```

- 以后，每次修改完ES6的代码，就要执行上面的两个命令，重新生成新的js文件

### commonJS 与ES6 模块化的区别

- commonJS在引入变量时是值拷贝, 而ES6模块化是引用拷贝
- commonJS是在运行时进行加载, ES6模块化是编译时的静态定义

通过代码证实：

- commonJS

```javascript
// module1
var value = 3
var func = function() {
    console.log(value)
}

module.exports = { value, func}

// module2
var module1 = require('./module1')
 
console.log(module1.value) // 3
module1.value++
module1.func() // 3
```

- ES6模块化

```javascript
// module1
var value = 3
var func = function() {
    console.log(value)
}
export { value, func }

// module2
import { value, func } from 'module1'

console.log(value) // 3
value++
func() // 4
```



