---
title: 自由行-搭建ss服务器
date: 2018-12-12 19:32:02
tags: [自由行, shadowsocks]
categories: 自由行
---

详细安装配置如下：

```shell
$ cd /etc/yum.repos.d/

$ curl -O https://copr.fedorainfracloud.org/coprs/librehat/shadowsocks/repo/epel-7/librehat-shadowsocks-epel-7.repo

$ yum install -y shadowsocks-libev

$ curl "https://bootstrap.pypa.io/get-pip.py" -o "get-pip.py"

$ python get-pip.py

$ pip install --upgrade pip

//修改配置，主要是设置端口和密码
$ vim /etc/shadowsocks.json
    {
    "server": "0.0.0.0",
    "local_address": "127.0.0.1",
    "local_port": 1080,
    "port_password": {
        "26888": "yourpassword",
        "26889": "yourpassword"
    },
    "timeout": 600,
    "method": "aes-256-cfb"
    }

//添加ss系统服务
$ vim /etc/systemd/system/shadowsocks.service
    [Unit]
    Description=Shadowsocks

    [Service]
    TimeoutStartSec=0
    ExecStart=/usr/bin/ssserver -c /etc/shadowsocks.json

    [Install]
    WantedBy=multi-user.target

//启动ss服务   
$  systemctl enable shadowsocks
```