---
title: "Future、FutureTask、CompletableFuture"
date: 2019-10-11
tags: [多线程]
categories: [java]
draft: true
---

1. Future
```java
public interface Future<V> {

    /**
	 * 取消任务的执行
	 * 如果任务已经完成，或者已经被取消，或者因为其他原因不能被取消，则返回失败
	 * 如果任务在调用时还未启动，那么返回成功
	 * 如果任务已经在执行过程中，则根据参数确定此执行任务的线程能否被中断，来试图停止任务的执行
	 * @param mayInterruptIfRunning
	 * @return
	 */
    boolean cancel(boolean mayInterruptIfRunning);

	/**
     * 判断任务是否已经取消，任务正常完成前将其取消，则返回true
     * @return
     */
    boolean isCancelled();

     /**
     * 判断任务是否已经完成，需要注意的是如果任务正常、异常或取消，都返回true
     * @return
     */
    boolean isDone();
 	/**
     * 等待任务执行结束，并返回结果
     * @return
     * @throws InterruptedException  线程被中断异常
     * @throws ExecutionException 任务执行异常
     */
    V get() throws InterruptedException, ExecutionException;

   /**
     * 等待任务执行结束，并返回结果，同上面get方法的区别是设置了超时时间，
     * @param timeout
     * @param unit
     * @return
     * @throws InterruptedException
     * @throws ExecutionException
     * @throws TimeoutException
     */
    V get(long timeout, TimeUnit unit)
        throws InterruptedException, ExecutionException, TimeoutException;
}
```

2. FutureTask介绍  
Future只是一个接口，无法直接创建对象，因此有了FutureTask。RunnableFuture继承了Runnable和Future接口，而FutureTask实现了RunnableFuture接口。
Future是一个接口， FutureTask类是Future 的一个实现类，并实现了Runnable，因此FutureTask可以传递到线程对象Thread中新建一个线程执行。
FutureTask实现了RunnableFuture接口，而RunnableFuture继承了Runnable和Future，也就是说FutureTask既是Runnable，也是Future。
```java
public class FutureTask<V> implements RunnableFuture<V> {
	......
}
```

```java
public interface RunnableFuture<V> extends Runnable, Future<V> {
    void run();
}
```

3. CompletableFuture（组合式异步编程）
future存在局限性：Future很难直接表述多个Future 结果之间的依赖性，开发中，我们经常需要达成以下目的：
3.1 将两个异步计算合并为一个（这两个异步计算之间相互独立，同时第二个又依赖于第一个的结果）
3.2 等待 Future 集合中的所有任务都完成。
3.3 仅等待 Future 集合中最快结束的任务完成，并返回它的结果。
future的缺点也很明显:
	- （1）实现了异步获取执行结果的需求，但是没有提供通知，无法知道任务什么时候完成；
    - （2）get方法获取结果的时候，会进入等待阻塞状态，这时候又变成同步操作，如果使用isDone循环判断任务是否完成，会耗费cpu资源。

complatableFuture弥补了Future模式的缺点，在异步的任务完成后，需要用其结果继续操作时，无需等待。可以直接将异步处理的结果交给另外一个异步事件处理线程来处理。

CompletableFuture是一个静态辅助方法，以下四个静态方法用来为一段异步执行的代码创建CompletableFuture对象：
```java
	//使用ForkJoinPool.commonPool()作为它的线程池执行异步代码，异步操作有返回值
    public static <U> CompletableFuture<U> supplyAsync(Supplier<U> supplier) {
        return asyncSupplyStage(asyncPool, supplier);
    }
 
    //使用指定的thread pool执行异步代码，异步操作有返回值
    public static <U> CompletableFuture<U> supplyAsync(Supplier<U> supplier,Executor executor) {
        return asyncSupplyStage(screenExecutor(executor), supplier);
    }
 
    //使用ForkJoinPool.commonPool()作为他的线程池执行异步代码
    public static CompletableFuture<Void> runAsync(Runnable runnable) {
        return asyncRunStage(asyncPool, runnable);
    }
 
    //使用指定的thread pool执行异步代码
    public static CompletableFuture<Void> runAsync(Runnable runnable,Executor executor) {
        return asyncRunStage(screenExecutor(executor), runnable);
    }

	//demo
	//使用ForkJoinPool.commonPool()来执行
 	CompletableFuture<FileTransferResponseDto> responseDtoFuture = CompletableFuture.supplyAsync(()-> uploadFile(file,urn));
	//使用自定义线程池来执行
	CompletableFuture<FileTransferResponseDto> responseDtoFuture = CompletableFuture.supplyAsync(()-> uploadFile(file,urn),threadPoolExecutor);
```

CompletableFuture.complete方法：在执行future.get()方法时，如果执行的结果还没有出来，会一直处于阻塞的状态，这时候可以调用complete方法会立即执行，但是complete方法只会执行一次，对于重复调用只会获取到第一次的结果，同时如果future已经能够返回结果，那么调用complete方法也会无效
```java
public boolean complete(T value) {
        boolean triggered = completeValue(value);
        postComplete();
        return triggered;
}
```

CompletableFuture.thenApply:接受一个参数用来转换CompletableFuture，可以使用多个thenApply，但是这多个之间是串行执行的，下一个的执行依赖于上一个的结果

| 方法名 |  描述   |
|  ---   |   ---  |
| &lt;U&gt;CompletableFuture&lt;U&gt; thenApply(Function fn)                         |	接受一个Function参数用来转换成CompletableFuture|
| &lt;U&gt;CompletableFuture&lt;U&gt; thenApplyAsync(Function fn)                    |	接受一个Function参数用来转换成CompletableFuture，使用的是ForkJoinPool|
| &lt;U&gt;CompletableFuture&lt;U&gt; thenApplyAsync(Function fn, Executor executor) | 	接受一个Function参数用来转换成CompletableFuture，使用指定的线程池|

CompletableFuture.thenCompose:
CompletableFuture.thenComposeAsync:

CompletableFuture.thenCombine:
CompletableFuture.thenCombineAsync: