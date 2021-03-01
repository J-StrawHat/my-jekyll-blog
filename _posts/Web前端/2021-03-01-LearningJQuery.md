---
title: jQuery 学习笔记
author: JoyDee
categories: [Web前端, jQuery]
tags: [jQuery, JS]
date: 2021-03-01 11:42:00 +0800
math: true
---

# Chapter 1. 概述

## 1.1 概念

jQuery 是一个快速、简洁的 JavaScript 框架，是继 Prototype 之后又一个优秀的 JavaScript 代码库。

它封装了 JavaScript 常用的功能代码，提供一种简便的 JavaScript 设计模式，优化 HTML 文档操作、事件处理、动画设计和 Ajax 交互。

## 1.2 快速入门

1. （下载并）引入 jQuery 到网页中，可使用两种方法：

   + 从 [https://jquery.com/download/](https://jquery.com/download/) 下载 jQuery 库

     ```html
     <head>
     <script src="jquery-1.10.2.min.js"></script>
     </head>
     ```

   + 从 CDN（内容分发网络） 中载入 jQuery，如：[jsdelivr](https://www.jsdelivr.com/package/npm/jquery)

     ```html
     <head>
     <script src="https://cdn.jsdelivr.net/npm/jquery@3.5.1/dist/jquery.min.js"></script>
     </head>
     ```

2. 使用：

   jQuery 基础语法：`$(selector).action()`

   ```html
   <!DOCTYPE html>
   <html lang="en">
   <head>
       <meta charset="UTF-8">
       <title>jQuery学习</title>
       <script src="https://cdn.jsdelivr.net/npm/jquery@3.5.1/dist/jquery.min.js"></script>
   </head>
   <body>
       <div id = "div1">我是div1</div>
       <div id = "div2">我是div2</div>
   <script>
       var div1 = $("#div1");
       var div2 = $("#div2");
       alert(div1.html() + " AND " + div2.html());
   </script>
   </body>
   </html>
   ```

## 1.3 jQuery 对象和 DOM 对象

在 JavaScript 中，通过 `getElementById()`、`getElementByClassName()` 和 `querySelector()` 等方法来获取页面中的 HTML 元素，如：

```javascript
var menuDiv = document.getElementById("menuDiv");
```

jQuery 对象和 DOM 对象的方法是不通用的。

两者相互转换：

+ 将 jQuery 对象转换为 JS 对象：

  `jQuery对象[索引]` 或者 `jQuery对象.get(索引)`：将 jQuery 对象转换成 DOM 对象时，可把 jQuery 对象看作一个 DOM 对象数组

  ```javascript
  var $divs = $("div"); //通过jQuery方式，来获取div标签的所有HTML元素对象
  $divs[0].innerHTML = "我变成DOM对象了!";
  $divs.get(1).innerHTML = "我也变成DOM对象了!";
  ```

+ 将 DOM 对象转换为 jQuery 对象：

  `$(你的DOM对象)`：该方法将 DOM 对象封装起来，并返回一个 jQuery 对象
  
  ```javascript
  var divs = document.getElementByTagName("div");
  for(var i = 0; i < divs.length; i++){
      $(divs[i]).html("我变成jQuey对象啦!");
  }
  ```

# Chapter 2. 选择器

## 2.1 基础语法

### 2.1.1 事件绑定

```javascript
//获取元素（以 b1 按钮为例）
$("#b1").click(function(){ //click方法接收方法对象
    alert("b1按钮被点击啦!");
});
```

### 2.1.2 入口函数

```javascript
$(document).ready(function(){
    //执行代码
});
或者
$(function(){
    //执行代码
});
```

等效于 JavaScript 的入口函数：

```javascript
window.onload = function(){
    //执行代码
}
```

|          | `window.onload`                                              | `$(document).ready()`                                      |
| -------- | ------------------------------------------------------------ | ---------------------------------------------------------- |
| 执行时机 | 必须等待网页**全部加载完毕**<br>（包括图片等），然后再执<br>行包裹代码 | 只需等待网页中 **DOM结构加载完毕**<br>就能执行包裹的代码。 |
| 执行次数 | 只能执行一次，若代码中有<br>第二个`window.onload` ，则<br>第一个的执行会被覆盖。 | 可执行**多次**，第 N 次都不会被<br>上一次执行所覆盖        |

### 2.1.3 样式控制：CSS 方法

举例使用：

```javascript
$("#div1").css("backgroundColor", "pink");
或
$("#div1").css("background-color", "red");
```

## 2.2 选择器

### 2.2.1 基本选择器

| 选择器     | 语法                       | 描述                                         |
| ---------- | -------------------------- | -------------------------------------------- |
| 标签选择器 | `$("html标签名")`          | 根据元素的标签名进行匹配                     |
| id 选择器  | `$("#id属性值")`           |                                              |
| 类选择器   | `$(".class属性值")`        |                                              |
| 并集选择器 | `$("选择器1,选择器2,...")` | 将每个选择器匹配的结果合并在一起返回         |
| 通配选择器 | `$("*")`                   | 匹配页面的所有元素，包括 HTML、head、body 等 |

### 2.2.2 层级选择器

| 选择器           | 语法                       | 描述                                       |
| ---------------- | -------------------------- | ------------------------------------------ |
| 后代选择器       | `$("ancestor descendant")` | 获得 `ancestor` 元素中所有子元素           |
| 子选择器         | `$("parent > child")`      | 选取 `parent` 元素中的**直接**子元素       |
| 选择下一个兄弟   | `$("prev+next")`           | 选取紧邻 `prev` 元素之后的 `next` 元素     |
| 选择下面所有兄弟 | `$("prev~siblings")`       | 选取 `prev` 元素之后的 `siblings` 兄弟元素 |

### 2.2.3 属性选择器

| 选择器         | 语法                           | 描述                           |
| -------------- | ------------------------------ | ------------------------------ |
| 属性名称选择器 | `$("A[属性名]")`               |                                |
| 属性选择器     | `$("A[属性名='值']")`          |                                |
|                | `$("A[属性名!='值']")`         |                                |
|                | `$("A[属性名^='值']")`         | 选取属性以某个值开始的元素     |
|                | `$("A[属性名$='值']")`         | 选取属性以某个值结尾的元素     |
|                | `$("A[属性名*='值']")`         | 选取属性中包含某个值的元素     |
| 复合属性选择器 | `$("A[属性名1='值'][...]...")` | 需要**同时满足**多个条件时使用 |

### 2.2.4 简单过滤选择器

| 选择器         | 语法             |
| -------------- | ---------------- |
| 首元素选择器   | `:first`         |
| 尾元素选择器   | `:last`          |
| 非元素选择器   | `:not(selector)` |
| 偶数选择器     | `:even`          |
| 奇数选择器     | `:odd`           |
| 等于索引选择器 | `:eq(index)`     |
| 大于索引选择器 | `:gt(index)`     |
| 小于索引选择器 | `:lt(index)`     |
| 标题选择器     | `:header`        |

### 2.2.5 内容过滤选择器

| 语法              | 描述                                   | 举例                       |
| ----------------- | -------------------------------------- | -------------------------- |
| `:contains(text)` | 选取包含 `text` 内容的元素。           | `$("td:contains('滑雪')")` |
| `:has(selector)`  | 选取含有 `selector` 所匹配的元素。     | `$("td:has('span')")`      |
| `:empty`          | 选取所有**不包含**文本或子元素的空元素 | `$("td:empty")`            |
| `:parent`         | 选取含有子元素或文本的元素             |                            |

### 2.2.6 表单过滤选择器

| 选择器           | 语法        | 描述                                       |
| ---------------- | ----------- | ------------------------------------------ |
| 可用元素选择器   | `:enabled`  | 选取表单中属性为可用的元素                 |
| 不可用元素选择器 | `:disabled` | 选完表单中属性不可用的元素                 |
| 选中选择器       | `:checked`  | 选取表单中被选中的元素（单选按钮、复选框） |
| 选中选择器       | `:selected` | 选取表单中被选中的选项元素（下拉列表）     |

```java
$(function(){
    $('#b1').click(function(){
       $("input[type='text']:enabled") .val("aaa");
    }); //改变表单内可用的<input>元素的值
    $('#b2').click(function(){
       alert($("input[type='checkbox']:checked").length); 
    }); //获取复选框选中的个数
    $('#b3').click(function(){
       alert($("#job > option:selected").length); 
    }); //获取下拉复选框选中的个数
})
```

# Chapter 3. DOM 操作

## 3.1 内容操作

+ `html()`：获取/设置元素的标签体内容
+ `text()`：获取/设置元素的标签体纯文本内容
+ `val()`：获取/设置元素的 `value` 值

```javascript
$(function () {
    var value = $("#myinput").val(); //获取id为myinput的value值
    $("#myinput").val("Luffy"); //设置它的value值为"Luffy"

    var html = $("#mydiv").html(); //获取id为mydiv的标签体内容
    $("#mydiv").html("<p>aaaa</p>"); //设置它的标签体内容

    //<div id = "mydiv"><p><a href = "#">文本</a></p></div>
    var text = $("#mydiv").text(); //返回值："文本"
    $("#mydiv").text("bbb");
    //此时变为：<div id = "mydiv">bbb</div>
})
```

## 3.2 属性操作

（一）通用属性操作

+ `attr()`

（二）对 class 属性操作

