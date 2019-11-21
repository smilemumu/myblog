---
title: "CountDownLatch和CyclicBarrier以及join()"
date: 2019-10-12
tags: [多线程]
categories: [java]
draft: true
---
# CountDownLatch、CyclicBarrier、join()  #
一、CountDownLatch(倒计时器)：

- 功能：同步辅助类，也可以理解为倒计时锁，用于同步线程状态，允许一个或多个线程，等待其他一组线程完成操作，再继续往下执行。（会阻塞主线程）
- 特点：不可复用！
- 重要方法：
    - countDown()方法：计数器-1，每次线程执行完后调用；
	- await（）方法：等待方法，在需要阻塞的地方调用，当所有线程都执行完后，自动往下执行
- 用法：构造的时候指定一个计数器的值，每个线程执行完后就减1，直到为0再往下走。

二、CyclicBarrier（循环栅栏）：

- 功能：同步辅助类，功能和CountDownLatch类似，用于同步线程状态，允许一组线程相互之间等待，达到一个共同点，再继续执行。(不会阻塞主线程)
- 特点：可复用！当组内所有线程都到达某个执行点后，count参数会被重置，于是就可重用了。
- 重要方法：
    - await（）方法：当某个线程到达某个点（比如执行完某个任务）后调用该方法，就会等待其他线程，直到所有线程都到达这个点，再自动往下执行。
    - 还有个重载方法await（long timeOut,TimeUnit unit），用- 于当某个线程执行超过指定时间后还未到达某个点时，就会抛出异常，不再等待这个线程，并往下执行。
- 用法：构造的时候指定一个线程数量的值和到达某个点后执行的动作。

三、join()方法

- join方法也是管理线程状态同步的一个方法，和CountDownLatch和CyclicBarrier均由自身调用不同的是，join的调用者为当前线程，后面的线程必须等调用join的线程执行完后才能执行。


refer to:https://www.cnblogs.com/be-thinking/p/9292290.html
refer to:https://blog.csdn.net/qq_39241239/article/details/87030142