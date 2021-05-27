---
title: 「CSS知识精炼02」盒子模型、浮动、文档流、定位
author: JoyDee
categories: [Web前端, HTML与CSS]
tags: [CSS]
date: 2020-07-20 20:04:00 +0800
math: true
---

# 七、文档流

文档流(`normal flow`)——网页的基础(最底下的一层)，我们所创建的元素默认都是在文档流中进行排列。

对于元素有两个状态：在文档流 或 脱离文档流。

**元素在文档流的特点**：

+ 块元素：会在页面中独占一行(自上向下**垂直**排列)，默认宽度为父元素的全部(撑满)，默认高度被(子元素)内容撑开。

+ 行内元素：行内元素不会占页面的一行，只占自身的大小。它在页面中从左向右水平排列，若一行无法容纳下所有的行内元素，则元素会换到第二行继续自左向右排列。行内元素的默认宽度和高度都是被内容撑开。

> 创建外部容器时，应这样搭配 `nav(div)`, `div(div)`, `ul(li)`

# 八、盒子模型(box model)

CSS将页面中的所有元素都设置为一个矩形的盒子，将元素设置为矩形的盒子后，对页面的布局就变成将不同的盒子摆放到不同的位置。

![](https://gitee.com/j__strawhat/MyImages/raw/master/20200819192057.png)

每一个盒子由这几部分组成：

- **内容区(content)**：元素中的所有子元素和文本内容都在内容区中排列，其大小由 `width` 和 `height` 两个属性设置。

- **内边距/填充(padding)**：内容区和边框之间的距离（也会影响盒子大小），有四个方向( `padding-top`、`padding-right`、`padding-bottom`、`padding-left` )，而背景颜色也会延伸到内边距上。`eg:padding:10px 20px 30px 40px;`

  > 如果盒子本身没有指定 `width/height` 属性，则 `padding` 无法撑开盒子的大小

- **边框(border)**：属于盒子的边缘（会影响整个盒子的大小），边框内部属于盒子内部，它可以设置这些样式：

  - **边框的宽度(border-width)**：默认值为3px，可用于指定四个方向的边框高度，但值的情况是按顺时针：四个参数：上->右->下->左；三个参数：上 左右 下；两个参数：上下 左右。此外还可以用`border-xxx-width`(xxx可为`top/right/bottom/left`)单独指定某一个边的宽度。
  - **边框的颜色(border-color)**：同样可以分别指定四个边的边框，如果省略不写默认为黑色。
  - **边框的样式(border-style)**：可选值有：(实线)solid/(点状虚线)dotted/(虚线)dashed/(双线)double/none) 。它的简写属性举例为：`border: 10px solid orange;` 或 `border-top: 10px solid red;`

- **外边距(margin)**

  - `margin-top`：上外边距，设置正值，元素会向下移动；此外也可设置为负值

    `margin-right`：默认情况下设置该参数不会产生影响

  - 由于元素在页面中是按照自左向右的顺序排列，默认情况下设置**下和右**外边距则会移动**其他元素**，设置**左和上**外边距会移动**元素本身**。

  - `border-radius`： 用于设置圆角半径

  ```css
  /*如果设置两个半径，就变成椭圆*/
  border-top-left-radius: 50px 100px 
  /*只要设置50%就会变成圆形元素*/
  border-radius: 50%; 
  ```

- `box-sizing`：用来设置盒子尺寸的计算方式，它的可选值有：

  - `content-box`：默认值，宽度和高度用于设置内容区的大小。
  - `border-box` ：宽度和高度用于设置整个盒子可见框的大小，此时和之前的不同，`width`和`height`指的是内容区和内边距和边框的**总大小**(注意不包括外边距)。

前三部分共同决定盒子可见框的**大小(内容区+内边距+边框)**，外边距决定盒子的**位置（不影响盒子可见框大小）**。

[附]：

- `outline` ：用于设置元素的轮廓线，它位于**边框边缘**的外围，用法与`border`相似，不同之处在于 不会影响可见框的大小(不影响整体布局)。

- `box-shadow` （盒子阴影）：用来设置元素的阴影效果(默认在元素正下方)，该阴影不会影响页面布局。语法格式如下：

  ```css
  box-shadow: h-shadow v-shadow blur spread color inset;
  ```

  | 值         | 描述                                               |
  | ---------- | -------------------------------------------------- |
  | `h-shadow` | 必需，水平阴影的偏移量（允许负值）                 |
  | `v-shadow` | 必需，垂直阴影的偏移量（允许负值）                 |
  | `blur`     | 可选，模糊的距离                                   |
  | `spread`   | 可选，阴影的尺寸                                   |
  | `color`    | 可选，阴影的颜色                                   |
  | `inset`    | 可选，将外部阴影（`outset`，即默认值）改为内部阴影 |

- `text-shadow` （文字阴影）：将阴影应用于文本。语法格式如下：

  ```css
  text-shadow: h-shadow v-shadow blur color;
  ```

## 元素的水平方向的布局

元素在其**父元素**中，水平布局必须满足以下等式：

`总元素的宽度 = margin-left + border-left + padding-left + width + padding-right + border-right + margin-right`

若该等式不成立，则该情况为过渡约束，等式会自动调整，其调整情况具体有：

+ 如果该七个参数没有`auto`的情况，则浏览器会**自动调整**`margin-right`值以使等式满足。
+ `width/margin-left/margin-right`中设为`auto`，则会自动调整该`auto`值使得等式成立。
+ 此外，若将一个宽度和一个外边距设置为`auto`，则宽度会自动调整到最大、外边距自动调整为`0`；若将三个参数都设置为`auto`，则外边距自动调都为`0`，宽度自动调整到最大；

### 如何将元素设置水平居中

> 下面方法针对于块级元素，若是行内元素（如 `span`）或行内块元素（如 `img`），需给其**父元素**，添加 `text-align: center` 

⭐如果**左右的外边距设置为auto，宽度设一固定值**（否则会继承父元素的宽度），则两个外边距会设置为**相同**的值。由此便能够使父元素水平居中，其常见写法如下：

```css
/* 写法一 */
margin-left: auto;
margin-right: auto;

/* 写法二（推荐）：上下外边距为0，左右外边距设auto，使得该元素水平居中*/
margin: 0 auto;
```

## 元素的垂直方向的布局

> ⭐若要让一个**文字在父元素中垂直居中**，只需将父元素的**line-height**设置为一个和父元素height相等的值

默认情况下父元素的高度被内容撑开，垂直方向布局无需满足等式，子元素的高度是在父元素的内容区中排列，若子元素的高度超过父元素，则子元素会从父元素中溢出。

如何**处理溢出问题**？——使用`overflow`属性来设置父亲如何处理溢出的子元素，可选值有：

+ `visible`：默认值，子元素允许从父元素中溢出，在父元素外部的位置显示。
+ `hidden`：溢出内容会被裁剪不会显示（不友好）
+ `scroll`：生成两个滚动条，通过滚动条来查看完整的内容
+ `auto`：根据需要生成滚动条（既不会生成多余的滚动条 ）

### 垂直外边距的折叠（重叠）

**相邻**的**垂直**方向外边距发生重叠现象（如上面元素的下外边距与下面元素的上外边距），有两种情况：

+ 兄弟元素间的相邻垂直外边距会取两者（正值）之间的**较大值**（同号折叠，异号相加）。

  <img src="https://gitee.com/j__strawhat/MyImages/raw/master/20210402222023.png" style="zoom: 67%;" />

  **解决方案**：尽量只给一个盒子添加 `margin` 值

+ 父子元素间（即嵌套关系），父元素有上外边距的同时子元素也有上外边距，此时父元素会塌陷较大的外边取值：

  <img src="https://gitee.com/j__strawhat/MyImages/raw/master/20210402222223.png" style="zoom:67%;" />

  **解决方案**：① 可为父元素定义上边距或**上内边距**；②可为父元素添加 `overflow: hidder` ；③见高度塌陷这一章

## 行内元素的盒模型

行内元素（eg：超链接）不支持修改`width`和`height`来设置内容区的大小，大小只能由内容决定。它们行内元素可设置`padding\border\margin`，但垂直方向的`padding\border\margin`不会影响页面的布局（不会将其他元素挤掉，只会覆盖）。

### 如何修改行内元素的宽高？

+ `display`属性用于设置元素显示的类型，可选值有：

  + **`inline`**：将元素设置为**行内元素**：此时盒子不会产生换行、`width` 与 `height` 属性将不起作用。

    > 做链接的 `<a>` 元素、 `<span>`、 `<em>` 以及 `<strong>` 都是默认处于 `inline` 状态的

  - **`block`**：将元素设置为**块元素**(从而可以修改宽高)。

  + `inline-block `：将元素设置为行内块元素（既可**设置宽高**，又不会独占一行，但中间若有换行，会有缝隙）
  + `table`：将元素设置为一个表格，同时也用于解决父元素与子元素的外边距重叠问题。
  + `none`：元素不在页面中显示。

  应用举例：

  ```html
  <!DOCTYPE html>
  <html lang="en">
  <head>
      <meta charset="UTF-8">
      <meta name="viewport" content="width=device-width, initial-scale=1.0">
      <title>Document</title>
      <style>
          .nav a {
              height: 52px;
              /* 垂直居中，需要将line-height设置与height相等 */
              line-height: 52px;
  
              width: 120px;
              text-align: center;
  
              /* 由于a标签属于行内元素，但希望设置宽高，故需要模式转换 */
              display: inline-block;
              
              color: white;
              background-color: #5a84fc;
              text-decoration: none;
          }
          .nav a:hover{
              background-color: #1b86f9;
          }
      </style>
  </head>
  <body>
      <div class="nav">
          <a href="#">导航1</a>
          <a href="#">导航2</a>
          <a href="#">导航3</a>
          <a href="#">导航4</a>
          <a href="#">导航5</a>
      </div>
  </body>
  </html>
  ```

  <img src="https://gitee.com/j__strawhat/MyImages/raw/master/20210402212253.png"/>

+ `visibility`属性用来设置元素的显示状态，可选值有：

  + `visible `：默认值，元素在页面中正常显示
  - `hidden `：元素在页面中隐藏，但是与`display:none`不同点在于，隐藏的元素的位置依旧占据页面的位置！

# 九、浮动

CSS的浮动，可以将一个元素向其父元素的左侧或右侧移动，同时其周围元素也会重新排列，常应用于布局。语法如下：

```css
选择器 { float: 属性值; }
```

可选值有：

- `none`：默认值，元素不浮动
- `left` 或 `right`：使元素向左或右浮动

注意，只要元素设置浮动(即可选值不为none)，它会完全从**文档流中脱离**，不再占用文档流的位置，它之前处于下边的仍在文档流的元素会自动向上移动，水平布局的等式不需要强制成立了。

> 网页布局第一准则：多个块级元素**纵向**排列找标准流，多个块级元素**横向**排列找浮动。

## 浮动的特点

1. 设置浮动以后元素会向父元素的左侧或右侧移动，但默认不会从父元素中移出。

   > 先用**标准流的父**元素排列**上下**位置，其内部**子元素**再采取**浮动**排列**左右**位置。

2. 浮动元素会完全脱离文档流，不再占据文档流中的位置，同时该元素的特点在脱离文档流后也会发生变化：

   + 对于块元素：① 块元素不会独占页面的一行；② 该元素的宽度和高度默认都被内容撑开

   + 对于行内元素：脱离文档流后会变成块元素，但不会独占一行，宽度和高度默认都被内容撑开，具有**行内块元素**的特性。

3. 浮动元素向左或右移动时，不会超过它前边的其他浮动元素（从而实现元素的横向排列，同时浮动的元素是互相贴靠在一起，**没有缝隙**），也不会超过它上一个的浮动的兄弟（哪怕两者元素浮动方向不同），最多与上一个元素一样的高度。

   > 一个元素浮动了，理论上其他兄弟元素也需要浮动。

4. 如果浮动元素的上边是一个没有浮动的块元素，则浮动元素无法上移

5. 浮动元素不会覆盖文字元素，文字会自动环绕在浮动元素的周围（从而实现**文字环绕图片**的效果）

使用举例：

<img src="https://gitee.com/j__strawhat/MyImages/raw/master/20210404154444.png"/>

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Document</title>
    <style>
        .box {
            width: 1200px;
            height: 460px;
            background-color: pink;
            margin: 0 auto; /*width+margin实现水平居中 */
        }
        .left {
            float: left;
            width: 230px;
            height: 460px;
            background-color: purple;
        }
        .right {
            float: left;
            width: 970px;
            height: 460px;
            background-color: skyblue;
        }
    </style>
</head>
<body>
    <div class="box">
        <div class="left">导航</div>
        <div class="right">海报</div>
    </div>
</body>
</html>
```



## 2. 高度塌陷

在浮动布局中，父元素的高度默认是被子元素撑开的，**当子元素浮动后会脱离文档流，将无法撑起父元素的高度**，导致父元素的高度丢失。父元素高度丢失后，其下的元素会自动上移，导致页面的布局混乱。

![](https://gitee.com/j__strawhat/MyImages/raw/master/20200819232042.png)

尽管给父元素写一固定高度能够解决表面问题，但我们更希望父元素的高度是跟随子元素高度而变化的。以下给出一系列解决方案：

### 方案一：BFC——为父元素添加overflow

BFC(`Block Formatting Context`)，块级格式化环境。BFC是CSS中的一个隐含的属性，可为某一元素开启BFC，使得该元素变为一个独立的布局区域，其特点有：

1. 该元素不会被浮动元素所覆盖

   <img src = "https://gitee.com/j__strawhat/MyImages/raw/master/20200819232513.png" width="400" >

2. 该元素的子元素和父元素的外边距不会重叠。

   > 然而，要解决子元素与父元素的外边距重叠，更优的解决方案是利用`clearfix`。

   <img src = "https://gitee.com/j__strawhat/MyImages/raw/master/20200819232520.png" width="400" >

3. 该元素**可以包含浮动**的子元素

   ![](https://gitee.com/j__strawhat/MyImages/raw/master/20200819232542.png)

开启元素BFC的推荐方式——将元素设置`overflow:hidden`（由此该元素能够包含浮动元素）（但仍存在副作用）

### 方案二：clear

**作用**：如果我们不希望某个元素因为其他元素浮动的影响而改变位置，可通过设置clear属性来清除**浮动元素对当前元素**所产生的影响。

**原理**：设置清除浮动以后，浏览器会自动会为元素添加一个上外边距(`margin-top`)，以使其位置不受其他元素的影响。

**可选值**：

- `left`： 清除左侧浮动元素对当前元素的影响。
- `right` ：清除右侧浮动元素对当前元素的影响。
- `both` ：清除两侧中影响最大的那一侧。

<img src = "https://gitee.com/j__strawhat/MyImages/raw/master/20200819233842.png" alt = "clear:left" width="300" >

<img src = "https://gitee.com/j__strawhat/MyImages/raw/master/20200819233855.png" alt="clear:right" width="400" >

### 方案三：为父级元素添加after伪元素

将两个相邻且发生高度塌陷的元素隔开，可以使用`::after`伪类来解决高度塌陷，即在浮动元素末尾添加一个空的标签

```css
.box1::after{ 
	content: '';
	display: block; /*转化为块级元素*/
	clear:both;
} /* 相当于在box1内部、box2后面，添加了<div class = "box3"></div> */
```

> `::after` 默认为行内元素，需要转化为块级元素

![](https://gitee.com/j__strawhat/MyImages/raw/master/20200819234940.png)

### 方案四：双伪元素——clearfix类（较优）

设计 `clearfix` 类，以近乎完美地解决**子元素与父元素外边距重叠**以及**高度塌陷**的方案，实例为：

```css
.clearfix::before, 
.clearfix::after{
	content: '';
	display: table;
    /* 虽然block/table可以解决高度塌陷，但table还可以隔开两个元素的外边距，避免两元素垂直外边距的重叠 */
	clear: both;
}
```

同时，在HTML中

```html
<div class = "box1 clearfix"> <!--为box1添加-->
	<div class = "box2"></div>
</div>
```

# 十、定位

定位(`position`)——更高级的布局手段，将元素摆放到页面的**任意位置**的同时，不会对其他元素的布局产生过多的影响。

使用position属性来指定元素的定位类型，可选值有：

- `static`：默认值，元素静止的，没有开启定位，遵循正常的文档流对象。

- `relative`：开启元素的相对定位，使它原本所占的空间不会改变。

  - **特点**：

    1. 开启相对定位以后，若没有设置偏移量，元素不会发生什么变化。
    2. 相对定位是参照于**元素在文档流中的位置**，相对于其正常位置来进行定位。
    3. 相对定位会提升元素的层级，但**没有脱离文档流**。
    4. 相对定位不会改变元素的性质(块元素认为块元素，行内元素仍为行内元素)，原来位置仍占着。

  - **偏移量**(`offset`)(不会影响周围元素，只会影响自身)，(必须开启定位后方起作用)有：

    - `top`: 定位元素和定位位置上边的距离，`top`值越大，定位元素越向下移动；
      
    - `buttom`: 定位元素和定位位置下边的距离, 与`top`相反。

      定位元素垂直方向的位置由`top`和`buttom`两个属性来控制，通常情况只取其一。

    - `left`: 定位元素和定位位置左侧的距离，`left`越大，元素越靠右；
      
    - `right`: 定位元素和定位位置右侧的距离。

      水平方向位置由left和right两个属性控制，通常只取其一。
    
    ```css
    .box2{
        width:200px;
        ...
        position:relative;
        top:100px; /*如果没有设置relative，而是设置margin-top，box3会被挤掉*/
    }
    ```
    
    <img src = "https://gitee.com/j__strawhat/MyImages/raw/master/20200821162855.png" width="400" >

- `absolute` 开启元素的绝对定位。一般来说**父相对子绝对**。

  - **特点**：

    1. 开启绝对定位后，若没设置偏移量，**元素的位置**不会发生什么变化。

    2. 绝对定位是相对于其**包含块**进行定位的。

       [附]：**包含块**(`containing block`)

       1. 正常情况下，包含块即为当前元素最近的祖先块元素

          ```html
           <div><span><em>hello</em></span></div> <!--span和em的包含块即为div-->
          ```
          
       2. 绝对定位的包含块：此处的包含块即为离它**最近**的且开启了**定位**的祖先元素。
       
       3. 如果所有祖先元素都没有开启定位，则**根元素**即为它的包含块。
       
       4. `html`元素：根元素、初始包含块 。

    3. 绝对定位会改变元素的性质，块元素变成**行内元素**，宽度被内容撑开。

    4. 绝对定位会使元素**提升一个层级**，元素会从文档流中脱离。

    ```html
    <div class = "box4"> 4
        <div class = "box5"> 5
        	<div class = "box2"> 2
            </div>
        </div>
    </div>
    ```

    ```css
    .box2{
        width:200px;
        ...
        position: absolute; /*子绝对定位，而父亲开启相对定位后，子的定位不再参考于根元素*/
    }
    .box4{
        ...
        position: relative;
    }
    .box5{
        ...
        position: relative;
    }
    ```

    <img src = "https://gitee.com/j__strawhat/MyImages/raw/master/20200821164115.png" width="400" >

  - **水平布局**：

    开启绝对定位后，水平布局在原有的7个参数增加至9个参数(增加`left`与`right`)。当发生过渡约束(不满足那个等式)：

    - 若9个参数中没有`auto`，则自动调整`right`值以使得等式成立

    - 若存在参数值为`auto`(可设`margin`,`width`, `left`, `right`)，则自动调整auto值以使得等式成立

    - 由于`left`和`right`的值默认为`auto`，若不初始化这两个参数，当等式不成立时，会自动调整这两个参数。

      >  水平布局**居中**需要`margin-left : auto; margin-right:auto; left:0; right:0`

  - **垂直布局**：

    参数同样增至九个: `top/bottom + margin-top/bottom + padding-top/bottom + border-top/bottom + height = 包含块高度`

  > 绝对定位**居中**(使元素在**包含块**内部**水平、垂直**方向均居中) ：
  >
  > `margin:auto; left:0;right:0;top:0;bottom:0;`

- `fixed`开启元素的固定定位(也是一种特殊的绝对定位)

  - 与绝对定位**不同的特点**：

    固定定位永远参照于**浏览器的视口**(视口大小不会变，与html根元素不同，浏览器页面滚动时会跟着屏幕移动，类似于广告)进行定位。

    <img src = "https://gitee.com/j__strawhat/MyImages/raw/master/20200821164955.png" width="400" >

- sticky 开启元素的粘滞定位

  - **特点**：

    与相对定位的特点基本一致，不同在于，粘滞定位可在元素到达某个位置时将其固定，但兼容性不太好。

    ![](https://gitee.com/j__strawhat/MyImages/raw/master/20200821164844.png)

## 元素的层级

对于开启定位元素，可通过`z-index`属性来指定元素的层级，值为整数，值越大元素的层级越高，也就越优先显示。

+ 若元素的层级一样，则优先显示结构靠下的元素。

+ 祖先元素的层级再高，也不能够盖住后代元素。

## 浏览器的默认样式

- 通常情况，浏览器为了确保没有样式也能够显示，设置了一些默认样式(可能会影响页面的布局，需要去除掉)

- 一般默认样式下会发现某些元素有外边距等默认样式(通过浏览器方可查看)，可能需要将`margin`和`padding`调为0，或单独去掉项目符号，或去掉链接元素的下划线样式。

  可引入常用的重置样式表`.css`文件到你的网页中，用于清楚浏览器的默认样式：

  + [reset.css](https://gitee.com/j__strawhat/MySource/blob/master/%E6%B5%8F%E8%A7%88%E5%99%A8%E9%BB%98%E8%AE%A4%E6%A0%B7%E5%BC%8F%E9%87%8D%E7%BD%AE/reset.css) 直接取出浏览器的默认样式

  + ```html
    <link rel = "stylesheet" href = "./css/reset.css" >
    ```

  + [normalize.css](https://gitee.com/j__strawhat/MySource/blob/master/%E6%B5%8F%E8%A7%88%E5%99%A8%E9%BB%98%E8%AE%A4%E6%A0%B7%E5%BC%8F%E9%87%8D%E7%BD%AE/normalize.css) 对默认样式进行了统一

    ```html
    <link rel = "stylesheet" href = "./css/normalize.css" >
    ```

    

  

