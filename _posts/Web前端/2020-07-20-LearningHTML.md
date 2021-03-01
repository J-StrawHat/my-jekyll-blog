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

## 1.3 jQuery 对象和 JS 对象

jQuery 对象和 JS 对象的方法是不通用的。

两者相互转换：

+ 将 jQuery 对象转换为 JS 对象：`jQuery对象[索引]` 或者 `jQuery对象.get(索引)`

  ```javascript
  var $divs = $("div"); //通过jQuery方式，来获取div标签的所有HTML元素对象
  $divs[0].innerHTML = "我变成JS对象了!";
  $divs.get(1).innerHTML = "我也变成JS对象了!";
  ```

+ 将 JS 对象转换为 jQuery 对象：`$(你的js对象)`

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

| 选择器     | 语法                       | 描述 |
| ---------- | -------------------------- | ---- |
| 标签选择器 | `$("html标签名")`          | 获得 |
| id 选择器  | `$("#id属性值")`           |      |
| 类选择器   | `$(".class属性值")`        |      |
| 并集选择器 | `$("选择器1,选择器2,...")` |      |

### 2.2.2 层级选择器

| 选择器     | 语法         | 描述 |
| ---------- | ------------ | ---- |
| 后代选择器 | `$("A B")`   | 获得 |
| 子选择器   | `$("A > B")` |      |

### 2.2.3 属性选择器

| 选择器         | 语法         | 描述 |
| -------------- | ------------ | ---- |
| 属性名称选择器 | `$("A B")`   | 获得 |
| 属性选择器     | `$("A > B")` |      |

### 2.2.4 过滤选择器



### 2.2.5 表单过滤选择器

