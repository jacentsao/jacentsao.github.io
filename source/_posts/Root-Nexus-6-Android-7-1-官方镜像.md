---
title: Root Nexus 6 Android 7.1 官方镜像
date: 2017-01-19 14:03:12
tags: [Android,Root]
categories: Android
---
 本教程适用于Android7.1官方系统的Nexus 6 Root, 使用于Android 7.1.1 N6F26Q Nougat,请确保你将要root的是该设备. 原文[地址](http://www.teamandroid.com/2017/01/11/root-nexus-6-android-7-1-1-n6f26q-nougat-official-factory-image/2/),可自行查看.
 <!-- more -->
##### 注意
 * 本教程仅适用于安装 Android 7.1.1 (N6F26Q) 的Nexus 6设备,我自己的美版型号root正常.
 * 手机需要解锁bootloader[教程](http://www.teamandroid.com/2014/12/11/unlock-bootloader-nexus-6/)
 * 系统必须为Android 7.1.1 N6F26Q Nougat官方镜像 [教程](http://www.teamandroid.com/2017/01/11/update-nexus-6-android-7-1-1-n6f26q-nougat-official-factory-image/)

##### 详细步骤
1. 下载Android SDK工具[地址](https://developer.android.com/studio/index.html#downloads),仅获取命令行工具即可.这个地址需要翻墙
2. 打开开发者选项里面的USB调试
3. 下载[SuperSU](http://www.devfiles.co/download/XYcfXURV/SuperSU-v2.78-201609011115.zip)
4. 拷贝到手机存储空间根目录
5. 下载[TWRP](http://www.devfiles.co/download/BWxptq2J/twrp-3.0.2-0-shamu.img)
6. 关机后按音量- 和 关机键进入bootloader模式,使用下载的命令行工具刷入twrp(如果没有添加命令行工具路径到环境变量则需要在放置命令行工具):
	
	fastboot flash recovery twrp-3.0.2-0-shamu.img
	
7. 	刷入完成后,选择RECOVERY模式进入
8. 选择flash zip from SD card,选中前面的SuperSU XX文件,按电源键确认刷入
9. 最后选择reboot system,重启以后如果有了SuperSU这个app说明root成功了.
 
 