## Executor 介绍

　　Executor接口的功能是处理提交的任务。它定义了一种标准的方式将任务提交和处理过程进行了解耦，并用Runnable来表示任务。
```java
public interface Executor {
    void execute(Runnable command);
}
```
　　通过实现Executor接口，我们可以灵活的执行多种任务处理策略。而当我们需要变更任务处理的策略时，只需要修改该接口的实现即可，而不需要大面积去修改我们的代码。例如我们可以立即执行任务：
```java
public  class DirectExecutor implements Executor {
    public void execute(Runnable r) {
        r.run();
    }
}
```
　　也可以通过创建新线程的方式去处理任务。
```java
public class ThreadPerTaskExecutor implements Executor {
    public void execute(Runnable r) {
        new Thread(r).start();
    }
}
```                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                         
　　我们已经知道如何创建一个Executor，那我们该如何管理Executor的生命周期呢？由于Excutor是以异步的方式，我们并不知道之前提交的任务的状态怎么样了。是已经完成，还是在运行，还是在队列中等待。而且，该如何关闭这些任务也是个问题。

　　因此Executor扩展了ExecutorService接口，并添加了一些用于生命周期的管理方法去管理Executor的生命周期。

## ExecutorService介绍和使用

```java
public interface ExecutorService extends Executor {

    void shutdown();

    List<Runnable> shutdownNow();

    boolean isShutdown();

    boolean isTerminated();

    boolean awaitTermination(long timeout, TimeUnit unit)
        throws InterruptedException;
	
    //用于任务提交的便利方法

    <T> Future<T> submit(Callable<T> task);

    Future<?> submit(Runnable task);
	
    //批量处理任务并返回所有任务的结果集

    <T> List<Future<T>> invokeAll(Collection<? extends Callable<T>> tasks)
        throws InterruptedException;

    <T> List<Future<T>> invokeAll(Collection<? extends Callable<T>> tasks,long timeout, TimeUnit unit)
        throws InterruptedException;

    //批量处理任务，返回第一个成功完成任务的结果。其它未完成的任务会被中断或者取消

    <T> T invokeAny(Collection<? extends Callable<T>> tasks)
        throws InterruptedException, ExecutionException;

    <T> T invokeAny(Collection<? extends Callable<T>> tasks,
        long timeout, TimeUnit unit)
        throws InterruptedException, ExecutionException, TimeoutException;
}
``` 
　　ExecutorService的生命周期有3种形态：运行，关闭，已终止

　　调用 shutdown() 方法：平缓的关闭

- 停止新任务提交。
- 等待正在运行和在队列中等待的任务执行完毕。
- 才会真正停止。

　　调用 shutdownNow() 方法：粗暴的关闭

- 尝试取消正在运行的任务
- 不再启动队列中未开始执行的任务
- 返回未执行的任务列表

　　调用 awaitTermination() 方法：从容准备关闭(线程阻塞)

- 直到等待正在运行和在队列中等待的任务执行完毕，返回 ture
- 若在规定时间未完成，返回 false
- 若还在执行被中断，抛出 InterruptedException

　　通常在调用 awaitTermination（） 方法 后会立即调用 shutdown（） 方法，从而产生同步关闭ExecutorService的效果。

　　参考书籍：《Java并发编程实战》

　　参考连接：[JDK1.8 API文档--java.util.concurrent](https://docs.oracle.com/javase/8/docs/api/index.html?java/util/concurrent/package-summary.html) 