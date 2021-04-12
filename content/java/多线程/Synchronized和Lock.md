---
title: "Synchronized和Lock"
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
'
//写法二
Object obj =new Object();
synchronized(obj) {
    //todo
}
```
### 2.5底层实现 
- **synchronized 同步代码块**的实现使用的是 **monitorenter 和 monitorexit** 指令，其中 monitorenter 指令指向同步代码块的开始位置，monitorexit 指令则指明同步代码块的结束位置。

- **synchronized 修饰的方法**并没有 monitorenter 指令和 monitorexit 指令，取得代之的确实是 **ACC_SYNCHRONIZED** 标识，该标识指明了该方法是一个同步方法。

### 2.6可重入锁
- AQS通过控制status状态来判断锁的状态，对于非可重入锁状态不是0则去阻塞；对于可重入锁如果是0则执行，非0则判断当前线程是否是获取到这个锁的线程，是的话把status状态＋1，释放的时候，只有status为0，才将锁释放。


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

# Lock关键字
## Lock和synchronized有以下几点不同
- 1）Lock是一个接口，而synchronized是Java中的关键字，synchronized是内置的语言实现；
- 2）synchronized会自动释放线程占有的锁，而Lock需要主动通过unLock()去释放锁，否则很可能造成死锁现象。
- 3）通过Lock可以知道有没有成功获取锁，而synchronized却无法办到。
- 4）Lock可以提高多个线程进行读操作的效率。

>在性能上来说，如果竞争资源不激烈，两者的性能是差不多的，而当竞争资源非常激烈时（即有大量线程同时竞争），此时Lock的性能要远远优于synchronized。所以说，在具体使用时要根据适当情况。

## Lock接口
```java
public interface Lock {
    void lock();
    void lockInterruptibly() throws InterruptedException;
    boolean tryLock();
    boolean tryLock(long time, TimeUnit unit) throws InterruptedException;
    void unlock();
    Condition newCondition();
}
```
```lock用法
Lock lock = ...;
lock.lock();
try{
    //处理任务
}catch(Exception ex){
     
}finally{
    lock.unlock();   //释放锁
}
----------
Lock lock = ...;
if(lock.tryLock()) {
     try{
         //处理任务
     }catch(Exception ex){
         
     }finally{
         lock.unlock();   //释放锁
     } 
}else {
    //如果不能获取锁，则直接做其他事情
}
--------------
public void method() throws InterruptedException {
   //调用它时需要主动抛出异常，如果获得锁就执行，如果锁已经被其它线程得到，那就抛InterruptedException异常
    lock.lockInterruptibly();
    try {  
     //.....
    }
    finally {
        lock.unlock();
    }  
}
```

## ReentrantLock
ReentrantLock，意思是“可重入锁”。ReentrantLock是唯一实现了Lock接口的类

## ReadWriteLock
ReadWriteLock也是一个接口
```
public interface ReadWriteLock {
    /**
     * Returns the lock used for reading.
     */
    Lock readLock();
 
    /**
     * Returns the lock used for writing.
     */
    Lock writeLock();
}
```

## 锁的相关概念
- 可重入锁  
可以重复获取已获得的锁
- 可中断锁
可以中断自己获取锁的过程
- 公平锁   
尽量以请求锁的顺序来获取锁
synchronized就是非公平锁，它无法保证等待的线程获取锁的顺序。
而对于ReentrantLock和ReentrantReadWriteLock，它默认情况下是非公平锁，但是可以设置为公平锁。
- 读写锁
读写锁将对一个资源（比如文件）的访问分成了2个锁，一个读锁和一个写锁。
