---
title: hexo-多设备之间无缝衔接
date: 2018-11-28 22:56:04
tags: [hexo,blog]
categories: 博客编写
---

最开始使用hexo的时候是在自己的Mac上面搭的，那时候每天都是背着自己的电脑上下班也没觉得不方便。后来老婆跟我在一起以后老是嫌弃我过安检的时候要走有包通道，非常耗时。所以就放弃了带自己的电脑上班，改为专门使用公司提供的Windows工作。这个时候就必须要把家里的电脑和公司的电脑的博客进行同步了。

<!-- -->

#### 安装依赖工具

1. 安装node

```shell
//下载
$ wget https://nodejs.org/dist/v10.14.0/node-v10.14.0-linux-x64.tar.xz

//解压
$ tar xf node-v10.14.0-linux-x64.tar.xz

//放到usr/local/node目录下
$ mkdir /usr/local/node
$ mv node-v10.14.0-linux-x64   /usr/local/node

//配置环境变量
$ vim ~/.zshrc
$ /usr/local/node/node-v10.14.0-linux-x64/bin 添加到环境变量中
$ source ~/.zshrc 刷新
$ node -v 确认node是否安装ok
```

#### 获取hexo博客源码

将自己的博客通过各种方式获取到当前设备，比如我自己用的是git。如下所示是我的源码分支（hexo-source）：

![](https://raw.githubusercontent.com/jacentsao/picbed/master/img/markdown20181212195458hexo-sourcefile.png)

以下是我的生成的网页的分支(master)：
![](https://raw.githubusercontent.com/jacentsao/picbed/master/img/markdown20181212200121hexo-master.png)

另外再把主题单独弄一个[项目](https://github.com/jacentsao/hexo-theme-next)进行同步。

我的做法是先拉取网页代码（master）分支，然后切换到源码分支（hexo-source），复制一份改名叫.deploy_git放在项目根目录下。
#### 安装启动hexo


```shell
$ npm install -g hexo-cli
$ npm install hexo --save
$ npm install hexo-deployer-git #git部署辅助
$ hexo s --debug  #运行测试
```

#### 提交代码

``` shell
# 部署网页分支（master分支）
hexo clean  
hexo g 
hexo d
# 提交源码分支（hexo-source分支）
git commit
git push

```