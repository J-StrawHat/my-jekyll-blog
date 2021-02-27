---
title: 「JavaWeb学习笔记07」过滤器(Filter)与监听器(Listener)
author: JoyDee
categories: [Java, JavaWeb]
tags: [JavaWeb]
date: 2021-02-26 14:15:00 +0800
---

# Chapter 12. 过滤器 Filter 与监听器(Listener)

## 12.1 过滤器概述

过滤器，能够对一部分客户请求先进行**预处理操作**，然后再把请求转发给响应的 Web 组件，等到 Web 组件生成了响应结果后，过滤器还能对响应结果进行**检查和修改**，然后再把修改后的响应结果发送给客户。

> 各个 Web 组件中的相同操作可放到同一个过滤器中来完成，这样即能减少重复编码。

具体来说，过滤器为 Web 组件（如 Servlet、JSP 或 HTML）提供如下过滤功能：

+ 在 Web 组件被调用之前**检查** ServletRequest 对象，**修改**请求头和请求正文的内容，或者对请求进行**预处理**操作。
+ 在 Web 组件被调用之后**检查** ServletResponse 对象，**修改**响应头和响应正文。

## 12.2 Filter 快速入门

所有自定义的过滤器必须实现 `javax.servlet.Filter` 接口，该接口含三个过滤器类必须实现的方法，见下：

```java
@WebFilter("/*")//当客户请求访问的URL与过滤器映射的URL匹配时，会先调用过滤器的doFilter()
public class FilterDemo1 implements Filter {
    @Override
    public void init(FilterConfig filterConfig) throws ServletException {
 		//过滤器的初始方法，该方法可通过 config 参数来读取 web.xml 文件中为过滤器配置的初始化参数
    }

    @Override
    public void doFilter(ServletRequest servletRequest, ServletResponse servletResponse, FilterChain filterChain) throws IOException, ServletException {
        
        //该方法完成实际的过滤操作，FilterChain 参数用于访问后续过滤器或Web组件
		
        /*  执行流程  */
        //①执行过滤器
        
        //②执行放行后的资源
        filterChain.doFilter(servletRequest,servletResponse);
        
        //③回来后，执行过滤器放行下边的代码
    }
    @Override
    public void destroy() {
		//销毁过滤器对象前调用该方法，该方法可释放过滤器占用的资源
    }
}
```

注意，两次启动原因：Tomcat 配置里面勾选了 “after launch” 选项，导致在服务器启动，自动访问了一次 `index.jsp`

当然，过滤器**映射的 URL** ，可在 `web\WEB-INF` 目录下的 `web.xml` 文档中配置：

> 在 `web.xml` 文件中，必须先**配置所有过滤器**，再配置 Servlet 。

```xml
<filter>
    <filter-name>demo1</filter-name>
    <filter-class>cn.itcast.web.filter.FilterDemo1</filter-class>
</filter>
<filter-mapping>
    <filter-name>demo1</filter-name>
    <!-- 拦截路径 -->
    <url-pattern>/*</url-pattern>
</filter-mapping>
```

## 12.3 过滤器生命周期

过滤器由 Servlet 容器创建，在它的生命周期中包含以下三个阶段：

+ **初始化阶段**：当 Web 应用启动时，Servlet 容器会加载过滤器类，创建过滤器配置对象（`FilterConfig`）和过滤器对象，并调用过滤器对象的 `init(FilterConfig config)` 方法
+ **运行时阶段**：当客户请求访问的 URL 与为过滤器映射的 URL 匹配时，Servlet 容器将先调用过滤器的 `doFilter` 方法。
+ **销毁阶段**：当 Web 应用终止时，Servlet 容器将先调用过滤器对象的 `destroy()` 方法，然后销毁过滤器对象。

## 12.4 过滤器的相关拦截配置

### 12.4.1 拦截路径配置

1. 具体资源路径：`/index.jsp` ，只有访问 `index.jsp` 资源时，过滤器才会被执行。
2. 拦截目录：`/user/*` ，访问 `/user` 下的所有资源时，过滤器都会被执行。
3. 后缀名拦截：`*.jsp`，访问所有后缀名为 `jsp` 资源时，过滤器都会被执行。
4. 拦截所有资源：`/*`，访问所有资源时，过滤器都会被执行。

### 12.4.2 拦截方式配置

即以什么方式访问资源时会拦截。

+ 注解配置：设置 `dispatcherTypes` 属性：
  + `REQUEST`：默认值，浏览器直接请求资源
  + `FORWARD`：转发访问资源
  + `INCLUDE`：包含访问资源
  + `ERROR`：错误跳转资源
  + `ASYNC`：异步访问资源
+ `web.xml` 配置：设置 `<dispatcher></dispatcher>` 标签即可。

## 12.5 过滤器链

过滤器优先级问题：

+ 注解配置：按照类名的字符串字典序比较，字典序小的先执行。
+ `web.xml` 配置：`<filter-mapping>` 谁定义在上边，谁先执行。

假设过滤器 A的优先级高于过滤器 B，则执行顺序如下，类似于栈的后进先出

```java
1. 过滤器A
2. 过滤器B
3. 资源执行
4. 过滤器B
5. 过滤器A
```

## 12.6 监听器 Listener

### 12.6.1 事件监听机制

+ 事件
+ 事件源：事件发生的地方
+ 监听器：一个对象
+ 注册监听：将事件、事件源、监听器绑定在一起。当事件源上发生某个事件后，执行监听器代码。

### 12.6.2 ServletContextListener

`ServletContextListener` 监听 ServletContext 对象的创建和销毁

#### 方法

+ `void contextDestroyed(ServletContextEvent sce)`：ServletContext 对象被销毁之前会调用该方法
+ `void contextInitialized(ServletContextEvent sce)`：ServletContext 对象创建后会调用该方法

#### 步骤

1. 定义一个类，实现 `ServletContextListener` 接口

2. 覆写上述两个方法

3. 配置方式：

   方式一——`web.xml` 配置

   ```xml
   <listener>
   	<listener-class>
           cn.itcast.web.listener.ContextLoaderListener
       </listener-class>
   </listener>
   ```

   

   方式二——注解：`@WebListener`