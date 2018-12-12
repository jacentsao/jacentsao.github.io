---
title: Android-IPC传递数据的大小限制
date: 2017-04-08 21:12:40
tags:	[Android,BUG修复]
categories: Android
---

最近碰到一个用户在使用app的某个功能的时候老是出现闪退情况，但是又没有别的用户反馈过这个问题。所以找用户拿数据过来看了一下，借过还真是用户的数据比较特殊：

用户在A界面传递一个LIST的对象到B界面，一个正常的使用Intent携带数据的情况，结果出现了闪退的情况。通过日志发现是：


		android.os.TransactionTooLargeException: data parcel size 4915644 bytes

然后查阅资料发现，现在的IPC机制其实是对传递的数据有大小限制的，一般来说是1MB，而我们传递的List中有483个对象，累积下来的大小远远超过1MB，故出现了这种问题。同样的如果我们传递一个Bitmap大小超过这个限制也会出现这种问题，所以我们在开发中要注意这些问题。