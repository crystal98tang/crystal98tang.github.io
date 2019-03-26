---
layout: post
title: Building Tools
date: 2019-03-10
categories: 
  - JAVA
tags:
  - Maven
  - Building Tool
---

==第二周WEB JAVA作业（2）==

<!-- more -->

### 概念

　　构建系统(build system)是用来从源代码生成用户可以使用的目标(targets)的自动化工具。目标可以包括库、可执行文件、或者生成的脚本等等。常用的构建系统包括GNU Make、GNU autotools、CMake、Apache Ant（主要用于JAVA）。此外，所有的集成开发环境（IDE）比如Qt Creator、Microsoft Visual Studio和Eclipse都对他们支持的语言添加了自己的构建系统配置工具。通常IDE中的构建系统只是基于控制台的构建系统（比如Autotool和CMake）的前端。

### 构建工具的任务

基本上构建工具的任务是编写或使一大部分任务自动执行的一个动作，而这些任务则是软件开发者的日常，像是：

1.  下载依赖
2.  将源代码编译成二进制代码
3.  打包生成的二进制代码
4.  进行单元测试
5.  部署到生产系统

### 为什么要使用构建工具

　　在小型项目中，开发者往往手动调用构建过程，这样在大型的项目中很不实用，在构建过程中难以跟踪什么需要被构建、按照什么顺序构建以及项目中存在哪些依赖。使用自动化工具会使构建过程更为连续。

### Java上的构建工具maven

　　Maven是一个[项目管理工具](https://baike.baidu.com/item/%E9%A1%B9%E7%9B%AE%E7%AE%A1%E7%90%86%E5%B7%A5%E5%85%B7)，它包含了一个项目对象模型 (Project Object Model)，一组标准集合，一个[项目生命周期](https://baike.baidu.com/item/%E9%A1%B9%E7%9B%AE%E7%94%9F%E5%91%BD%E5%91%A8%E6%9C%9F)(Project Lifecycle)，一个依赖管理系统(Dependency Management System)，和用来运行定义在生命周期阶段(phase)中[插件](https://baike.baidu.com/item/%E6%8F%92%E4%BB%B6)(plugin)目标(goal)的逻辑。当你使用Maven的时候，你用一个明确定义的项目对象模型来描述你的项目，然后Maven可以应用横切的逻辑，这些逻辑来自一组共享的（或者自定义的）插件。

　　Maven 有一个生命周期，当你运行 mvn install 的时候被调用。这条命令告诉 Maven 执行一系列的有序的步骤，直到到达你指定的生命周期。遍历生命周期旅途中的一个影响就是，Maven 运行了许多默认的[插件](https://baike.baidu.com/item/%E6%8F%92%E4%BB%B6)目标，这些目标完成了像编译和创建一个 JAR 文件这样的工作。

　　此外，Maven能够很方便的帮你管理项目报告，生成站点，管理JAR文件，等等。
