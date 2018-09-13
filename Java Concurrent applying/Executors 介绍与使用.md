## Executors 介绍与使用　　
　　java.util.concurrent包提供了一个灵活的线程池以及一些有用的默认配置。我们可以通过调用Executors中一系列的静态工厂方法来创建各种线程池。
  
## newFixedThreadPool
　　newFixedThreadPool：创建固定长度 n 的线程池，允许创建的最大线程也为n。其采用LinkedBlockingQueue作为工作队列（无界队列，可能会大量堆积请求，导致OOM）。

　　FixedThreadPool适用于为了满足资源管理的需求，而需要限制当前线程数量的应用场景，它适用于负载比较重的服务器。
```java
    public static ExecutorService newFixedThreadPool(int nThreads) {
        return new ThreadPoolExecutor(nThreads, nThreads,
                                      0L, TimeUnit.MILLISECONDS,
                                      new LinkedBlockingQueue<Runnable>());
    }
```

## newSingleThreadExecutor
　　newSingleThreadExecutor：创建单个线程执行任务，如果这个线程异常结束，会创建另一个线程代替。该方法并不等价于newFixedThreadPool(1)，它无法确保当前线程异常结束时，能配置额外线程进行工作。

　　SingleThreadExecutor适用于需要保证顺序地执行各个任务；并且在任意时间点，不会有多个线程是活动的应用场景。
```java
    public static ExecutorService newSingleThreadExecutor() {
        return new FinalizableDelegatedExecutorService
            (new ThreadPoolExecutor(1, 1,
                                    0L, TimeUnit.MILLISECONDS,
                                    new LinkedBlockingQueue<Runnable>()));
    }
```

## newCachedThreadPool
　　newCachedThreadPool：其基本线程大小为0，允许最大线程数为Integer.MAX_VALUE，空闲线程的最大等待时间为60s。采用SynchronousQueue作工作队列，由于其并不存储元素，每个线程的操作都要等到另一个线程调用移除操作。若当前没有空闲线程，任务不会被加入队列，该线程池会创建新线程执行该任务，然后在使用完后迅速销毁。对于执行很多短期异步任务的程序而言，这种线程池的吞吐量，性能会高于使用LinkedBlockingQueue许多。（如果主线程提交任务的速度高于maximumPool中线程处理任务的速度时，CachedThreadPool会不断创建新线程，从而导致OOM）。

　　CachedThreadPool是大小无界的线程池，适用于执行很多的短期异步任务的小程序，或者是负载较轻的服务器。
```java
    public static ExecutorService newCachedThreadPool() {
        return new ThreadPoolExecutor(0, Integer.MAX_VALUE,
                                      60L, TimeUnit.SECONDS,
                                      new SynchronousQueue<Runnable>());
    }
```

## newScheduledThreadPool
　　newScheduledThreadPool：可以延迟或者定期执行任务的线程池。基本线程大小为 n，允许最大线程数为Integer.MAX_VALUE。采用DelayedWorkQueue进行实现，其中的对象只能在时间到期时被取走。

　　ScheduledThreadPoolExecutor适用于需要多个后台线程执行周期任务，同时为了满足资源管理的需求而需要限制后台线程的数量的应用场景。
```java
 	public static ScheduledExecutorService newScheduledThreadPool(int corePoolSize) {
        return new ScheduledThreadPoolExecutor(corePoolSize);
    }

	public ScheduledThreadPoolExecutor(int corePoolSize) {
        super(corePoolSize, Integer.MAX_VALUE, 0, TimeUnit.NANOSECONDS,
              new DelayedWorkQueue());
    }
```

## newSingleThreadScheduledExecutor
　　newSingleThreadScheduledExecutor：创建单个线程执行定期或延时任务。如果这个线程异常结束，会创建另一个线程代替。该方法并不等价于newScheduledThreadPool(1)，它无法确保当前线程异常结束时，能配置额外线程进行工作。

　　SingleThreadScheduledExecutor适用于需要单个后台线程执行周期任务，同时需要保证顺序地执行各个任务的应用场景。

```java
    public static ScheduledExecutorService newSingleThreadScheduledExecutor() {
        return new DelegatedScheduledExecutorService
            (new ScheduledThreadPoolExecutor(1));
    }

    public ScheduledThreadPoolExecutor(int corePoolSize) {
        super(corePoolSize, Integer.MAX_VALUE, 0, NANOSECONDS,
              new DelayedWorkQueue());
    }
```

　　参考书籍：《Java并发编程实战》
  
　　参考书籍：《Java 并发编程艺术》
  
　　参考书籍：《阿里巴巴 Java 开发手册》
