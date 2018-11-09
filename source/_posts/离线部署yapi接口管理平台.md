---
title: 内网部署yapi接口管理平台
date: 2018-05-13 22:29:29
tags: [api]
categories: api
---

YApi 是一个可本地部署的、打通前后端及QA的、可视化的接口管理平台，[项目官网](https://yapi.ymfe.org),[Github地址](https://github.com/YMFE/yapi)。

本文是对自己在多平台内网部署的时候的过程及遇到的问题的记录。

<!-- more -->

#### 内网部署

##### 环境要求

* nodejs (7.6+)
* mongodb (2.6+)
* git 

##### 安装

使用yapi-cli工具部署。执行yapi sever启动可视化部署程序。

##### 升级

cd  {项目目录}
yapi ls //查看版本号列表
yapi update //更新到最新版本
yapi update -v {Version} //更新到指定版本

#### centos安装环境

##### 安装nodejs

使用EPEL安装nodejs

```
$ yum info epel-release  检查是否有安装

$ sudo yum install epel-release 安装epel

$ sudo yum install -y nodejs 安装nodejs

$ sudo npm install -g cnpm --registry=https://registry.npm.taobao.org

$ sudo npm install -g n

$ sudo n stable 安装最新的稳定版本

```


##### 安装mongodb

1 . 创建文件

```
$ vim /etc/yum.repos.d/mongodb-org.repo

```

2 . 复制下面的内容

```
[mongodb-org-3.6]
name=MongoDB Repository
baseurl=https://repo.mongodb.org/yum/redhat/$releasever/mongodb-org/3.6/x86_64/
gpgcheck=1
enabled=1
gpgkey=https://www.mongodb.org/static/pgp/server-3.6.asc
```
国内的服务器替换 baseurl 为阿里源,否则你回感受来自共产主义的负加成

```
baseurl=http://mirrors.aliyun.com/mongodb/yum/redhat/6/mongodb-org/stable/x86_64/
```

我搭建的时候用的

```
baseurl=http://mirrors.aliyun.com/mongodb/yum/redhat/7Server/mongodb-org/3.2/x86_64/
```

下载速度很快但是每次卡在最后一步，最后把3.2版本改为3.6以后一下载就安装好了


3 . 更新文件，安装

```
$ yum udpate 
$ yum -y install mongodb=org
```

##### 配置nginx代理
由于centos是丢在一台Windows机器的Vmware WorkStation上面，所以还用了nginx反向代理yapi，配置如下

```
location / {
            proxy_http_version 1.1;
	    proxy_set_header Upgrade $http_upgrade;
	    proxy_set_header Connection "upgrade";
            proxy_pass   http://127.0.0.1:3011;
    }
```


#### Macos安装

由于开了全局代理，终端翻墙，安装过程基本按照[官方教程](https://github.com/suxiaoxin/yapi_user_guide/blob/master/mac%E7%8E%AF%E5%A2%83%E9%85%8D%E7%BD%AE.md)过程很流畅。


#### 结语

做开发，一要网络好，二要代理好。感谢GCD，感谢阿里巴巴。