---
title: Android Studio 代理设置
date: 2017-10-22 16:16:15
tags: [Android, Proxy]
categories: Android 
---

最近因为Lantern老是出问题，又买了一个搬瓦工的，搭建好后速度还是可以的，Youtube上面1080P没有卡顿现象。但是各种工具的是使用又出现了问题，所以特意记录一下，节省下次切换工具配置的时间。

#### git配置

首先是配置git的代理，以下是http(s)配置

```shell
#http协议代理
git config --global http.proxy 'http://127.0.0.1:65322'

#通过socks协议
git config --global http.proxy 'socks5://127.0.0.1:1080'

#取消设置代理
git config --global --unset http.proxy
```

```shell
#终端代理
export http_proxy=http://127.0.0.1:1087;
export https_proxy=http://127.0.0.1:1087;
```

```shell
#只针对Github进行代理设置
git config --global http.https://github.com.proxy socks5://127.0.0.1:1080

#取消代理
git config --global --unset http.https://github.com.proxy
```

```
#对于使用git@协议的，可以配置socks5代理
在~/.ssh/config 文件后面添加几行，没有可以新建一个

Host github.com
ProxyCommand nc -X 5 -x 127.0.0.1:1080 %h %p
```

#### Gradle配置

在全局的gradle.properties中添加以下配置，这样子每个项目的gradle同步都回使用ss代理了。

```
systemProp.http.proxyHost=127.0.0.1
systemProp.http.proxyPort=1087
systemProp.https.proxyHost=127.0.0.1
systemProp.https.proxyPort=1087
```

#### Android Studio代理设置

目前来看启用自动代理即可