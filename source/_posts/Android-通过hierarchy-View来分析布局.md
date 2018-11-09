---
title: Android - 通过hierarchy View来分析布局
date: 2017-08-28 20:49:49
tags: [Android, Performance]
categories: Android
---


Hierarchy Viewer是Android device monitor内嵌的一个工具，可以通过这个工具来检查你的层次视图的属性和Layout的速度。他能够帮你找到层次布局结构的性能瓶颈，帮助你简化层次布局减少过度绘制。

#### 开始

如果使用Android模拟器的话可以略过这一段。否则的话，需要对你的设备进行如下的配置：

注意：你的设备需要时Android4.1及以上API版本

1. 打开设备的开发者模式
2. 在你的开发电脑上设置环境变ANDROID_HVPROTO=ddm，这个变量告诉Hierarchy Viewer使用和DDMS协议一致的ddm协议来连接你的设备。但是host只能够有一个进程连接到设备，所以你必须关闭其他通过Hierarchy Viewer运行的DDMS会话

#### 开始使用Hierarchy Viewer

打开Android device monitor后如下图所示：

1. 连接你的设备到电脑。
2. 打开Android Studio，运行一个软件到你的设备
3. 在Android Studio-Tools-Android-Android device monitor。
4. 选择Hierarchy View
5. 在左边的窗口双击你的包名。

![](https://developer.android.com/images/tools/performance/hierarchy-viewer/gettingstarted_image005.png)

#### 熟悉工具

下图是Hierarchy Viewer的面板:

![](https://developer.android.com/images/tools/performance/hierarchy-viewer/gettingstarted_image008.png)





