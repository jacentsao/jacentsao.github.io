---
title: Android-Duplicate files copied in APK META-INF/LICENSE
date: 2017-03-15 16:46:54
tags: [Android,错误]
categories: Android
---

今天使用jackson的时候碰到了如下错误：


	Error:Execution failed for task ':app:transformResourcesWithMergeJavaResForDebug'.
	> com.android.build.api.transform.TransformException: com.android.builder.packaging.DuplicateFileException: Duplicate files copied in APK META-INF/LICENSE
	  	File1: /Users/Eveee/.gradle/caches/modules-2/files-2.1/com.fasterxml.jackson.core/jackson-core/2.7.2/8b8310381b690e317f5f0574e9b2dd7034778b4c/jackson-core-2.7.2.jar
	  	File2: /Users/Eveee/.gradle/caches/modules-2/files-2.1/com.fasterxml.jackson.core/jackson-annotations/2.7.0/19f42c154ffc689f40a77613bc32caeb17d744e3/jackson-annotations-2.7.0.jar
	  	File3: /Users/Eveee/.gradle/caches/modules-2/files-2.1/com.fasterxml.jackson.core/jackson-databind/2.7.2/84ffa765dd258dbab8695963c41308b054f3a1cb/jackson-databind-2.7.2.jarError:Execution failed for task ':app:transformResourcesWithMergeJavaResForDebug'.
	> com.android.build.api.transform.TransformException: com.android.builder.packaging.DuplicateFileException: Duplicate files copied in APK META-INF/LICENSE
	  	File1: /Users/Eveee/.gradle/caches/modules-2/files-2.1/com.fasterxml.jackson.core/jackson-core/2.7.2/8b8310381b690e317f5f0574e9b2dd7034778b4c/jackson-core-2.7.2.jar
	  	File2: /Users/Eveee/.gradle/caches/modules-2/files-2.1/com.fasterxml.jackson.core/jackson-annotations/2.7.0/19f42c154ffc689f40a77613bc32caeb17d744e3/jackson-annotations-2.7.0.jar
	  	File3: /Users/Eveee/.gradle/caches/modules-2/files-2.1/com.fasterxml.jackson.core/jackson-databind/2.7.2/84ffa765dd258dbab8695963c41308b054f3a1cb/jackson-databind-2.7.2.jar


​	  	
​	  	
在module的gradle文件中添加如下代码即可:

	Android{
		packagingOptions {
		        exclude 'META-INF/DEPENDENCIES'
		        exclude 'META-INF/NOTICE'
		        exclude 'META-INF/LICENSE'
		        exclude 'META-INF/LICENSE.txt'
		        exclude 'META-INF/NOTICE.txt'
		    }
	}  	
