---
title: "Synchronized关键字"
date: 2019-10-12
tags: [多线程]
categories: [java]
draft: true
---

# Synchronized关键字

## 1.作用
**保证同一时刻最多只有1个线程执行 被Synchronized修饰的方法 / 代码**  
其他线程 必须等待当前线程执行完该方法 / 代码块后才能执行该方法 / 代码块

## 2.修饰

### 2.1修饰方法

```java
//对象锁
//写法1
public synchronized void method()
{
   // todo
}
//写法2，写法2其实是同步代码块的写法，但在这里也是相当于修饰了方法
public void method()
{
   synchronized(this) {
      // todo
   }
}
```

### 2.2修饰静态方法
```java
//类锁
public synchronized static void method()
{
   // todo
}
```

### 2.3修饰类
```java
//类锁
class ClassName {
   public void method() {
      synchronized(ClassName.class) {
         // todo
      }
   }
}
```


### 2.4修饰代码块
```java
//对象锁
//写法一
synchronized(this) {
    //todo
}
//写法二
Object obj =new Object();
synchronized(obj) {
    //todo
}
```

### 3.总结
1.	当synchronized用来修饰静态方法或者类时，将会使得这个类的所有对象都是共享一把类锁，导致线程阻塞，所以这种写法一定要**规避**
2.  无论synchronized关键字加在方法上还是对象上，如果它作用的对象是非静态的，它取得的是对象锁；如果synchronized作用的对象是一个静态方法或一个类，该类所有对象持有同一把锁。
3.  每个对象只有一个锁（lock）与之相关联，谁拿到这个锁谁就可以运行它所控制的那段代码。
4.  实现同步是要很大的系统开销作为代价的，甚至可能造成死锁，所以尽量避免无谓的同步控制。

### 4.特别注意
Synchronized修饰方法时存在缺陷：若修饰1个大的方法，将会大大影响效率

### 5.特点
1. 保证原子性、可见性、有序性
2. 可重入
3. 重量级