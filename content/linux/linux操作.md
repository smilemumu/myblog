---
title: "linux命令"
date: 2019-10-08
tags: [linux]
categories: [linux]
draft: true
---

- 一行shell命令杀死指定进程名称的进程方法

```
ps -efww|grep -w '程序名称'|grep -v grep|cut -c 9-15|xargs kill -9
```

- 杀死程序名称为ssz-h5的程序

```
[root@sagdb ssz-h5]# ps -ef|grep java
root     28414     1  2 Sep27 ?        05:39:12 java -jar ssz-llyy.jar
root     29837     1 99 14:37 pts/0    00:00:03 java -jar ssz-h5.jar
root     29856 29557  0 14:37 pts/0    00:00:00 grep java
[root@sagdb ssz-h5]# ps -efww|grep -w 'ssz-h5'|grep -v grep|cut -c 9-15|xargs kill -9
```

- 远程启动脚本

```
nohup java -Xdebug -Xrunjdwp:transport=dt_socket,address=2345,server=y,suspend=n -jar ssz-h5.jar testinfo2 > h5.log 2>&1 &
```



### curl
- 使用curl发送GET请求：curl protocol://ip:port/url?args
```
curl https://proxy.mimvp.com/login?user=admin&passwd=12345678  
```
 

- 使用curl发送POST请求： （推荐）
```
curl -d "key1=value1&key2=value2&key3=value3" protocol://ip:port/path
```
```
示例1：curl -d 'post_data=i_love_mimvp.com' https://proxy.mimvp.com/ip.php   // 测试 post ，发送什么数据就返回什么数据，如 'i_love_mimvp.com'
示例2：curl -d "user=admin&passwd=12345678" https://proxy.mimvp.com/login    // 测试 post ，模拟发送登录的用户名和密码
```
 
- 这种方法是参数直接在header里面的，如需将输出指定到文件可以通过重定向进行操作.
```
curl -H "Content-Type:application/json" -X POST -d 'json data' URL
```
```
示例1：curl -H "Content-Type:application/json" -X POST -d '{"post_data":"i_love_mimvp.com"}' 'https://proxy.mimvp.com/ip.php'
示例2：curl -H "Content-Type:application/json" -X POST -d '{"user": "admin", "passwd":"12345678"}' https://proxy.mimvp.com/login  
```

-查找文件
```
find ./ |grep "xxx"
```