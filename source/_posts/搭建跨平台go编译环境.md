---
title: 搭建跨平台go编译环境
date: 2017-01-18 20:12:15
tags: [Golang,Android,CrossCompile]
categories: Golang
---

本文主要是为了搭建golang跨平台环境,从而实现一套golang代码运行在多重环境(OSX,Linux,Android,iOS,Windows),实现跨平台共享代码,简化开发流程.

1. 安装Docker   
2. 安装golang
3. 安装xgo镜像及编译指令
4. 配置环境变量

<!-- more -->
#### 1. 安装docker 
针对Mac和Windows可以直接下载安装包进行安装,其他系统可以使用命令行进行安装.[参考链接](https://www.docker.com/products/docker#/mac),最后通过docker version 指令进行查看
	
	# Jacen at EveeedeMacBook-Pro.local in ~ [10:51:52] docker version
	Client:
	Version:      1.12.5
	API version:  1.24
	Go version:   go1.6.4
	Git commit:   7392c3b
	Built:        Fri Dec 16 06:14:34 2016
	OS/Arch:      darwin/amd64
	
	Server:
	Version:      1.12.5
	API version:  1.24
	Go version:   go1.6.4
	Git commit:   7392c3b
	Built:        Fri Dec 16 06:14:34 2016
	OS/Arch:      linux/amd64
	
#### 2. 安装golang
同样的在Windows和Mac下面都有一键安装包,针对其他系统也是通过命令行进行安装.[参考链接](https://golang.org/dl/),安装好以后还需要进行环境变量的配置.

在mac系统和Linux系统下面,Windows配置环境变量

	export GOROOT="/usr/local/go"
	export GOPATH="你的工作目录"  //这里主要是影响到后面需要安装的xgo
 
再执行go env查看是否配置成功:

	GOARCH="amd64"
	GOBIN=""
	GOEXE=""
	GOHOSTARCH="amd64"
	GOHOSTOS="darwin"
	GOOS="darwin"
	GOPATH="/Volumes/Work/go"
	GORACE=""
	GOROOT="/usr/local/go"
	GOTOOLDIR="/usr/local/go/pkg/tool/darwin_amd64"
	CC="clang"
	GOGCCFLAGS="-fPIC -m64 -pthread -fno-caret-diagnostics -Qunused-arguments -fmessage-length=0 -fdebug-prefix-map=/var/folders/w4/75xgx41n2bb7mm3m0dxbp0d40000gn/T/go-build003828011=/tmp/go-build -gno-record-gcc-switches -fno-common"
	CXX="clang++"
	CGO_ENABLED="1"
	
	
#### 3. 安装xgo docker镜像		
xgo的docker镜像是我们真正的编译环境,[Github地址](https://github.com/karalabe/xgo)

1. 安装xgo docker镜像,这个镜像大概4.xG,需要花较长的时间下载

	docker pull karalabe/xgo-latest 
	
这里latest指的最新的编译环境,如果需要的话也可以指定安装具体版本的镜像,[在这里](https://hub.docker.com/u/karalabe/),这个版本的差异主要是golang版本的不同,由于编译的时候可以指定golang版本进行编译,所以我们直接安装最新版本即可.
	
2. 安装xgo 
	
	go get github.com/karalabe/xgo
	
安装xgo编译工具,这个工具封装了go build,简化了我们的操作.


#### 4. 配置环境变量
如果我们使用的是bash的话可以将环境变量配置到.bash_profile中,免去export的过程
	
##### 使用bash
1. 在 ~ 目录下编辑.bash_profile 文件,在末尾添加
	
	export GOPATH="你的工作目录"
	export GOROOT="usr/local/go"
	export PATH=$PATH:$GOPATH/bin 
	export GOROOT_BOOTSTRAP=$GOROOT
2. source .bash_profile使得配置文件生效

##### 使用如zsh shell程序
1. 在~ 目录下编辑.zshrc 文件,添加上面同样的文件
2. 同上source生效

#### 5. 测试xgo交叉编译环境是否OK

	$ cd $GOPATH/src       // If you have multiple (I have at least 3 in there), change into any, doesn't matter
	$ mkdir xgo_test
	$ cd xgo_test
	$ echo -e 'package main\n import "fmt"\n func main(){\nfmt.Println("Hello")\n}' > main.go
	$ xgo .

执行完上述命令后在当前目录查看是否有编译生成的可执行文件

#### 6. 打包

	  ~ xgo  /Volumes/Public\ Storage/go/src/zhjd()
	  ~ xgo --targets=android-14/arm /Volumes/Storage/go/src/zhjd 指定生成Android端专用的会快很多很多,这里的14指的是最小支持SDK版本
	Checking docker installation...
	Client:
	 Version:      1.12.3
	 API version:  1.24
	 Go version:   go1.6.3
	 Git commit:   6b644ec
	 Built:        Wed Oct 26 23:26:11 2016
	 OS/Arch:      darwin/amd64
	
	Server:
	 Version:      1.12.3
	 API version:  1.24
	 Go version:   go1.6.3
	 Git commit:   6b644ec
	 Built:        Wed Oct 26 23:26:11 2016
	 OS/Arch:      linux/amd64
	
	Checking for required docker image karalabe/xgo-latest... found.
	Cross compiling zhjd...
	Building locally zhjd...
	Assembling toolchain for android-16/arm...
	Bootstrapping android-16/arm...
	Compiling for android-16/arm...
	Assembling toolchain for android-16/386...
	Bootstrapping android-16/386...
	Compiling for android-16/386...
	No API C header specified, skipping android-16/aar... 重点关注
	Compiling for linux/amd64...
	Compiling for linux/386...
	Bootstrapping linux/arm-5...
	Compiling for linux/arm-5...
	Cleaning up Go runtime for linux/arm-5...
	Bootstrapping linux/arm-6...
	Compiling for linux/arm-6...
	Cleaning up Go runtime for linux/arm-6...
	Bootstrapping linux/arm-7...
	Compiling for linux/arm-7...
	Cleaning up Go runtime for linux/arm-7...
	Compiling for linux/arm64...
	Compiling for linux/mips64...
	Compiling for linux/mips64le...
	Compiling for windows-4.0/amd64...
	Compiling for windows-4.0/386...
	Compiling for darwin-10.6/amd64...
	Compiling for darwin-10.6/386...
	Bootstrapping ios-5.0/arm-7...
	# runtime/cgo
	ldid.cpp(602): _assert(): Swap(mach_header_->filetype) == MH_EXECUTE || Swap(mach_header_->filetype) == MH_DYLIB || Swap(mach_header_->filetype) == MH_BUNDLE
	# net
	ldid.cpp(602): _assert(): Swap(mach_header_->filetype) == MH_EXECUTE || Swap(mach_header_->filetype) == MH_DYLIB || Swap(mach_header_->filetype) == MH_BUNDLE
	# os/user
	ldid.cpp(602): _assert(): Swap(mach_header_->filetype) == MH_EXECUTE || Swap(mach_header_->filetype) == MH_DYLIB || Swap(mach_header_->filetype) == MH_BUNDLE
	Compiling for ios-5.0/arm-7...
	# github.com/mattn/go-sqlite3
	ldid.cpp(602): _assert(): Swap(mach_header_->filetype) == MH_EXECUTE || Swap(mach_header_->filetype) == MH_DYLIB || Swap(mach_header_->filetype) == MH_BUNDLE
	# github.com/mattn/go-sqlite3
	ldid.cpp(602): _assert(): Swap(mach_header_->filetype) == MH_EXECUTE || Swap(mach_header_->filetype) == MH_DYLIB || Swap(mach_header_->filetype) == MH_BUNDLE
	Cleaning up Go runtime for ios-5.0/arm-7...
	Bootstrapping ios-5.0/arm64...
	# runtime/cgo
	ldid.cpp(602): _assert(): Swap(mach_header_->filetype) == MH_EXECUTE || Swap(mach_header_->filetype) == MH_DYLIB || Swap(mach_header_->filetype) == MH_BUNDLE
	# net
	ldid.cpp(602): _assert(): Swap(mach_header_->filetype) == MH_EXECUTE || Swap(mach_header_->filetype) == MH_DYLIB || Swap(mach_header_->filetype) == MH_BUNDLE
	# os/user
	ldid.cpp(602): _assert(): Swap(mach_header_->filetype) == MH_EXECUTE || Swap(mach_header_->filetype) == MH_DYLIB || Swap(mach_header_->filetype) == MH_BUNDLE
	Compiling for ios-5.0/arm64...
	# github.com/mattn/go-sqlite3
	ldid.cpp(602): _assert(): Swap(mach_header_->filetype) == MH_EXECUTE || Swap(mach_header_->filetype) == MH_DYLIB || Swap(mach_header_->filetype) == MH_BUNDLE
	# github.com/mattn/go-sqlite3
	ldid.cpp(602): _assert(): Swap(mach_header_->filetype) == MH_EXECUTE || Swap(mach_header_->filetype) == MH_DYLIB || Swap(mach_header_->filetype) == MH_BUNDLE
	Cleaning up Go runtime for ios-5.0/arm64...
	Cleaning up build environment...
	
	
	
查看编译生成的文件


	➜  all ls -al
	total 427872
	drwxr-xr-x  21 silence  staff       714 Dec 29 11:37 .
	drwxr-xr-x+ 29 silence  staff       986 Dec 29 11:37 ..
	-rw-r--r--@  1 silence  staff      6148 Dec 29 11:37 .DS_Store
	-rwxr-xr-x   1 silence  staff  14633608 Dec 29 11:27 zhjd-android-16-386
	drwxr-xr-x   5 silence  staff       170 Dec 29 11:27 zhjd-android-16-aar
	-rwxr-xr-x   1 silence  staff  14705872 Dec 29 11:26 zhjd-android-16-arm
	-rwxr-xr-x   1 silence  staff  10418780 Dec 29 11:32 zhjd-darwin-10.6-386
	-rwxr-xr-x   1 silence  staff  11578172 Dec 29 11:31 zhjd-darwin-10.6-amd64
	-rwxr-xr-x   1 silence  staff  11224720 Dec 29 11:34 zhjd-ios-5.0-arm64
	-rwxr-xr-x   1 silence  staff  10569184 Dec 29 11:33 zhjd-ios-5.0-armv7
	drwxrwxrwx   3 silence  staff       102 Dec 29 11:35 zhjd-ios-5.0-framework
	-rwxr-xr-x   1 silence  staff  14611432 Dec 29 11:28 zhjd-linux-386
	-rwxr-xr-x   1 silence  staff  17246480 Dec 29 11:27 zhjd-linux-amd64
	-rwxr-xr-x   1 silence  staff  13034956 Dec 29 11:28 zhjd-linux-arm-5
	-rwxr-xr-x   1 silence  staff  12965808 Dec 29 11:29 zhjd-linux-arm-6
	-rwxr-xr-x   1 silence  staff  12939788 Dec 29 11:29 zhjd-linux-arm-7
	-rwxr-xr-x   1 silence  staff  17075720 Dec 29 11:29 zhjd-linux-arm64
	-rwxr-xr-x   1 silence  staff  18700376 Dec 29 11:30 zhjd-linux-mips64
	-rwxr-xr-x   1 silence  staff  18699640 Dec 29 11:31 zhjd-linux-mips64le
	-rwxr-xr-x   1 silence  staff   9734407 Dec 29 11:31 zhjd-windows-4.0-386.exe
	-rwxr-xr-x   1 silence  staff  10889885 Dec 29 11:31 zhjd-windows-4.0-amd64.exe

可以发现,针对不同平台生成了不同类型的文件.Android支持生成aar库文件,iOS支持生成Framework库文件


### 平台实测
#### Android端测试
1. 推送到设备中,之所以不直接推送到app目录下是因为权限问题,这里如果提示adb命令不存在同样的也要把Android SDK中的adb命令配置到环境变量中
	
		//直接推送到app目录下会有权限问题
		
		adb push zhjd-android-16-arm data/data/com,kingdee.zhihuiji 
		adb: error: failed to copy 'zhjd-android-16-arm' to 'data/data/	com.kingdee.zhihuiji': Permission denied
	
		//所以推送到该目录下
		
		adb push zhjd-android-16-arm data/local/tmp                
		[100%] data/local/tmp/zhjd-android-16-arm


2. 切换到Android设备并执行该文件,这里只能使用已经**root的设备**
	
	
		adb shell 进入手机命令行
		su 切换超级用户
		cd /data/local/tmp 
		./zhjd-android-16-arm
	
最后在终端会输出可执行文件的执行结果
