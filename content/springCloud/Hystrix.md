---
title: "Hystrix"
date: 2021-01-21
tags: [Hystrix]
categories: [SpringCloud]
draft: true
---
## Hystrix
Spring Cloud可以用RestTemplate+Ribbon和Feign来调用。为了保证其高可用，单个服务通常会集群部署。由于网络原因或者自身的原因，服务并不能保证100%可用，如果单个服务出现问题，调用这个服务就会出现线程阻塞，此时若有大量的请求涌入，Servlet容器的线程资源会被消耗完毕，导致服务瘫痪。服务与服务之间的依赖性，故障会传播，会对整个微服务系统造成灾难性的严重后果，这就是服务故障的“雪崩”效应。

Netflix开源了Hystrix组件，实现了断路器模式，SpringCloud对这一组件进行了整合。
较底层的服务如果出现故障，会导致连锁故障。当对特定的服务的调用的不可用达到一个阀值（Hystric 是5秒20次） 断路器将会被打开。
断路打开后，可用避免连锁故障，fallback方法可以直接返回一个固定值。



###启用hystrix
第一步：添加依赖
```
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-starter-hystrix</artifactId>
</dependency>
```
第二步：在程序的启动类加@EnableHystrix注解开启Hystrix

第三步：在HelloService方法上加上**@HystrixCommand**注解。该注解对该方法创建了熔断器的功能。
指定**fallbackMethod**熔断方法为sayError;
```
@Service
public class HelloService {

    @Autowired
    RestTemplate restTemplate;

    @HystrixCommand(fallbackMethod = "sayError")
    @GetMapping("/sayHelloWord")
    public Object sayHelloWord() {
        return serverApi.sayHelloWord();
	}

    public String sayError() {
        return "sorry,error!";
    }
}
```

###Feign中使用断路器
Feign是自带断路器的，在D版本的Spring Cloud中，它没有默认打开
```
feign.hystrix.enabled=true
```

```
@FeignClient(name = "service-hello",fallback = ServiceHelloHystric.class)
public interface ServerApi {

    @GetMapping("/hello/world")
    String sayHelloWord();
}
```
ServiceHelloHystric需要实现ServerApi 接口，并注入到Ioc容器中，代码如下：
```
@Component
public class ServiceHelloHystric implements ServerApi {
    @Override
    public String sayHelloWord(String name) {
        return "sorry ,error";
    }
}
```

### Hystrix Dashboard (断路器：Hystrix 仪表盘)

- 在pom.xml引入spring-cloud-starter-hystrix-dashboard的起步依赖：

```
<dependency>
			<groupId>org.springframework.boot</groupId>
			<artifactId>spring-boot-starter-actuator</artifactId>
		</dependency>

		<dependency>
			<groupId>org.springframework.cloud</groupId>
			<artifactId>spring-cloud-starter-hystrix-dashboard</artifactId>
		</dependency>
```

- 在启动类中加入@EnableHystrixDashboard注解，开启hystrixDashboard：
- 在浏览器中输入：http://localhost:8764/hystrix即可访问

refer to : https://www.fangzhipeng.com/springcloud/2017/06/04/sc04-hystrix.html
