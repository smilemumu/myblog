---
title: "ThreadLocal"
date: 2019-10-16
tags: [多线程]
categories: [java]
draft: true
---
# ThreadLocal

## ThreadLocal是什么？
ThreadLocal是一个本地线程变量副本工具类。
保存线程上下文信息，在任意需要的地方可以获取！！！
## ThreadLocal的作用？
ThreadLocal的作用是提供线程内的局部变量，这种变量在线程的生命周期内起作用，减少同一个线程内多个函数或者组件之间一些公共变量的传递的复杂度。
## ThreadLocal实践？
写一个验证token的切面
```java
@Aspect
@Component
public class DemoAspect {
	
	@Pointcut("execution(* *.*..*Controller.*(..))")
    public void pointService(){

    }

	@Around("pointService()")
    public Object aroundMethod(ProceedingJoinPoint joinPoint) throws Throwable {

		threadLocal.set(Object);
		//before
		Object obj = null;
        try {
            obj = joinPoint.proceed();
        } catch (Throwable throwable) {
            ...
        } finally{
			threadLocal.remove();
		}
		//after
        return obj;
	}
}

在请求方法中可以在任意地点获取存放的本地线程副本变量
```
## ThreadLocal注意事项
如果使用ThreadLocal的set方法之后，没有显示的调用remove方法，就有可能发生内存泄露，所以养成良好的编程习惯十分重要，使用完ThreadLocal之后，记得调用remove方法。

## ThreadLocal与Synchronized
ThreadLocal和Synchronized都是为了解决多线程中相同变量的访问冲突问题，不同的点是
- Synchronized是通过线程等待，牺牲时间来解决访问冲突
- ThreadLocal是通过每个线程单独一份存储空间，牺牲空间来解决冲突，并且相比于Synchronized，ThreadLocal具有线程隔离的效果，只有在线程内才能获取到对应的值，线程外则不能访问到想要的值。
