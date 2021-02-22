---
title: 「JavaWeb学习笔记04」请求与响应
author: JoyDee
categories: [Java, JavaWeb]
tags: [JavaWeb]
date: 2021-02-21 18:30:00 +0800
---

# Chapter 5. HTTP

## 5.1 HTTP 概述

HTTP 协议（Hypertext Transfer Protocol，超文本传输协议），顾名思义，即关于如何在网络上传输超文本（即 HTML 文档）的协议。

HTTP协议，规定了 Web 的基本运作过程，以及**浏览器与 Web 服务器**之间的通信细节。

特点：

1. 在分层的网络体系结构中，HTTP协议位于**应用层**，建立在 **TCP/IP 协议的基础上**。

   <img src="https://gitee.com/j__strawhat/MyImages/raw/master/20210223012555.png"/>

2. 默认端口号是 **`80`**。

3. HTTP 协议，规定 Web 基本运作过程基于**客户/服务器**通信模式：客户端主动发出 HTTP 请求，服务器端接收 HTTP 请求，再返回相应的 HTTP 响应结果。

   > 举个例子，当用户再浏览器输入 URL 地址：`http://www.javathinker.org/java/book.html` 后：
   >
   > 1. 浏览器与网络上的域名为 `www.javathinker.org` 的 Web 服务器建立 **TCP 连接**。
   > 2. 浏览器发出要求访问 `java/book.html` 的 **HTTP 请求**。
   > 3. Web 服务器在接收到 HTTP 请求后，解析 HTTP 请求，然后发回包含 `book.html` 文件数据的 **HTTP 响应**。
   > 4. 浏览器在接收到 HTTP 相应后，解析 HTTP 响应，并在窗口中展示 `book.html` 文件。
   > 5. 浏览器与 Web 服务器之间的 TCP 连接关闭

历史版本：

+ 1.0：每一次请求相应都会建立新的连接
+ 1.1：复用连接

## 5.2 HTTP 请求格式

HTTP 请求由三部分构成：

+ 请求行
+ 请求头（Request Header）
+ 请求正文（Request Content）

通过火狐浏览器的开发者工具，我们能够看到下面的 HTTP 请求的例子：

<img src="https://gitee.com/j__strawhat/MyImages/raw/master/image-20210221221751029.png"/>

```http
POST /MyEEProjects_war_exploded/login.html HTTP/1.1
Host: localhost:8080
User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64; rv:85.0) Gecko/20100101 Firefox/85.0
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/webp,*/*;q=0.8
Accept-Language: zh-CN,zh;q=0.8,zh-TW;q=0.7,zh-HK;q=0.5,en-US;q=0.3,en;q=0.2
Accept-Encoding: gzip, deflate
Referer: http://localhost/MyEEProjects_war_exploded/login.html
Connection: keep-alive
Upgrade-Insecure-Requests: 1

username=Luffy&submit=submit
```

### 5.2.1 请求行

HTTP 请求的第一行即为 **请求行**，它包括 **请求方式**、**URI**（URL 是 URI 的一个子类别）、**协议版本** 这三项内容，以空格分开：

```http
GET /MyEEProjects_war_exploded/login.html HTTP/1.1
```

> UR**I**（统一资源定位**符**），用于标识要访问的网络资源。在HTTP请求中，通常只要给出服务器的根目录的相对目录即可，以 `/` 开头。

HTTP请求有7种请求方式，常用的有两种：

+ `GET`：
  1. 请求参数在**请求行**中，可理解为 紧跟在 URL 后；
  2. 请求的 URL 长度有限制（不适用于文件的上传）
  3. 不太安全
+ `POST`：
  1. 请求参数在**请求体**中
  2. 请求的 URL 长度没有限制
  3. 相对安全

### 5.2.2 请求头

请求头包含许多有关客户端环境和请求正文的有用信息。例如：请求头可以声明浏览器的类型、所用语言、请求正文的类型，以及请求正文的长度等。

```http
Host: localhost:8080 
User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64; rv:85.0) Gecko/20100101 Firefox/85.0
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/webp,*/*;q=0.8
Accept-Language: zh-CN,zh;q=0.8,zh-TW;q=0.7,zh-HK;q=0.5,en-US;q=0.3,en;q=0.2
Accept-Encoding: gzip, deflate
Referer: http://localhost/MyEEProjects_war_exploded/login.html
Connection: keep-alive
Upgrade-Insecure-Requests: 1
```

常用的请求头：

1. `User-Agent`：浏览器告诉服务器，所使用的浏览器版本信息。通过该请求头，可以在服务器端获取该头的信息，解决浏览器的兼容性问题。
2. `Referer`：告诉服务器，当前请求从哪里来。作用：①防盗链；②统计工作

### 5.2.3 请求正文

HTTP 协议规定，请求头和请求正文间必须以**空行分割**（即只有 CRLF 符号的行），非常重要，表示了请求头已经结束，接下来是请求正文。

> CRLF，是指回车符和行结束符 `"\r\n"`

在请求正文中可以包含客户以 `POST` 方式提交的表单数据（参数）：

```http
username=Luffy&submit=submit
```

`GET` 请求，是没有请求体的。



## 5.3 HTTP 响应格式