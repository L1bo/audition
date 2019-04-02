[TOC]

# 线程池，以及实现固定大小线程池底层是如何实现的？

讲了下四种线程池: 
1. 单一线程池
    SingleThreadExecutor
    只会用唯一的工作线程来执行任务，保证所有任务按照指定顺序(FIFO, LIFO, 优先级)执行
2. 固定大小线程池
    FixedThreadPool
    可控制线程最大并发数，超出的线程会在队列中等待
3. 缓存线程池
    CachedThreadPool
    如果线程池长度超过处理需要，可灵活回收空闲线程，若无可回收，则新建线程
4. 定时线程池
    ScheduledThreadPool
    支持定时及周期性任务执行

但是关于固定大小线程池底层是如何实现的，回答的不好，面试官直接问底层的源码是不是没看过，就说是的。面试官说没关系。。。

```java
public class ThreadPoolExecutor extends AbstractExecutorService {

    public ThreadPoolExecutor(int corePoolSize,
                                int maximumPoolSize,
                                long keepAliveTime,
                                TimeUnit unit, 
                                BlockingQueue<Runnable> workQueue);

    public ThreadPoolExecutor(int corePoolSize,
                                int maximumPoolSize,
                                long keepAliveTime,
                                TimeUnit unit,
                                BlockingQueue<Runnable> workQueue,
                                ThreadFactory threadFactory);

    public ThreadPoolExecutor(int corePoolSize,
                                int maximumPoolSize,
                                long keepAliveTime,
                                TimeUnit unit,
                                BlockingQueue<Runnable> workQueue,
                                RejectedExecutionHandler handler);

    public ThreadPoolExecutor(int corePoolSize,
                                int maximumPoolSize,
                                long keepAliveTime,
                                TimeUnit unit,
                                BlockingQueue<Runnable> workQueue,
                                ThreadFactory threadFactory,
                                RejectedExecutionHandler handler);

}
```
线程池底层都是通过
```java
ThreadPoolExecutor(int corePoolSize,
                    int maximumPoolSize,
                    long keepAliveTime,
                    TimeUnit unit,
                    BlockingQueue<Runnable> workQueue,
                    ThreadFactory threadFactory,
                    RejectedExecutionHandler handler)
```
来实现的。

- corePoolSize: 表示需要设置的线程个数
- maximumPoolSize: 线程池允许的最大线程个数
- keepAliveTime: 空闲线程存活的时间，超过这个时间就会被回收
- unit: 存活时间的单位
- workQueue: 需要执行的任务队列
- threadFactory: 线程工厂，用于创建线程，一般用默认的即可
- handler: 拒绝策略，当任务太多来不及处理，如何拒绝任务
    拒绝策略: 
    - 直接丢弃(DiscardPolicy)
    - 丢弃队列中最老的任务(DiscardOldestPolicy)
    - 抛异常(AbortPolicy)
    - 将任务分给调用线程来执行(CallerRunsPolicy)