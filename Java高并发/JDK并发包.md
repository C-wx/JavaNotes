大家好，我是小菜，一个渴望在互联网行业做到蔡不菜的小菜。可柔可刚，点赞则柔，白嫖则刚！
**死鬼~看完记得给我来个三连哦！**![](https://user-gold-cdn.xitu.io/2020/4/11/17169c46045528af?w=240&h=224&f=jpeg&s=7529)

> 本文主要介绍 `JDK的那些并发包`
> 如有需要，可以参考
> 如有帮助，不忘 **点赞** ❥

为了更好地支持并发程序，JDK内部提供了大量实用的API和框架。

### 同步控制

说到同步控制，最先想到的便是`synchronized`关键字，这是一种最简单的控制方法， 它决定了一个线程是否可以访问临界区资源。配合`wait()`方法和`notify()`方法可以达到线程等待和通知的作用。而同步控制的另一种方式便是使用*重入锁*。**重入锁可以完全替代关键字synchronized**

*ReentrantLock使用示例*：

![](https://ae01.alicdn.com/kf/Hf7ea4a591eff488a99660942758c5f9fD.jpg)

使用重入锁可以保护临界区资源 `i`，确保多线程对 `i` 操作的安全性。与`synchronized`相比，重入锁有显示的操作过程。开发人员必须手动指定何时加锁，何时释放锁。也正是因为这样，重入锁对逻辑控制的灵活性要远远优于关键字`synchronized`。但要注意的是，在退出临界区时，必须记得释放锁，否则其他线程就没有机会再访问临界区了。

#### 中断响应

对于`synchronized`关键字来说，如果一个线程在等待锁，那么结果只有两种，要么它获得这把锁继续执行，要么它就继续保持等待。而使用重入锁，那么就多了一种可能，那就是等待的线程可以中断，让它停止等待。这种中断机制是很有必要的，它对于处理死锁是有一定帮助的。

![](https://ae01.alicdn.com/kf/H6c9f831f3070436d9d3d276a892ee2133.jpg)

上图是模拟一个死锁的状态，t1 线程开启后获得 lock1 锁，t2 线程开始后获得 lock2 锁，t1 线程等待 1 秒后想要获取 lock2 锁，t2 线程等待 1 秒后想要获取 lock1 锁，这样就会造成死锁。在第 58 行，我们通过 `t2.interrupt()` 发出中断标识，在第 35 行和 第 38 行通过 `isHeldByCurrentThread()`来对中断进行响应，就可以是 t1 线程获取到 lock2 锁，继续执行下去。最后真正完成工作的只有 t1，而 t2 线程则放弃其任务直接退出，释放资源。

*除了等待外部通知之外，要避免死锁还有一种方式就是`限时等待`*

![](https://ae01.alicdn.com/kf/Hc77a8c63d7c64cc9abcf76197298d1d1H.jpg)

上图中，`tryLock()`方法接收两个参数，一个表示等待时长，一个表示计时单位。在规定的等待时间中，如果还没得到锁，就会返回`flase`，如果成功获取锁就会返回`true`。这里设置了 3 秒等待时间，但是占用锁的线程持有锁的时间为 5 秒，因此第二个线程会请求锁失败。

`tryLock()`方法也可以不带参数直接运行，这种情况下，当前线程会尝试获得锁，如果锁并未被其他线程占用，则申请锁成功，并返回`true`，如果锁被其他线程占用，则当前锁不会进行等待，直接返回`flase`。

![](https://ae01.alicdn.com/kf/Ha135c6c8d4704a23941727b079d7421e3.jpg)

#### 公平锁

在大多数情况下，锁的申请都是非公平的。

`非公平锁`：A 线程请求到了 锁1，B 线程也来请求锁1，又来了一个 C 线程也来请求锁 1，这里虽然 B 线程比 C 线程早来一步请求，但是最终哪个线程请求到了锁1也是不一定的，以为是从线程等待队列中随机挑选一个。

`公平锁`：它会按照时间的先后顺序，保证先到者先得，后到者后得。它不会造成饥饿现象，只要你排队，最终还是可以拿到资源的。

如果我们使用`synchronized`关键字进行锁控制，那么产生的锁就是非公平的，而重入锁允许我们对其公平性进行设置。

```java
public ReentrantLock(boolean fair){}
```

当`fair`传入为`true`时，表示锁时公平的。但是公平锁需要维护一个**有序队列**，实现成本会比较高，性能会比较低下。默认情况下，锁是非公平的。

*公平锁使用示例*：![](https://ae01.alicdn.com/kf/H2737b760fb3f4b9f84e2f4c7056967b94.jpg)

在公平锁的情况下，得到的输出顺序是依次执行的。

*非公平锁使用示例*：![](https://ae01.alicdn.com/kf/H9f6cfd2bcba04233a1f288e5763533f0F.jpg)

可以看出，根据系统的调度，一个线程会倾向于再次获取已经持有的锁，这种分配的方式是高效的，但是无公平性而言。

*ReentrantLock的几个重要方法*：

- `lock()`：获得锁，如果锁被占用则等待
- `lockInterruptibly()`：获得锁，但优先响应中断
- `tryLock()`：尝试获得锁，如果成功则返回 true；否则返回 false；该方法不等待，立即返回
- `tryLock(long time, TimeUnit unit)`：在给定时间获得锁
- `unlock()`：释放锁

*重入锁的三个重要要素*：

- `原子状态`：原子状态使用CAS操作来存储当前锁的状态，判断锁是否已经被别的线程持有了
- `等待队列`：所有没有请求到锁的线程会进入等待队列等待。待有线程释放锁后，系统就能从等待队列中唤醒一个线程继续工作
- `阻塞源语 park() 和 unpark()`：用来挂起和恢复线程。没有得到锁的线程将会被挂起。

#### 重入锁的搭档（Condition）

`Condition`的作用和 `wait()`和`notify()`是大致相同的，但是`wait()`和`notify()`是配合`synchronized`关键字使用的，而`condition`是和重入锁相关联的。

*Condition 接口提供的基本方法*：

```java
void await() throws InterruptedException;   //使当前线程等待，同时释放锁。可以等待的时候响应中断
boolean await(long time, TimeUnit unit) throws InterruptedException;	//等待指定时间
void awaitUninterruptibly();				//与 await 相同，但是等待的时候不会响应中断
long awaitNanos(long nanosTimeout) throws InterruptedException;
boolean awaitUntil(Date deadline) throws InterruptedException;
void signal();		//唤醒一个等待中的线程
void signalAll();	//唤醒所有等待中的线程
```

*Condition 使用示例*：![](https://ae01.alicdn.com/kf/He86967f3e7664a5581864101f50471c1N.jpg)

*注意*：第30行我们需要释放锁，让 A 线程重新获取锁，不然虽然已经唤醒了 A 线程，但是因为它没有重新获取到锁，也就无法真正执行。

*ArrayBlockingQueue 的部分实现代码*：

![](https://ae01.alicdn.com/kf/He79c9d9cd0804f9bb04cc1b73a80b92aK.jpg)

#### 信号量（Semaphore）

信号量是为多线程提供了更为强大的控制方法。从广义上来讲，信号量是对锁的扩展。无论是内部锁`synchronized`还是重入锁`ReentrantLock`，一次都只允许一个线程访问一个资源，而信号量却可以指定多个线程，同时访问某一个资源。

*Semaphore构造方法*：

```java
public Semaphore(int permits)｛｝
public Semaphore(int permits, boolean fair)｛｝ //第二个参数用来控制是否是公平的
```

*Semaphore主要方法*：

```java
//尝试获取一个准入的许可，无法获得则会等待，直到有线程释放一个许可或线程被中断，这个方法可以响应中断
public void acquire() throws InterruptedException {}  
public void acquireUninterruptibly() {}		//与 acquire() 方法相同，但是不响应中断
public boolean tryAcquire() {}		//尝试获得一个许可，如果成功则返回true，失败则返回false，不会进行等待
public boolean tryAcquire(long timeout, TimeUnit unit) {}	//等待指定的时间
public void release() {}			//释放一个许可
```

*Semaphore使用示例*：![](https://ae01.alicdn.com/kf/H56267ce74eec480fa973619bad5d14bdF.jpg)

申请信号量使用`acquire()`方法操作，在离开时，务必使用`replease()`方法释放信号量。

####  读写锁（ReadWriteLock）

读写分离锁可以有效地帮助减少锁竞争，提高系统性能。比如：A1、A2、A3三个线程进行写操作，B1、B2、B3三个线程进行读操作，如果使用重入锁或者内部锁，那么所有读之间，读与写之间，写之间都是串行操作。但是因为读操作并不会造成数据的完整性破坏，因此这种等待是不合理的。

*读写锁的访问约束情况*：

|      |   读   |  写  |
| :--: | :----: | :--: |
|  读  | 非阻塞 | 阻塞 |
|  写  |  阻塞  | 阻塞 |

*读写锁使用示例*：

![](https://ae01.alicdn.com/kf/H7843374f9f7f4a45b2cbff115839dcb2a.jpg)

**读线程完全是并行的，写会阻塞读**

#### 倒计数器（CountDownLatch）

`CountDownLatch`是一个非常实用的多线程控制工具类。这个工具通常用来控制线程等待，它可以让一个线程等待直到倒计数结束，再开始执行。

*CountDownLatch构造器*：

```java
public CountDownLatch(int count) {} //count 表示这个计数器的计数个数
```

*CountDownLatch使用示例*：

![](https://ae01.alicdn.com/kf/H9a4ff114776d412f8d971740d77676c3w.jpg)

这里计数数量为*6*，每当一个线程完成任务后，倒计数器就会减*1*，使用`await()`方法，要求主线程等待所有任务都执行完成后才能执行。

#### 循环栅栏（CyclicBarrier）

`CyclicBarrier`是另外一种多线程并发控制工具。Cyclic 意为循环，也就是说这个计数器可以**反复使用**，它比`CountDownLatch`更加强大一点，它可以接收一个参数作为*barrierAction*，*barrierAction*就是当计数器一次计数完成后，系统会执行的动作。

*CyclicBarrier构造函数*：

```java
public CyclicBarrier(int parties, Runnable barrierAction) {} //parties 表示计数总数
```

*CyclicBarrier使用示例*：![](https://ae01.alicdn.com/kf/H565787cf70724a8a9da38bd70704ddd0m.jpg)

### 线程复用：线程池

与进程相比，线程是一种轻量级的工具。但是再怎么轻量，创建和销毁依然要花费时间，如果为每一个小任务都创建一个线程，就很可能**出现创建和销毁线程的时间比线程真实工作的时间还要久**，当线程数量过大时，反而**会耗尽CPU和内存资源**。而且，**线程本身也是要占用内存空间**，大量的线程会抢占宝贵的内存资源，如果处理不当，会导致`Out Of Memory`

*为了避免系统频繁创建和销毁线程，我们可以让创建的线程复用*，那么这个时候**线程池**就出现了，有点类似数据库的**连接池**，当系统需要使用数据库时，并不是创建一个新的连接，而是从连接池中获取一个连接即可，使用数据库连接池可以维护一些数据库连接，让它们长期保持在一个激活状态。**线程池也是类似的概念**。 

JDK中提供了一套`Executor`框架，帮助开发人员有效进行线程控制，其本质就是一个线程池。其中`ThreadPoolExecutor`表示一个线程池，`ThreadPoolExecutor`类实现了`Executor`接口，因此通过这个接口，任何`Runnable`的对象都可以被`ThreadPoolExecutor`线程池调度。

`Executor`框架的各种类型的线程池：

```java
public static ExecutorService newFixedThreadPool(int nThreads) {}
public static ExecutorService newCachedThreadPool() {}
public static ExecutorService newSingleThreadExecutor() {}
public static ScheduledExecutorService newSingleThreadScheduledExecutor() {}
public static ScheduledExecutorService newScheduledThreadPool(int corePoolSize) {}
public static ScheduledExecutorService newScheduledThreadPool(int corePoolSize) {}
```

- `newFixedThreadPool`：返回一个**固定线程数量**的线程池，该线程池中线程的数量始终不变。当有一个新的任务提交时，线程池中若有空闲线程，则立即执行。若没有，则新的任务会被暂存在一个任务队列中，待有线程空闲时，便处理任务队列中的任务。
- `newCachedThreadPool`：该方法返回一个**可根据实际情况调整线程数量**的线程池。线程池的线程数量不确定，若有空闲线程可以复用，则会优先使用可复用的线程。若所有线程均在工作，又有新的任务提交，则会创建新的线程处理任务，当所有线程在当前任务执行完毕后，将返回线程池复用。
- `newSingleThreadExecutor`：该方法返回一个**只有一个线程**的线程池。若有新的任务提交，便会保存在任务队列中，待线程空闲，按先入先出的顺序执行队列中的任务。

*newFixedThreadPool 使用示例*：![](https://ae01.alicdn.com/kf/Hd6cabb3fc6c8451abedb3bb932103b262.jpg)

其他几种类型的线程池使用类似

#### 线程池的内部实现

无论是`newFixedThreadPool`，`newCachedThreadPool`还是`newSingleThreadExecutor`，内部均使用了`ThreadPoolExecutor`类。

- *newFixedThreadPool*：

```java
public static ExecutorService newFixedThreadPool(int nThreads) {
    return new ThreadPoolExecutor(nThreads, nThreads,
                                  0L, TimeUnit.MILLISECONDS,
                                  new LinkedBlockingQueue<Runnable>());
}
```

- *newCachedThreadPool*：

```java
public static ExecutorService newCachedThreadPool() {
    return new ThreadPoolExecutor(0, Integer.MAX_VALUE,
                                  60L, TimeUnit.SECONDS,
                                  new SynchronousQueue<Runnable>());
}
```

- *newSingleThreadExecutor*：

```java
public static ExecutorService newSingleThreadExecutor() {
    return new FinalizableDelegatedExecutorService
        (new ThreadPoolExecutor(1, 1,
                                0L, TimeUnit.MILLISECONDS,
                                new LinkedBlockingQueue<Runnable>()));
}
```

*ThreadPoolExecutor*类的构造器：

```java
public ThreadPoolExecutor(int corePoolSize,
                          int maximumPoolSize,
                          long keepAliveTime,
                          TimeUnit unit,
                          BlockingQueue<Runnable> workQueue,
                          ThreadFactory threadFactory,
                          RejectedExecutionHandler handler) {}
```

- `corePoolSize`：线程池中的核心线程数

- `maximumPoolSize`：线程池的最大线程数

- `keepAliveTime`：多余线程的存活时间

- `unit`：存活时间的单位

- `workQueue`：任务队列，存放被提交但未被执行的任务

  - *SynchronousQueue*：直接提交队列。该队列**没有容量**，每一个插入操作都要等待一个删除操作，反之，每一个删除操作都要等待一个插入操作。如果没有空闲的线程，而且线程数也已经达到最大线程数，则会执行拒绝策略。
  - *ArrayBliockingQueue*：有界队列。可以带一个**容量大小参数**，表示该队列最大容量。有界队列仅当任务队列满时才可能将线程数提升到`maximumPoolSize`，若继续提交任务，则会执行拒绝策略。
  - *LinkedBlockingQueue*：无界队列。除非资源耗尽，否则无界的任务队列不存在任务入队失败的情况。
  - *PriorityBlockingQueue*：优先队列。是一个特殊的无界队列，可以根据任务自身的优先级顺序先后执行。

  ![](https://ae01.alicdn.com/kf/H2545988413bd44f8bb1760215c95646fA.jpg)

- `threadFactory`：线程工厂，用于创建线程，一般用默认的即可
- `handler`：拒绝策略，当任务太多来不及处理时，拒绝任务的策略
  - *AbortPolicy 策略*：直接抛出异常，阻止系统正常运行。
  - *CallerRunsPolicy 策略*：不会真正丢弃任务，在线程池未关闭的时候，运行当前被丢弃的任务。
  - *DiscardOldestPolicy 策略*：丢弃最老的一个任务，也就是即将被执行的一个任务，并尝试再次提交当前任务。
  - *DiscardPolicy 策略*：默默丢弃无法处理的任务，不予任何处理。

![看完不赞，都是坏蛋](https://imgconvert.csdnimg.cn/aHR0cHM6Ly93d3cuNTJkb3V0dS5jbi9zdGF0aWMvdGVtcC9waWMvOWJkNjhkMTUwZjA3ODdjNTYwYTQzOWRhMzU5YTU4MGEucG5n?x-oss-process=image/format,png#pic_center)

> 今天的你多努力一点，明天的你就能少说一句求人的话！
>
> *我是小菜，一个和你一起学习的男人。* `💋`