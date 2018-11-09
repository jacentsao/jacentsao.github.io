---
title: Linux 命令行学习
date: 2017-05-24 21:57:27
tags: [Command, Linux]
categories: Linux
---

#### 1: 别名
alias 别名
列表展示： alias
设置： alias name='string' eg: alias foo='ls -al'
取消设置: unalias foo

#### 2：I/O重定向
* cat - Concatenate files /连接文件
* sort - Sort lines of text /排序文件
* uniq - Report or omit repeated lines /报道或省略重复行 
* grep - Print lines matching a pattern /打印匹配行
* wc - Pring newline, word, and byte counts for each file /打印文件中的换行符，字，和字节个数 
* head - Output the first part of a file /输出文件开头部分
* tail - Output the last part of a file /输出文件结尾部分
* tee - Read from standard input and write to standard output and files /读取标准输入，写入标准输出和文件

#### 3：echo
* echo - Diaplay a line of text / 显示一行文本

#### 4：进程
* sof -i tcp:port 查看端口占用，显示PID
* kill -9 pid 干掉占用的进程
*  netstat -an | grep port