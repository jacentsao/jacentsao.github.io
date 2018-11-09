---
title: Mac OS X 配置环境变量
date: 2017-01-16 22:15:59
categories: Shell
---



/etc/bashrc 是针对系统所有用户的全局变量，只有root用户才能修改这两个文件，对一般用户来说是他们是只读的。一般用户要想修改它们，可以在命令前加sudo，意思是以Root身份执行，比如：sudo vi /etc/profile  ，然后按照提示输入密码即可。(以上是针对使用默认shell的用户,如果使用zsh的话则对应的修改文件是.zshrc)

因此，对于一般用户而言，通常我们建议去修改~/.bash_profile来设置环境变量，它是用户级的设置，只对当前用户有效。
操作步骤:

			1、打开Terminal（终端）
            2、输入：vi ~/.bash_profile
            3、设置PATH：export PATH=/usr/local/mysql/bin:$PATH
            4、输入：:wq    //保存并退出vi
            5、修改立即生效：source ~/.bash_profile
            6、查看环境变量的值：echo $PATH
