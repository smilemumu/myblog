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
## ThreadLocal解决什么问题？
- 并发问题:使用 ThreadLocal 代替 Synchronized 来保证线程安全,同步机制采用空间换时间 -> 仅仅先提供一份变量，各个线程轮流访问，后者每个线程都持有一份变量，访问时互不影响。
- 数据存储问题: ThreadLocal 为变量在每个线程中创建了一个副本，所以每个线程可以访问自己内部的副本变量。
## threadlocal在Spring中的使用->解决线程安全问题。
正常情况下，在Web中从接收请求到响应请求都应该属于同一个线程，而 ThreadLocal 是一个很好的机制，它为每个线程提供了一个独立的变量副本解决了变量并发访问的冲突问题，比如每个请求的用户信息。
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

## ThreadLocal与Synchronized区别
- 同步机制(synchronized关键字)采用了以“时间换空间”的方式，提供一份变量，让不同的线程排队访问。
- 而ThreadLocal采用了“以空间换时间”的方式，为每一个线程都提供一份变量的副本，从而实现同时访问而互不影响。



## ThreadLocal源码
```java
--set源码
public void set(T value) {
    //(1)获取当前线程（调用者线程）
    Thread t = Thread.currentThread();
    //(2)以当前线程作为key值，去查找对应的线程变量，找到对应的map
    ThreadLocalMap map = getMap(t);
    //(3)如果map不为null，就直接添加本地变量，key为当前线程，值为添加的本地变量值
    if (map != null)
        map.set(this, value);
    //(4)如果map为null，说明首次添加，需要首先创建出对应的map
    else
        createMap(t, value);
}

ThreadLocalMap getMap(Thread t) {
    return t.threadLocals; //获取线程自己的变量threadLocals，并绑定到当前调用线程的成员变量threadLocals上
}

void createMap(Thread t, T firstValue) {
     t.threadLocals = new ThreadLocalMap(this, firstValue);
}

--get源码
public T get() {
    //(1)获取当前线程
    Thread t = Thread.currentThread();
    //(2)获取当前线程的threadLocals变量
    ThreadLocalMap map = getMap(t);
    //(3)如果threadLocals变量不为null，就可以在map中查找到本地变量的值
    if (map != null) {
        ThreadLocalMap.Entry e = map.getEntry(this);
        if (e != null) {
            @SuppressWarnings("unchecked")
            T result = (T)e.value;
            return result;
        }
    }
    //(4)执行到此处，threadLocals为null，调用该更改初始化当前线程的threadLocals变量
    return setInitialValue();
}

private T setInitialValue() {
    //protected T initialValue() {return null;}
    T value = initialValue();
    //获取当前线程
    Thread t = Thread.currentThread();
    //以当前线程作为key值，去查找对应的线程变量，找到对应的map
    ThreadLocalMap map = getMap(t);
    //如果map不为null，就直接添加本地变量，key为当前线程，值为添加的本地变量值
    if (map != null)
        map.set(this, value);
    //如果map为null，说明首次添加，需要首先创建出对应的map
    else
        createMap(t, value);
    return value;
}

--remove实现
public void remove() {
    //获取当前线程绑定的threadLocals
     ThreadLocalMap m = getMap(Thread.currentThread());
     //如果map不为null，就移除当前线程中指定ThreadLocal实例的本地变量
     if (m != null)
         m.remove(this);
 }
```

## ThreadLocalMap
ThreadLocalMap 其内部利用 Entry 来实现 key-value 的存储，类似 HashMap 的结构 如下代码，从上面代码中可以看出 Entry 的 key 就是 ThreadLocal ，而value 就是值。同时，`Entry 也继承WeakReference` ，所以说Entry所对应key（ThreadLocal实例）的引用为一个弱引用,弱引用相关的在总结里有。
```java
static class Entry extends WeakReference<ThreadLocal<?>> {
     /** The value associated with this ThreadLocal. */
     Object value;

     Entry(ThreadLocal<?> k, Object v) {
         super(k);
         value = v;
     }
 }
```

## 如果不执行remove为什么会出现内存泄漏？
ThreadLocalMap内部实际上是一个Entry数组,而Entry的key使用的是ThreadLocal对象的弱引用（Entry类继承WeakReference<ThreadLocal<?>>类），在没有其他地方对ThreadLoca依赖，ThreadLocalMap中的ThreadLocal对象就会被回收掉，但是对应的value不会被回收，这个时候Map中就可能存在key为null但是value不为null的项，这需要实际的时候使用完毕及时调用remove方法避免内存泄漏。

>强引用：Java中默认的引用类型，一个对象如果具有强引用那么只要这种引用还存在就不会被GC。
>软引用：简言之，如果一个对象具有弱引用，在JVM发生OOM之前（即内存充足够使用），是不会GC这个对象的；只有到JVM内存不足的时候才会GC掉这个对象。
>弱引用（这里讨论ThreadLocalMap中的Entry类的重点）：如果一个对象只具有弱引用，那么这个对象就会被垃圾回收器GC掉(被弱引用所引用的对象只能生存到下一次GC之前，当发生GC时候，无论当前内存是否足够，弱引用所引用的对象都会被回收掉)。

## ThreadLocal原理总结
- 每个Thread维护着一个ThreadLocalMap的引用
- ThreadLocalMap是ThreadLocal的内部类，用Entry来进行存储
- 调用ThreadLocal的set()方法时，实际上就是往ThreadLocalMap设置值，key是ThreadLocal对象，值是传递进来的对象
- 调用ThreadLocal的get()方法时，实际上就是往ThreadLocalMap获取值，key是ThreadLocal对象
- ThreadLocal本身并不存储值，它只是作为一个key来让线程从ThreadLocalMap获取value。

正是因为这几点,所以能够实现数据隔离，获取当前线程的局部变量值，和其它线程无关。



参考：<https://www.jianshu.com/p/e200e96a41a0>