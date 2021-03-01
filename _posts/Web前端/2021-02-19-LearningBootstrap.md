---
title: Bootstrap 学习笔记
author: JoyDee
categories: [Web前端, Bootstrap]
tags: [Bootstrap, 配置]
date: 2021-02-19 10:30:00 +0800
math: true
---

[Bootstrap](https://www.bootcss.com/) 是 HTML、CSS 和 JS 的一个框架，用于开发响应式布局、移动设备优先的 WEB 项目。

<img src="https://gitee.com/j__strawhat/MyImages/raw/master/20210220152710.png" style="zoom: 30%;" />

# 一、Bootstrap 概述

## Bootstrap 包的内容

- **基本结构**：Bootstrap 提供了一个带有网格系统、链接样式、背景的基本结构。
- **CSS**：Bootstrap 自带以下特性：全局的 CSS 设置、定义基本的 HTML 元素样式、可扩展的 class，以及一个先进的网格系统。
- **组件**：Bootstrap 包含了十几个可重用的组件，用于创建图像、下拉菜单、导航、警告框、弹出框等等。
- **JavaScript 插件**：Bootstrap 包含了十几个自定义的 jQuery 插件。您可以直接包含所有的插件，也可以逐个包含这些插件。
- **定制**：您可以定制 Bootstrap 的组件、LESS 变量和 jQuery 插件来得到您自己的版本。

## Bootstrap v3.3.7 环境安装及快速入门

1. 下载预编译的压缩包。

   <img src="https://gitee.com/j__strawhat/MyImages/raw/master/20210220133225.png" style="zoom:67%;" />

2. 解压刚下载完毕的压缩包，便可得到如下的目录结构：

   ```
   bootstrap/
   ├── css/
   │   ├── bootstrap.css
   │   ├── bootstrap.css.map
   │   ├── bootstrap.min.css
   │   ├── bootstrap.min.css.map
   │   ├── bootstrap-theme.css
   │   ├── bootstrap-theme.css.map
   │   ├── bootstrap-theme.min.css
   │   └── bootstrap-theme.min.css.map
   ├── js/
   │   ├── bootstrap.js
   │   └── bootstrap.min.js
   └── fonts/
       ├── glyphicons-halflings-regular.eot
       ├── glyphicons-halflings-regular.svg
       ├── glyphicons-halflings-regular.ttf
       ├── glyphicons-halflings-regular.woff
       └── glyphicons-halflings-regular.woff2
   ```

3. 将上面的三个文件夹复制到你的项目中

4. 创建一个 HTML 页面，其基本模板如下，修改 CSS、JS 的资源文件路径即可。

   > jQuery 的资源文件 `jquery-3.2.1.min.js`，需要去 jQuery 官网额外下载

   ```html
   <!DOCTYPE html>
   <html lang="zh-CN">
     <head>
       <meta charset="utf-8">
       <meta http-equiv="X-UA-Compatible" content="IE=edge">
       <meta name="viewport" content="width=device-width, initial-scale=1">
       <!-- 上述3个meta标签*必须*放在最前面，任何其他内容都*必须*跟随其后！ -->
       <title>Bootstrap 101 Template</title>
   
       <!-- Bootstrap -->
       <link href="https://cdn.jsdelivr.net/npm/bootstrap@3.3.7/dist/css/bootstrap.min.css" rel="stylesheet">
       <!-- 当你已经下载了预编译包后，可以将上面的href改为你本地的bootstrap.min.css路径-->
         
   
       <!-- HTML5 shim 和 Respond.js 是为了让 IE8 支持 HTML5 元素和媒体查询（media queries）功能 -->
       <!-- 警告：通过 file:// 协议（就是直接将 html 页面拖拽到浏览器中）访问页面时 Respond.js 不起作用 -->
       <!--[if lt IE 9]>
         <script src="https://cdn.jsdelivr.net/npm/html5shiv@3.7.3/dist/html5shiv.min.js"></script>
         <script src="https://cdn.jsdelivr.net/npm/respond.js@1.4.2/dest/respond.min.js"></script>
       <![endif]-->
         
       <!-- jQuery (Bootstrap 的所有 JavaScript 插件都依赖 jQuery，所以必须放在前边) -->
       <script src="https://cdn.jsdelivr.net/npm/jquery@1.12.4/dist/jquery.min.js"></script>
       <!-- 加载 Bootstrap 的所有 JavaScript 插件。你也可以根据需要只加载单个插件。 -->
       <script src="https://cdn.jsdelivr.net/npm/bootstrap@3.3.7/dist/js/bootstrap.min.js"></script>
       <!-- 当你已经下载了预编译包后，可以将上面的href改为你本地的bootstrap.min.js路径 --> 
         
     </head>
     <body>
       <h1>你好，世界！</h1>
   
       
     </body>
   </html>
   ```

# 二、Bootstrap的栅格系统（Grid System）

## 响应式设计

响应式设计即是 RWD，**Responsive Web Design**。它与移动端开发有着密切的联系。

> 根据维基百科及其参考文献，理论上，响应式界面能够**适应不同的设备**。描述响应式界面最著名的一句话就是“Content is like water”，中文即是“如果将屏幕看作容器，那么内容就像水一样”。

Jeffrey Zeldman 总结道，RWD 定义为**一切能用来为各种分辨率和设备性能优化视觉体验的技术**。

关于响应式设计的更详细介绍，可以参阅[这篇文章](https://mp.weixin.qq.com/s/XZ45ElgevrfiCp-fc8Ki0Q)。

##  栅格系统

对于 Bootstrap，它提供了一套**响应式、移动设备优先**的流式网格系统，随着屏幕或视口（viewport）尺寸的增加，系统会自动分为**最多 12 列**。（简单来说，就是将一行平均分成 12 个格子，可指定元素占多少个格子）

<img src="https://gitee.com/j__strawhat/MyImages/raw/master/20210220140524.png" style="zoom:67%;" />

### 移动设备有限策略

内容：决定什么是最重要的。

布局：

+ 优先设计更小的宽度。
+ 基础的 CSS 是移动设备优先，媒体查询时针对于平板电脑、台式电脑

渐进增强：随着屏幕大小的增加而添加元素

### 定义步骤

1. 定义容器（相当于 HTML 中的 `<table>`），有两种容器：

   + `.container`：固定宽度，可以理解为 “两边存在空隙”
   + `.container-fluid`：100% 宽度

2. 定义行（相当于 `<tr>`）。通过行 （`row`），在水平方向创建一组列（`column`）

3. 定义元素，指定该元素在不同设备上，所占的格子数目。格式为：`col-设备代号-格子数目`

   |          | 超小屏幕 | 小屏幕 |  中等屏幕  |    大屏幕    |
   | :------: | :------: | :----: | :--------: | :----------: |
   | 应用场景 |   手机   |  平板  | 桌面显示器 | 大桌面显示器 |
   | 设备代号 |   `xs`   |  `sm`  |    `md`    |     `lg`     |
   | 像素尺寸 |  <768px  | ≥768px |   ≥992px   |   ≥1200px    |

注意：

1. 在设计时，如果一 行（`row`）中包含了的 列（`col`）数**大于 12**，多余的 列（`col`）所在的元素将被作为一个整体另起一行排列。

2. 如果真实设备宽度小于了设置栅格类属性的设备代码的最小值，此时一个元素占满一整行。

   > 是否不希望在小屏幕设备上所有列（`col`）都堆叠在一起？那就使用针对超小屏幕和中等屏幕设备所定义的类，即 `.col-xs-*` 和 `.col-md-*`。

3.  栅格类属性可以 **向上兼容** 。栅格类适用于与屏幕宽度大于或等于分界点大小的设备。

响应式演示：

<img src="https://gitee.com/j__strawhat/MyImages/raw/master/栅格系统演示.gif"/>

```html
<!DOCTYPE html>
<html lang="zh-CN">
<head>
    <meta charset="utf-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1">
    <!-- 上述3个meta标签*必须*放在最前面，任何其他内容都*必须*跟随其后！ -->
    <title>栅格系统演示</title>
    <!-- Bootstrap -->
    <link href="css/bootstrap.min.css" rel="stylesheet">
    <!-- jQuery (Bootstrap 的所有 JavaScript 插件都依赖 jQuery，所以必须放在前边) -->
    <script src="js/jquery-3.2.1.min.js"></script>
    <!-- 加载 Bootstrap 的所有 JavaScript 插件。你也可以根据需要只加载单个插件。 -->
    <script src="js/bootstrap.min.js"></script>
    <style>
        .inner{
            border:1px solid dodgerblue;
        }
    </style>
</head>
<body>
    <div class = "container-fluid">
        <div class = "row">
            <!-- 在大显示器上一行显示12个格子，在平板显示一行6个格子 -->
            <div class = "col-lg-1 col-sm-2 inner">我是栅格</div>
            <div class = "col-lg-1 col-sm-2 inner">我是栅格</div>
            <div class = "col-lg-1 col-sm-2 inner">我是栅格</div>
            <div class = "col-lg-1 col-sm-2 inner">我是栅格</div>
            <div class = "col-lg-1 col-sm-2 inner">我是栅格</div>
            <div class = "col-lg-1 col-sm-2 inner">我是栅格</div>
            <div class = "col-lg-1 col-sm-2 inner">我是栅格</div>
            <div class = "col-lg-1 col-sm-2 inner">我是栅格</div>
            <div class = "col-lg-1 col-sm-2 inner">我是栅格</div>
            <div class = "col-lg-1 col-sm-2 inner">我是栅格</div>
            <div class = "col-lg-1 col-sm-2 inner">我是栅格</div>
            <div class = "col-lg-1 col-sm-2 inner">我是栅格</div>
        </div>
    </div>
</body>
</html>
```

布局排列的演示：

<img src="https://gitee.com/j__strawhat/MyImages/raw/master/20210220152048.png"/>

```html
<div class = "container">
        <div class = "row" style="padding: 10px">
            <div class = "col-md-4">
                <img src="../imgs/National.jpg" height="358">
            </div>

            <div class = "col-md-8" >
                <!--分两行排列，每一行排三张图片-->
                <div class = "row">
                    <div class = "col-md-4"> <!--第一张图片-->
                        <div class = "thumbnail"> <!--自动加小圆角边框的样式-->
                            <img src = "../imgs/scene.jpg">
                            <p>Come here now!</p>
                        </div>
                    </div>
                    <div class = "col-md-4"> <!--第二张图片-->
                        <div class = "thumbnail"> <!--自动加小圆角边框的样式-->
                            <img src = "../imgs/scene.jpg">
                            <p>Come here now!</p>
                        </div>
                    </div>
                    <div class = "col-md-4"> <!--第三张图片-->
                        <div class = "thumbnail"> <!--自动加小圆角边框的样式-->
                            <img src = "../imgs/scene.jpg">
                            <p>Come here now!</p>
                        </div>
                    </div>
                </div>
                <div class = "row">
                    <div class = "col-md-4"> <!--第一张图片-->
                        <div class = "thumbnail"> <!--自动加小圆角边框的样式-->
                            <img src = "../imgs/scene.jpg">
                            <p>Come here now!</p>
                        </div>
                    </div>
                    <div class = "col-md-4"> <!--第二张图片-->
                        <div class = "thumbnail"> <!--自动加小圆角边框的样式-->
                            <img src = "../imgs/scene.jpg">
                            <p>Come here now!</p>
                        </div>
                    </div>
                    <div class = "col-md-4"> <!--第三张图片-->
                        <div class = "thumbnail"> <!--自动加小圆角边框的样式-->
                            <img src = "../imgs/scene.jpg">
                            <p>Come here now!</p>
                        </div>
                    </div>
                </div>

            </div>
        </div>
    </div>
```

# 三、CSS 样式 与 JS插件

## 全局 CSS 样式

### 1. 按钮

文档可参考：https://v3.bootcss.com/css/?#buttons

为 `<a>`、`<button>` 或 `<input>` 元素添加按钮类（button class）即可使用 Bootstrap 提供的样式，也就说，为上述的标签设置类属性来获取好看的样式：`class = "btn btn-default"`

注意，导航和导航条组件只支持 `<button>` 元素。

```html
<a class="btn btn-default" href="#" role="button">Link</a>
<button class="btn btn-default" type="submit">Button</button>
<input class="btn btn-default" type="button" value="Input">
<input class="btn btn-default" type="submit" value="Submit">
```

### 2. 图片

在 Bootstrap 版本 3 中，通过为图片添加 `.img-responsive` 类可以让图片支持响应式布局，从而让图片在其父元素中更好的缩放。

```html
<img src="..." class="img-responsive" alt="..."> <!--尽量使用相对路径-->
```

如果需要让使用了 `.img-responsive` 类的图片**水平居中**，请使用 `.center-block` 类。

### 3. 表单

文档可参考：https://v3.bootcss.com/css/?#forms

所有设置了**`.form-control` 类**的 `<input>`、`<textarea>` 和 `<select>` 元素都将被默认设置宽度属性为 `width: 100%;`。 

将 `label` 元素和前面提到的控件包裹在 **`.form-group`** 中可以获得最好的排列。

推荐参考：水平排列的表单

<img src="https://gitee.com/j__strawhat/MyImages/raw/master/20210220145518.png" style="zoom: 80%;" />

>  需要自行调整输入框的宽度。

```html
<form class="form-horizontal">
  <div class="form-group">
    <label for="inputEmail3" class="col-sm-2 control-label">Email</label>
    <div class="col-sm-10">
      <input type="email" class="form-control" id="inputEmail3" placeholder="Email">
    </div>
  </div>
  <div class="form-group">
    <label for="inputPassword3" class="col-sm-2 control-label">Password</label>
    <div class="col-sm-10">
      <input type="password" class="form-control" id="inputPassword3" placeholder="Password">
    </div>
  </div>
  <div class="form-group">
    <div class="col-sm-offset-2 col-sm-10">
      <div class="checkbox">
        <label>
          <input type="checkbox"> Remember me
        </label>
      </div>
    </div>
  </div>
  <div class="form-group">
    <div class="col-sm-offset-2 col-sm-10">
      <button type="submit" class="btn btn-default">Sign in</button>
    </div>
  </div>
</form>
```

## 部分组件

### 1. 导航条

参阅文档：https://v3.bootcss.com/components/#navbar

### 2. 分页条

参阅文档：https://v3.bootcss.com/components/#pagination

## JS 插件

### 1. 轮播图（carousel）

参阅文档：https://v3.bootcss.com/javascript/#carousel

