---
title: Android-应用认领---空包写入签名
date: 2017-07-12 16:10:58
tags: [Android,日常]
categories: Android
---

最近公司项目在百度进行认领，需要将自己的签名写入百度的空包上传，过程如下：

1.执行如下命令

```

jarsigner -verbose -keystore appkey.keystore -signedjar sign.apk Baidu_Claim_unsigned.apk keyAlias

```

其中appkey.keystore：自己的签名文件，可以加上绝对路径

sign.apk：输出文件

Baidu_Claim_unsigned.apk：原始文件

keyAlias: 密玥的别名

此过程需要输入密玥的密码，然后按enter即会执行。

2.执行过程中的输出

```
Enter Passphrase for keystore:
adding: META-INF/MANIFEST.MF
adding: META-INF/___.SF
adding: META-INF/___.RSA
signing: AndroidManifest.xml
signing: res/drawable/ic_launcher.png
signing: res/layout/activity_main.xml
signing: resources.arsc
signing: classes.dex
jar signed.

Warning:
No -tsa or -tsacert is provided and this jar is not timestamped. Without a timestamp, users may not be able to validate this jar after the signer certificate's expiration date (2043-11-01) or after any future revocation date. 
```


3.执行完毕会在当前目录生成一个新的apk，用这个apk上传即可
