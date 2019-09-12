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

```
echo "# myblog" >> README.md
git init
git add README.md
git commit -m "first commit"
git remote add origin https://github.com/smilemumu/myblog.git
git push -u origin master
```