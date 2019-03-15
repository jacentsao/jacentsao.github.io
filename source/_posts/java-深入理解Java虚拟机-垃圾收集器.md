---
title: java-深入理解Java虚拟机-垃圾收集器
date: 2019-03-14 12:33:20
tags: [java, jvm]
categories: java
---


### 3.5 垃圾收集器

Java虚拟机规范中对垃圾收集器应该如何实现并没有任何规定。如下图所示是HotSpot虚拟机包含的所有垃圾收集器：

![](https://raw.githubusercontent.com/jacentsao/picbed/master/img/markdown20190314hotspot%20jvm%20garbage%20collections.png)

图3-5展示了7种作用于不同分代的收集器，连线代表收集器可以搭配使用，所处区域代表时属于新生代还是老年带的收集器。

概念：

并行（Parallel）指多条垃圾收集线程并行工作，用户线程仍然处于等待状态。
并发（Concurrent）指用户线程与垃圾收集线程同时执行（但并不一定时并行的，可能会交替执行），用户程序在继续运行，而垃圾收集程序运行于另一个CPU上

#### 3.5.1 Serial收集器

Serial收集器只会使用一个CPU或一条收集线程去完成垃圾收集工作，且垃圾收集时，必须暂停其他工作线程。如下所示时Serial/Serial Old收集器的运行过程：

![](https://raw.githubusercontent.com/jacentsao/picbed/master/img/markdown20190314Serial%20Serial%20Old.png)

Serial时虚拟机运行在Client模式下的默认新生代收集器。它的优势在于：简单而高效（与其它收集器的单线程比），对于限定单个CPU的环境来说，Serial收集器由于没有线程交互的开销，专心做垃圾收集自然可活得最高效的单线程收集效率。

#### 3.5.2 ParNew收集器

ParNew收集器起始就是Serial收集器的多线程版本。如下图所示是ParNew收集器的工作过程：

![](https://raw.githubusercontent.com/jacentsao/picbed/master/img/markdown20190314ParNew.png)

ParNew收集器时徐都运行在Server模式下的虚拟机中首选的新生代收集器，主要是因为只有它能与CMS收集器配合工作。

#### 3.5.3 Parallel Scavenge收集器

Parallel Scavenge收集器是一个新生代使用复制算法的收集器， Parallel Scavenge收集的特点是它的关注点与其它收集器不同，CMS等收集器的关注点是尽可能地缩短垃圾收集时用户线程的停顿时间，而Parallel Scavenge收集器的目的则是达到一个可控制的吞吐量（Throughput）。所谓吞吐量就是CPU用于运行用户代码的时间与CPU总耗时的比值。

#### 3.5.4 Serial Old收集器

Serial Old是Serial收集器的老年代版本，同样是一个是哦那个“标记-整理”算法的单线程收集器。这个收集器的主要意义也是在于给Client模式下的虚拟机使用。

#### 3.5.5 Parallel Old收集器

Parallel Old是Parallel Scavenge收集器的老年带版本，使用多线程和“标记-整理”算法。

![](https://raw.githubusercontent.com/jacentsao/picbed/master/img/markdown201903142Parallel%20Scavenge%20%2B%20Parallel%20Old.png)

#### 3.5.6 CMS收集器（Concurrent Mark Sweep）

CMS收集器是一种以获取最短回收停顿时间为目标的，基于“标记-清除”算法收集器。工作过程如下：

* 初始标记（CMS initial remark）
* 并发标记（CMS Concurrent mark）
* 重新标记（CMS remark）
* 并发清除（CMD Concurrent sweep）

其中，初始标记、重新标记这两个步骤任然需要“Stop The World”，如下图所示。整个过程中耗时最长的并发标记和并发清除过程收集器线程都可以与用户线程一起工作。所以，从总体上来说，CMS收集器的内存回收过程是与用户线程一起并发执行的。

![](https://raw.githubusercontent.com/jacentsao/picbed/master/img/markdown20190314CMS%20Collections.png)

CMS的特点：并发收集、低停顿。但是也有如下几个明显的缺点：

* CMS收集器堆CPU资源非常敏感。
* CMS收集器无法处理浮动垃圾（Floating Garbage）。
* “标记-清除”算法实现，会产生大量空间碎片。

#### 3.5.7 G1收集器（Garbage-First）

G1是一款面向服务端应用的垃圾收集器，具备如下特点：

* 并行与并发
* 分代收集
* 空间整合
* 可预测的停顿

G1收集器的运作大致可分为以下几个步骤：

* 初始标记（Initial Marking）
* 并发标记（Concurrent Marking）
* 最终标记（Final Marking）
* 筛选回收（Live Data Counting and Evacutaion）

示意图如下：

![](https://raw.githubusercontent.com/jacentsao/picbed/master/img/markdown20190314G1.png)




