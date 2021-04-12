---
title: "Condition"
date: 2020-12-24
tags: [多线程]
categories: [java]
draft: true
---


# Condition

## Condition概述
- 在线程的同步时可以使一个线程阻塞而等待一个信号，同时放弃锁使其他线程可以能竞争到锁。

- 在synchronized中我们可以使用Object的wait()和notify方法实现这种等待和唤醒。

- 在Lock可以实现相同的功能就是通过Condition。Condition中的**await()和signal()/signalAll()**就相当于Object的wait()和notify()/notifyAll()。

- 除此之外，Condition还是对多线程条件进行更精确的控制。notify()是唤醒一个线程，但它无法确认是唤醒哪一个线程。 但是，通过Condition，就能明确的指定唤醒读线程。

## 



参考：<https://www.cnblogs.com/qdhxhz/p/9206076.html>