---
title: Android-判断Library处于release或者debug模式
date: 2017-02-21 14:05:30
tags: Android
categories: Android
---

如何在Library中判断是否是release包:

1. 在Library的gradle文件中添加一个变量标记和设置没有默认发布类型

		Android{
				release {
	            buildConfigField "boolean", "RELEASE_MODE", "true"
	            minifyEnabled false
	            proguardFiles getDefaultProguardFile('proguard-android.txt'), 'proguard-rules.pro'
	        	}
		        debug {
		            buildConfigField "boolean", "RELEASE_MODE", "false"
		        }
		        
		        publishNonDefault true   //设置没有默认发布类型
        }
        
2. 在app module的gradle文件中新增对应Library的release依赖和debug依赖
	
	
		releaseCompile project(path: ':app', configuration: 'release')
	    debugCompile project(path: ':app', configuration: 'debug')    
	    
	    
3. 在Library中引用


		if (BuildConfig.RELEASE_MODE == true) {}    