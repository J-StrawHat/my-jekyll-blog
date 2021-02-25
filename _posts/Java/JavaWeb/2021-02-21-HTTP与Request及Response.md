---
title: 「JavaWeb学习笔记04」请求与响应
author: JoyDee
categories: [Java, JavaWeb]
tags: [JavaWeb, HTTP]
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

> UR**I**（统一资源定位**符**），用于标识要访问的网络资源。在HTTP请求中，通常只要给出服务器的根目录的相对目录即可，以 `/` 开头。

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

HTTP 响应，也由 三个部分 构成，分别是：

+ 响应行
+ 响应头（Response Header）
+ 响应体（Response Content）

一个 HTTP 响应的例子：

```http
HTTP/1.1 200 OK
Content-Type: text/html;charset=UTF-8
Content-Length: 124
Date: Wed, 24 Feb 2021 14:36:20 GMT
Keep-Alive: timeout=20
Connection: keep-alive

<html>
  <head>
    <title>Tomcat服务器启动啦</title>
  </head>
  <body>
  <H1>Hello, World!</H1>
  </body>
</html>
```

### 5.3.1 响应行

HTTP 响应行的第一行包括**服务器使用的 HTTP 协议的版本**、**状态代码**，以及**对状态代码的描述**，这3项内容之间以空格分隔，如下：

```http
HTTP/1.1 200 OK
```

状态代码是一个 三位 整数，以 `1`、`2`、`3`、`4`、`5` 开头。

+ `1xx`：信息提示，代表请求已被接受，需要继续处理。临时的响应。
+ `2xx`：相应成功，表名服务器成功地接收了客户端请求。如：`200`——相应成功。
+ `3xx`：重定向。如：`302`——重定向、`304`——访问缓存。
+ `4xx`：客户端错误。如：`404`——请求路径没有对应资源；`405`——服务器不支持客户的请求方式，没有对应的 `doXxx` 方法。
+ `5xx`：服务器错误，表名服务器由于遇到某种错误而不能响应客户端请求。如：`500`——服务器内部出现异常

### 5.3.2 响应头

响应头包含许多有用的信息。常见的响应头：

+ `Content-Type`：服务器告诉客户端，本次响应体**数据格式**以及**编码格式**。
+ `Content-disposition`：服务器告诉客户端以什么格式打开响应体数据。有这些值：
  + `in-line`：默认值，在当前页面内打开。
  + **`attachment;filename=xxx`**：以附件形式打开响应体。常用于文件下载

```http
Content-Type: text/html;charset=UTF-8 //正文类型
Content-Length: 124 //正文长度
```

### 5.3.3 响应体（响应正文）

响应正文就是服务器返回的具体数据，它是浏览器**真正请求访问的信息**，最常见的是 HTML 文档：

```html
<html>
  <head>
    <title>Tomcat服务器启动啦</title>
  </head>
  <body>
  <H1>Hello, World!</H1>
  </body>
</html>
```

## 5.4 正文部分的 MIME 类型

如何保证接收方能看得懂发送方发送的正文数据？HTTP 协议采用 MIME 协议来规范正文的数据格式。

MIME（Multipurpose Internet Mail Extension），指**多用途网络邮件拓展协议**。此处的邮件不单指 E-Mail，还包括应用层协议在网络上传输的数据（如，HTTP 协议中的请求正文和响应正文，也可看做邮件），而 MIME 正是规定了邮件的标准数据格式。

遵守 MIME 协议的数据类型统称为 **MIME 类型**。在 HTTP 请求头及响应头中的 `Content-type` 项正是指定正文部分的 MIME 类型。

MIME 类型的格式往往是 `大类型/小类型` ，它与 **文件拓展名** 有一定关系。如：`.pdf` 文件拓展名，对应于 MIME 类型的 `application/pdf`；`.jpg` 、`jpeg`对应 `image/jpeg`

### 获取 MIME 类型的方法

+ `String getMimeType(String file)`：传入参数为文件名称字符串，该方法能够通过文件名称识别并返回对应的 MIME 类型串。







# Chapter 6. Request

## 6.1 ServletRequest 概述

在 Servlet 接口的 `service(ServletRequest request, ServletResponse response)` 方法中，有一个 `ServletRequest` 类型的参数。

`ServletRequest` 类表示来自**客户端的请求**。当 Servlet 容器接收到客户端要求访问特定的 Servlet 的请求时，容器先解析客户端的原始请求数据，把它包装成一个 `ServletRequest` 对象。当容器调用 `service()` 方法时，便将该对象作为参数传入。

`ServletRequest` 接口，及其子接口 `HttpServletRequest` 接口，提供了一系列用于**读取客户端请求数据**的方法。我们下文姑且讨论的是 `HttpServletRequest`  接口。

## 6.2 （常用的）获取请求消息数据的相关方法

### 6.2.1 获取请求行数据

+ 获取请求方式（如 `GET`）：`String getMethod()` 

+ ⭐️获取客户端所请求访问的 Web 应用的 **URL 入口（即虚拟目录）**： `String getContextPath()`

  > 如客户端访问 URL 为：`http://localhost:8080/helloapp/info`，那么该方法返回 `"/helloapp"`

+ 获取 HTTP 请求中的查询字符串，即 URL 中的 `"?"` 后面的内容：`String getQueryString()`

  > 如客户端访问的URL为：`http://localhost:8080/helloapp/info?username=Tom`，那么该方法返回 `"username=Tom"`

+ ⭐️获取请求 **URI**：

  + `String getRequestURI()`
  + `StringBuffer getRequestURL()`

  > 如客户端访问 URL 为：`http://localhost:8080/helloapp/info`，那么`getRequestURI()`方法返回 `"/helloapp/info"`。而 `getRequestURL()` 方法返回 `"http://localhost:8080/helloapp/info"`

### 6.2.2 获取请求头数据

+ ⭐️通过请求头的名称获取相应的请求头的值：`String getHeader(String name)`

```java
@WebServlet("/RequestDemo01")
public class RequestDemo01 extends HttpServlet {
    protected void doPost(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
        //....
    }

    protected void doGet(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
        /* 演示获取请求头数据：user-agent */
        String agent = request.getHeader("user-agent"); //传入参数不区分大小写
        //判断 agent 浏览器版本
        if(agent.contains("Chrome")){
            System.out.println("谷歌来了!");
        }
        else if(agent.contains("Firefox")){
            System.out.println("火狐来了!");
        }

        /* 演示获取请求头数据：referer */
        String referer = request.getHeader("referer");
        if(referer != null){
            if(referer.contains("/bilibili")){
                response.setContentType("text/html;charset=utf-8");
                response.getWriter().write("加载视频中....");
            }
            else {
                response.setContentType("text/html;charset=utf-8");
                response.getWriter().write("无法在此网站上播放视频....");
            }
        }
    }
}
```

### 6.2.3 获取请求参数的通用方法

无论 `GET` 还是 `POST` 请求方式，都可以使用下列方法来获取请求参数。

+ 根据参数名称获取**参数值**：`String getParameter(String name)`  （适用于单个参数，如 `username=Luffy` ）
+ 根据参数名称获取参数值**数组**：`String[] getParameterValues(String name)`（适用于“复选框”的数据，如 `hobby=xx&hobby=gsd`）
+ 获取**所有**请求的参数名称：`Enumeration<String> getParameterNames()`
+ 获取所有参数的 `Map` 集合：`Map<String, String[]> getParameterMap()`

代码演示如下：

```java
package top.JoyDee.LearningRequest;

import javax.servlet.ServletException;
import javax.servlet.annotation.WebServlet;
import javax.servlet.http.HttpServlet;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;
import java.io.IOException;
import java.util.Enumeration;
import java.util.Map;
import java.util.Set;

@WebServlet("/RequestDemo03")
public class RequestDemo03 extends HttpServlet {
    protected void doPost(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
        //POST 方式获取请求参数
        String username = request.getParameter("username");
        System.out.println("得到用户名：" + username);
        String[] tasks = request.getParameterValues("task");
        System.out.println("得到该用户的任务完成情况：");
        for(String task_e : tasks){
            System.out.println(task_e);
        }
        //获取所有请求的参数名称及相应的值
        Enumeration<String> parameterNames = request.getParameterNames();
        while(parameterNames.hasMoreElements()){
            String name = parameterNames.nextElement();
            String[] value = request.getParameterValues(name);
            System.out.println(name + "-----" + value[0]);
        }
        //获取所有参数的map集合
        Map<String, String[]> parameterMap = request.getParameterMap();
        Set<String> keyset = parameterMap.keySet(); //遍历
        for(String name : keyset){
            String[] values = parameterMap.get(name);
            System.out.println("该参数对应的值为:");
            for(String val : values){
                System.out.println(val);
            }
            System.out.println("-------------");
        }

    }

    protected void doGet(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
        //GET 方式获取请求参数
        this.doPost(request, response);
    }
}
```

<img src="https://gitee.com/j__strawhat/MyImages/raw/master/20210223162233.png" style="zoom:67%;" />

### ⭐️6.2.4 中文乱码问题

+ `GET` 方式：Tomcat 8 已将 `GET` 方式乱码问题解决了

+ `POST` 方式：在获取其他参数前，需要设置 `request` 的编码：

  ```java
  request.setCharacterEncoding("utf-8");
  ```

## 6.3 获取 ServletContext

+ `ServletContext getServletContext()`

## 6.4 转发和共享

Web 应用在响应客户端的一个请求时，有可能响应过程很复杂，**需要多个 Web 组件共同协作**，才能生成响应结果。

### 6.4.1 请求转发

请求转发，Servlet（源组件）先对客户端请求做一些预处理操作，然后把请求转发到其他 Web 组件（目标组件）来完成包括生成响应结果在内的后续操作。

特点：

1. 源组件和目标组件处理的都是**同一个**客户请求，故浏览器**地址栏路径**不发生变化。
2. 只能转发到当前服务器**内部**组件中。其中，目标组件都可以为 Servlet、JSP 或 HTML 文档

转发步骤：

1. 通过 `request` 对象获取 请求转发器 对象：`RequestDispatcher getRequestDispatcher(String path)`

   > 注意，此处方法是来自 `request` 的，其传入参数 `path` 既可以是绝对路径（以 `/` 开头），也可以使 相对路径。

2. 通过 `RequestDispatcher` 对象调用此方法，来进行转发：`forward(ServletRequest request, ServletResponse response)`

### 6.4.2 共享数据

域对象 ：一个有作用范围的对象，可在范围内共享数据。

**请求范围**：服务器端响应一次客户请求的过程，从 Servlet 容器接收到一个客户请求开始，到返回相应结果结束。具体实现上，请求范围与 `ServletRequest` 及 `ServletResponse` 对象的生命周期对应。

> 请求范围内的**共享数据**可作为 `ServletRequest` 对象的属性而存在。Web 组件只要**共享同一个** `ServletRequest` 对象，也就能共享请求范围内的共享数据。

相关方法：

+ 在请求范围内保存一个属性，参数 `name` 表示属性名，参数 `object` 表示属性值：`void setAttribute(String name, Object obj)`
+ 根据 `name` 参数给定的属性名，返回请求范围内的匹配的属性值：`Object getAttribute(String name)`
+ 从请求范围内删除一个属性：`void removeAttribute(String name)`

### 6.4.3 举例

<img src="https://gitee.com/j__strawhat/MyImages/raw/master/20210225001117.png" style="zoom:67%;" />

如上廖雪峰老师的图，一个映射`/morning` 的 `ForwardServlet` 中

```java
//存储数据到request域中
request.setAttribute("msg", "Hello, another Servlet!");
//转发请求
request.getRequestDispatcher("/hello").forward(request, response);
//前面两句可以合并~
```

 `ForwardServlet` 收到请求后，它并不发送响应，而是将请求和响应转发给另一个映射为为`/hello`的 `HelloServlet`中：

```java
//获取数据
Object msg = request.getAttribute("msg");
```

## ⭐️6.5 用户登录案例

源码及相关导包可到我的仓库查看：[https://github.com/J-StrawHat/LoginProject](https://github.com/J-StrawHat/LoginProject)

### 任务需求

1. 编写 `login.html` 登录页面，包含两个用户输入框：`username` 与 `passwo；rd`。
2. 登录页面中，如果用户：
   + 登录成功，则跳转到 `SuccessServlet` ，页面展示为：登录成功！`用户名`，欢迎您！
   + 登录失败，则跳转到 `FailServlet` ，页面展示为：登录失败，用户名或密码错误
3. 使用 Druid **数据库连接池**技术，操作 MySQL中已经创建好的 `userlist` 表；
4. 使用 **JdbcTemplate 技术**封装 JDBC；

### 页面效果

用户输入**已注册好**的信息：

<img src="https://gitee.com/j__strawhat/MyImages/raw/master/20210223222436.png"/>

登录成功：

<img src="https://gitee.com/j__strawhat/MyImages/raw/master/20210223222448.png"/>

登录失败：

<img src="https://gitee.com/j__strawhat/MyImages/raw/master/20210223222508.png"/>

### 环境配置

+ Java EE version：Java EE 7
+ Tomcat：8.5.63
+ Servlet：3.1
+ MySQL：5.7.30

### 附加

一、预先设计好的数据库

```sql
SHOW DATABASES;

CREATE DATABASE mydb;
USE mydb;

CREATE TABLE userlist(
	id INT PRIMARY KEY AUTO_INCREMENT,
	username VARCHAR(32) UNIQUE NOT NULL,
	PASSWORD VARCHAR(32) NOT NULL
);

DESC userlist;

INSERT INTO userlist VALUES(NULL, 'Luffy', '66666');
SELECT * FROM userlist;
```

<img src="https://gitee.com/j__strawhat/MyImages/raw/master/20210223182146.png"/>

并增加一条数据：

<img src="https://gitee.com/j__strawhat/MyImages/raw/master/20210223182409.png"/>



二、源代码中使用了 BeanUtils 简化封装 JavaBean

在 `src\top\JoyDee\web\servlet\LoginServlet.java` 中：

```java
//2. 获取所有请求参数
Map<String, String[]> parameterMap = request.getParameterMap();
//3.1 创建 User 对象
User loginUser = new User();
//3.2 使用 BeanUtils 封装
try {
    BeanUtils.populate(loginUser, parameterMap);
} catch (IllegalAccessException e) {
    e.printStackTrace();
} catch (InvocationTargetException e) {
    e.printStackTrace();
}
```



# Chapter 7. Response

在 Servlet 接口的 `service(ServletRequest request, ServletResponse response)` 方法中，另一个参数是 `ServletResponse` 类型的。

当 Servlet 容器接收到客户端要求访问特定 Servlet 的请求时，容器会创建一个 `ServletResponse` 对象，并把它作为参数传给 Servlet 的 `service()` 方法。Servlet 通过 `ServletResponse`  对象来**生成响应结果**。

在 `ServletResponse` 接口 及其子接口 `HttpServletResponse` ，定义了一系列与设置响应结果相关的方法。

## 7.1 （常用）设置响应结果相关的方法

+ 设置状态码：`setStatus(int sc)`
+ 设置响应头：`setHeader(String name, String value)`

+ 设置响应体，步骤如下：

  1. 获取输出流：

     字符输出流：`PrintWriter getWriter()`

     字节输出流：`ServletOutputStream getOutputStream()`

  2. 使用输出流，将数据输出到客户端浏览器

  > 在 Tomcat 实现中，若 Servlet 的 `service()` 方法没有调用上述的输出流`PrintWriter` 或 `ServletOutputStream` 的 `close()` 方法，那么 Tomcat 在调用完 Servlet 的 `service()` 方法后，**会关闭上述的输出流**，以确保 Servlet 输出的所有数据被提交给客户。

### 中文乱码问题

由于 `response` 及其相关方法来自 Tomcat ，故字符默认编码为 ISO-8859-1，在使用输出流将数据输出到浏览器时，有可能导致输出的中文乱码。

故，在获取（无论字节还是字符）输出流**之前**，需要设置响应体数据的编码，其方法浓缩为一条语句：

```java
response.setContentType("text/html;charset=utf-8");
```

举例如下：

```java
@WebServlet("/ResponseDemo")
public class ResponseDemo extends HttpServlet {
    protected void doPost(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
        //0. 获取流对象之前，将流的默认编码：ISO-8859-1 更改为: utf-8
        //response.setCharacterEncoding("utf-8");

        //同时，要告诉【浏览器】，服务器发送的消息体数据的编码。建议浏览器去使用开发人员制定的编码进行解码。
        response.setContentType("text/html;charset=utf-8");

        //1. 获取字符输出流
        PrintWriter pw = response.getWriter();
        //2. 输出数据
        pw.write("<h1>你好, Response!</h1>");
    }

    protected void doGet(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
        this.doPost(request, response);
    }
}
```

<img src="https://gitee.com/j__strawhat/MyImages/raw/master/20210224175730.png"/>



## 7.2 重定向

HTTP 协议规定重定向机制，其运作流程如下：

1. 用户再浏览器输入特定 URL，请求访问服务器端的某个组件
2. 服务器端的组件返回一个**状态代码为 `302`** 的响应结果（即表明，让浏览器端再请求访问另一个 Web 组件），同时响应结果中还**提供了另一 Wen 组件的 URL**（这另一组件有可能是同一 Web 服务器，也有可能不在同一个 Web 服务器上）
3. 当浏览器端接收到这种响应结果后，再立即**自动请求**访问另一个 Web 组件。
4. 浏览器端接收到来自另一 Web 组件的响应结果。

如下图所示：

<img src="https://gitee.com/j__strawhat/MyImages/raw/master/20210225114927.png" style="zoom:67%;" />

`HttpServletResponse` 接口有一方法便是用于实现重定向：

+ `sendRedirect(String location)`

### ⭐️重定向与转发的区别

|             转发（`forward`）             |     重定向（`redirect`）     |
| :---------------------------------------: | :--------------------------: |
|             浏览器地址栏不变              |     浏览器地址栏发生变化     |
|        只能访问当前服务器下的资源         | 可访问其他站点（服务器）资源 |
| 一次请求，可使用<br>`request`对象共享数据 |      两次请求，无法共享      |

> 可理解为，转发：该商店没有该顾客需要的货，商家去别的店为他调货；重定向：商家让顾客去其他的商店。

### ⭐️关于路径

#### 路径分类

（一）相对路径：不以 `/` 开头，而是以 `.` 开头。如：`./index.html`

规则：需要找到当前资源和目标资源之间的相对位置关系。

+ `./`：为当前目录，其中⭐️ **`./` 可以省略不写**
+ `../`：后退一级目录

（二）绝对路径：以 `/` 开头的路径，如：`http://localhost/day15/responseDemo2`  简写为 `/day15/responseDemo2`

规则：判定请求从哪里发出

+ 若给客户端浏览器使用：需要加上 虚拟目录（项目的**入口**的映射路径），如：`<a>`、`<form>` 重定向 等

  ```html
  <a href = "/day15/responseDemo">前往登陆</a>
  <!--浏览器点击（说明请求来自于客户端浏览器）-->
  ```

  ```java
  response.sendRedirect("/day15/responseDemo");
  ```

+ 给服务器使用：无需加 虚拟目录，如 转发路径。

  ```java
  request.getRequestDispatcher("/responseDemo2").forward(request, response);
  ```

#### 动态获取虚拟目录

如上的绝对路径中，若将项目中的代码将虚拟目录“写死”了，改动较大。为解决该问题，可调用 **`request`** 的方法获取动态的虚拟目录字符串：

+ `getContextPath()`：（Request 章节中有提及）返回客户端所请求访问的 Web 应用的 URL 入口。

  > 如客户端访问 URL 为：`http://localhost:8080/helloapp/info`，那么该方法返回 `"/helloapp"`

```java
String contextPath = request.getContextPath(); //注意是从 request 获取
response.sendRedirect(contextPath + "/responseDemo2"); //调用的 response 的重定向方法
```

另外， `ServletContext` 的 `getRealPath(String path)` 方法是，根据虚拟路径，返回文件系统中的一个真实路径

## 7.3 文件下载案例

### 需求

1. 页面显示超链接
2. 点击超链接后**弹出下载提示框**
3. 完成图片文件下载

### 步骤：

1. 定义 `download.html` 页面，编辑超链接元素的 `href` 属性，**指向 `Servlet`**，**传递资源名称** `filename`
2. 定义 `Servlet`：
   + 获取传递的参数，即文件名称
   + 使用**字节输入流**加载文件进内存
   + 指定 `response ` **响应头**：
     + `content-type=文件的MIME类型`
     + `content-disposition:attachment;filename=文件名`
   + 将数据写出到 `response ` 的输出流

<img src="https://gitee.com/j__strawhat/MyImages/raw/master/20210225133525.png"/>

`download.html` 文件：

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Download Here</title>
</head>
<body>
<a href = "/MyEEP/imgs/a.jpg">图片预览</a>
<a href = "/MyEEP/DownloadServlet?filename=a.jpg">下载图片</a>
</body>
</html>
```

`DownloadServlet.java` 文件：

```java
package top.JoyDee.servlet;

import javax.servlet.ServletContext;
import javax.servlet.ServletException;
import javax.servlet.ServletOutputStream;
import javax.servlet.annotation.WebServlet;
import javax.servlet.http.HttpServlet;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;
import java.io.FileInputStream;
import java.io.IOException;

@WebServlet("/DownloadServlet")
public class DownloadServlet extends HttpServlet {
    protected void doPost(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
        //1. 获取请求参数，文件名称
        String filename = request.getParameter("filename");
        //2. 使用字节输入流加载文件进内存
        //2.1 找到文件服务器路径
        ServletContext servletContext = this.getServletContext();
        String realPath = servletContext.getRealPath("/imgs/"+filename);
        //2.2 用字节输入流将服务器中的文件进行关联
        FileInputStream fis = new FileInputStream(realPath);

        //3. 设置response的响应头
        //3.1 设置响应头类型：content-type，从而指定下载文件的类型
        String mimeType = servletContext.getMimeType(filename);
        response.setHeader("content-type", mimeType);
        //3.2 设置响应头打开方式：content-disposition
        response.setHeader("content-disposition", "attachment;filename=" + filename);

        //4. 将输入流的数据写出到输出流中
        ServletOutputStream sos = response.getOutputStream();
        byte[] buff = new byte[1024 * 8];
        int len = 0;
        while((len = fis.read(buff)) != -1){ //读入一个字节数组，返回实际读入的字节数
            sos.write(buff, 0, len); //写出知道了长度为len的字节数组（从0开始）
        }
    }
    protected void doGet(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
        this.doPost(request, response);
    }
}
```

