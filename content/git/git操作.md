---
title: "git创建新仓库"
date: 2019-09-11
tags: [git]
categories: [git]
draft: true
---

通过命令行方式对本地文件创建git，再远程创建git并提交。  
<!--more-->

```git
echo "# myblog" >> README.md
git init
git add README.md
git commit -m "first commit"
git remote add origin https://github.com/smilemumu/myblog.git
git push -u origin master
```

远程仓库相关命令
```git;
检 出 仓 库： $ git clone git://github.com/jquery/jquery.git
查看远程仓库：$ git remote -v
添加远程仓库：$ git remote add [name] [url]
删除远程仓库：$ git remote rm [name]
拉取远程仓库：$ git pull [remoteName] [localBranchName]
推送远程仓库：$ git push [remoteName] [localBranchName]
git本地分支跟踪远程分支 $ git branch -u origin/[remoteBranchName]
```
分支(branch)操作相关命令
```git
查看本地分支：$ git branch
查看远程分支：$ git branch -r
创建本地分支：$ git branch [name] ----注意新分支创建后不会自动切换为当前分支
切换分支：$ git checkout [name]
创建新分支并立即切换到新分支：$ git checkout -b [name]
删除分支：$ git branch -d [name] ---- -d选项只能删除已经参与了合并的分支，对于未有合并的分支是无法删除的。如果想强制删除一个分支，可以使用-D选项
合并分支：$ git merge [name] ----将名称为[name]的分支与当前分支合并
创建远程分支(本地分支push到远程)：$ git push origin [name]
```

版本(tag)操作相关命令
```git
查看版本：$ git tag
创建版本：$ git tag [name]
删除版本：$ git tag -d [name]
查看远程版本：$ git tag -r
创建远程版本(本地版本push到远程)：$ git push origin [name]
```

其他
查看命令历史：查看仓库的操作历史：
```git
$ git reflog
```

版本回退：可以将当前仓库回退到历史的某个版本
```git
git reset 版本回退
第一种用法：回退到上一个版本（HEAD代表当前版本，有一个^代表上一个版本，以此类推）
$ git reset --hard HEAD^

第二种用法：回退到指定版本(其中d7b5是想回退的指定版本号的前几位)
$ git reset --hard d7b5

```
比较：如果文件修改了，还没有提交，就可以比较文件修改前后的差异
```git
$ git diff filename 
```
查看git库的状态，未提交的文件，分为两种，add过已经在缓冲区的，未add过的
```git
$ git status 
``` 
查看日志
```git
$ git log
```

初始化：创建一个git仓库，创建之后就会在当前目录生成一个.git的文件
```git
$ git init
```

添加文件：把文件添加到缓冲区
```git
$ git add filename
```

添加所有文件到缓冲区（从目前掌握的水平看，和后面加“.”的区别在于，加all可以添加被手动删除的文件，而加“.”不行）：
```git
$ git add .
$ git add --all
```

删除文件
```git
$ git rm filename
```

提交：提交缓冲区的所有修改到仓库(注意：如果修改了文件但是没有add到缓冲区，也是不会被提交的)
commit可以一次提交缓冲区的所有文件
```git
$ git commit -m "提交的说明"
```

将文件缓存到本地
```git
git add .
```

将缓存的文件提交到本地
```git
git commit -m "提交的说明"
```

将本地Master提交到远程Master
```git
git push -u origin master 
```
刷新远程分支
git remote update origin --prune
git remote update origin -p





如果 git merge了错误分支，如何优雅的回退到merge前的状态？
没push的话
git reset --hard merge前的那个版本号
然后重新 git merge

refer to: https://www.cnblogs.com/springbarley/archive/2012/11/03/2752984.html  
refer to: https://blog.csdn.net/lxw198902165221/article/details/89228458