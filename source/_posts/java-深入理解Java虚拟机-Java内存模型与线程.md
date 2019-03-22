---
title: java-深入理解Java虚拟机-Java内存模型与线程
date: 2019-03-15 08:55:21
tags: [java, jvm]
categories: java
---


### 12.1 概述

衡量一个服务性能的高低好坏，每秒事务处理数（Transaction Per Second，TPS）

#### 12.2 硬件的效率与一致性

下图为处理器、高速缓存、主存之间的交互关系：


![](https://raw.githubusercontent.com/jacentsao/picbed/master/img/markdown20190316processor%20Cache%20memory.png)


如果存在一个计算任务依赖另外一个计算任务的中间结果，那么其顺序性并不能靠代码的先后顺序来保证，因为为了保证处理器内部的运算单元能够尽量被充分利用，处理器可能会对代码进行乱序执行优化（Our-Of-Order Ececution）。类似的Java虚拟机的即时编译器也有类似的指令重排序（Instruction Reorder）优化。

### 12.3 Java内存模型（Java Memory Model）

#### 12.3.1 主内存与工作内存

如下图所示：

![](https://raw.githubusercontent.com/jacentsao/picbed/master/img/markdown20190316thread%20main%20memory%20working%20memroy%20.png)

#### 12.3.2 内存间交互操作

Java内存模型中定义了一下8种操作来完成，虚拟机实现时必须保证下面提及的每一种操作都是原子的、不可再分的（对于double和long类型的变量来说，load、store、read和write操作在某些平台上允许有例外，见12.3.4）

* lock：作用于主内存的的变量，把一个变量标识为一条线程独占的状态。
* unlock：作用于主内存的变量，把一个处于锁定状态的变量释放出来，释放后的变量才可以被其他线程锁定。
* read：作用于主内存的变量，把一个变量的值从主内存传输到线程的工作内存中。
* load：作用于工作内存的变量，把read操作从主内存中得到的标量值放入工作内存的变量副本中。
* use：作用于工作内存的变量，把工作内存中一个变量的值传递给执行引擎，每当虚拟机遇到一个需要使用到变量的值的字节码指令时将会执行这个操作。
* assign：作用于工作内存的变量，把一个从执行引擎接收到的值赋给工作内存的变量，每当虚拟机遇到一个给变量赋值的字节码指令时将会执行这个操作。
* store：作用于工作内存的变量，它把工作内存中一个变量的值传送到主内存中，以便随后的write操作使用.
* write：作用于主内存的变量，把store操作从工作内存中得到的变量的值放入主内存的变量中。

Java虚拟机还规定了在执行上述8种基本操作时必须满足如下规则：

* 不允许read和load、store和write操作之一单独出现，即不允许一个变量从主内存读取了但工作内存不接受，或者工作内存发起了回写但主内存不接受的情况出现。
* 不允许一个线程丢弃它的最近的assign操作，即变量在工作内存中改变了之后必须把该变化同步回主内存。
* 不允许一个线程无原因地（最近发生过任何assign操作）把数据从线程的工作内存同步回主内存中。
* 一个新的变量只能在主内存中“诞生”，不允许在工作内存中直接使用一个未被初始化（load或assign）的变量，换句话说，就是对一个变量实施use、store操作之前，必须先执行过了assign和load操作。
* 一个变量在同一个时刻只允许一条线程对其执行lock操作，但lock操作可以被同一条线程重复执行多次，多次执行lock后，只有执行相同次数的unlock操作，变量才会被解锁。
* 如果对一个变量执行lock操作，那将会清空工作内存中此变量的值，在执行引擎使用这个变量前，需要重新执行load或者assign操作初始化变量的值。
* 如果一个变量事先没有被lock操作锁定，那就不允许对它执行unlock操作，也不允许区unlock一个被其它线程锁定住的变量。
* 对一个变量执行unlock操作之前，必须先把此变量同步回主内存中（执行store、write操作）

#### 12.3.3 对于volatile型变量的特殊规则

关键字volatile可以说是Java虚拟机提供的最轻量级的同步机制。当一个变量定义为volatile之后，它将具备两种特性：

* 保证此变量对所有线程可见性，指一条线程修改了这个变量的值，新值对于其他线程来说是可以立即得知的（常规变量会有一个线程之间的变量同步，所以会存在不同线程之间的值不等的情况）。
* 禁止指令重排：不管如何重排序（编译器与处理器为了提高并行度），（单线程环境下，指令执行的最终效果应当与其在顺序执行下的效果一致，否则这种优化便会失去意义。这句话有个专业术语叫做as-if-serial semantics (as-if-serial语义)）程序的结果不能被改变。


对于volatile变量的可见性，实际上volatile变量在各个线程的工作内存中（在各个线程的工作内存中，volatile变量也可以存在不一致的情况，但由于每次使用之前都要先刷新，执执行引擎看不到不一致的情况，因此可以认为不存在一致性问题），但是Java里面的运算并非原子操作，导致volatile变量的运算在并发下一样是不安全的。

一条字节码指令可能转化为若干条本地机器码指令，所以一条字节码并不一定代表是原子操作。

由于volatile变量只能保证可见性，使用场景如下。

* 运算结果并不依赖变量的当前值，或者能够确保只有单一的线程修改变量的值。
* 变量不需要与其它的状态变量共同参与不变约束。


```
volatile boolean shutdownRequested;

public void shutdown() {
	shutdownRequested = true;
}

public void doWork() {
	while(!shutdownRequested) {
		//do stuff
	}
}
```


#### 12.3.4 对于long和double型变量的特殊规则

Java内存模型要求lock、unlock、read、load、assign、use、store、write这八个操作都具有原子性，但是对于64位的数据类型（long和double），在模型中特别定义了一条相对宽松的规定：允许虚拟机将没有被volatile修饰的64位数据的读写操作划分为两次32位操作来进行，即允许虚拟机实现选择可以不保证64位数据的load、store、read和write这4个操作的原子性。这点就是所谓的long和double的非原子性协定（Nonatomic Treatment of double and long Variables）

虽然Java内存模型允许虚拟机不把long和double变量的读写实现成原子操作，但是一般虚拟机实现都是原子性操作的，所以编程的时候不用特意把long和double变量申明位volatile。

#### 12.3.5 原子性、可见性和有序性

* 原子性（Atomicity）：变量操作read、load、assign、use、store和write
* 可见性（Visibility）：可见性指的当一个线程修改了共享变量的值，其它线程能够立即得知这个修改。volatile的特殊规则保证了值能够立即同步到主内存，以及每次使用前立即从主内存刷新。出了volatile之外，还可以通过synchronized和final让变量可见。同步块的可见性是由“对一个变量执行unlock操作之前，必须把此变量同步回主内存中（执行store、write操作）”这条规则活得的。final关键字的可见性是指：被final修饰的字段在构造器中一旦初始化完成，并且构造器没有把“this”的引用传递出去（this引用逃逸是一件很危险的事情，其它线程有可能通过这个引用访问到“初始化了一半”的对象），那么其它线程中能看见final字段的值。
* 有序性（Ordering）：Java内存模型的有序性可以总结为：**如果在本线程内观察，所有的操作都是有序的；如果在一个线程中观察另一个线程，所有的操作都是无序的。**前半句是指“线程内表现为串行的语义”，后半句是指“指令重排序”现象和“工作内存与主内存同步延迟”现象。

#### 12.3.6 先行发生原则（happens-before）

在计算机科学中，[先行发生](https://en.wikipedia.org/wiki/Happened-before)关联是指的两个具有先后关系以及后执行者受到前面执行者影响的两个事件之间的先后关系，即使这两个事件被计算器优化以后乱序执行了，他们之间也应该有先执行者后执行者。

Java语言无需任何同步手段保障就能成立的先行发生规则表现如下几点：

* 程序次序执行（Program Order Rule）：在一个线程内，按照程序代码顺序，书写在前面的操作先行发生于后书写的操作。准确的说，应该是控制流顺序而不是程序代码车顺序，因为要考虑分支、循环等结构。
* 管程锁定规则（Monitor Lock Rule）：一个unlock操作先行发生于后面对同一个锁的lock操作。这里必须强调的是同一个锁，而“后面”是指时间上的先后顺序。
* volatile变量规则（Volatile Variable Rule）：对一个volatile变量的写操作先行发生于后面对这个变量的读操作，这里的“后面”指的是时间上的先后顺序。
* 线程启动规则（Thread Start Rule）：Thread对象的start方法先行发生于此线程的每一个动作。
* 线程终止规则（Thread Termination Rule）：线程中的所有操作都先行发生于对此线程的终止检测，我们可以通过Thread.join()方法结束、Thread.isAlive()的返回值等手段检测到线程已经终止执行。
* 线程中断规则（Thread Interruption Rule）：对线程interrupt()方法的调用先行发生于被中断线程的代码检测到终端事件的发生，可以通过Thread.interrupted()方法检测到是否有中断发生。
* 对象终结规则（Finalizer Rule）：一个对象的初始化完成（构造函数执行结束）先行发生于它的finalize()方法的开始。
* 传递性（Transitivity）：如果操作A先行发生于操作B，操作B先行发生于操作C，那就可以得出操作A先行发生于操作C的结论。

### 12.4 Java与线程

在Java中讨论并发主要是讨论线程级别的实现。

#### 12.4.1 线程的实现

线程是比进行更轻量级的调度执行单位，线程的引入，可以把一个进行的资源分配和执行调度分开，各个线程即可以共享进程资源（内存、文件I/O等），又可以独立调度（线程时CPU调度的基本单位）。

实现线程主要有3种方式：使用内核线程实现、使用用户线程实现和使用用户线程加轻量级进程混合实现。

**1.使用内核线程实现**

内核线程（Kernel Level Thread,KLT）就是直接由操作系统内核（Kernel，下称内核）支持的线程，这种线程由内核来完成线程切换，内核通过操纵调度器（Scheduler）对线程进行调度，并负责将线程的任务映射到各个处理器上。每个内核线程可以视作为内核的一个分身，这样操作系统就有能力处理多件事情，支持多线程的内核较多多线程内核（Multi-Threads Kernel）。

程序一般不会直接去使用内核线程，而是去使用内核线程的一种高级接口---轻量级进程（Light Weight Process，LWP），轻量级进程就是我们通常意义上所讲的线程。如下图所示是轻量级进程与内核线程之间的1：1关系图：

![](https://raw.githubusercontent.com/jacentsao/picbed/master/img/markdownLight%20Weight%20Process%20vs%20Kernel-level%20Threa%201%3A1%20relation.png)

系统调用的代价相对较高，需要在用户态（User Mode）和内核态（Kernel Mode）中来回切换，同时由于轻量级进程和内核线程1：1的关系，支持的线程数量有限。

**2.使用用户线程实现**

从广义上来讲，一个线程只要只要不是内核线程，就可以认为是用户线程（UT，User Thread），因此，从这个定义上来讲，轻量级进程也属于用户线程，但轻量级进程的实现始终是建立在内核之上的，许多操作都要进行系统的调用，效率会受到限制。

狭义上的用户线程指的是完全建立在用户空间的线程库上，系统内核不能感知线程存在的实现。用户线程的建立、同步、销毁和调度完全在用户态中完成，不需要内核的帮助。这种进行与用户线程1：N的关系称为一对多的线程模型，如下图所示。

![](https://raw.githubusercontent.com/jacentsao/picbed/master/img/markdownUser%20Thread%20vs%20CPU.png)

使用用户线程的优势在于不需要系统内核支援，劣势也在于没有系统内核的支援，所有的线程操作都需要用户程序自己处理。Java、Ruby都曾经使用过用户线程，最终又都放弃使用它。

**3.使用用户线程加轻量级进程混合实现**

线程除了依赖内核线程实现和完全由用户程序自己实现之外，还有一种将内核线程与用户线程一起使用的实现方式。在这种混合模式下，用户线程与轻量级进程的数量比是不一定的，即为N：M的关系，如下图所示：
![](https://raw.githubusercontent.com/jacentsao/picbed/master/img/markdownUser%20Thread%20vs%20Light%20Weight%20Process%20N%3AM.png)

**4.Java线程的实现**

Java线程以Sun JDK来说，它的Windows和Linux版本都是使用的一对一的线程模型实现的，一条Java线程就映射到一条轻量级进程之中，因为Windows和Linux系统提供的线程模型就是一对一的。

在Solaris平台上可以选择一对一和多对多的线程模型。

#### 12.4.2 Java线程调度

线程调度是指系统为线程分配处理器使用权的过程，主要调度方式有两种，分别是协同式线程调度（Cooperative Threads-Scheduling）和抢占式线程调度（Preemptive Threads-Scheduling）。

* 协同式线程调度：线程的执行时间由线程本身来控制，线程把自己的工作执行完后主动通知系统进行切换。
* 抢占式调度：有系统来分配执行时间，线程的切换不由线程本身来决定（可以通过Thread.yield()让出执行时间，但是没法获取执行时间）。

抢占式调度可以通过线程优先级“建议”系统给某些线程多分配一点执行时间。但是这个并不太靠谱，优先级可能被系统自行改变。

#### 12.4.3 状态转化

Java语言定义了5种线程状态：

* 创建（New）：创建后尚未启动的线程处于这种状态。
* 运行（Runnable）：包括了操作系统线程状态中的Running和Ready，也就是处于这种状态的线程可能正在执行，也可能正在等待CPU为它分配执行时间。
* 无限期等待（Waiting）：不会被分配CPU时间，需要被其他线程显示地唤醒。以下方法会让线程先入无限期等待状态：
	* 没有设置Timeout参数的Object.wait()方法。
	* 没有设置Timeout参数的Thread.join()方法。
	* LockSupport.park()方法。
* 限期等待（Timed Waiting）：不会被分配CPU执行时间，不过无需等待被其他线程显示地唤醒，在一定时间之后他们会由系统自动唤醒。以下方法会让线程进入限期等待状态：
	* Thread.sleep()。
	* 设置Timeout参数的Object.wait()方法。
	* 设置Timeout参数的Thread.join()方法。
	* LockSupport.parkNanos()方法。
	* LockSupport.parkUntil()方法。
* 阻塞（Blocked）：线程被阻塞了，“阻塞状态”与“等待状态”的区别是：“阻塞状态”在等待着获取到一个排它锁，这个事件将在另外一个线程放弃这个锁的时候发生；而“等待状态”则是在等待一段时间，或者唤醒动作的发生。
* 结束（Terminated）：已终止线程的线程状态，线程已经结束执行。

线程状态的转换图如下所示：

![](https://raw.githubusercontent.com/jacentsao/picbed/master/img/markdownThread%20State%20.png)
	 	
