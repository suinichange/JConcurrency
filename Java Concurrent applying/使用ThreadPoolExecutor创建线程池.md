## 线程池的创建

ThreadPoolExecutor 构造器方法
```java
public ThreadPoolExecutor(
	【必填】int corePoolSize,
	【必填】int maximumPoolSize,
	【必填】long keepAliveTime,
	【必填】TimeUnit unit,
	【必填】BlockingQueue<Runnable> workQueue,
	【可选】ThreadFactory threadFactory,
	【可选】RejectedExecutionHandler handler) {}
```

### ThreadPoolExecutor 构造器参数

corePoolSize：线程池的基本大小

maximumPoolSize：线程池允许创建的最大线程数

keepAliveTime：线程池的工作线程空闲后，保持存活的时间

TimeUnit：线程活动保持时间的单位

workQueue：任务队列，用于保存等待执行的任务的阻塞队列

ThreadFactory：用于设置创建线程的工厂，可以通过线程工厂统一为创建的线程设置优先级，名字，是否为守护进程等参数  

RejectedExecutionHandler：饱和策略，当队列和线程池都满时，该采取什么策略处理提交的新任务<br/>

### ThreadPoolExecutor 参数解析
　　默认情况下，线程池中并没有任何线程，而是等待有任务到来才创建线程去执行任务，除非调用了prestartAllCoreThreads()方法，线程池才会提前创建并启动所有基本线程。

　　当新任务提交时:

　　若当前的 PoolSize < corePoolSize,创建新线程

　　若当前的 PoolSize = corePoolSize,将任务加入工作队列等待

　　若工作队列满了且 corePoolSize < maximumPoolSize,线程池会再创建新的线程执行任务。（因此若采用了无界队列，那么工作队列永远不会饱和，那么maximumPoolSize 这个参数就没什么意义了）

　　若工作队列满了且 corePoolSize = maximumPoolSize,ThreadPoolExecutor根据设置的饱和策略处理提交的新任务。

- AbortPolicy：默认策略，无法处理新任务时直接抛出异常
- CallerRunsPolicy：只用调用者所在线程来运行任务
- DiscardOldestPolicy：丢弃队列里最近的一个任务，并执行当前任务
- DiscardPolicy：不处理，丢弃掉

　　当然，也可以根据应用场景需要来实现RejectedExecutionHandler接口自定义策略。如记录日志或持久化存储不能处理的任务。

　　**阻塞队列常用种类**

　　ArrayBlockingQueue是一个基于数组结构的有界阻塞队列。
```java
    public ArrayBlockingQueue(
	【必填】int capacity //队列大小
	【可选】boolean fair //是否为公平锁，默认为false
	【可选】Collection<? extends E> c //始化时加入一个集合
	) 
```

　　LinkedBlockingQueue是一个基于链表结构的阻塞队列，吞吐量通常要高于ArrayBlockingQueue。默认请求队列长度为Integer.MAX_VALUE，可能会堆积大量的请求，从而导致 OOM。
```java
    public LinkedBlockingQueue(
	【可选】int capacity //队列大小，默认为 Integer.MAX_VALUE
	【可选】Collection<? extends E> c //始化时加入一个集合
	)
```

　　PriorityBlockingQueue是一个具有优先级的无限阻塞队列。
```java
    public PriorityBlockingQueue(
	【必填】int initialCapacity //初始容量，默认为11，超过自动扩容
	【可选】Comparator<? super E> comparator //定义优先级比较方式
	【可选】Collection<? extends E> c //始化时加入一个集合
	) 
```

　　SynchronousQueue是一个不存储元素的阻塞队列。每个插入操作必须等到另一个线程调用移除操作，否则插入操作一直处于阻塞状态，吞吐量通常要高于Linked-BlockingQueue。SynchronousQueue可以看成是一个传球手，负责把生产者线程处理的数据直接传递给消费者线程。队列本身并不存储任何元素，非常适合传递性场景。
```java
    public SynchronousQueue(
	【可选】boolean fair //是否为公平锁，默认为false
	)
```
　　DelayQueue是一个支持延时获取元素的无界阻塞队列。队列使用PriorityQueue来实现。队列中的元素必须实现Delayed接口，在创建元素时可以指定多久才能从队列中获取当前元素。只有在延迟期满时才能从队列中提取元素。
```java
    public DelayQueue(
	【可选】Collection<? extends E> c  //始化时加入一个集合
	) 
```
　　**keepAliveTime的使用**

　　keepAliveTime：线程池的工作线程空闲后，保持存活的时间。所以，
如果任务很多，并且每个任务执行的时间比较短，可以调大时间，提高线程的利用率。

　　时间的单位

- TimeUnit.DAYS（天）
- TimeUnit.HOURS（小时）
- TimeUnit.MINUTES（分钟）
- TimeUnit.SECONDS（秒）
- TimeUnit.MILLISECONDS（毫秒）
- TimeUnit.MICROSECONDS（微秒）
- TimeUnit.NANOSECONDS（纳秒，千分之一微秒）

　　参考书籍：《Java 并发编程实战》





