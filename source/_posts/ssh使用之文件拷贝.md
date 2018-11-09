---
title: ssh使用之文件拷贝
date: 2017-11-30 20:53:21
tags: [ssh]
categories: SSH
---

最近想学习一下Golang,自己买了一个188一年的京东云服务器，准备丢点东西上去，过程实在是各种折腾，门外汉的痛苦。

#### ssh传输文件

 ```
 上传文件
 scp /path/filename username@servername:/path
 下载文件
 scp username@servername:/path/filename /var/www/local_dir（本地目录）
 下载目录
 scp -r username@servername:/var/www/remote_dir/（远程目录） /var/www/local_dir（本地目录）
 上传目录
 scp  -r local_dir username@servername:remote_dir
 
 ```   
 
 

