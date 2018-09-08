　　Runnnable作为线程任务实现的基本形式之一，具有较大的局限性，它不能返回一个值或者抛出一个受检查的异常。<br/>
```java
public interface Runnable {
    public abstract void run();
}
```
　　但是许多任务实际上都是存在延迟计算的，比如数据库的查询，从网络获取资源，或者计算某个复杂功能。此时我们可以使用Callable。它认为主入口点（即 call）将返回一个值，并可能抛出异常。<br/>

```java
public interface Callable<V> {
    /**
     * Computes a result, or throws an exception if unable to do so.
     * @return computed result
     * @throws Exception if unable to compute a result
     */
    V call() throws Exception;
}
```
　　Future 表示一个任务的执行周期，并提供了相应的方法来判断任务是否已经完成或取消，以及获取任务的结果。<br/>

```java
public interface Future<V> {
    boolean cancel(boolean mayInterruptIfRunning);
    boolean isCancelled();
    boolean isDone();

    /**
     * Waits if necessary for the computation to complete, and then
     * retrieves its result.
     *
     * @return the computed result
     * @throws CancellationException if the computation was cancelled
     * @throws ExecutionException if the computation threw an
     * exception
     * @throws InterruptedException if the current thread was interrupted
     * while waiting
     */
    V get() throws InterruptedException, ExecutionException;

    V get(long timeout, TimeUnit unit)
        throws InterruptedException, ExecutionException, TimeoutException;
}
```

　　get（）方法的行为取决于任务的状态（尚未开始，正在运行，已完成）。

- 如果任务完成就返回结果。
- 如果任务没完成，那么get将阻塞直到任务完成。
- 如果任务抛出异常，那么get将抛出ExecutionException。
- 如果任务被取消，那么get将抛出CancellationException。





