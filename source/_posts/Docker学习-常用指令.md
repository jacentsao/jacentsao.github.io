---
title: 'Docker学习,常用指令'
date: 2017-01-24 12:29:04
tags: [Docker,运维]
categories: Docker
---
#### Docker
Docker 使用 Google 公司推出的 Go 语言 进行开发实现，基于 Linux 内核的 cgroup，namespace，以及 AUFS 类的 Union FS 等技术，对进程进行封装隔离， 属于操作系统层面的虚拟化技术。

<!-- more -->
#### 常用指令
* docker run -p 2222:22 imagename //运行改image做为container,不存在则会从Docker hub获取并运行; -p 指定本地映射关系,前面是访问地址端口,后面是映射的容器端口
* docker exec -it goofy_banach /bin/bash 对比于attach方式,会立即显示进入,并且退出不会导致容器关闭
* docker ps //获取正在运行的镜像 -a 所有的镜像
* touch Dockerfile //创建docker脚本
* cat Dockerfile //查看脚本
* docker build -t docker-whale . //创建镜像, -t打tag,后面的.(表示当前目录)不能少
* docker tag 7d9495d03763 maryatdocker/docker-whale:latest 标记docker镜像
* docker login 登录
* docker push maryatdocker/docker-whale 上传到docker hub
* docker rmi -f 7d9495d03763/imagename 移除镜像
* docker run yourusername/docker-whale
* docker images 查看所有镜像
* docker rmi $(docker images -q -f dangling=true) 删除无用的悬浮镜像 
* docker exec  -it webserver bash 运行命令行并启动
* docker commit [选项] <容器ID或容器名> [<仓库名>[:<标签>]] 提交更新,创建镜像**谨慎使用,因为会对容器环境照成较大影响** //docker commit  --author "Tao Wang <twang2218@gmail.com>"  --message "修改了默认网页" webserver  nginx:v2
* docer diff NAMES 查看永久性更新
* docker history nginx:v2 查看历史更新
* docker rm  删除容器
* docker run -it -–entrypoint /bin/bash  karalabe/xgo-latest 强制使用bash启动docker镜像
* docker run -it -v /Volumes/Work/go/src/golang.org:/src golang/mobile /bin/bash 挂载宿主机目录到容器
* sudo yum upgrade docker 更新docker
* docker cp 2e8a06b7cff6:/gopath/src/golang.org/x/mobile/example/GoHello/app/src  Storage-2e8a06b7cff6/
* docker logs id/names 查看操作日志
* docker top 显示容器进程
* docker stats 查看容器进程状态



####tag本地镜像并提交到docker仓库
step1——找到本地镜像的ID：docker images

step2——登陆Hub：docker login --username=username --password=password --email=email

step3——tag：docker tag <imageID> <namespace>/<image name>:<version tag eg latest>

step4——push镜像：docker push <namespace>/<image name>

### Dockerfile
#### 创建 
* mkdir test
* cd test
* touch Dockerfile

#### 基本用法
1. FROM nginx 指定基础镜像,这个是必须的(可以指定一个空白镜像名为scratch)
2. RUN  没一个RUN代表一层,因此同一个目的的RUN通过&&合并命令,浓缩到一层 (层数限制旧版42,新版127)
3. COPY  package.json /usr/src/app/ ||  hom?.txt /mydir/
4. ADD  ubuntu-xenial-core-cloudimg-amd64-root.tar.gz /  src可以是url,加强版的COPY,还可移植性解压缩
5. docker build -t docker-whale . docker build [选项] <上下文路径/URL/-> .代表当前目录 -t代表tag
6. ENTRYPOINT 只有最后一个生效 run的时候通过--entrypoint使其生效


### Docker基本概念
Docker包括三个基本概念,包含了Docker的整个生命周期

* 镜像(Image)
* 容器(Container)
* 仓库(Repository)

#### Docker镜像
Docker镜像,相当于一个root文件系统(操作系统分为内核和用户空间,内核启动后,会挂载root文件系统为用户空间提供支持),镜像不包含任何**动态数据**.提供容器运行时所需的程序、库、资 源、配置等文件外,还包含了一些为运行时准备的一些配置参数（如匿名卷、环境 变量、用户等）.
##### 分层存储
镜像包含操作系统完整的root文件系统,体积较大,Docker设计时,采用了[Union FS](https://en.wikipedia.org/wiki/Union_mount)技术,分层存储的架构.

镜像构建时，会一层层构建，前一层是后一层的基础。每一层构建完就不会再发生 改变，后一层上的任何改变只发生在自己这一层,任何额外的东西应该在该层构建结束前清理掉。

#### Docker容器
类比于面向对象程序设计中 的 类 和 实例 一样，镜像是静态的定义，容器是镜像运行时的实体。


