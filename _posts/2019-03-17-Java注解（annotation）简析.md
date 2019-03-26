---
layout: post
title: Java注解（annotation）简析
date: 2019-03-17
tags:
  - annotation
categories: 
  - JAVA
---

==第三周WEB JAVA作业（2）==

<!-- more -->

## 概念

Annotation其实是代码里的特殊标记，这些标记可以在编译、类加载、运行时被读取，并执行相应的处理。通过使用Annotation，程序开发人员可以在不改变原有逻辑的情况下，在源文件嵌入一些补充信息。代码分析工具、开发工具和部署工具可以通过这些补充信息进行验证或者进行部署。

Annotation提供了一条为程序元素设置元数据的方法，从某些方面来看，Annotation就像修饰符一样被使用，可用于修饰包、类、构造器、方法、成员变量、参数、局部变量的声明，这些信息被存储在Annotation的“name=value”对中。

## 元注解

### 基本内置注解

@Override

它的作用是对覆盖超类中方法的方法进行标记，如果被标记的方法并没有实际覆盖超类中的方法，则编译器会发出错误警告。

```java
/**
* 测试Override注解`
* @author Administrator
*/
public class OverrideDemoTest {
  //@Override
  public String tostring() {
    return "测试注解";
  }
}
```

@Deprecated

它的作用是对不应该再使用的方法添加注解，当编程人员使用这些方法时，将会在编译时显示提示信息，它与javadoc里的@deprecated标记有相同的功能，准确的说，它还不如javadoc @deprecated，因为它不支持参数，使用@Deprecated的示例代码示例如下：

```java
/**
 * 测试Deprecated注解
 * @author Administrator
 */
public class DeprecatedDemoTest {
    public static void main(String[]args) {
        // 使用DeprecatedClass里声明被过时的方法
        DeprecatedClass.DeprecatedMethod();
    }
}
 
class DeprecatedClass {
    @Deprecated
    public static void DeprecatedMethod() {
    }
}
```


@SuppressWarnings

其参数有：

deprecation，使用了过时的类或方法时的警告

unchecked，执行了未检查的转换时的警告

fallthrough，当 switch 程序块直接通往下一种情况而没有 break 时的警告

path，在类路径、源文件路径等中有不存在的路径时的警告

serial，当在可序列化的类上缺少serialVersionUID 定义时的警告

finally ，任何 finally 子句不能正常完成时的警告

all，关于以上所有情况的警告

```java
import java.util.ArrayList;
import java.util.List;
 
public class SuppressWarningsDemoTest {
    public static List list = new ArrayList();
    @SuppressWarnings("unchecked")
    public void add(String data) {
        list.add(data);
    }
}
```

### 自定义注解

它类似于新创建一个接口文件，但为了区分，我们需要将它声明为@interface,如下例：

```java
Java代码
public @interface NewAnnotation {
}
```


使用自定义的注解类型

```java
public class AnnotationTest {
    @NewAnnotation
    public static void main(String[]args) {
    }
}
```


为自定义注解添加变量

```java
public @interface NewAnnotation {
    String value();
}
```

```java
public class AnnotationTest {
    @NewAnnotation("mainmethod")
    public static void main(String[]args) {
        saying();
    }
     
    @NewAnnotation(value="saymethod")
    public static void saying() {
    }
}
```

定义一个枚举类型，然后将参数设置为该枚举类型，并赋予默认值

```java
public @interface Greeting {
    public enum FontColor {
        BLUE,
        RED,
        GREEN
    };
    String name();
    FontColor fontColor() default FontColor.RED;
}
```

这里有两种选择，其实变数也就是在赋予默认值的参数上，我们可以选择使用该默认值，也可以重新设置一个值来替换默认值


```java
public class AnnotationTest {
    @NewAnnotation("mainmethod")
    public static void main(String[]args) {
        saying();
        sayHelloWithDefaultFontColor();
        sayHelloWithRedFontColor();
    }
 
    @NewAnnotation("saymethod")
    public static void saying() {
    }
     
    // 此时的fontColor为默认的RED
    @Greeting(name="defaultfontcolor")
    public static void sayHelloWithDefaultFontColor() {
    }
 
    // 将fontColor改为BLUE
    @Greeting(name="notdefault", fontColor=Greeting.FontColor.BLUE)
    public static void sayHelloWithRedFontColor() {
    }
}
```

