---
title: java-深入理解Java虚拟机-Java内存区域与内存溢出异常
date: 2019-03-07 22:21:36
tags: [java, jvm]
categories: java
---

#### 2.2 运行时数据区域(Java VM runtime data areas)
 
 Java虚拟机在执行Java程序的过程中会把它管理的内存划分位若干个不同的数据区域，这些区域都有各自的用途，以及创建和销毁的时间，有的区域随着虚拟机进程的启动而存在，有些区域则依赖用户线程的启动和结束而简历和销毁。如下图所示，Java运行时数据区域分为以下几个部分：
 
 ![](https://raw.githubusercontent.com/jacentsao/picbed/master/img/markdown20190312jvm-runtime-data-area.png)
 
 
##### 2.2.1  程序计数器(Program Counter Register)

程序计数器是一块较小的内存空间，可以看做当前线程所执行的字节码的行号指示器。在虚拟机的概念模型里（仅是概念模型，各种虚拟机可能会通过一些更高效的方式去实现），字节码解释器工作时就是通过改变这个计数器的值来选取下一条需要执行的字节码指令，分支、循环、跳转、异常处理、线程恢复等基础功能都需要依赖这个计数器完成。

由于Java虚拟机的多线程是通过线程切换cpu时间片实现的，在任何一个确定的时刻，一个处理器（对于多核处理器来说是一个内核）都只会执行一条线程中的指令。因此，为了线程切换后能恢复到正确的执行位置，每条线程都需要一个独立的PC计数器，各个线程之间计数器互不影响，独立存储，我们称这类内存区域为“线程私有”的内存。

如果线程正在执行的是一个Java方法，这个计数器记录的是正在执行的虚拟机字节码指令的地址；如果正在执行的是Native方法，这个计数器值则为空（Undefined）。此区域内存是唯一一个在Java虚拟机规范中没有规定任何OutOfMemoryError情况的区域。

##### 2.2.2 Java虚拟机栈（Java VIrtual Machine Stacks）

于PC counter一样，Java虚拟机栈也是线程私有的，它的生命周期与线程相同。虚拟机栈描述的是Java方法执行的内存模型：每个方法在执行的同事回创建一个栈帧（Stack Frame）用于存储局部变量表、操作数栈、动态链接、方法出口等信息。每一个方法从调用直至执行完成的过程，就对应着一个栈帧在虚拟机栈中入栈到出栈的过程。

虚拟机栈中的局部变量表存放了编译期可知的各种功能基本数据类型（boolean、byte、char、int、short、long、double、float)、对象引用（reference类型，它不等通于对象本身，可能是一个指向对象起始地址的引用指针，也可能是只想一个代表对象的句柄或其他与此对象相关的位置）和returnAddress（只想了一条字节码指令的地址）。

在Java虚拟机规范中，对这个区域规定了两种异常情况：

* StackOverflowError 线程请求的虚拟机栈深度大于虚拟机所允许的最大深度。
* OutOfMemoryError 如果虚拟机栈可以动态扩展，扩展时无法申请到足够的内存的时候。

##### 2.2.3 本地方法栈（Native Method Stacks）

本地方法栈和虚拟机栈所发挥的作用非常相似的，他们之间的区别不过是虚拟机栈为虚拟机执行Java方法（也就是字节码）服务，而本地方法栈为虚拟机使用到的Native方法服务。同样的本地方法栈也会抛出StackOverflowError和OutOfMemoryError。

##### 2.2.4 Java堆（Java Heap）

对于大多数应用来说，Java堆是Java虚拟机所管理的内存中最大的一块。Java堆是线程共享的一块内存区域，在虚拟机启动时创建。

此区域的唯一目的就是存放对象实例，在Java虚拟机规范中的描述是：所有的对象实例以及数组都要在堆上分配，但是随着JIT（Just-In-Time）编译器的反战和逃逸分析技术逐渐成熟，栈上分配、标量替换优化技术将会导致一些微妙的变化发生，所有的对象都分配在堆上也渐渐变得不是那么绝对了。

Java堆是垃圾回收器管理的主要区域，Java堆垃圾回收采用分代收集算法，该区域的划分和虚拟机配置参数如下，分为两大块Young Generation和Old Generation，Permanent Generation属于接下来要讲的方法区。而Young Generation又可以细分为Eden（伊甸区）、From Survivor、To Survivor区。

![](https://github.com/jacentsao/picbed/blob/master/img/markdownJava%20Heap%20Construction.gif?raw=true)

##### 2.2.5 方法区（Method Area）

方法区也是线程共享的内存区域，他用于存储已被Java虚拟机加载的类信息、常量、静态变量、即时编译器编译后的代码等数据。虽然Java虚拟机规范把方法去描述为堆的一个逻辑部分，但是它有一个别名叫做非堆（Non-Heap），目的应该是与Java堆区分开来。

方法区又被称之为永久带（Permanent Generation），本质上两者并不等价，仅仅是因为HotSpot虚拟机的设计团队选择把GC分代收集扩展至方法区，或者说使用永久带来实现方法区而已。这样HotSpot的垃圾收集器就可以像管理Java堆一样管理这部分内存，省去专门为方法区开发内存管理代码的工作。对于其它虚拟机来说不存在方法区。

在jdk1.8之前，永久带在虚拟机运行时数据区域的分块如下：

![](https://raw.githubusercontent.com/jacentsao/picbed/master/img/markdown20190312jdk1.7jvm-runtime-data-area.png)

在jdk1.8时，永久带被合并至Metaspace，如下所示：

![](https://raw.githubusercontent.com/jacentsao/picbed/master/img/markdown20190312jdk1.8%20jvm%20runtime%20data%20area%20.png)

根据Java虚拟机规范的规定，当方法区无法满足内存分配的需求时，将抛出OutOfMemoryError异常。

##### 2.2.6 运行时常量池（Runtime Constant Pool）

运行时常量池时方法区的一部分，受到方法区内存的限制，当常量池无法再申请到内存时会抛出OutOfMemoryError异常。

##### 2.2.7 直接内存（Direct Memory）

直接内存并不是虚拟机运行时数据区的一部分，也不是Java虚拟机规范中定义的内存区域。由于此区域也可能导致OutOfMemoryError异常，故放在一起讲解。

在JDK1.4中心加入了NIO(New Input Output)类，引入了一种基于通道（Channel）与缓冲区（Buffer）的I/0方式，他可以使用Native函数库直接分配堆外内存，然后通过一个存储在Java堆中的DirectByteBuffer对象做为这块内存的引用操作。这样能在一些场景中显著提高性能，因为避免了在Java堆和Native堆中来回复制数据。

该区域收到本机总内存大小以及处理器寻址控件的限制，动态扩展时如果申请不到内存会出现OutOfMemoryError异常。