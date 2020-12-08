---
title: MarkDown使用之LaTeX表示数学公式
author: JoyDee
categories: [其他知识/技术分享, LaTeX]
tags: [LaTeX]
math: true
---

对于文本排版格式，对于$Microsoft\,Word$来说，功能尽全，可调的参数十分多，人们可能会将不少的时间放在具体的文字大小、实现样式。而$markdown$语法能够让人们通过符号去替代样式，尽管实现的样式没有像$word$那样多，但在日常使用中足矣。$Markdown$语法正是希望我们回归到内容本身。

自从接触博客后，需要用到不少的$markdown$语法，同时算法相关的随笔需要借助数学语言表达，在此总结下$markdown$的$LaTeX$来排版数学公式。当教授让我们布置$word$文件报告，需要写数学公式时，直接在[软件$typora$](https://typora.io/)，将公式写好，再**直接导出为$word$文件**即可！（翻到下面有[教程](https://www.cnblogs.com/J-StrawHat/p/13452821.html#%E5%9B%9B%E3%80%81markdown%E6%A0%BC%E5%BC%8F%E6%96%87%E4%BB%B6%E5%AF%BC%E5%87%BA%E4%B8%BAword%E6%A0%BC%E5%BC%8F%E6%96%87%E4%BB%B6)）

本篇文章部分参考了@[Kiven_1994](https://www.jianshu.com/u/04a58d82d762)的简书[文章](https://www.jianshu.com/p/0ea47ae02262)，节选了我使用频率高的命令公式与符号。

# 一、基础语法

> $tips:$ $LaTeX$语法基于`$...$`或者 `$$...$$` 格式，简单来说就是你在写文章时，需要将公式放入内`$...$`或者 `$$...$$` 内部

$LaTeX$公式有两类：

* **行内公式** ：常用于夹在正文中，格式为`$...$`，$eg:$  `$\sum_{i=0}^{\infty}\frac{1}{n}$`显示为$\sum_{i=0}^{\infty}\frac{1}{n}$ 

* **独立公式**：其实就是将公式独占一行(~~比如理工科教材的数学公式~~)，格式为`$$...$$`
  $$
  \sum_{i=0}^{\infty}\frac{1}{n}
  $$

# 二、常用数学表达命令

## 上下标表示

* 上标：使用`^`表示，如：`$x^{2k+1}$`显示为$x^{2k+1}$；

* 下标：使用`_`表示，如：`$a_{2k}$`显示为$a_{2k}$；

* 上下标混用：实例：`$x_1^2$`显示为$x_1^2$ ；`$x^{y_z}$`显示为$x^{y_z}$

  

## 分数样式

   * 分式根据环境设置样式，如`$\frac{x}{y}$`显示为$\frac{x}{y}$

   * 复杂分式，待补充

  

## 根式

* 二次根式：使用`$\sqrt{...}$`，如`$\sqrt{233}$`显示为$\sqrt{233}$
* $n$次根式：使用`$\sqrt[n]{...}$`，如`$\sqrt[233]{666}$`显示为$\sqrt[66]{233}$

## 向量

* 使用`$\vec{...}$`，例如 `$\vec{AB}$`，显示为$\vec{AB}$

## 空间间距—占位宽度

以数字$233$举例：

|           |   举例   |  显示效果   |
| :-------: | :------: | :---------: |
|  无空格   |  `$23$`  | 显示为 $23$ |
|  小空格   | `$2\,3$` |   $2\,3$    |
| 1/3个空格 | `$2\ 3$` |   $2\ 3$    |

## 省略号

* 使用`$\dots$`，显示为$\dots$

## 公式组

```latex
  $$
      \begin{align}
            x+y+z=1 \nonumber\\
            x+5y-z=6 \nonumber\\
            x-y+z=5 \nonumber
      \end{align}
  $$
```

$$
      \begin{align}
            x+y+z=1 \nonumber\\
            x+5y-z=6 \nonumber\\
            x-y+z=5 \nonumber
      \end{align}
$$

> $tips:$其中的`{align}`表示为公式中间对齐；而`{nonumber}`表示不需要给公式编号；注意，要形成多行公式的话，除了最后一行的公式以外，其他公式行末需要加`\\`作结尾

## 分支公式 (分段函数)

```latex
  $$
      y=\begin{cases}
            1, &x = -1 \\
           -x, &x = 0  \\
            x, &x > 0
      \end{cases}
  $$
```

$$
y=\begin{cases}
           1, &x = -1 \\
          -x, &x = 0 \\
           x, &x > 0
      \end{cases}
$$

> 				$tips:$使用`{cases}`作为始末

## 矩阵

```latex
  $$
      \begin{pmatrix}
	a & b \\
	c & d 
      \end{pmatrix}

      \begin{bmatrix}
	a & b \\
	c & d
      \end{bmatrix}
  $$
```

$$
      \begin{pmatrix}
	a & b \\
	c & d 
      \end{pmatrix}
      \quad
      \begin{bmatrix}
	a & b \\
	c & d
      \end{bmatrix}
$$

> $tips:$使用`{pmatrix}`表示的小括号边界的矩阵；使用`{bmatrix}`表示的方括号边界的矩阵

## 积分

* 不定积分：使用`$\int ... $`，例如`$\int h(x)dx$`，显示为$\int h(x)dx$

* 定积分：使用`$\int_{下限}^{上限} ... $`，例如`$\int_{a}^{b}h(x)dx$`，显示为$\int_{a}^{b} h(x)dx$

* 二重积分：举例`$\iint_D f(x, y)dxdy$`显示为$\iint_D f(x, y)dxdy$ ；或者，`$\iint_D f(x, y)d\sigma$`，显示为$\iint_D f(x, y)d\sigma$ 

* > $tips:$`int`前面多少个$i$表示多少重积分；`int`前面为$o$表示积分区域闭合

* 第二型闭合曲线积分：举例`$\oint_L Pdx+Qdy$`，显示为$\oint_L Pdx+Qdy$

# 三、常用数学符号整理

## 希腊字母

~~此处选了常用的~~

| 符号     | 命令       | 符号          | 命令            | 符号       | 命令         |
| -------- | ---------- | ------------- | --------------- | ---------- | ------------ |
| $\alpha$ | `$\alpha$` | $\beta$       | `$\beta$`       | $\gamma$   | `$\gamma$`   |
| $\delta$ | `$\delta$` | $\varepsilon$ | `$\varepsilon$` | $\zeta$    | `$\zeta$`    |
| $\eta$   | `$\eta$`   | $\theta$      | `$\theta$`      | $\lambda$  | `$\lambda$`  |
| $\mu$    | `$\mu$`    | $\nu$         | `$\nu$`         | $\xi$      | `$\xi$`      |
| $o$      | `$o$`      | $\pi$         | `$\pi$`         | $\rho$     | `$\rho$`     |
| $\sigma$ | `$\sigma$` | $\tau$        | `$\tau$`        | $\upsilon$ | `$\upsilon$` |
| $\phi$   | `$\phi$`   | $\varphi$     | `$\varphi$`     | $\omega$   | `$\omega$`   |

## 二元关系符 (附-AMS二元关系符)

| 符号        | 命令          | 符号        | 命令          | 符号      | 命令        |
| ----------- | ------------- | ----------- | ------------- | --------- | ----------- |
| $\le$       | `$\le$`       | $\ge$       | `$\ge$`       | $\equiv$  | `$\equiv$`  |
| $\ll$       | `$\ll$`       | $\gg$       | `$\gg$`       | $\approx$ | `$\approx$` |
| $\subset$   | `$\subset$`   | $\supset$   | `$\supset$`   | $\cong$   | `$\cong$`   |
| $\subseteq$ | `$\subseteq$` | $\supseteq$ | `$\supseteq$` | $\ne$     | `$\ne$`     |
| $\in$       | `$\in$`       | $\ni$       | `$\ni$`       | $\notin$  | `$\notin$`  |

> $Tips:$在上述符号相应命令**之前**加上`\not`得到否定形式，如`$\not\subset$`，得到$\not\subset$

## 二元运算符

|  符号   |   命令    |   符号   |    命令    |       符号       |        命令        |
| :-----: | :-------: | :------: | :--------: | :--------------: | :----------------: |
|   $+$   |   `$+$`   |   $-$    |   `$-$`    |      $\div$      |     `$\div$ `      |
|  $\pm$  |  `$\pm$`  |  $\mp$   |   `$\mp`   |   $\setminus$    |   `$\setminus$`    |
| $\cdot$ | `$\cdot$` | $\times$ | `$\times$` | $\bigtriangleup$ | `$\bigtriangleup$` |
| $\cup$  | `$\cup$`  |  $\cap$  |  `$\cap$`  |     $\oplus$     |     `$\oplus$`     |
| $\vee$  | `$\vee$`  | $\land$  | `$\land$`  |     $\odot$      |     `$\odot$`      |

## 大尺寸运算符

|   符号    |    命令     |   符号    |    命令     |  符号   |   命令    |
| :-------: | :---------: | :-------: | :---------: | :-----: | :-------: |
|  $\sum$   |  `$\sum$`   |  $\prod$  |  `$\prod$`  | $\int$  | `$\int$`  |
| $\bigcup$ | `$\bigcup$` | $\bigcap$ | `$\bigcap$` | $\oint$ | `$\oint$` |

## 箭头

|     符号     |      命令      |       符号        |      命令       |         符号         |          命令          |
| :----------: | :------------: | :---------------: | :-------------: | :------------------: | :--------------------: |
| $\leftarrow$ | `$\leftarrow$` |   $\rightarrow$   | `$\rightarrow$` |  $\leftrightarrow$   |  `$\leftrightarrow$`   |
|  $\uparrow$  |  `$\uparrow$`  |   $\downarrow$    | `$\downarrow$`  | $\rightleftharpoons$ | `$\rightleftharpoons$` |
| $\Leftarrow$ | `$\Leftarrow$` | **$\Rightarrow$** | `$\Rightarrow$` |  $\Leftrightarrow$   |  `$\Leftrightarrow$`   |

## 定界符(包括大型)

|     符号      |    命令     |   符号    |    命令     |     符号     |      命令      |
| :-----------: | :---------: | :-------: | :---------: | :----------: | :------------: |
| **$\lfloor$** | `$\lfloor$` | $\rfloor$ | `$\rfloor$` |   $\vert$    |   `$\vert$`    |
| **$\lceil$**  | `$\lceil$`  | $\rceil$  | `$\rceil$`  |     $/$      |     `$/$`      |
|     $ \{ $      |   `$\{$`    |   $\}$    |   `$\}$`    | $\backslash$ | `$\backslash$` |



# 四、markdown格式文件导出为word格式文件

首先，肯定是将$typora$下载并安装好。我们在$typora$中将作业写好后，按照下图绿色箭头点中。

![image-20201006150459920](https://gitee.com/j__strawhat/MyImages/raw/master/image-20201006150459920.png)

接下来它会弹出个$pandoc$的相关文档（$pandoc$软件能够帮你将文档从一种格式转换为其他格式，极其方便），点击$installing$，要么会给你自动生成链接

![image-20201006151014393](https://gitee.com/j__strawhat/MyImages/raw/master/image-20201006151014393.png)

或者它会让你跳转到$github$网站下载。选择**$.msi$格式**文件下载即可。

![image-20201006151216654](https://gitee.com/j__strawhat/MyImages/raw/master/image-20201006151216654.png)

下完后，无脑点击安装就行。装完之后重启下$typora$软件。

**以后**，导出$word$文档的时候，直接如下图点击，

![image-20201006150459920](https://gitee.com/j__strawhat/MyImages/raw/master/image-20201006150459920.png)

$typora$上方显示导出成功后，点开查看

![image-20201006151603117](https://gitee.com/j__strawhat/MyImages/raw/master/image-20201006151603117.png)



大功告成~:smile_cat:

![image-20201006151907986](https://gitee.com/j__strawhat/MyImages/raw/master/image-20201006151907986.png)



# 五、值得学习

[如何用 Markdown&LaTeX 写一篇排版整齐的题解?](https://studyingfather.blog.luogu.org/blog-written-guide) 