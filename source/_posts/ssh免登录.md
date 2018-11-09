---
title: ssh免登录
date: 2017-10-22 08:47:53
tags: [shell, ssh]
categories: shell
---

#### 基本知识

[Secure Shell](https://zh.wikipedia.org/wiki/Secure_Shell)（缩写为SSH），由IETF的网络工作小组（Network Working Group）所制定；SSH为一项创建在应用层和传输层基础上的安全协议，为计算机上的Shell（壳层）提供安全的传输和使用环境。

平时我们会通过ssh远程登录我们的主机来进行管理，在Mac下面一般我会通过自带的Terminal或者[iterm2](https://www.iterm2.com/)直接登录，而在Windows下面的话通常会使用[Putty](http://www.putty.org/)进行登录。				

#### 登录流程

```
基本登录
ssh user@host (用户名+主机地址)

如果本机用户名跟远程的主机用户名一致，可以省略掉用户
ssh host

ssh默认端口是22，如果端口不是默认值则需要指定端口登录
ssh user@host -p 1111 指定端口1111，使用用户user登录到主机

在输入完上面的指令以后接下来就是输入密码了，此处就不再累述了
```

#### 公钥登录

在上面的流程中，我们每次都需要重新输入密码才能够进行登录，那么可不可以免密码直接登录了，答案当然是可以的了。

通过公钥登录可以实现免密码登录，首先在客户端生成一对公钥私钥，然后将公钥添加到远程主机的```~/.ssh/authorized_keys```目录下。每次登录的时候远程主机都回发送一段随机的字符串给客户端，客户端使用私钥进行加密，随后返回远程主机。远程主机使用公钥进行解密，如果解密成功则登录成功，免去了输入密码的流程。

具体流程如下：

```
1. 在客户端生成一对公钥私钥
	ssh-keygen
这个时候在目录~/.ssh/下面就会生成id_rsa.pub（公钥），id_rsa（私钥）
2. 将公钥拷贝到远程主机
	ssh-copy-id user@host
```

经过上述流程我们就实现了ssh免密码登录的流程了。