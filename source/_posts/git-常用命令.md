---
title: git-常用命令
date: 2017-08-07 22:22:27
tags: [git, 命令行]
categories: 工具
---



###  开始
1. git clone http://xx/zhj_android.git
2. 添加.gitignore到根目录，[参考资料](https://github.com/github/gitignore)
3. git add .gitignore 添加该文件到版本控制，提交到暂存区
4. git commit -m "add .gitignore file" 从暂存区提交到当前分支
5. git push 提交到远程仓库

### 常用的git命令
1. git rm -r xx 从版本控制中移除xx目录及子目录
2. rm -rf xx 如果xx中还有git的子模块，需要使用该命令删除
3. git reset --hard HEAD 重置未提交的记录到HEAD Version，HEAD^回滚到上个版本,HEAD^^上上个版本，依次类推，或者指定回退HEAD~xx,xx个版本,或者指定log 提交的id
4. git init 初始化一个目录为git仓库
5. git status 查看仓库状态
6. git diff 查看变更，同时也可以指定xx文件或者目录
7. git add xx 添加xx到git版本控制，每一次修改文件以后都需要添加到版本控制系统
8. git commit -m "xx" 提交到本地仓库，提交信息为xx (每次修改，如果不add到暂存区，那就不会加入到commit中。)
9. git log 查看提交日志
10. git log --pretty=oneline git log的美化版
11. git reflog 查看每一次的git提交命令
12. git checkout -b branchname 新建分支
13. git checkout branchname 切换到分支
14. git branch 列举所有分支，带*号的为当前都在分支，加上 -r参数则是列举的远程分支
15. git merge branchname 合并分支到当前分支，合并模式有FAST_FORWARD 无冲突自动合并，
16. git branch -d branchname 删除分支
17. git log --pretty=oneline --abbrev-commit
18. git tag v0.9 6224937
19. git push origin v5.0.1 创建远程tag或者分支，前提是这个要存在
20. git checkout -- xxx 回滚xxx
21. git remote add origin http://192.168.204.42/ZHJ/zhj_kernel.git 添加远程

#### 暂存当前更改

1. git stash   存储
2. git stash list  存储列表
3. git stash apply  取出存储的内容不删除缓存
4. git stash pop  去除存储的内容并删除缓存
5. git stash clear 清除所有的存储内容


#### 合并指定commit

1. git cherry-pick log-hash
