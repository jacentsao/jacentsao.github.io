---
title: flutter-环境异常问题合集
date: 2019-08-08 22:48:06
tags:
---

#### 1. 白屏，无法热更新问题

##### 问题现象

环境变量中配置代理后vscode启动flutter项目白屏，Android Studio无法热更新，使用flutter doctor显示如下：

```shell
$ flutter doctor
Doctor summary (to see all details, run flutter doctor -v):
[✓] Flutter (Channel stable, v1.7.8+hotfix.4, on Mac OS X 10.14.6 18G84, locale zh-Hans-CN)

[✓] Android toolchain - develop for Android devices (Android SDK version 29.0.0)
[✓] Xcode - develop for iOS and macOS (Xcode 10.3)
[!] iOS tools - develop for iOS devices
    ✗ Verify that all connected devices have been paired with this computer in Xcode.
      If all devices have been paired, libimobiledevice and ideviceinstaller may require updating.
      To update with Brew, run:
        brew update
        brew uninstall --ignore-dependencies libimobiledevice
        brew uninstall --ignore-dependencies usbmuxd
        brew install --HEAD usbmuxd
        brew unlink usbmuxd
        brew link usbmuxd
        brew install --HEAD libimobiledevice
        brew install ideviceinstaller
[✓] Android Studio (version 3.4)
[✓] Android Studio (version 3.4)
[✓] IntelliJ IDEA Ultimate Edition (version 2019.1.3)
[✓] IntelliJ IDEA Ultimate Edition (version 2019.1.3)
[✓] VS Code (version 1.36.1)
[!] Proxy Configuration
    ! NO_PROXY is not set
[✓] Connected device (1 available)

! Doctor found issues in 2 categories.
```
##### 解决方案

在环境变量中添加一下内容
```shell
export NO_PROXY=localhost,127.0.0.1;
```