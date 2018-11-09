---
title: 搭建gomobile开发环境
date: 2017-01-18 15:24:21
tags:
categories: Golang
---
gomobile是谷歌开发的一种跨平台技术解决方案,可以针对iOS和Android平台进行直接开发或者进行Library开发.
<!-- more -->
### 配置gomobile开发环境
1. 设置终端http,https代理
2. 配置git,及环境变量
3. 配置golang及gopath环境及环境变量
4. 配置gomobile及环境变量

### gomobile的优缺点
#### 优点
* 直接构建用于ios的framework文件,可以直接调用生成的类
* 直接构建用于Android的aar文件,使用jni直接调用
* 统一三端规则,避免重复开发和一些错误


#### 缺点:
##### 数据类型限制
* int and float;
* string and boolean;
* byte[]. The current implementation doesn’t allow to use []byte as the     function argument (https://golang.org/issues/12113);
* function has to return only supported types, it may not return the result, it may return one or two types wherein the second type should be an error;
* interfaces could be used if they are exported to files of any supported type;
* struct type, only in case all fields meet the constraints.

]


### 环境搭建流程
#### 官方教程
1. [官网地址](https://godoc.org/golang.org/x/mobile/cmd/gomobile),安装gomobile,当然首先得安装golang才能够使用go命令

	
		$ go get golang.org/x/mobile/cmd/gomobile
		$ gomobile init
		//然后就可以通过gomobile指令查看了
		$ gomobile

2. 前面地址进不去的话有个[Github地址](https://github.com/golang/mobile)

#### 使用Github镜像安装
由于使用谷歌的官网资源需要在命令行翻墙,比较麻烦,所以直接使用Github的镜像进行安装.

	$ mkdir -p $GOPATH/src/github.com/golang
	$ cd $GOPATH/src/github.com/golang
	$ git clone https://github.com/golang/mobile.git
	$ mkdir -p $GOPATH/src/golang.org/x	
	$ cp -r $GOPATH/src/github.com/golang/mobile $GOPATH/src/golang.org/x 
	$ cd $GOPATH/src/golang.org/x/mobile/cmd/gomobile
	$ go install  //会在$GOPATH/src/bin 目录下生成gomobile  
	$ gomobile init //安装NDK之类的
	