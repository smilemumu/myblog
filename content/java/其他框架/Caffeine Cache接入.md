---
title: "Caffeine Cache接入"
date: 2019-11-20
tags: [缓存]
categories: [java]
draft: true
---





##  Caffeine Cache接入

-   Caffeine Cache，目前最快的Java内存框架，堆内存。
	-   接入方式：
		-   1.引入pom文件
		```
		<dependency>
            <groupId>com.github.ben-manes.caffeine</groupId>
            <artifactId>caffeine</artifactId>
            <version>2.7.0</version>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-cache</artifactId>
        </dependency>
		```
		-   2.编写CaffeineConfig并注入CacheManager

			```
			import org.springframework.cache.CacheManager;
			import org.springframework.cache.annotation.CachingConfigurerSupport;
			import org.springframework.cache.annotation.EnableCaching;
			import org.springframework.cache.caffeine.*;
			import org.springframework.context.annotation.Bean;
			import org.springframework.context.annotation.Configuration;
			import com.github.benmanes.caffeine.cache.Caffeine;
			
			import java.util.concurrent.TimeUnit;
			
			@Configuration
			@EnableCaching
			public class CaffeineConfig extends CachingConfigurerSupport {
			
			    @Bean
			    public CacheManager caffeineCacheManager() {
			        CaffeineCacheManager cacheManager = new CaffeineCacheManager();
			        //定制化缓存Cache
			        cacheManager.setCaffeine(Caffeine.newBuilder()
			                .expireAfterWrite(3, TimeUnit.MINUTES)
			                .initialCapacity(100)
			                .maximumSize(10000))
			        ;
			        return cacheManager;
			    }
			}
			```
		-   3.通过注解使用
		    ```
			@Cacheable(cacheNames = "cache name", key = "#patam1+'-'+#param2")
			public void test(String param1,String param2){
			}
			```