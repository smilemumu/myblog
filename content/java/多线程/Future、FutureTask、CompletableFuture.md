---
title: "Future、FutureTask、CompletableFuture"
date: 2019-10-11
tags: [多线程]
categories: [java]
draft: true
---
# Future、FutureTask、CompletableFuture

## Future

### Feature接口

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

## FutureTask介绍  
- Future只是一个接口，无法直接创建对象，因此有了FutureTask。RunnableFuture继承了Runnable和Future接口，而FutureTask实现了RunnableFuture接口。
- Future是一个接口， FutureTask类是Future 的一个实现类，并实现了Runnable，因此FutureTask可以传递到线程对象Thread中新建一个线程执行。
- FutureTask实现了RunnableFuture接口，而RunnableFuture继承了Runnable和Future，也就是说FutureTask既是Runnable，也是Future。
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

## CompletableFuture（组合式异步编程）
- future存在局限性：Future很难直接表述多个Future 结果之间的依赖性，开发中，我们经常需要达成以下目的：
1. 将两个异步计算合并为一个（这两个异步计算之间相互独立，同时第二个又依赖于第一个的结果）
2. 等待 Future 集合中的所有任务都完成。
3. 仅等待 Future 集合中最快结束的任务完成，并返回它的结果。
- future的缺点也很明显:
1. 实现了异步获取执行结果的需求，但是没有提供通知，无法知道任务什么时候完成；
2. get方法获取结果的时候，会进入等待阻塞状态，这时候又变成同步操作，如果使用isDone循环判断任务是否完成，会耗费cpu资源。

- complatableFuture弥补了Future模式的缺点，在异步的任务完成后，需要用其结果继续操作时，无需等待。可以直接将异步处理的结果交给另外一个异步事件处理线程来处理。

- CompletableFuture是一个静态辅助方法，以下四个静态方法用来为一段异步执行的代码创建CompletableFuture对象：
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

*run方法不支持返回值。*
*supply可以支持返回值。*


- CompletableFuture.complete方法：
在执行future.get()方法时，如果执行的结果还没有出来，会一直处于阻塞的状态，这时候可以调用complete方法会立即执行，但是complete方法只会执行一次，对于重复调用只会获取到第一次的结果，同时如果future已经能够返回结果，那么调用complete方法也会无效
```java
public boolean complete(T value) {
        boolean triggered = completeValue(value);
        postComplete();
        return triggered;
}
```

### CompletableFuture处理方法
- 串行
**then** 直译【然后】，也就是表示下一步，所以通常是一种串行关系体现, then 后面的单词（比如 run /apply/accept）的方法的参数为对应的函数式接口，它的作用和那几个函数式接口的作用一样
```java
CompletableFuture<Void> thenRun(Runnable action)
    CompletableFuture<Void> thenRunAsync(Runnable action)
    CompletableFuture<Void> thenRunAsync(Runnable action, Executor executor)
      
    <U> CompletableFuture<U> thenApply(Function<? super T,? extends U> fn)
    <U> CompletableFuture<U> thenApplyAsync(Function<? super T,? extends U> fn)
    <U> CompletableFuture<U> thenApplyAsync(Function<? super T,? extends U> fn, Executor executor)
      
    CompletableFuture<Void> thenAccept(Consumer<? super T> action) 
    CompletableFuture<Void> thenAcceptAsync(Consumer<? super T> action)
    CompletableFuture<Void> thenAcceptAsync(Consumer<? super T> action, Executor executor)
      
    <U> CompletableFuture<U> thenCompose(Function<? super T, ? extends CompletionStage<U>> fn)  
    <U> CompletableFuture<U> thenComposeAsync(Function<? super T, ? extends CompletionStage<U>> fn)
    <U> CompletableFuture<U> thenComposeAsync(Function<? super T, ? extends CompletionStage<U>> fn, Executor executor)
```

- 并行
并行可理解成组合的聚合处理。

- 组合-and聚合
combine... with... 和 both...and... 都是要求两者都满足，也就是and关系
```java
<U,V> CompletableFuture<V> thenCombine(CompletionStage<? extends U> other, BiFunction<? super T,? super U,? extends V> fn)
    <U,V> CompletableFuture<V> thenCombineAsync(CompletionStage<? extends U> other, BiFunction<? super T,? super U,? extends V> fn)
    <U,V> CompletableFuture<V> thenCombineAsync(CompletionStage<? extends U> other, BiFunction<? super T,? super U,? extends V> fn, Executor executor)
     
    <U> CompletableFuture<Void> thenAcceptBoth(CompletionStage<? extends U> other, BiConsumer<? super T, ? super U> action)
    <U> CompletableFuture<Void> thenAcceptBothAsync(CompletionStage<? extends U> other, BiConsumer<? super T, ? super U> action)
    <U> CompletableFuture<Void> thenAcceptBothAsync( CompletionStage<? extends U> other, BiConsumer<? super T, ? super U> action, Executor executor)
      
    CompletableFuture<Void> runAfterBoth(CompletionStage<?> other, Runnable action)
    CompletableFuture<Void> runAfterBothAsync(CompletionStage<?> other, Runnable action)
    CompletableFuture<Void> runAfterBothAsync(CompletionStage<?> other, Runnable action, Executor executor)
```

- 组合-or聚合
Either...or... 表示两者中的一个，也就是 Or 的体现
```java
<U> CompletableFuture<U> applyToEither(CompletionStage<? extends T> other, Function<? super T, U> fn)
    <U> CompletableFuture<U> applyToEitherAsync(CompletionStage<? extends T> other, Function<? super T, U> fn)
    <U> CompletableFuture<U> applyToEitherAsync(CompletionStage<? extends T> other, Function<? super T, U> fn, Executor executor)
     
    CompletableFuture<Void> acceptEither(CompletionStage<? extends T> other, Consumer<? super T> action)
    CompletableFuture<Void> acceptEitherAsync(CompletionStage<? extends T> other, Consumer<? super T> action)
    CompletableFuture<Void> acceptEitherAsync(CompletionStage<? extends T> other, Consumer<? super T> action, Executor executor)
     
    CompletableFuture<Void> runAfterEither(CompletionStage<?> other, Runnable action)
    CompletableFuture<Void> runAfterEitherAsync(CompletionStage<?> other, Runnable action)
    CompletableFuture<Void> runAfterEitherAsync(CompletionStage<?> other, Runnable action, Executor executor)
```

- 异常处理
1. whenComplete 和 handle 的区别在于接受的参数函数式接口不同，前者使用Comsumer, 自然也就不会2有返回值；后者使用 Function，自然也就会有返回值
2. 另外exceptionally->try/catch , whenComplete/handle->try/finally
```java
CompletableFuture<T> exceptionally(Function<Throwable, ? extends T> fn)
    CompletableFuture<T> exceptionallyAsync(Function<Throwable, ? extends T> fn)
    CompletableFuture<T> exceptionallyAsync(Function<Throwable, ? extends T> fn, Executor executor)

    CompletableFuture<T> whenComplete(BiConsumer<? super T, ? super Throwable> action)
    CompletableFuture<T> whenCompleteAsync(BiConsumer<? super T, ? super Throwable> action)
    CompletableFuture<T> whenCompleteAsync(BiConsumer<? super T, ? super Throwable> action, Executor executor)

    <U> CompletableFuture<U> handle(BiFunction<? super T, Throwable, ? extends U> fn)
    <U> CompletableFuture<U> handleAsync(BiFunction<? super T, Throwable, ? extends U> fn)
    <U> CompletableFuture<U> handleAsync(BiFunction<? super T, Throwable, ? extends U> fn, Executor executor)
```

- DEMO
1. runAsync,入参为runnable,无结果
2. supplyAsync，异步处理，返回结果
3. thenAccept,串行后续处理上一异步结果，不返回结果
4. thenRun,串行不处理上一异步结果，不返回结果
5. thenApplyAsync为thenRun的异步变体
6. thenCompose，类似于lambda的flatMap，将结果“拍平”，将原本返回CompletableFuture<CompletableFuture<Double>>的结果返回为CompletableFuture<Double>
7. thenCombine,and组合俩个独立的结果,有返回CompletableFuture对象
8. allOf和anyOf
9. exceptionally,就相当于 catch，出现异常，将会跳过 thenApply 的后续操作，直接捕获异常，进行一场处理
10. handle,用多线程，良好的习惯是使用 try/finally 范式，handle 就可以起到 finally 的作用	








参考:<https://blog.csdn.net/Mr_Flouxetin/article/details/107485191>