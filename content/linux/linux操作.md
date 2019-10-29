---
title: "linux命令"
date: 2019-10-08
tags: [linux]
categories: [linux]
draft: true
---

一行shell命令杀死指定进程名称的进程方法

```
ps -efww|grep -w '程序名称'|grep -v grep|cut -c 9-15|xargs kill -9
```

ps:杀死程序名称为ssz-h5的程序
```
[root@sagdb ssz-h5]# ps -ef|grep java
root     28414     1  2 Sep27 ?        05:39:12 java -jar ssz-llyy.jar
root     29837     1 99 14:37 pts/0    00:00:03 java -jar ssz-h5.jar
root     29856 29557  0 14:37 pts/0    00:00:00 grep java

[root@sagdb ssz-h5]# ps -efww|grep -w 'ssz-h5'|grep -v grep|cut -c 9-15|xargs kill -9

```