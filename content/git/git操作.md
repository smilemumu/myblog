---
title: "git创建新仓库"
date: 2019-09-11
tags: [git]
categories: [git]
draft: true
---

通过命令行方式对本地文件创建git，再远程创建git并提交。  
<!--more-->
or create a new repository on the command line

```git
echo "# myblog" >> README.md
git init
git add README.md
git commit -m "first commit"
git remote add origin https://github.com/smilemumu/myblog.git
git push -u origin master
```

```git
推送远程仓库：$ git push [remoteName] [localBranchName]
```

将文件缓存到本地
```git
git add .
```

将缓存的文件提交到本地
```git
git commit -m "本次提交的原因及改动"
```

将本地Master提交到远程Master
```git
git push -u origin master 
//等价于
git push origin master master
```