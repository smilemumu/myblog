---
title: "Feign"
date: 2021-01-21
tags: [Feign]
categories: [SpringCloud]
draft: true
---
## Feign
Feign 采用的是基于接口的注解
Feign 整合了ribbon


#### Feign服务调用
1.需要添加依赖spring-cloud-starter-openfeign
```
<dependency>
　　<groupId>org.springframework.cloud</groupId>
　　<artifactId>spring-cloud-starter-openfeign</artifactId>
　　<version>2.1.1.RELEASE</version>
</dependency>
```
2.需要在启动类中添加@EnableFeignClients注解
需要在启动类中添加@EnableDiscoveryClient注解

3.Client端创建一个新的接口并定义Client端需要调用的服务方法
```
@FeignClient(name = "service-hello")
public interface ServerApi {

    @GetMapping("/hello/world")
    String sayHelloWord();
}

```
使用时
```
@GetMapping("/sayHelloWord")
    public Object sayHelloWord() {
        return serverApi.sayHelloWord();
}
```


refer to : https://www.fangzhipeng.com/springcloud/2017/06/03/sc03-feign.html
