---
title: 「jQuery 学习笔记02」动画、对象遍历、事件
author: JoyDee
categories: [Web前端, jQuery]
tags: [jQuery, JS]
date: 2021-03-02 15:35:00 +0800
math: true
---

# Chapter 4. 动画

其中下面各方法的三个参数：

+ `speed`：指动画的速度，预定义值有：`"slow"`、`"normal"`、`"fast"` 或表示动画时长的毫秒数值（如：1000）
+ `easing`：用来指定切换效果，默认值：`"swing"`（动画执行效果是“先慢，中间快，最后慢”），另外的预定义值为 `"linear"`（动画执行速度匀速）
+ `fn`：在动画完成时执行的**函数**，每个元素执行一次

## 4.1 默认的显示和隐藏方式

+ `show(speed, easing, fn)`
+ `hide(speed, easing, fn)`
+ `toggle(speed, easing, fn)`

```javascript
$(function(){
   setTimeout(adShow, 3000); //定义定时器，3s后调用adShow方法
   setTimeout(adHide, 8000); //8s后调用adHide方法
});
function adShow(){ //显示广告
    $("#ad").show("slow");
}
function adHide(){ //隐藏广告
    $("#ad").hide("slow");
}
```

## 4.2 滑动的显示和隐藏方式

+ `slideDown(speed, easing, fn)`
+ `slideUp(speed, easing, fn)`
+ `slideToggle(speed, easing, fn)`

## 4.3 淡入淡出的显示和隐藏方式

+ `fadeIn(speed, easing, fn)`
+ `fadeOut(speed, easing, fn)`
+ `fadeToggle(speed, easing, fn)`

从右到左的动画，分别是 淡入淡出、滑动、默认 的显示和隐藏方式

<img src="https://gitee.com/j__strawhat/MyImages/raw/master/jQuery动画淡入淡出.gif" style="zoom:80%;" />

```html
<!DOCTYPE html>
<html>
<head>
    <meta charset="UTF-8">
    <title>Insert title here</title>
    <script type="text/javascript" src="../js/jquery-3.3.1.min.js"></script>
    <script>
        //切换显示和隐藏div
        function toggleDefault() {
            $("#showDiv").toggle(5000, "linear")
        }
        function toggleSlide() {
            $("#showDiv").slideToggle("slow");
        }
        function toggleFn(){
            $("#showDiv").fadeToggle("slow");
        }
    </script>
</head>
<body>
<input type="button" value="点击按钮切换div显示和隐藏" onclick="toggleDefault()">
<input type="button" value="点击按钮切换div显示和隐藏" onclick="toggleSlide()">
<input type="button" value="点击按钮切换div显示和隐藏" onclick="toggleFn()">
<div id="showDiv" style="width:300px;height:300px;background:#276cda">
</div>
</body>
</html>
```

# Chapter 5. 遍历

## 5.1 JS的遍历方式

+ `for(初始化值; 循环结束条件; 步长)`

举例如下：

```javascript
$(function(){
    var cities = $("#city li");
    for(var i = 0; i < cities.length; i++){
        alert(i + ":" + cities[i].innerHTML);
    }
});
```

## 5.2 jQuery 的遍历方式

（一）`jQuery对象.each(callback)`

+ 语法：`jQuery对象.each(function(index, element){})`
  + `index` ：元素在集合中的索引
  + `element`：集合中的每一个元素对象
+ 回调函数返回值：
  + `true`：若当前 `function` 返回值为 `true`，则结束整个循环（相当于 `break`）
  + `false`：若当前 `function` 返回值为 `false`，则结束本轮循环，直接进行下一轮循环（相当于 `continue`）

举例如下：

```javascript
$(function(){
    cities.each(function(index, element){
        //获取li对象的方式一：
        alert($(this).html());
        //获取li对象的方式二：
        alert(index + ":" + $(element).html());
        //输出遍历中每个元素的索引，及其HTML中的文本
        
       	//判断li的文本是否为"广州"，若是则结束循环
        if("广州" == $(element).html()){
            return false;
        }
    });
});
```

（二）`$.each(object, [callback])`

```javascript
$(function(){
   	$.each(cities, function(){
		alert($(this).html());
	}); 
});
```

（三）`for(元素对象 of 容器对象)`（jQuery 3.0版本之后所提供的方式）

# Chapter 6. 事件

## 6.1 jQuery 标准的绑定方式

+ `jQuery对象.事件方法(回调函数);`

  > 注：若调用事件方法，不传递回调函数，则会触发浏览器默认行为。

```javascript
$(function(){
   $("#name").click(function(){
       alert("我被点击了...")
   }); //获取name对象，绑定click事件
    
   $("#name").mouseover(function(){
       alert("鼠标来了")
   }); //为name对象绑定鼠标移动到
   
   $("#name").focus(); //让文本输入框获得焦点
});
```

## 6.2 on绑定事件 及 off解除绑定

+ `jQuery对象.on("事件名称", 回调函数)`

+ `jQuery对象.off("事件名称")`

  > 注：若 `off()` 方法不传递任何参数，则将组件上的所有事件全部解绑。

```javascript
$(function(){
   $("#btn").on("click", function(){
      alert("我被点击了...");
   }); //使用on给按钮绑定单机事件
   $("#btn2").click(function(){
      $("#btn").off(); //将btn按钮上的所有事件解绑
   });
});
```

## 6.3 事件切换：toggle

+ `jQuery对象.toggle(fn1, fn2, ...)`：当点击 jQuery 对象所对应的组件后，会执行 `fn1`；第二次点击时会执行 `fn2`……

  > 注：1.9 版本中的 `toggle()` 方法被删除，需用 jQuery Migrate（迁移）插件来恢复此功能。
  >
  > ```javascript
  > <script src="../js/jquery-migrate-1.0.0.js" type="text/javascript" charset="utf-8"></script>
  > ```

# Chapter 7.  jQuery 增强

1. `$.fn.extend(object)`：对象进行方法拓展，举例如下：

   ```javascript
   $.fn.extend({ 
       check:function(){  //定义的check()方法，使得所有jQuery对象均可调用该方法
           this.prop("checked", true); //复选框选中
       }, //其中this对象，为“调用该方法”的jQuery对象
       uncheck:function(){
           this.prop("checked", false); //复选框不选中
       }
   });
   $(function(){
      $("#btn-check").click(function(){
         $("input[type='checkbox']").check();
      }); //获取复选框对象，并将其全部置为选择
       
      $("#btn-uncheck").click(function(){
         $("input[type='checkbox']").uncheck();
      }); //选择所有复选框对象,并将其全部置为取消选择
   });
   ```

2. `$.extend(object)`：全局进行方法拓展，举例如下：

   ```javascript
   $.extend({
      max:function(a, b){
          return a >= b ? a : b;
      },
      max:function(a, b){
          return a <= b ? a : b;
      }
   });
   var mymax = $.max(4, 3);
   var mymin = $.min(1, -2);
   ```

   

