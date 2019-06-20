---
layout: post
title: servlet injection analysis
date: 2019-03-26
tags:
  - JAVA 
  - 注入
categories: 
  - JAVA
---
==第五周WEB JAVA作业（2）==
<!-- more -->

@WebServlet("/cdiservlet")
public class NewServlet extends HttpServlet {
private Message message;

```
@Override             ||重写doGet方法
public void init() {
    message = new MessageB();    ||对message初始化
}

@Override
public void doGet(HttpServletRequest request, HttpServletResponse response)
              throws IOException {              ||抛出可能出现IO异常
    response.getWriter().write(message.get());   ||捕获可能发生的异常
}

```

}

public interface Message { ||servlet实现Message接口的一个对象实例
public String get();
}
public class MessageB implements Message { ||servlet创建以下对象的实例
public MessageB() { }

```
@Override
public String get() {
    return "message B";
}

```

}
@WebServlet("/cdiservlet") ||使用CDI，servlet声明它对Message实例的依赖性，并让它由CDI运行时自动注入
public class NewServlet extends HttpServlet {
@Inject private Message message;

```
@Override
public void doGet(HttpServletRequest request, HttpServletResponse response)
              throws IOException {       ||抛出可能出现IO异常
      response.getWriter().write(message.get());  ||捕获可能发生的异常

```

}
}
