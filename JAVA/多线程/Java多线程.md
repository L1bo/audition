[TOC]

## 并行和并发有什么区别？
- 并行：多个处理器或多核处理器同时处理多个任务。
- 并发：多个任务在同一个 CPU 核上，按细分的时间片轮流(交替)执行，从逻辑上来看那些任务是同时执行。

并发和并行
- 并发 = 两个队列和一台咖啡机。
- 并行 = 两个队列和两台咖啡机。

## 线程和进程的区别？
一个程序下至少有一个进程，一个进程下至少有一个线程，一个进程下也可以有多个线程来增加程序的执行速度。

## 守护线程是什么？
守护线程是运行在后台的一种特殊进程。它独立于控制终端并且周期性地执行某种任务或等待处理某些发生的事件。在 Java 中垃圾回收线程就是特殊的守护线程。

## 创建线程有哪几种方式？
创建线程有三种方式：
1. 继承 Thread 重新 run 方法；
2. 实现 Runnable 接口；
3. 实现 Callable 接口。

## 说一下 runnable 和 callable 有什么区别？
runnable 没有返回值，callable 可以拿到有返回值，callable 可以看作是 runnable 的补充。

## 线程有哪些状态？
线程的状态：
- NEW 尚未启动
- RUNNABLE 正在执行中
- BLOCKED 阻塞的（被同步锁或者IO锁阻塞）
- WAITING 永久等待状态
- TIMED_WAITING 等待指定的时间重新被唤醒的状态
- TERMINATED 执行完成

## sleep() 和 wait() 有什么区别？
- 类的不同：sleep() 来自 Thread，wait() 来自 Object。
- 释放锁：sleep() 不释放锁；wait() 释放锁。
- 用法不同：sleep() 时间到会自动恢复；wait() 可以使用 notify()/notifyAll()直接唤醒。

## notify()和 notifyAll()有什么区别？
notifyAll()会唤醒所有的线程，notify()之后唤醒一个线程。notifyAll() 调用后，会将全部线程由等待池移到锁池，然后参与锁的竞争，竞争成功则继续执行，如果不成功则留在锁池等待锁被释放后再次参与竞争。而 notify()只会唤醒一个线程，具体唤醒哪一个线程由虚拟机控制。

## 线程的 run() 和 start() 有什么区别？
start() 方法用于启动线程，run() 方法用于执行线程的运行时代码。run() 可以重复调用，而 start() 只能调用一次。

## 创建线程池有哪几种方式？
线程池创建有七种方式，最核心的是最后一种：
1. newSingleThreadExecutor()
   它的特点在于工作线程数目被限制为 1，操作一个无界的工作队列，所以它保证了所有任务的都是被顺序执行，最多会有一个任务处于活动状态，并且不允许使用者改动线程池实例，因此可以避免其改变线程数目；
2. newCachedThreadPool()
   它是一种用来处理大量短时间工作任务的线程池，具有几个鲜明特点：它会试图缓存线程并重用，当无缓存线程可用时，就会创建新的工作线程；如果线程闲置的时间超过 60 秒，则被终止并移出缓存；长时间闲置时，这种线程池，不会消耗什么资源。其内部使用 SynchronousQueue 作为工作队列；
3. newFixedThreadPool(int nThreads)
   重用指定数目（nThreads）的线程，其背后使用的是无界的工作队列，任何时候最多有 nThreads 个工作线程是活动的。这意味着，如果任务数量超过了活动队列数目，将在工作队列中等待空闲线程出现；如果有工作线程退出，将会有新的工作线程被创建，以补足指定的数目 nThreads；
4. newSingleThreadScheduledExecutor()
   创建单线程池，返回 ScheduledExecutorService，可以进行定时或周期性的工作调度；
5.  newScheduledThreadPool(int corePoolSize)
   和newSingleThreadScheduledExecutor()类似，创建的是个 ScheduledExecutorService，可以进行定时或周期性的工作调度，区别在于单一工作线程还是多个工作线程；
6.  newWorkStealingPool(int parallelism)
   这是一个经常被人忽略的线程池，Java 8 才加入这个创建方法，其内部会构建ForkJoinPool，利用Work-Stealing算法，并行地处理任务，不保证处理顺序；
7.  ThreadPoolExecutor()
   是最原始的线程池创建，上面1-3创建方式都是对ThreadPoolExecutor的封装。

## 线程池都有哪些状态？
- RUNNING：这是最正常的状态，接受新的任务，处理等待队列中的任务。
- SHUTDOWN：不接受新的任务提交，但是会继续处理等待队列中的任务。
- STOP：不接受新的任务提交，不再处理等待队列中的任务，中断正在执行任务的线程。
- TIDYING：所有的任务都销毁了，workCount 为 0，线程池的状态在转换为 TIDYING 状态时，会执行钩子方法 terminated()。
- TERMINATED：terminated()方法结束后，线程池的状态就会变成这个。

## 线程池中 submit() 和 execute() 方法有什么区别？
- execute()：只能执行 Runnable 类型的任务。
- submit()：可以执行 Runnable 和 Callable 类型的任务。
Callable 类型的任务可以获取执行的返回值，而 Runnable 执行无返回值。

## 在 Java 程序中怎么保证多线程的运行安全？
方法一：使用安全类，比如 Java. util. concurrent 下的类。
方法二：使用自动锁 synchronized。
方法三：使用手动锁 Lock。
手动锁
```java
Lock lock = new ReentrantLock();
lock. lock();
try {
    System. out. println("获得锁");
} catch (Exception e) {
    // TODO: handle exception
} finally {
    System. out. println("释放锁");
    lock. unlock();
}
```

## 多线程中 synchronized 锁升级的原理是什么？
synchronized 锁升级原理：在锁对象的对象头里面有一个 threadid 字段，在第一次访问的时候 threadid 为空，jvm 让其持有偏向锁，并将 threadid 设置为其线程 id，再次进入的时候会先判断 threadid 是否与其线程 id 一致，如果一致则可以直接使用此对象，如果不一致，则升级偏向锁为轻量级锁，通过自旋循环一定次数来获取锁，执行一定次数之后，如果还没有正常获取到要使用的对象，此时就会把锁从轻量级升级为重量级锁，此过程就构成了 synchronized 锁的升级。

锁的升级的目的：锁升级是为了减低了锁带来的性能消耗。在 Java 6 之后优化 synchronized 的实现方式，使用了偏向锁升级为轻量级锁再升级到重量级锁的方式，从而减低了锁带来的性能消耗。

## 什么是死锁？
当线程 A 持有独占锁a，并尝试去获取独占锁 b 的同时，线程 B 持有独占锁 b，并尝试获取独占锁 a 的情况下，就会发生 AB 两个线程由于互相持有对方需要的锁，而发生的阻塞现象，我们称为死锁。

## 怎么防止死锁？
尽量使用 tryLock(long timeout, TimeUnit unit)的方法(ReentrantLock、ReentrantReadWriteLock)，设置超时时间，超时可以退出防止死锁。
尽量使用 Java. util. concurrent 并发类代替自己手写锁。
尽量降低锁的使用粒度，尽量不要几个功能用同一把锁。
尽量减少同步的代码块。

## ThreadLocal 是什么？有哪些使用场景？
ThreadLocal 为每个使用该变量的线程提供独立的变量副本，所以每一个线程都可以独立地改变自己的副本，而不会影响其它线程所对应的副本。

ThreadLocal 的经典使用场景是数据库连接和 session 管理等。

## 说一下 synchronized 底层实现原理？
synchronized 是由一对 monitorenter/monitorexit 指令实现的，monitor 对象是同步的基本实现单元。在 Java 6 之前，monitor 的实现完全是依靠操作系统内部的互斥锁，因为需要进行用户态到内核态的切换，所以同步操作是一个无差别的重量级操作，性能也很低。但在 Java 6 的时候，Java 虚拟机 对此进行了大刀阔斧地改进，提供了三种不同的 monitor 实现，也就是常说的三种不同的锁：偏向锁（Biased Locking）、轻量级锁和重量级锁，大大改进了其性能。

## synchronized 和 volatile 的区别是什么？
volatile 是变量修饰符；synchronized 是修饰类、方法、代码段。
volatile 仅能实现变量的修改可见性，不能保证原子性；而 synchronized 则可以保证变量的修改可见性和原子性。
volatile 不会造成线程的阻塞；synchronized 可能会造成线程的阻塞。

## synchronized 和 Lock 有什么区别？
synchronized 可以给类、方法、代码块加锁；而 lock 只能给代码块加锁。
synchronized 不需要手动获取锁和释放锁，使用简单，发生异常会自动释放锁，不会造成死锁；而 lock 需要自己加锁和释放锁，如果使用不当没有 unLock()去释放锁就会造成死锁。
通过 Lock 可以知道有没有成功获取锁，而 synchronized 却无法办到。

## synchronized 和 ReentrantLock 区别是什么？
synchronized 早期的实现比较低效，对比 ReentrantLock，大多数场景性能都相差较大，但是在 Java 6 中对 synchronized 进行了非常多的改进。

主要区别如下：

ReentrantLock 使用起来比较灵活，但是必须有释放锁的配合动作；
ReentrantLock 必须手动获取与释放锁，而 synchronized 不需要手动释放和开启锁；
ReentrantLock 只适用于代码块锁，而 synchronized 可用于修饰方法、代码块等。
volatile 标记的变量不会被编译器优化；synchronized 标记的变量可以被编译器优化。

## 说一下 atomic 的原理？
atomic 主要利用 CAS (Compare And Wwap) 和 volatile 和 native 方法来保证原子操作，从而避免 synchronized 的高开销，执行效率大为提升。

# 线程池

## 线程池以及实现固定大小线程池底层是如何实现的？
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

## 线程池中任务有三种排队策略
1. 直接提交
    直接提交策略表示线程池不对任务进行缓存。
    新进任务直接提交给线程池，当线程池中没有空闲线程时，创建一个新的线程处理此任务。
    这种策略需要线程池具有无限增长的可能性。
    实现为：SynchronousQueue
2. 有界队列
    当线程池中线程达到corePoolSize时，新进任务被放在队列里排队等待处理。
    有界队列（如ArrayBlockingQueue）有助于防止资源耗尽，但是可能较难调整和控制。
    队列大小和最大池大小可能需要相互折衷：使用大型队列和小型池可以最大限度地降低 CPU 使用率、操作系统资源和上下文切换开销，但是可能导致人工降低吞吐量。如果任务频繁阻塞（例如，如果它们是 I/O 边界），则系统可能为超过您许可的更多线程安排时间。
    使用小型队列通常要求较大的池大小，CPU 使用率较高，但是可能遇到不可接受的调度开销，这样也会降低吞吐量。
3. 无界队列
    使用无界队列(例如，不具有预定义容量的 LinkedBlockingQueue)将导致在所有 corePoolSize 线程都忙时新任务在队列中等待。
    这样，创建的线程就不会超过 corePoolSize。
    (因此，maximumPoolSize 的值也就无效了。)当每个任务完全独立于其他任务，即任务执行互不影响时，适合于使用无界队列；
    例如，在 Web 页服务器中。这种排队可用于处理瞬态突发请求，当命令以超过队列所能处理的平均数连续到达时，此策略允许无界线程具有增长的可能性。

## 线程池的四种拒绝策略
当线程数量高于线程池的处理速度的时候，任务会被缓存到本地队列中，队列也有大小如果超过了这个大小就需要有拒绝策略，不然就会内存溢出

1. AbortPolicy: 直接抛出 java.util.concurrent.RejectedExecutionException 异常
    处理程序遭到拒绝将抛出运行时 RejectedExecutionException 
    ```java
    public void rejectedExecution(Runnable r, ThreadPoolExecutor e) {throw new RejectedExecutionException();} 
    ```
    这种策略直接抛出异常，丢弃任务。（jdk默认策略，队列满并线程满时直接拒绝添加新任务，并抛出异常，所以说有时候放弃也是一种勇气，为了保证后续任务的正常进行，丢弃一些也是可以接收的，记得做好记录）
2. CallerRunsPolicy: 主线程直接执行该任务，执行完之后尝试添加下一个任务到线程池中，这样可以有效降低向线程池中添加任务的速度
    线程调用运行该任务的 execute 本身。此策略提供简单的反馈控制机制，能够减缓新任务的提交速度。 
    ```java
    public void rejectedExecution(Runnable r, ThreadPoolExecutor e) { if (!e.isShutdown()) { r.run(); }} 
    ```
    这个策略显然不想放弃执行任务。但是由于池中已经没有任何资源了，那么就直接使用调用该execute的线程本身来执行。
3. DiscardPolicy: 丢弃当前任务本身,不能执行的任务将被删除
    ```java
    public void rejectedExecution(Runnable r, ThreadPoolExecutor e) {} 
    ```
    这种策略和AbortPolicy几乎一样，也是丢弃任务，只不过他不抛出异常。
4. DiscardOldestPolicy: 丢弃最老的,如果执行程序尚未关闭，则位于工作队列头部的任务将被删除，然后重试执行程序（如果再次失败，则重复此过程） 
    ```java
    public void rejectedExecution(Runnable r, ThreadPoolExecutor e) { if (!e.isShutdown()) {e.getQueue().poll();e.execute(r); }} 
    ```
    该策略就稍微复杂一些，在pool没有关闭的前提下首先丢掉缓存在队列中的最早的任务，然后重新尝试运行该任务。这个策略需要适当小心。
