---
title: gomobile相关的错误处理
date: 2017-12-19 12:16:06
tags:
---


**务必确认在构建非Windows包的时候，根目录下面无syso版本信息文件，否则各种各样的奇葩错误**


##### seq_android.c:213:3: error

```gomobile: go build -pkgdir=/Users/Eveee/Storage/go/pkg/gomobile/pkg_android_arm64 -buildmode=c-shared -o=/var/folders/w4/75xgx41n2bb7mm3m0dxbp0d40000gn/T/gomobile-work-159245723/android/src/main/jniLibs/arm64-v8a/libgojni.so /var/folders/w4/75xgx41n2bb7mm3m0dxbp0d40000gn/T/gomobile-work-159245723/androidlib/main.go failed: exit status 2
# _/var/folders/w4/75xgx41n2bb7mm3m0dxbp0d40000gn/T/gomobile-work-159245723/gomobile_bind
seq_android.c:213:3: error: implicitly declaring library function 'memcpy' with type 'void *(void *, const void *, unsigned long)' [-Werror,-Wimplicit-function-declaration]
seq_android.c:213:3: note: include the header <string.h> or explicitly provide a declaration for 'memcpy'```

解决方案

```https://github.com/golang/go/issues/22766 seq_android.c:213:3: error ```