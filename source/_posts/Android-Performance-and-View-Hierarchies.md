---
title: Android - Performance and View Hierarchies
date: 2017-08-27 21:10:02
tags: [Android, performance]
categories: Android
---

布局的层次对于应用的性能影响也是很显著的。

#### 布局和测量性能

渲染本身包含了Layout和Measure两个过程，系统在这两个过程中确定View hierarchy的具体位置。Measure过程确定了View的尺寸和边界，Layout过程则确定了View在屏幕的位置。

这两个过程本身会占用一些系统的资源。当然，大多数时候，这个过程占用的系统资源较少，不会对性能造成显著的影响。但是，如果你的应用需要添加或者移除View对象就会显著开销更多的资源，例如Recycleview回收或者复用。或者当一个View对象需要重绘来满足它的约束的时候，也会占用更多的资源。例如，给一个WRAP_CONTENT的View对象设置文本，会导致重绘。

如果一个渲染过程过长，就会导致无法在16ms内渲染完一帧，这就会导致掉帧，动画也会变得卡顿。

由于UI的绘制是处于主线程的，所以对于这些导致卡顿的问题需要优化，从而让我们的应用变得流畅。

##### 管理复杂度：Layout相关

Android布局允许你嵌套UI对象，这种嵌套经常会导致额外的开销。当你的应用渲染一个Layout的对象，应用同时需要渲染它的所有的子View。对于一个复杂的Layout，有时候系统只有在第一次加载的时候会比较占用系统资源。例如，你的应用中有一个复杂的复用过的Recycleview，系统需要加载所有的对象。其他例子，一个微不足道的变化可能会导致视图层次的一系列的重绘。

Layout过程中视图层次的嵌套会显著导致绘制的时间增加。因此，越少层次的嵌套，Layout过程花费的时间就越少。

如果你使用RelativeLayout，但是你也可能在不使用weight属性的情况下，通过使用LinearLayout实现。另外，如果你的target版本是Android7.0的话，你还可以使用ConstraintLayout，这是一个用于替换RelativeLayout的布局，它是一个功能类似，性能更好的RelativeLayout。

##### “双重税收”

一般来说，系统执行Layout或者Measure过程是非常快的。但是，对于一些很复杂的Layout，系统可能需要多次绘制才能够最终确定所有对象的位置。这种超过一次的Layout和Measure过程就是所谓的双重税收。

例如，当你使用一个可以通过一个View对象确定另一个View对象的位置的RelativeLayout容器。系统会执行以下步骤：

1. 根据每一个子View的约束条件，逐个执行Layout和Measure过程
2. 使用前面的数据，同时计算View的weight，来给相对的View找到一个合适的位置
3. 再次执行Layout过程来确定这些相对位置的View
4. 执行下一个渲染过程

如果你的视图层次越多，那么潜在性能问题就越多。

除了RelativeLayout其他容器也会导致双重税收。例如：

* 水平的线性布局会导致两次Layout和Measure过程。如果你给线性布局添加了[measureWithLargestChild](https://developer.android.com/reference/android/widget/LinearLayout.html#attr_android:measureWithLargestChild)（其实是由于使用了weight属性的View的尺寸等于最大的子View的最小尺寸）也有可能由于要进行第二次Layout和Measure来确定每一个View对象的大小。
* GridLayout同样也有类似的问题。这个容器同样允许相对位置，正常情况下，GridLayout会通过预处理确定号子View的相对关系来避免双重税收。但是，如果Layout使用了weight或者Gravity的时候，它的预处理就没了，当容器是RelativeLayout的时候系统可能需要多次执行这个过程。

多次的Layout和Measure过程本身并不是一个性能负担。但是，他们会在某些场景下成为一种负担。我们应该对以下的情况保持足够的警惕：

* 布局层次的根布局
* 布局层次过多
* 一个界面产生很多的实例，例如ListView


#### 诊断布局层次问题

布局性能问题受到很多因素的影响而变成了一个复杂的问题。以下是一些帮助你找到性能瓶颈的工具。当然也有一些其他的，没有那么确定性的工具，但也能提供一些提示帮助我们找到问题。

##### Systrace

[Systrace](https://developer.android.com/studio/profile/systrace.html)是一个Android SDK内嵌的诊断性能问题的工具。这个工具允许我们查看整个设备的时间信息，允许你查看什么时候会因为Layout的性能问题影响到设备的性能。

##### GPU呈现模式

##### lint检查

##### Hierarchy Viewer

#### 解决布局层次问题

##### 减少无用的嵌套

##### 使用merge/include标签

##### 整体替换新的Layout

