---
title: Android-基础-FileProvider使用
date: 2017-10-27 22:49:48
tags: [Android, FileProdvider]
categories: Android
---

移动平台对于安全和隐私是越来越注重了，Android在权限方面也在不断的收紧，一定程度上对于流氓软件还是有克制作用的。

今天学习了一下[FileProvider](https://developer.android.com/training/secure-file-sharing/setup-sharing.html)和[文档](https://developer.android.com/reference/android/support/v4/content/FileProvider.html#getUriForFile(android.content.Context, java.lang.String, java.io.File))的基本使用：

##### 1.定义Provider

首先要在MManifest文件中定义一个Provider，其中authorities=包名.fileprovider。

```
<manifest xmlns:android="http://schemas.android.com/apk/res/android"
    package="com.example.myapp">
    <application
        ...>
        <provider
            android:name="android.support.v4.content.FileProvider"
            android:authorities="com.example.myapp.fileprovider"
            android:grantUriPermissions="true"
            android:exported="false">
            <meta-data
                android:name="android.support.FILE_PROVIDER_PATHS"
                android:resource="@xml/filepaths" />
        </provider>
        ...
    </application>
</manifest>
```

##### 2.创建filepaths文件

在res目录下面新建一个xml目录，并在新建的xml目录下面创建一个filepaths的文件，内容如下所示：

```
<paths xmlns:android="http://schemas.android.com/apk/res/android">
    <files-path name="my_images" path="images/"/>
    ...
</paths>
```

定义的路径又分为如下几种，分别对应内部存储和外部存储

```
<files-path name="name" path="path" />  Context.getFilesDir().
<cache-path name="name" path="path" />  getCacheDir().
<external-path name="name" path="path" />  Environment.getExternalStorageDirectory().
<external-files-path name="name" path="path" />Context#getExternalFilesDir(String) Context.getExternalFilesDir(null).
<external-cache-path name="name" path="path" /> Context.getExternalCacheDir().
```

其中，name是出于安全考虑用于替换path显示的，path则是真实的次级目录

##### 3.创建一个可分享的Uri

```
File imagePath = new File(Context.getFilesDir(), "images");
File newFile = new File(imagePath, "default_image.jpg");
Uri contentUri = FileProvider.getUriForFile(getContext(), "com.mydomain.fileprovider", newFile);
```

最终获取到的Uri是```content://com.mydomain.fileprovider/my_images/default_image.jpg```，可以看到实际的images路径被替换为my_images了。
