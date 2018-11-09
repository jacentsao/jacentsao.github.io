---
title: Zheng项目学习
date: 2018-05-14 23:12:04
tags: [java,web]
categories: [java]
---


#### 1. 环境配置

##### 1. 安装redis

Redis是一个使用ANSI C编写的开源、支持网络、基于内存、可选持久性的键值对存储数据库。

###### Windows安装

从Github上面下载
```
https://github.com/MicrosoftArchive/redis/releases
```

###### Mac安装
```

$ brew install redis

To have launchd start redis now and restart at login:
  brew services start redis
Or, if you don't want/need a background service you can just run:
  redis-server /usr/local/etc/redis.conf
  
  
$ redis-cli 进入redis命令  
```


##### 2. 安装zookeeper

Zookeeper是一种分布式协调服务，用于管理大型主机。在分布式环境中协调和管理服务是一个复杂的过程，Zookeeper通过其简单的架构和API解决了这些问题。Zookeeper允许开发人员专注与核心应用程序逻辑，而不必担心应用程序的分布式特征。

分布式应用正在运行的一组系统成为**集群**，集群中运行的每台机器称为**节点**。

###### Macos通过brew安装
```
$ brew install zookeeper
To have launchd start zookeeper now and restart at login:
  brew services start zookeeper
Or, if you don't want/need a background service you can just run:
  zkServer start

```
###### 从Apache官网下载


下载地址：

```
http://mirror.bit.edu.cn/apache/zookeeper/stable/zookeeper-3.4.12.tar.gz
```

下载解压以后需要更改配置文件

1. 在解压的目录下新建配置文件zoo.cfg
2. 新增以下配置

```
	tickTime = 2000	dataDir = /path/to/zookeeper/data	clientPort = 2181	initLimit = 5	syncLimit = 2
```
3. 启动解压目录下的zkServer.sh(linux)或者zkServer.cmd(Windows)
4. 启动解压目录下的zkCli.sh(linux)或者zkCli.cmd(Windows)

##### 3. 安装 Apache ActiveMQ

```
$ brew install apache-activemq
To have launchd start activemq now and restart at login:
  brew services start activemq
Or, if you don't want/need a background service you can just run:
  activemq start
```


##### 4. 安装MySQL

```
$ brew install mysql
We've installed your MySQL database without a root password. To secure it run:
    mysql_secure_installation

MySQL is configured to only allow connections from localhost by default

To connect run:
    mysql -uroot

To have launchd start mysql now and restart at login:
  brew services start mysql
Or, if you don't want/need a background service you can just run:
  mysql.server start
```

##### 5. 安装jenkins

```
Note: When using launchctl the port will be 8080.

To have launchd start jenkins now and restart at login:
  brew services start jenkins
Or, if you don't want/need a background service you can just run:
  jenkins
==> Summary
🍺  /usr/local/Cellar/jenkins/2.121: 7 files, 74.4MB, built in 42 seconds
```
