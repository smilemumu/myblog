---
title: "Ribbon"
date: 2021-01-21
tags: [Ribbon]
categories: [SpringCloud]
draft: true
---
## Ribbon
ribbon是一个负载均衡客户端，可以很好的控制htt和tcp的一些行为。Feign默认集成了ribbon。

### 默认负载 均衡策略
SpringCloud默认的负载策略为轮询方式
@LoadBalanced注解会自动采用默信的负载策略（轮询）
在工程的启动类中,
通过**@EnableDiscoveryClient**向服务中心注册;
并且向程序的ioc注入一个bean: restTemplate;
并通过@LoadBalanced注解表明这个restRemplate开启负载均衡的功能。
```
@Bean
@LoadBalanced
RestTemplate restTemplate() {
	return new RestTemplate();
}
```
SpringCloud中提供的常见的为:随机、轮询、哈希、权重等
SpringCloud底层采用的是Ribbon来实现的负载均衡，Ribbon的核心组件为IRule
IRule常见的子类有RandomRule、RoundRobinRule、WeightedResponseTimeRule等。分别对应着随机、轮询、和权重

两种方式设置负载均衡策略
- 代码方式
注入bean
```
@Bean
    public IRule initIRule() {
        return new RandomRule();
}
```
- yml方式
```
注册中心服务端名字:
	ribbon:
		NFLoadBalancerRuleClassName: com.netflix.loadbalancer.RandomRule
```




refer to : https://www.fangzhipeng.com/springcloud/2017/06/02/sc02-rest-ribbon.html
