---
title: 使用docker安装WordPress
date: 2018-05-31 08:40:17
tags: [博客]
categories: [博客]
---


####  下载WorldPress镜像


```

$ sudo docker pull wordpress:latest
$ sudo docker pull mysql
$ docker run --name docker-mysql -e MYSQL_ROOT_PASSWORD=KingDee@2017 -d mysql:5.7
$ docker run --name docker-wordpress --link docker-mysql:mysql -p 6000:80 -d wordpress

$ 安装插件：https://wordpress.org/plugins/useso-take-over-google/

$ docker run -p 3306:3306 --name e-mysql -e MYSQL_ROOT_PASSWORD=10086.com -d mysql:5.7

$ docker exec -it 88dab2f338c6 bash  
$ mysql -uroot -p -h localhost
$ use mysql

$ docker run -p 6379:6379 -v $PWD/data:/data  -d redis:3.2 redis-server --appendonly yes

$ docker exec -it  b1125e1d09b8  redis-cli

```


