---
layout: post
title: CDI Features
date: 2019-04-10
tags:
  - JAVA 
categories: 
  - JAVA
---
==第七周WEB JAVA作业（2）==
<!-- more -->

一、EL/SpEL

1、EL语言(CDI与表达式语言（EL）集成，允许在JavaServer Faces页面或JavaServer Pages页面中直接使用任何组件)

1）概述；
EL是JSP内置的表达式语言，用以访问页面的上下文以及不同作用域中的对象 ，
取得对象属性的值，或执行简单的运算或判断操作。EL在得到某个数据时，会自动进行数据类型的转换。
使用EL表达式输出数据时，如果有则输出数据，如果为null则什么也不输出。

2）语法：
a.EL表达式总是放在{}中，而且前边有一个作为前缀:作为前缀:作为前缀:{}
b.获取对象的属性值可以直接通过“对象.属性名”:user.name;注意：这里的属性名是get和set方法对应的属性值，并不是对象中的变量名。c.获取对象的属性也可以通过“对象[“属性名”]”:{user.name};注意：这里的属性名是get和set方法对应的属性值，并不是对象中的变量名。c.获取对象的属性也可以通过“对象[“属性名”]”:user.name;注意：这里的属性名是get和set方法对应的属性值，并不是对象中的变量名。c.获取对象的属性也可以通过“对象[“属性名”]”:{user[“name”]}
d.获取Map中属性时可以以直接通过属性的key:map.key,{map.key},map.key,{map[key]}
e.在指定域中获取属性:
在EL表达式中如果我们直接使用属性名如：${user}，它将会在四个域中由小到大依次查找。
顺序：pageScope、requestScope、sessionScope、applicationScope。
也可以指定从哪个域中获取：
${ pageScope .user }：当前页面
${requestScope.user}：当前请求
${sessionScope.user}：当前会话
${sessionScope.user}：当前应用

3）EL中包含11个隐含对象，这些对象可以在EL表达式中直接使用：
a.pageContext,和JSP中的pageContext功能一样
b.请求域:pageScope/requestScope/sessionScope/applicationScope
c.请求参数,参数对象主要用于获取get或post请求中的参数:
param:获取指定的请求参数,param.usernameparamValues:获取请求参数数组，如：{param.username}paramValues:获取请求参数数组，如：param.usernameparamValues:获取请求参数数组，如：{paramValues.sport[1]}
d.其他:header/headerValues/initParam/cookie

4)EL支持数学运算和逻辑运算：
a.加减乘除:17+5=>22b.取余{17+5} =&gt; 22b.取余%或mod：17+5=>22b.取余{17%5} => 2
c.比较运算符>,<,==,!=,<=,>=,eq,ne,lt,gt,le,ge：3>5或{3&gt;5}或3>5或{3 gt 5} =>false
d.逻辑比较 &&或and，!或not，||或or，empty：${!true} => false

2.SpEL

1）Spring框架的表达式语言（简称SpEL）：是一个支持运行时查询和操作对象图的强大的表达式语言。
SpEL 为 bean 的属性进行动态赋值提供了便利.

2）语法：SpEL 使用 #{…} 作为定界符，所有在大框号中的字符都将被认为是 SpEL。

3）运用范围：
a. 对 bean 进行引用，调用属性值：#{[book.name](http://book.name/)}
b.调用方法以及引用对象中的属性
引用方法：#{dog.run()},引用静态方法：#{T(java.lang.Math).PI}
引用对象的属性：#{[user.name](http://user.name/)}
c.计算表达式的值
加减乘除：#{counter.total + 40},#{T(java.lang.Math).PI * 2}
加号作为字符串连接符：#{[user.name](http://user.name/) + ’ ’ + user.address}
比较运算符(>,<,=,>=,<=,,lt,gt,eq,le,ge)：
#{counter.total == 100},#{counter.total le 1000}
if-else条件判断，三元运算符：
#{[user.name](http://user.name/)’Tom’ ? ‘Jess’}

d.正则表达式的匹配(matches)
#{[user.name](http://user.name/) matches ‘[1](https://blog.csdn.net/weixin_38182535/article/details/89165171#fn1){4,16}$’}
e.字面量的表示：
#{5},#{89.7},#{1e4},#{false}
可使用单/双引号作为字符串表达符号:#{‘Chuck’},#{“Chuck”}

二、Decorator(装饰注入组件的能力)

@Decorator

功能：修饰器，横向
注册一个修饰器类PaintedDecorator.java

@Decorator
public class PaintedDecorator implements Room {

@Inject
@Delegate
Room roomToBeDecorated; //注册被修饰的房子，这里是BlankRoom

public String showRoom() {
doPainting();
return roomToBeDecorated.showRoom() + “刷墙漆”;
}

## // 刷墙漆
private void doPainting() {
System.out.print(“刷墙漆”);
}
}

@Decorator
public class FlooredDecorator implements Room {

@Inject
@Delegate
Room roomToBeDecorated; //注册被修饰的房子，这里是BlankRoom

public String showRoom() {
doFlooring();
return roomToBeDecorated.showRoom() + “铺地板”;
}
// 铺地板
private void doFlooring() {
System.out.print(“装饰地板”);
}
}
注册两个被修饰类BlankRoom.java,修饰的基础

public class BlankRoom implements Room {

public String showRoom() {
return “毛胚房”;
}
}

激活修饰器，执行顺序由下至上

cn.edu.sdut.softlab.FlooredDecorator cn.edu.sdut.softlab.PaintedDecorator 具体实现

@Named(“room”)
public class RoomController {

@Inject
Room room;

public String showRoom() {
return room.showRoom();
}
}
输出

毛胚房刷墙漆铺地板

三、Interceptor(使用类型安全拦截器绑定将拦截器与组件相关联的能力)

@Interceptor

拦截器

功能：断开逻辑插入功能，比如Logger
定义一个拦截器LoggedInterceptor.java
@Logged
@Interceptor
public class LoggedInterceptor implements Serializable {

public LoggedInterceptor(){}

// 拦截之后具体实现
@AroundInvoke
public Object logMethod(InvocationContext ctx) throws Exception {
System.out.println(“Entering method:” + ctx.getMethod().getName()

*   " in class: " + ctx.getMethod().getDeclaringClass().getName());
    return ctx.proceed();
    }
    }
    调用拦截器

@Logged
public void checkin(){
System.out.println(“checkin room…”);
}
当然拦截器需要在beans.xml上激活

cn.edu.sdut.r314.AuditInterceptor
cn.edu.sdut.r314.LoggedInterceptor

当然拦截器也可以修饰一整个组件，这样只要调用这个组件的时候就会执行拦截器的内容

@Named
@Audit
public class RoomController {

/**

*   订房
*   @return
    */
    public void checkin(){
    System.out.println(“checkin room…”);
    }

/**

*   退房
*   @return
    */
    public void checkout(){
    System.out.println(“checkout room…”);
    }

四、Producer(动态注入)

@Produces

功能: 制造组件,自定义组件分配

我们先定义好一个接口Tasks.java以及他的实现类AsyncTask.java , SyncTask.java。以及一个枚举类TaskType.java

public enum TaskType {
ASYNC,SYNC;
}
然后看一下@Produces是怎么调用的

@RequestScoped
public class TaskProducers implements Serializable {

TaskType taskType = TaskType.ASYNC;
public TaskProducers() {
System.out.println(“TaskProducers constructor called”);
}

/**

*   change from @RequestScoped to @ApplicationScoped to see what happened.

*   @TODO why @SessionScoped failed here?
*   @param asyncTask async task
*   @param syncTask sync task
*   @return Task according to TaskType
    */
    @Produces
    @Preferred
    @SessionScoped
    public Task getTask(AsyncTask asyncTask, SyncTask syncTask) {
    System.out.println(“getTask called…”);
    switch (taskType) {
    case ASYNC:
    return asyncTask;
    case SYNC:
    return syncTask;
    default:
    return null;
    }
    }
    }
    大家看到这里@Produces修饰Task类型的getTask()方法，并且添加一个Qualifter: @Preferred,也就是说produces 也可以通过qualifter来区分。而且大家都知道在CDI中名字并不重要，重要的是组件类型。也就是说getTask()方法可以修改为任意名字。还有就是可被申请的组件需要同过参数的形式注入。

调用组件TaskController.java

@Model
public class TaskController {

@Inject
@Preferred
Task task;

public String sayHello() {
return “hello task:” + task.getName();
}

@PostConstruct
public void init() {
System.out.println(“GreetingController post construct…”);
}

@PreDestroy
public void destroy() {
System.out.println(“GreetingController pre destroy…”);
}
}
这样调用的就是Async组件；

介绍其他几个注解：

@Model = @Name + @RequestScope
@PostConstruct:初始化组件，功能相当于构造器
@PreDestory:销毁组件
