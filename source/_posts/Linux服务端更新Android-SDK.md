---
title: Linux服务端更新Android SDK
date: 2017-01-19 00:57:39
tags: [自动构建,Android,Jenkins]
categories: Android
---

#### 更新内容

* **更新（2018-11-18，更新使用sdkmanager，更新jcenter和google的仓库）**

#### 使用android

前段时间因为新版本需求更新了一下项目的Recycleview版本,对应的SDK也更新了.项目组的几位同事的项目全都因为找不到对应的SDK报错了,后面把自己的SDK拷贝一份给他们才OK(实在想吐槽一下,我觉得做为一个Android程序员翻墙是一项基本的技能).
<!-- more -->
然而就在今天根据新的安排,后续打包统一走运维.而运维使用的是jenkins集成打包,然后上传到[fir](http://fir.im/),测试人员直接下载安装然后进行测试.结果服务端果然出现了同样的错误.

		
		Could not resolve all dependencies for configuration ':app:_debugCompile'.
		> Could not find com.android.support:recyclerview-v7:23.4.0.
     Searched in the following locations:
         https://jcenter.bintray.com/com/android/support/recyclerview-v7/23.4.0/recyclerview-v7-23.4.0.pom
         https://jcenter.bintray.com/com/android/support/recyclerview-v7/23.4.0/recyclerview-v7-23.4.0.jar
         file:/root/.jenkins/jobs_android/workspace/appbs/recyclerview-v7-23.4.0.jar
         file:/root/.jenkins/jobs_android/workspace/appbs/recyclerview-v7.jar
         file:/usr/local/android/extras/android/m2repository/com/android/support/recyclerview-v7/23.4.0/recyclerview-v7-23.4.0.pom
         file:/usr/local/android/extras/android/m2repository/com/android/support/recyclerview-v7/23.4.0/recyclerview-v7-23.4.0.jar
         file:/usr/local/android/extras/google/m2repository/com/android/support/recyclerview-v7/23.4.0/recyclerview-v7-23.4.0.pom
         file:/usr/local/android/extras/google/m2repository/com/android/support/recyclerview-v7/23.4.0/recyclerview-v7-23.4.0.jar
     Required by:
         workspace:app:unspecified
         workspace:app:unspecified > com.yanzhenjie:recyclerview-swipe:1.0.1
         > Could not find com.android.support:recyclerview-v7:23.4.0.
     Searched in the following locations:
         https://jcenter.bintray.com/com/android/support/recyclerview-v7/23.4.0/recyclerview-v7-23.4.0.pom
         https://jcenter.bintray.com/com/android/support/recyclerview-v7/23.4.0/recyclerview-v7-23.4.0.jar
         file:/root/.jenkins/jobs_android/workspace/appbs/recyclerview-v7-23.4.0.jar
         file:/root/.jenkins/jobs_android/workspace/appbs/recyclerview-v7.jar
         file:/usr/local/android/extras/android/m2repository/com/android/support/recyclerview-v7/23.4.0/recyclerview-v7-23.4.0.pom
         file:/usr/local/android/extras/android/m2repository/com/android/support/recyclerview-v7/23.4.0/recyclerview-v7-23.4.0.jar
         file:/usr/local/android/extras/google/m2repository/com/android/support/recyclerview-v7/23.4.0/recyclerview-v7-23.4.0.pom
         file:/usr/local/android/extras/google/m2repository/com/android/support/recyclerview-v7/23.4.0/recyclerview-v7-23.4.0.jar
     Required by:
         workspace:app:unspecified > com.android.support:design:23.1.1
         
         
然后我就跟运维的胖哥搜索教程,第一步去Github上面下载了[lantern](https://github.com/getlantern/lantern) (Lantern都有付费版本了),然后去谷歌搜索Linux update Android SDK,然后找到了官方的[教程](http://tools.android.com/recent/updatingsdkfromcommand-line),在GFW的帮助下,并没有什么用.![Official Guidance](http://tools.android.com/recent/updatingsdkfromcommand-line/2011-08-19%20tools%20screenshot%20r12%20install%20no-ui%20b.png?attredirects=0)

第二步,想在运维那里直接使用云梯翻墙更新(这里吐槽一下,云梯实在很不稳定,建议自己买VPS+Shadowsocks才是王道).跟预料的一样,根本连不上.

第三步,其实一开始思路就是错的,用什么谷歌搜索,只有少数的被国家保护的公民才有这种问题啊,所以直接使用百度搜索即可.果然还是自家人懂得自家事,找到了很多[内容](https://segmentfault.com/a/1190000004639481).


		//step 0:cd 到你安装SDK目录的Tools目录下(例如:/Volumes/work/sdk/tools)
		
		//step 1:设置代理获取可更新列表
		./android list sdk --extended --proxy-host android-mirror.bugly.qq.com --proxy-port 8080 -s
				
		//step 2:设置更新代理并选择下载文件,我这里选择的所有 -a
		./android update sdk --proxy-host android-mirror.bugly.qq.com --proxy-port 8080 -s -u -a		
		
		
		//当然你也可以自定义参数
		 Updates the SDK by suggesting new platforms to install if available.
		Options:
		  -f --force     : Forces replacement of a package or its parts, even if
		                   something has been modified.
		  -n --dry-mode  : Simulates the update but does not download or install
		                   anything.
		     --proxy-host: HTTP/HTTPS proxy host (overrides settings if defined)
		  -s --no-https  : Uses HTTP instead of HTTPS (the default) for downloads.
		  -t --filter    : A filter that limits the update to the specified types of
		                   packages in the form of a comma-separated list of
		                   [platform, system-image, tool, platform-tool, doc, sample,
		                   source]. This also accepts the identifiers returned by
		                   'list sdk --extended'.
		  -u --no-ui     : Updates from command-line (does not display the GUI)
		     --proxy-port: HTTP/HTTPS proxy port (overrides settings if defined)
		  -p --obsolete  : Deprecated. Please use --all instead.
		  -a --all       : Includes all packages (such as obsolete and non-dependent
		                   ones.)

		
		
		//step 3:同意license内容,反正你也没的选,然后就会自动下载并解压
		Do you accept the license 'android-sdk-preview-license-52d11cd2' [y/n]:
		
这里代理服务器其实蛮多的,参考[AndroidDev](http://www.androiddevtools.cn/),不懂翻墙人士的福音.深圳地区设置腾讯代理以后下载速度基本上都是2-3M起,速度非常快.

**使用上面命令行是因为Linux Server没有可用图形操作界面,如果有图形操作界面的话还是不要使用这种方式了**,把时间花在更有用的地方吧!毕竟我们是一群需求没定好就能把项目做完的码农!


#### 使用sdkmanager

sdkmanager是谷歌推出的新的工具，以前的android工具已经是deprecated状态，所以大家尽量切换到新的sdkmanager进行管理。同样的，我们也要对工具进行代理才能够正常的更新。

```
//获取所有的工具列表
sdkmanager --list --no_https --proxy=http --proxy_host=g.cn --proxy_port=80
//安装指定的工具（"platforms;android-28"）
sdkmanager "platforms;android-28" --no_https --proxy=http --proxy_host=g.cn --proxy_port=80
//也可以同时指定安装多个工具
sdkmanager "platforms;android-28" "build-tools;28.0.3" --no_https --proxy=http --proxy_host=g.cn --proxy_port=80
```

如下所示：
![](https://raw.githubusercontent.com/jacentsao/picbed/master/img/markdownnecessary%20android%20sdk%20tools.png)

#### 使用阿里云仓库

阿里云仓库地址 http://maven.aliyun.com/mvn/view， gradle版本的一些说明  https://developer.android.com/studio/releases/gradle-plugin

```
//替换jcenter()
maven {url 'https://maven.aliyun.com/repository/jcenter'}
//替换google(),从gradle3.0.0开始就需要添加谷歌仓库
maven {url 'https://maven.aliyun.com/repository/google'}
//公共仓库
maven {url 'https://maven.aliyun.com/repository/public'}
```