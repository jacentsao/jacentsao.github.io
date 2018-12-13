---
title: hexo-使用nginx部署
date: 2018-12-13 08:38:25
tags: [nginx,blog]
categories: 博客编写
---

通常使用hexo在本地调试运行的时候我们运行`hexo s`即可，如果我们想要部署博客供人访问的时候就要采用别的方式了。我这里选择的是使用nginx部署：


#### 安装nginx

安装nginx可以参考[菜鸟教程](http://www.runoob.com/linux/nginx-install-setup.html)

#### 使用nginx配置hexo

``` nginx
location / {
    root   /usr/local/blog/jacentsao.github.io/.deploy_git;
    index  index.html index.htm;
}
```

#### 获得免费的https证书

```shell
$ yum install epel-release
$ yum-config-manager --enable rhui-REGION-rhel-server-extras rhui-REGION-rhel-server-optional
$ yum install python2-certbot-nginx
$ yum install -y certbot
//获取证书
$ certbot certonly --standalone -d example.com --agree-tos --email yourmail
```

#### 配置nginx

```nginx
 server {
        listen       80;
        server_name  localhost;
        listen 443 ssl;
        ssl_certificate /etc/letsencrypt/live/yourdomain/fullchain.pem;
        ssl_certificate_key /etc/letsencrypt/live/yourdomain/privkey.pem;
        .....
 }       
```



#### 自动更新证书

```shell
//自动刷新证书（证书有效期90天）
$ 10 1 * */2 * certbot renew --pre-hook "systemctl stop nginx" --post-hook "systemctl start nginx" 
```