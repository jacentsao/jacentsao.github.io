---
title: Android-通过不同的res目录指定不同的资源文件
date: 2017-07-19 22:12:55
tags: [Android, 基础]
categories: Android
---

在Android开发中，我们可能会遇到针对不同的版本进行不同的配置，比如说使用不同的资源文件。[官网传送门](https://developer.android.com/studio/write/add-resources.html)。


##### 自定义资源文件夹路径
默认的资源文件实在  ```module-name/src/source-set-name/res/```目录下. 例如：main的资源文件就在src/main/res/目录下。

不过我们也可以通过自定义的方式指定资源文件目录，如下所示：

```
	android {
		sourceSets {
			main {
				res.srcDirs = ['resources/main']
			}
			
			debug {
				res.srcDirs = ['resources/debug']
			}
		}
	}
```

当然我们也可以指定多个资源文件目录：

```
	android {
		sourceSets {
			main {
				res.srcDirs = ['resources/main', 'resources/test']
			}
			
			debug {
				res.srcDirs = ['resources/debug']
			}
		}
	}
```

不过要注意的是同时指定多个资源文件夹的时候文件不能重复，否则在后续构建[合并资源文件](https://developer.android.com/studio/write/add-resources.html#resource_merging)的时候会出现重复的错误。

#####资源合并