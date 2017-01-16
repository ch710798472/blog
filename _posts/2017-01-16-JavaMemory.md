---
layout: post
title: STOP THE WORLD -- JAVA MEMORY and GC
category: java
modified: 2017-01-16
tags: [java]
comments: true
pinned: true
excerpt: "1.运行时数据区 2.对象的创建 3.对象的死亡 4.垃圾收集算法与收集器..."
---
### 1.简介
　　Java内存模型网上资料一搜一大把，为什么还要写，我的目的是给自己一个总结的机会和查资料不需要四处Google。
   `STOP THE WORLD!`
　　
### 2.运行时数据区
　　JVM所管理的内存将包括以下几个Java虚拟机运行时数据区：
    1.线程共享：
    `方法区 Method Area`
    `堆 Heap`
    2.线程隔离：
    `虚拟机栈 VM Stack`
    `本地方法栈 Native Method Stack`
    `程序计数器 Program Counter Register`
    方法区：别名Non-Heap,虽然jvm规范将方法区描述为堆的一个逻辑部分，但是从别名可以看出来并不是一回事。它存储JVM加载的类的信息、常量、
静态变量、即时编译器编译后的代码等数据。有时候在HotSpot中，它还跟"永久代"一起提，是因为GC分代扩展到了方法区，或者说使用永久代来实现方法区。
    堆：JVM中最大的一块内存区域（不然怎么叫面向对象编程呢？），它包含了几乎所有对象的实例（TLAB(Thread-local allocation buffer)和栈上(逃逸分析)也会分配）。
现代收集器都是基于分代的收集算法，所以JAVA堆细分为：新生代(Eden+From Survivor+To Survivor)和老年代。
    虚拟机栈：如果熟悉C中方法调用时候栈的分配以及执行过程，VM Stack理解下来简直不费力气。方法调用会产生一个栈帧，用来记录局部变量、入参、动态
链接地址、返回地址等信息。
    本地方法栈：Native方法执行栈，与VM Stack类似。
    程序计数器：有点类似汇编中的PC，与C中的下一条执行地址一样，都是用来控制程序执行的流程，实现分支循环跳转异常处理等基础功能的依赖。
    
    `直接内存 Direct Memory`
    直接内存并不是虚拟机运行时数据区的一部分，而不是JVM规范中定义的内存区域，但是这部分在很多地方被使用，所以还是提一下比较好，在JDK1.4中新加入
的NIO就使用到这一块的内存，再比如Netty框架中也使用到了，有可能错误的使用方式还是会导致OOME，所以OOME不一定只会在运行时数据区发生，有可能会在Direct Memory中发生。    

### 3.对象的创建
   * 这里仅叙述从new关键字创建的对象，不包括数组和Class对象等。
　　![图片1](https://github.com/ch710798472/blog/raw/gh-pages/img/NewObject.png)
   * 在HotSpot虚拟机中，对象在内存的存储布局可以分为三个区域：对象头(Header)，实例数据(Instance Data)和对齐填充(Padding).
　　![图片2](https://github.com/ch710798472/blog/raw/gh-pages/img/ObjectHead.png)
   * 对象的访问定位,如果明白汇编的间接寻址和直接寻址等会很容易理解。
   ![图片3](https://github.com/ch710798472/blog/raw/gh-pages/img/ObjectRef.jpg)

### 4.对象的死亡
   * 引用计数算法
   * 可达性分析算法
### 5.垃圾收集算法与收集器
　　
