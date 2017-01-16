---
layout: post
title: STOP THE WORLD -- JAVA MEMORY and GC
category: java
modified: 2017-01-16
tags: [java]
comments: true
pinned: true
excerpt: "1.运行时数据区 2.对象的创建 3.对象的死亡 4.垃圾收集算法与收集器 5.内存分配策略..."
---
### 1.简介
　　Java内存模型网上资料一搜一大把，为什么还要写，我的目的是给自己一个总结的机会和查资料不需要四处Google。
   `STOP THE WORLD!`
　　
### 2.运行时数据区
　　JVM所管理的内存将包括以下几个Java虚拟机运行时数据区：
    **1.线程共享：**
    `方法区 Method Area`
    `堆 Heap`
    **2.线程隔离：**
    `虚拟机栈 VM Stack`
    `本地方法栈 Native Method Stack`
    `程序计数器 Program Counter Register`
    **方法区**：别名Non-Heap,虽然jvm规范将方法区描述为堆的一个逻辑部分，但是从别名可以看出来并不是一回事。它存储JVM加载的类的信息、常量、
静态变量、即时编译器编译后的代码等数据。有时候在HotSpot中，它还跟"永久代"一起提，是因为GC分代扩展到了方法区，或者说使用永久代来实现方法区。
    **堆**：JVM中最大的一块内存区域（不然怎么叫面向对象编程呢？），它包含了几乎所有对象的实例（TLAB(Thread-local allocation buffer)和栈上(逃逸分析)也会分配）。
现代收集器都是基于分代的收集算法，所以JAVA堆细分为：新生代(Eden+From Survivor+To Survivor)和老年代。
    **虚拟机栈**：如果熟悉C中方法调用时候栈的分配以及执行过程，VM Stack理解下来简直不费力气。方法调用会产生一个栈帧，用来记录局部变量、入参、动态
链接地址、返回地址等信息。
    **本地方法栈**：Native方法执行栈，与VM Stack类似。
    **程序计数器**：有点类似汇编中的PC，与C中的下一条执行地址一样，都是用来控制程序执行的流程，实现分支循环跳转异常处理等基础功能的依赖。    
    `直接内存 Direct Memory`
    **直接内存**并不是虚拟机运行时数据区的一部分，而不是JVM规范中定义的内存区域，但是这部分在很多地方被使用，所以还是提一下比较好，在JDK1.4中新加入
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
   </br>判断对象是否存活：给对象添加一个引用计数器，每当有一个地方引用了计数器就+1，失效了就-1，只要计数器的值为0就代表对象不可能再被使用。
   基本上属于实现比较简单、高效，但是有一个致命的缺陷，那就是循环引用问题。
   * 可达性分析算法
   </br>从"GC ROOT"出发，向下搜索，当一个对象没有任何引用链和GC ROOT相连（即对象不可达），就说明对象不可能再被使用了。
     GC ROOT的对象主要包括：
       + 虚拟机栈中引用的对象
       + 方法区中类静态属性引用对象
       + 方法区中常量引用的对象
       + 本地方法栈中Native引用的对象
   
   JDK1.2之后，将JAVA中引用分为：强引用，软引用，弱引用，虚引用。
###### 对象如何做到起死回生
   一个对象要真正的死亡，至少要经历两次标记的过程：经过可达性分析没有与GC ROOT相连，那么会进行第一次标记并且进行一次**筛选**，筛选的条件是
此对象是否有必要执行finalize()方法。没必要执行finalize的情况是：没有覆盖finalize方法，已经执行过一次finalize方法。
   所以要起死回生，逃过一劫的关键就在finalize方法中，我们可以把此对象复制给某变量或者对象的成员变量，从而拯救自己。

### 5.垃圾收集算法与收集器
   + 垃圾收集算法
       - 标记清除(Mark-Sweep)
           </br>标记之后清除，算法简单易实现
       - 复制(Copying)
           </br>内存分两块，一块用来分配一块用来复制，每次只使用一块，然后将一块的复制到另一块，然后清除。新生代中就是这么做的，当survivor中To 
           区域不足的时候就需要老年代进行分配担保。
       - 标记整理(Mark-Compact)
           </br>标记后将存活对象移动到一端，然后清理剩下的一端。
   + 垃圾收集器
       - Serial
           </br>
       - ParNew
           </br>
       - Parallel Scavenge
           </br>
       - Serial Old
           </br>
       - CMS
           </br>
       - G1
           </br>

### 6.内存分配策略
