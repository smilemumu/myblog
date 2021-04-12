---
title: "Eureka服务注册服务发现模块"
date: 2021-01-20
tags: [Eureka]
categories: [SpringCloud]
draft: true
---
## Eureka服务注册服务发现

### 创建服务注册中心
@EnableEurekaServer，
在application启动类上加：@EnableEurekaServer注解

- 具体配置文件：

```
server:
  port: 8761

eureka:
  instance:
    hostname: localhost
  client:
    registerWithEureka: false
    fetchRegistry: false
    serviceUrl:
      defaultZone: http://${eureka.instance.hostname}:${server.port}/eureka/
```
通过eureka.client.registerWithEureka：false和fetchRegistry：false来表明自己是一个eureka server

### 创建服务提供者
当client向server注册时，它会提供一些元数据，例如主机和端口，URL，主页等。Eureka server 从每个client实例接收心跳消息。 如果心跳超时，则通常将该实例从注册server中删除。

@EnableEurekaClient
1.在application启动类上加：@EnableEurekaClient注解
2.在配置文件中注明自己的服务注册中心的地址
- 具体配置文件
```
eureka:
  client:
    serviceUrl:
      defaultZone: http://localhost:8761/eureka/
server:
  port: 8762
spring:
  application:
    name: service-hello
```

假设存在接口/hello/word

spring.application.name,服务与服务之间相互调用一般都是根据这个name 

### 服务调用
#### RestTemplate
例如：RestTemplate中提供的getForObject方法

```
方式1：
String result = template.getForObject("http://127.0.0.1:8762/hello/word", String.class);

方式2：
ServiceInstance serviceInstance = loadBalancerClient.choose("service-hello");
String url = String.format("http://%s:%s/server/get", serviceInstance.getHost(), serviceInstance.getPort());
String result = template.getForObject(url, String.class);

方式3：需要在RestTemplate实例化的地方添加了@LoadBalanced注解
@Bean
@LoadBalanced
RestTemplate restTemplate() {
	return new RestTemplate();
}

@Service
public class HelloService {

    @Autowired
    RestTemplate template;

    public String sayHelloWord(String name) {
        return template.getForObject("http://service-hello/server/get,String.class);
    }

}
```





refer to : https://segmentfault.com/a/1190000018531262
refer to : https://www.fangzhipeng.com/springcloud/2017/06/01/sc01-eureka.html