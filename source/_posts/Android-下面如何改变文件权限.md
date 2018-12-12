---
title: Android-下面如何改变文件权限
date: 2017-02-24 19:55:40
tags: [Android, Linux]
categories: Android
---

如何在Android系统下面改变我们的文件权限,实现某些目的:


				String command = "chmod 777 " + filePath;
	            Runtime runtime = Runtime.getRuntime();
	            runtime.exec(command);

如上所示,获取文件的绝对路劲,通过Runtime去执行该文件,777代表可读可写可执行,这里不做更多说明.    