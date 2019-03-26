---
layout: post
title: Java部署描述符（deployment descriptor）简析
date: 2019-03-16
tags:
  - 描述符
categories: 
  - JAVA
---

==第三周WEB JAVA作业（1）==

<!-- more -->

### 定义头和根元素

部署描述符表达了应用开发人员、应用集成人员和Web应用部署人员之间的元素和配置信息。
部署描述符文件就像所有XML文件一样，必须以一个XML头开始。这个头声明可以使用的XML版本并给出文件的字符编码。
DOCYTPE声明必须立即出现在此头之后。这个声明告诉服务器适用的servlet规范的版本（如2.2或2.3）并指定管理此文件其余部分内容的语法的DTD(Document Type Definition，文档类型定义)。
所有部署描述符文件的顶层（根）元素为web-app。请注意，XML元素不像HTML，他们是大小写敏感的。因此，web-App和WEB-APP都是不合法的，web-app必须用小写。

### 配置介绍

#### <discription></discription> 是对站台的描述

#### <display-name></display-name> 定义站台的名称

#### <distributable><distributable/> 是指定该站台是否可分布式处理

#### <context-param></context-param> 用来设定web站台的环境参数，它包含两个子元素：
    * <param-name></param-name> 用来指定参数的名称
    * <param-value></param-value> 用来设定参数值

比如：
```html
<context-param>
    <param-name>my_param</param-name>
    <param-value>hello</param-value>
</context-param>
```

此设定的参数，可以在servlet中用getServletContext().getInitParameter("my_param") 来取得


<filter></filter> 是用来声明filter的相关设定，它包含以下子元素：
    * <filter-name></filter-name> 这当然就是指定filter的名字
    * <filter-class></filter-class> 这是用来定义filter的类的名称
    * <init-param></init-param> 用来定义参数，它有两个子元素：
    * <param-name></param-name> 用来指定参数的名称
    * <param-value></param-value> 用来设定参数值

比如：
```html
<filter>
<filter-name>setCharacterEncoding</filter-name>
<filter-class>com.myTest.setCharacterEncodingFilter</filter-class>
<init-param>
<param-name>encoding</param-name>
<param-value>GB2312</param-value>
</init-param>
</filter>
```

与<filter></filter>同时使用的是<filter-mapping></filter-mapping> 用来定义filter所对应的URL，它有两个子元素：
    * <filter-name></filter-name> 指定filter的名字
    * <url-pattern></url-pattern> 指定filter所对应的URL

比如：
```html
<filter-mapping>
<filter-name>setCharacterEncoding</filter-name>
<url-pattern>/*</url-pattern>
</filter-mapping>
<listener></listener> 用来设定Listener接口，它的主要子元素为
    <listener-class></listener-class> 定义Listener的类名称
```



比如：
```html
<listener>
<listener-class>com.myTest.ContextListener</listener-class> 
</listener>
```

<servlet></servlet> 用来声明一个servlet的数据，主要有以下子元素：
    <servlet-name></servlet-name> 指定servlet的名称
    <servlet-class></servlet-class> 指定servlet的类名称
    <jsp-file></jsp-file> 指定web站台中的某个JSP网页的完整路径
    <init-param></init-param> 用来定义参数，和前面的<init-param>差不多

同样，与<servlet></servlet>一起使用的是<servlet-mapping></servlet-mapping> 用来定义servlet所对应的URL，包含两个子元素：
    <servlet-name></servlet-name> 指定servlet的名称
    <url-pattern></url-pattern> 指定servlet所对应的URL

配置servlet路径
```html
<servlet>
<servlet-name>Hello1</servlet-name>
<servlet-class>china.hubei.HelloServelt</servlet-class>
</servlet>
<servlet-mapping>
<servlet-name>Hello1</servlet-name>
<url-pattern>/</url-pattern>
</servlet-mapping>
```



**参考文献**
* [JavaWeb里面web.xml配置部署描述符文件详解](https://blog.csdn.net/hsd2012/article/details/50936297)
* [servlet3.1规范翻译：第14章 部署描述符](https://blog.csdn.net/mhmyqn/article/details/8599422)
