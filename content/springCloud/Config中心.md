---
title: "Config"
date: 2021-01-21
tags: [Config]
categories: [SpringCloud]
draft: true
---
## Config
在分布式系统中，由于服务数量巨多，为了方便服务配置文件统一管理，实时更新，所以需要分布式配置中心组件。
在spring cloud config 组件中，分两个角色，一是config server，二是config client。

### 使用spring cloud config
### 创建config server
第一步：启动类加上@EnableConfigServer

第二步：配置文件
```
spring.application.name=config-server
server.port=8888

spring.cloud.config.server.git.uri=https://github.com/forezp/SpringcloudConfig/
spring.cloud.config.server.git.searchPaths=respo
spring.cloud.config.label=master
spring.cloud.config.server.git.username=your username
spring.cloud.config.server.git.password=your password
```

- spring.cloud.config.server.git.uri：配置git仓库地址
- spring.cloud.config.server.git.searchPaths：配置仓库路径
- spring.cloud.config.label：配置仓库的分支
- spring.cloud.config.server.git.username：访问git仓库的用户名
- spring.cloud.config.server.git.password：访问git仓库的用户密码



### 创建config client

配置文件：
```
spring.application.name=config-client
spring.cloud.config.label=master
spring.cloud.config.profile=dev
spring.cloud.config.uri= http://localhost:8888/
server.port=8881

```

- spring.cloud.config.label 指明远程仓库的分支
- spring.cloud.config.profile
	- dev开发环境配置文件
    - test测试环境
    - pro正式环境
- spring.cloud.config.uri= http://localhost:8888/ 指明配置服务中心的网址。

用@Value可读取配置内容

![配置读取过程图](img/2279594-40ecbed6d38573d9.png)

refer to : https://www.fangzhipeng.com/springcloud/2017/06/06/sc06-config.html