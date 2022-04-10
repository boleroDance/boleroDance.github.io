---
layout:     post
title:      Javascript的事件对象
subtitle:   介绍原生事件与自定义事件
date:       2022-04-10
author:     bolero
header-img: img/post-bg-re-vs-ng2.jpg
catalog: 	true
tags:
            - Javascript
            - 基础知识
            - 事件对象
---

### 前言

> 如果说DOM操作是JS与HTML交互的基础，那么事件就是动态交互的核心技术
>
> 在开始之前我们有必要回顾一下响应函数的概念
>
> **响应函数** 或者说个 **处理程序（handler）**—— 一个在事件发生时运行的函数

#### 绑定事件的简单介绍

给一个节点绑定响应函数有两种方法, 这两种方法分别被写进W3C的DOM1与DOM2的规范中

- DOM1方法 

```
window.onclick = function(){}
```

但这种分配处理程序的方式的根本问题是 —— 我们不能为一个事件分配多个处理程序，因此，有必要引入第二种方法：

- DOM2方法

```
document.addEventListener('click', function(){})
```

多次调用 `addEventListener` 允许添加多个处理程序，如下例所示：

```javascript
<input id="elem" type="button" value="Click me"/>

<script>
  function handler1() {
    alert('Thanks!');
  };

  function handler2() {
    alert('Thanks again!');
  }

  elem.onclick = () => alert("Hello");
  elem.addEventListener("click", handler1); // Thanks!
  elem.addEventListener("click", handler2); // Thanks again!
</script>
```

###### 对于某些事件，只能通过`addEventListener` 设置处理程序

例如，`DOMContentLoaded` 事件，该事件在文档加载完成并且 DOM 构建完成时触发

```javascript
// 永远不会运行
document.onDOMContentLoaded = function() {
  alert("DOM built");
};
// 这种方式可以运行
document.addEventListener("DOMContentLoaded", function() {
  alert("DOM built");
})
```

所以 `addEventListener` 更通用

#### 事件对象

为了正确处理事件，当事件发生时，浏览器会创建一个 **`event` 对象**，将详细信息放入其中，并将其作为参数传递给处理程序。

- 可以给响应函数添加形参来调用这个事件对象

```javascript
// 从 event 对象获取鼠标指针的坐标的示例
<input type="button" value="Click me" id="elem">
<script>
  elem.onclick = function(event) {
    // 显示事件类型、元素和点击的坐标
    alert(event.type + " at " + event.currentTarget); 
    alert("Coordinates: " + event.clientX + ":" + event.clientY);
  };
</script>
```

- `event` 对象的一些常用属性

  - `event.type`

    事件类型，这里是 `"click"`

  - `event.currentTarget`

    处理事件的元素。这与 `this` 相同，除非处理程序是一个箭头函数，或者它的 `this` 被绑定到了其他东西上，之后我们就可以从 `event.currentTarget` 获取元素了

  - `event.clientX / event.clientY`

    事件发生时鼠标在窗口相对坐标

  - `pageX/pageY`

    返回触发此事件时鼠标在触发此事件的元素中的X/Y坐标，仅支持IE8以上及其他浏览器

#### 对象处理程序 handleEvent

我们不仅可以分配函数，还可以使用 `addEventListener` 将一个对象分配为事件处理程序。当事件发生时，就会调用该对象的 `handleEvent` 方法

例如：

```javascript
<button id="elem">Click me</button>

<script>
 let obj = {
   handleEvent(event) {
     console.log(event.type)
   }
 }
  
 elem.addEventListener('click', obj) // click
</script>  
```

当 `addEventListener` 接收一个对象作为处理程序时，在事件发生时，它就会调用 `obj.handleEvent(event)` 来处理事件

我们也可以对此使用一个类：

```javascript
<button id="elem">Click me</button>

  class Menu {
    handleEvent(event) {
      switch(event.type) {
        case 'mousedown':
          elem.innerHTML = "Mouse button pressed";
          break;
        case 'mouseup':
          elem.innerHTML += "...and released.";
          break;
      }
    }
  }
  
  let menu = new Menu();
  elem.addEventListener('mousedown', menu);
  elem.addEventListener('mouseup', menu);
```

这里，同一个对象处理两个事件(mousedown/mouseup)。请注意，我们需要使用 `addEventListener` 来显式设置事件，以指明要监听的事件

#### 事件冒泡和捕获

冒泡（bubbling）原理： 当一个事件发生在一个元素上，它会首先运行在该元素上的处理程序，然后运行其父元素上的处理程序，然后一直向上到其他祖先上的处理程序。

假设我们有 3 层嵌套 `FORM > DIV > P`，它们各自拥有一个处理程序：

```javascript
<body>
  <form onclick="alert('form')">
    FORM
    <div onclick="alert('div')">
      DIV
      <p onclick="alert('p')">p</p>
    </div>
  </form>
</body>

<style>
  body * {
    margin: 10px;
    border: 1px solid blue;
  }
</style>
```

我们点击 `<p>`，那么我们将看到 3 个 alert：`p` → `div` → `form`。

![](https://zh.javascript.info/article/bubbling-and-capturing/event-order-bubbling.svg)

######  event.target和currentTarget

- `event.target` —— 引发事件的那个嵌套层级最深的元素, 它在冒泡过程中不会发生变化
- `event.currentTarget`—— “当前”元素，其中有一个当前正在运行的处理程序

如：

```javascript
<script>
  form.onclick  = function(event) {
    alert('form') 
    alert(event.currentTarget) // [object HTMLFormElement]
    alert(event.target) // [object HTMLParagraphElement]
  }
  div.onclick = function(event) {
    alert('div')
  }
  p.onclick = function(event) {
    alert('p')
  }
</script>
```

​	我们点击 `<p>`，那么我们将看到 5 个 alert：`p` → `div` → `form` →`object HTMLFormElement`→ `object HTMLParagraphElement `

###### 停止冒泡

用于停止冒泡的方法是 `event.stopPropagation()`	

通常，没有真正的必要去阻止冒泡。一项看似需要阻止冒泡的任务，可以通过其他方法解决。其中之一就是使用自定义事件，稍后我们会介绍它们

###### 捕获

浏览器在实现时对于事件传播微软与网景有不同的理解方式

- 微软认为事件由子元素向父元素传播

- 网景认为事件由父元素向子元素传播

W3C标准实现时参考两种方法给出了浏览器中事件执行的阶段：

1. 捕获阶段（Capturing phase）—— 事件（从 Window）向下走近元素
2. 目标阶段（Target phase）—— 事件到达目标元素, 事件在目标阶段执行
3. 冒泡阶段（Bubbling phase）—— 事件从元素上开始冒泡

为了在捕获阶段捕获事件，我们需要将处理程序的 `capture` 选项设置为 `true`

```
elem.addEventListener(..., {capture: true})
// 或者，用 {capture: true} 的别名 "true"
elem.addEventListener(..., true)
```

捕获和冒泡：

```javascript
<style>
  body * {
    margin: 10px;
    border: 1px solid blue;
  }
</style>

<form>
	FORM
  <div>
  	DIV
    <p>P</p>
  </div>
</form>

<script>
    // 为文档中的每个元素都设置了点击处理程序，以查看哪些元素上的点击事件处理程序生效了。
  for(let elem of document.querySelectorAll('*')) {
    elem.addEventListener("click", e => alert(`Capturing: ${elem.tagName}`), true);
    elem.addEventListener("click", e => alert(`Bubbling: ${elem.tagName}`));
  }
</script>
```

当点击了 `<p>`，那么顺序是：

1. `HTML` → `BODY` → `FORM` → `DIV`（捕获阶段第一个监听器）：
2. `P`（目标阶段，触发两次，因为我们设置了两个监听器：捕获和冒泡）
3. `DIV` → `FORM` → `BODY` → `HTML`（冒泡阶段，第二个监听器）。

#### 事件委托

- 捕获和冒泡允许我们实现一种被称为 **事件委托** 的强大的事件处理模式。

- 如果我们有许多以类似方式处理的元素，那么就不必为每个元素分配一个处理程序 —— 而是将单个处理程序放在它们的共同祖先上。

- 在处理程序中，我们获取 `event.target` 以查看事件实际发生的位置并进行处理

通过案例理解：

```JavaScript
// 一个9个单元格的表格，我们任务是在点击时高亮显示被点击单元格<td>

  <table id="table">
    <tr>
      <th>chart</th>
    </tr>
    <tr>
      <td>northwest</td>
      <td>north</td>
      <td>northeast</td>
    </tr>
    <tr>
      <td>west</td>
      <td>center</td>
      <td>east</td>
    </tr>
      <tr>
        <td>southwest</td>
        <td>south</td>
        <td>southeast</td>
      </tr>
  </table>
  
  <script>
  table.onclick = function(event) {
    let selected = event.target
    if(selected.style.backgroundColor) { 
      selected.style.backgroundColor = ""
      return
    }
    selected.style.backgroundColor = "pink"
  }
</script>
```

与其为每个 `<td>`（可能有很多）分配一个 `onclick` 处理程序 —— 我们可以在 `<table>` 元素上设置一个“捕获所有”的处理程序

使用 `event.target` 来获取点击的元素并高亮显示它

###### 事件委托的其他用途

我们想要编写一个有“保存”、“加载”和“搜索”等按钮的菜单。并且，这里有一个具有 `save`、`load` 和 `search` 等方法的对象。

我们可以为整个菜单添加一个处理程序，并为具有方法调用的按钮添加 `data-action` 特性（attribute）：

```javascript
<div id="menu">
  <button data-action="save">Save</button>
  <button data-action="load">Load</button>
  <button data-action="search">Search</button>
</div>

<script>
  class Menu {
    constructor(elem) {
      // 将this.onClick绑定给this,否则内部的this将引用DOM元素（elem）,而不是Menu对象
      console.log(this) // Menu
      elem.onclick = this.onClick.bind(this); 
    }

    save() {
      alert('saving');
    }

    load() {
      alert('loading');
    }

    search() {
      alert('searching');
    }

    onClick(event) {
      let action = event.target.dataset.action;
      if (action) {
        this[action]();
      }
    };
  }

  new Menu(menu);
</script>
```

