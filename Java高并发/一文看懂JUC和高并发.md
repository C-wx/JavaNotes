大家好，我是小菜，一个渴望在互联网行业做到蔡不菜的小菜。可柔可刚，点赞则柔，白嫖则刚！
**死鬼~看完记得给我来个三连哦！**


![](https://user-gold-cdn.xitu.io/2020/4/11/17169c46045528af?w=240&h=224&f=jpeg&s=7529)


>本文主要介绍 `并发入门`
>
>如有需要，可以参考
>
>如有帮助，不忘 **点赞** ❥
>
>
>微信公众号已开启，**小菜良记**，没关注的同学们记得关注哦！

### 一、Volatile

*volatile 是Java虚拟机提供的轻量级的同步机制*

#### 1）保证可见性

`JMM模型的线程工作：`
各个线程对主内存中共享变量X的操作都是各个线程各自拷贝到自己的工作内存操作后再协会主内存中。

`存在的问题：`
如果一个线程A 修改了共享变量X的值还未写回主内存，这是另外一个线程B又对内存中的一个共享变量X进行操作，但是此时线程A工作内存中的共享变量对线程B来说事并不可见的。这种工作内存与主内存延迟的现象就会造成了可见性的问题。

`解决（volatile）：`
当多个线程访问同一个变量时，一个线程修改了这个变量的值，其他线程能够立即看到修改的值

![](https://cbuc.top/1604146143822.png)

#### 2）不保证原子性

`原子性：` 
不可分割、完整性，即某个线程正在做某个具体业务时，中间不可以被加塞或者被分割，需要整体完整，要么同时成功，要么同时失败

`解决方法：` 

- **加入synchronized**

- **使用JUC下的AtomicInteger**

![](https://ae01.alicdn.com/kf/H4aff1b2c93814ba5abe139293a37b4adz.jpg)

#### 3）禁止指令重排

`指令重排：` 
多线程环境中线程交替执行，由于编译器优化重排的存在，两个线程中使用的变量能否保证一致性是无法确定的，结果无法预测。

`指令重排过程：`
源代码 -> 编辑器优化的重排 -> 指令并行的重排 -> 内存系统的重排 ->最终执行的指令

`内存屏障作用:` 

- 保证特定操作的执行顺序
- 保证某些变量的内存可见性（利用该特性实现volatile的内存可见性）

### 二、CAS
#### 1）什么是CAS

- CAS 全称： Compare-And-Set , **它是一条CPU并发源语**

- 它的功能就是*判断内存某个位置的值是否为预期值，如果是则更新为新的值*，这个过程是**原子**的。

- CAS并发源语体现在Java语言中就是*sun.miscUnSafe*类中的各个方法，调用UnSafe类中的CAS方法，JVM会帮我实现CAS汇编指令，这是一种完全依赖于`硬件`功能，通过它实现了原子操作，再次强调，由于CAS是一种系统源语，源语属于操作系统用于范畴，是由若干个指令组成，用于完成某个功能的一个过程，并且源语的执行必须是连续的，在**执行过程中不允许中断，也即是说CAS是一条原子指令，不会造成所谓的数据不一致的问题**

```java
public class CASDemo{
	public static void main(String[] args) {
        AtomicInteger atomicInteger = new AtomicInteger();
        System.out.println(atomicInteger.compareAndSet(0,5));       //true
        System.out.println(atomicInteger.compareAndSet(0,2));       //false
        System.out.println(atomicInteger);                          //5
    }
}
```
#### 2）CAS原理

```java
	public final int getAndIncrement() {
        return unsafe.getAndAddInt(this, valueOffset, 1); //引出问题 --> 何为unsafe
    }
```
#### 3）何为UnSafe

- UnSafe是*CAS的核心类*，由于Java方法无法直接访问底层，需要通过本地（native）方法来访问，UnSafe相当于一个后面，基于该类可以直接操作额定的内存数据。UnSafe类在于*sun.misc*包中。其中内部方法可以向C的指针一样直接操作内存，因为Java中CAS操作的主要依赖于UnSafe类的方法

- 变量 **ValueOffset** ， 是该变量在内存中偏移地址，因为*UnSafe就是根据内存偏移地址来获取数据的*。

- 变量 value 由 **volatile** 修饰，保证了多线程之间的可见性。

#### 4）CAS缺点

1. **循环时间开销很大**

![](https://ae01.alicdn.com/kf/H8d082a94d76f492d920ff6e099a3485d4.jpg)

2. **只能保证一个共享变量的原子性**
当对一个共享变量执行操作的时候，我们可以使用循环CAS的方式来保证原子操作，但是对多个共享变量操作时，循环CAS就无法保证操作的原子性，这个时候就可以用锁来保证原子性。
3. **存在ABA问题**
#### 5）ABA问题

*何为ABA问题*：
在一个时间差的时段内会造成数据的变化。比如说一个线程AA从内存中取走A，这个时候另一个线程BB也从内存中取走A，这个时候A的值为X，然后线程BB将A的值改为Y，过一会又将A的值改为X，这个时候线程AA回来进行CAS操作发现内存中A的值仍然是X，因此线程AA操作成功。**但是尽管线程AA的CAS操作成功，但是不代表这个过程就是没问题的**

*原子引用*：

![](https://ae01.alicdn.com/kf/H7f9969d4269140dd823a8511e3be618bF.jpg)

*解决*：（**时间戳原子引用：`AtomicStampedReference`**）

![](https://cbuc.top/1604146185114.png)

### 三、集合类不安全问题

#### 1）故障现象

出现`java.util.ConcurrentModificationException`异常
![](https://user-gold-cdn.xitu.io/2020/4/5/1714a475d714129e?w=834&h=183&f=png&s=9658)

#### 2) 导致原因

**并发争抢修改导致**

```java
public static void main(String[] args) {
    List<String> stringList = new ArrayList<>();
    for (int i = 0; i < 30; i++) {
        new Thread(()->{
            stringList.add(UUID.randomUUID().toString().substring(0,8));
            System.out.println(stringList);
        },"线程"+i).start();
    }
}
```
#### 3）解决方法

- **Vector** ：线程安全
- **Collections.synchronizedList(new ArrayList<>())**
-  **new CopyOnWriteArrayList<>()**
   + List线程：`new CopyOnWriteArrayList<>();`
   + Set线程：`new CopyOnWriteArraySet<>();`
   + Set线程：`ConcurrentHashMap();`
### 四、锁

#### 1）公平锁/非公平锁

*定义*：

**公平锁：** 是指多个线程按照申请锁的顺序来获取锁，类似于排队，FIFO规则
**非公平锁：** 是指在多线程获取锁的顺序并不是按照申请锁的顺序，有可能后申请的线程比先申请的线程优先获到锁，在高并发的情况下，有可能造成优先级反转或者饥饿现象。

*两者的区别*：

>并发包ReentrantLock的创建可以指定函数的boolean类型来得到公平锁或者非公平锁，默认是非公平锁

**公平锁：** 就是很公平，在并发环境中，每个线程在获取锁时会先查看此锁维护的等待队列，如果为空，或者当前线程是等待队列的第一个，就占有锁，否则就会加入到等待队列中，以后会按照FIFO的规则从队列中抽取到自己。
**非公平锁：** 非公平锁比较粗鲁，上来就直接尝试占有锁，如果尝试失败，就再采用类似公平锁的那种方式。

*就 Java ReentrantLock 而言，通过构造函数指定该锁是否是公平锁， 默认 **非公平锁** ，非公平锁的优点在于吞吐量比公平锁大，就 synchronized 而言，它是一种非公平锁。*

#### 2）可重入锁（递归锁）
可重入锁也称之为`递归锁`，指定是同一个线程外层函数获得锁之后，内层递归函数仍然能获取该锁的代码，在同一个线程在外层方法获取锁的时候，在进入内层方法会自动获取锁。也就是说， **线程可以进入任何一个它已经拥有的锁所同步着的代码块**

`ReentrantLock 和 syschronized` 就是一个典型的可重入锁

***ReentrantLock** 举例*：![](https://ae01.alicdn.com/kf/H3d03dd6933194272b102b632fe309c49Q.jpg)

***syschronized** 举例*：![](https://ae01.alicdn.com/kf/He82fcabd6a9846b9bf10b1ab40a08887V.jpg)

#### 3）自旋锁
是指尝试获取锁的线程不会立即阻塞，而是采用**循环**的方式去尝试获取锁，这样的好处是减少线程上下文切换的消耗，缺点是循环会消耗CPU。

*上面的CAS问题中的unsafe 用到的就是自旋锁。*

```java
public final int getAndAddInt(Object var1, long var2, int var4) {
    int var5;
    do {
        var5 = this.getIntVolatile(var1, var2);
    } while(!this.compareAndSwapInt(var1, var2, var5, var5 + var4));
    return var5;
}
```
*例子*：![](https://ae01.alicdn.com/kf/H4ea5ea30e44d40ad9adad21ad1b5f7127.jpg)

#### 4）独占锁(写)/共享锁(读)/互斥锁
- **独占锁：**  指该锁一次只能被一个线程所持有。对ReentrantLock和Synchronize而言都是独占锁。
- **共享锁：** 指该锁可被多个线程所持有。

*对ReentrantReadWriteLock而言，其读锁是共享锁，其写锁是独占锁。读锁的共享锁可以保证并发度是非常高效的。读写，写读，写写的过程是互斥的。*

*例子*：

![](https://cbuc.top/1604146253764.png)

#### 5）CountDownLatch
- 让一些线程阻塞直到另外一些线程完成后才别唤醒

- CountDownLatch主要有两个方法，当一个或多个线程调用**await** 方法时，调用线程会被阻塞，其他线程调用**countDown** 方法计数器减1（调用**countDown** 方法时线程不会阻塞），当计数器的值变为0，因调用**await** 方法被阻塞的线程会被唤醒，进而继续执行。

  *关键方法*：
  1）**await()**  方法 
  2） **countDown()**  方法

  *例子*：

  一个教室有1个班长和若干个学生，班长要等所有学生都走了才能关门，那么要如何实现。

![](https://ae01.alicdn.com/kf/H68dee3e70d67446eb902c2811f149c27N.jpg)

#### 6）CyclicBarrier
- CyclicBarrier 的字面意思是可循环(Cyclic)使用的屏障(Barrier)。它要做的事情是，让一组线程到达一个屏障（也可以叫做同步点）时被阻塞，知道最后一个线程到达屏障时，屏障才会开门，所有被屏障拦截的线程才会继续干活，线程进入屏障通过CyclicBarrier的await()方法。

  *例子*：

  跟上面一样，一个班级有六个学生，要等学生都离开后班长才能关门。

![](https://cbuc.top/1604146297114.png)

*CountDownLatch 和 CyclicBarrier 其实是相反的操作，一个是相减到0开始执行，一个是相加到指定值开始执行*

#### 7）Semaphore
- 信号量的主要用户两个目的，一个是用于**共享资源的相互排斥使用** ，另一个是用于**并发资源数的控制**。
- 例子：抢车位问题，此时有六部车辆，但是只有三个车位的问题。

![](https://cbuc.top/1604146324834.png)

### 五、阻塞队列

`概念:` 阻塞队列，拆分为“阻塞”和“队列”，所谓阻塞，在多线程领域，某些情况下会刮起线程（即线程阻塞），一旦条件满足，被挂起的线程优先被自动唤醒。
![](https://user-gold-cdn.xitu.io/2020/4/5/1714a475d7e0c027?w=461&h=176&f=png&s=54561)**Tread 1 往阻塞队列中添加元素，Thread 2 往阻塞队列中移除元素**

1. 当阻塞队列是空时，从队列中**获取**元素的操作将会被阻塞。
 2. 当阻塞队列是满时，从队列中**添加**元素的操作将会被阻塞。
#### 1） 种类
1. **ArrayBlockingQueue：** 是一个基于**数组结构** 的有界阻塞队列，此队列按照FIFO（先进先出）规则排序。
2. **LinkedBlockingQueue：** 是一个基于**链表结构**的有界阻塞队列（大小默认值为Integer.MAX_VALUE），此队列按照FIFO（先进先出）对元素进行排序，吞吐量通常要高于ArrayBlockingQueue。
3. **SynchronusQueue：** 是一个**不储存元素**的阻塞队列，每个插入操作必须等到另一个线程调用移除操作，否则插入操作一直处于阻塞状态，吞吐量通常要高于LinkedBlockingQueue。
4. PriorityBlockingQueue：支持优先级排序的无界阻塞队列
5. DelayQueue：使用优先级队列实现的延迟无界阻塞队列。
6. LinkedTransferQueue：由链表结构组成的无界阻塞队列。

*吞吐量*：`SynchronusQueue` > `LinkedBlockingQueue` > `ArrayBlockingQueue`

#### 2） 使用好处

我们不需要关心什么时候胡需要阻塞线程，什么时候需要唤醒线程，因为BlockingQueure都一手给你办好了。在concurrent包，发布以前，在多线程环境下，**我们必须自己去控制这些细节，尤其还要兼顾效率和线程安全，** 而这会给我们的程序带来不小的复杂度

#### 3） 核心方法
| 方法类型 | 抛异常    | 特殊值   | 阻塞   | 超时                        |
| -------- | --------- | -------- | ------ | --------------------------- |
| 插入方法 | add(o)    | offer(o) | put(o) | offer(o, timeout, timeunit) |
| 移除方法 | remove(o) | poll()   | take() | poll(timeout, timeunit)     |
| 检查方法 | element() | peek() | 不可用|不可用

- 抛异常：如果操作不能马上进行，则抛出异常
- 特殊值：如果操作不能马上进行，将会返回一个特殊的值，一般是 true 或者 false
- 一直阻塞：如果操作不能马上进行，操作会被阻塞
- 超时退出：如果操作不能马上进行，操作会被阻塞指定的时间，如果指定时间没执行，则返回一个特殊值，一般是 true 或者 false
#### 4）用处
- 生产者消费者模式
- 线程池
- 消息中间件

*生产者消费者模式--传统版*：

![](https://cbuc.top/1604146356927.png)

*生产者消费者模式--阻塞队列版*：

![](https://ae01.alicdn.com/kf/H177cf083c6724a028d3aa2de1527b466z.jpg)

### 六、线程池
`概念：` 线程池做的工作主要是控制运行的线程的数量，**处理过程中将任务加入队列**，然后在线程创建后启动这些任务，**如果线程超过了最大数量，超出的线程将排队等候**，等其他线程执行完毕，再从队列中取出任务来执行。
`特点：` 

- 线程复用
- 控制最大并发数
- 管理线程

`优点：`

- 降低资源消耗，通过重复利用自己创建的线程减低线程创建和销毁造成的消耗。
- 提高响应速度，当任务到达时，任务可不需要等到线程创建就能立即执行。
- 提高线程的可管理性，线程是稀缺西苑，如果无限制的创建，不仅会消耗系统资源，还会降低体统的稳定性，使用线程可以进行统一分配，调优和监控。

#### 1）线程创建几种方法
1）*继承Thead*

```java
class ThreadDemo extends Thread{
    @Override
    public void run() {
        System.out.println("ThreadDemo 运行中...");
    }
    public static void main(String[] args) {
        ThreadDemo threadDemo = new ThreadDemo();
        threadDemo.start();
    }
}
```
2）*实现 Runnable 接口*

```java
class RunnableDemo{
    public static void main(String[] args) {
        new Thread(new Runnable() {
            @Override
            public void run() {
                System.out.println("RunnableDemo 运行中...");
            }
        }).start();
    }
}
```
3）*实现 Callable*

```java
public static void main(String[] args) throws ExecutionException, InterruptedException {
        FutureTask<Integer> futureTask = new FutureTask<>(new Callable<Integer>() {
            @Override
            public Integer call() throws Exception {
                return 1;
            }
        });
        new Thread(futureTask).start();
        System.out.println(futureTask.get());
    }
```
#### 2）架构说明
Java中的线程池使用过Excutor框架实现的，该框架中用到了`Executor`，`Executors`，`ExecutorService`，`ThreadPoolExecutor`这几个类。

![](https://user-gold-cdn.xitu.io/2020/4/5/1714a475e4d5f336?w=413&h=426&f=png&s=19977)
#### 3）重点了解
- `Executors.newFixedThreadPool()`
	
	**特点：**
	1. 创建一个定长线程池，可控制线程的最大并发数，超出的线程会在队列中等待。
	2. newFixedThreadPool 创建的线程池CorePoolSize和MaximumPoolSize是相等的，它使用的是**LinkedBlockingQueue** 。
	```java
	public static ExecutorService newFixedThreadPool(int nThreads) {
	        return new ThreadPoolExecutor(nThreads, nThreads, 0, 
	                TimeUnit.MICROSECONDS, new LinkedBlockingDeque<Runnable>());
	    }
	```
- `Executors.newSingleThreadExecutor()`
	
	**特点：**
	1. 创建一个单线程化的线程池，它只会用唯一的工作线程来执行任务，保证所有任务都按照指定的顺序执行。
	2. newSingleThreadExecutor将corePoolSize和MaximumPoolSize都设置为1，它使用的是**LinedBlockingQueue** 。
	```java
  public static ExecutorService newSingleThreadExecutor() {
        return new ThreadPoolExecutor(1, 1, 0,
                TimeUnit.MICROSECONDS, new LinkedBlockingDeque<Runnable>());
	  }
	```
- `Executors.newCachedThreadPool()`
	
	**特点：**
	1. 创建一个可缓存线程池，如果线程池长度超过处理需要，可灵活回收空闲线程，若无可回收，则创建新线程。
	2. newCacheThreadPool将corePoolsize设置为0，MaximumPoolSize设置为Integer.MAX_VALUE，它使用的是**SynchronousQueue** ，也就是说来了任务就创建线程运行，如果线程空闲超过60秒，就销毁线程
	```java
  public static ExecutorService newCachedThreadPool() {
        return new ThreadPoolExecutor(0, Integer.MAX_VALUE, 60,
	              TimeUnit.SECONDS, new SynchronousQueue<>());
	 	}
	```
#### 4）七大参数

|参数|	作用|
|--|--|
|corePoolSize|	线程池中常驻核心线程数|
|maximumPoolSize	| 线程池能够容纳同时执行的最大线程数，需大于1|
|keepAliveTime	|多余空闲线程的存活时间，当空间时间达到keepAliveTime值时，多余的线程会被销毁直到只剩下corePoolSize个线程为止|
|TimeUnit|	keepAliveTime| 时间单位|
|workQueue|	阻塞任务队列|
|threadFactory|	表示生成线程池中工作线程的线程工厂，用户创建新线程，一般用默认即可|
|RejectedExecutionHandler| 拒绝策略，表示当线程队列满了并且工作线程大于线程池的最大显示数（maximumPoolSize）时如何来拒绝|

![](https://ae01.alicdn.com/kf/Hf641046bf73f45a4b8d24275b6e0ac78v.jpg)

#### 5）线程池工作原理

![](https://ae01.alicdn.com/kf/H767da65cb8f045fcacfd52866a3fb4b7B.jpg)
![image](https://user-gold-cdn.xitu.io/2020/4/5/1714a475db6ae0ce?w=571&h=372&f=png&s=63290)
![](https://ae01.alicdn.com/kf/H41c61f1fe09c4f3fa7d083c09b92d94f4.jpg)
*例子：*

假设一家银行总共有六个窗口（`maximumPoolSize`），周末开了三个窗口提供业务办理（`corePoolSize`），上班期间来了3个人办理业务，三个窗口能够应付的过来，这个时候又来了1个，三个窗口便忙不过来了，，只好让新来的客户去等待区（`workQueue`）等待，接下来如果还有来客户的话便让客户去等待区（`workQueue`）等待。但是如果等待区也坐满了。业务经理（`threadFactory`）便通知剩下的窗口开启来进行业务办理，但是如果六个窗口都占满了，而且等待区也坐不下了。这个时候银行便要考虑采用什么方式（`RejectedExecutionHandler`）来拒绝客户。时间慢慢的过去了，办理业务的客户也差不多走了，只剩下3个客户在办理。这个时候空闲了3个新增的窗口，他们便开始等待（`keepAliveTime`）一定时间，如果时间到了还没有客户来办理业务的话，这3个新增窗口便可以关闭，回去休息。但是原来的三个窗口（`corePoolSize`）还得继续开着。

#### 6）拒绝策略
等待队列已经排满，再也塞不下新的任务，而且也达到了 **maximumPoolSize** 数量，无法继续为新任务服务，这个时候我们便要采取拒绝策略机制合理的处理这个问题。
*以下内置拒绝策略均实现了RejectExecutionHandler接口*

- `AbortPolicy（默认）`：

直接抛出RejectedException异常来阻止系统正常运行。

- `CallerRunPolicy`：

“调用者运行” 一种调节机制，该策略既不会抛弃任务，也不会抛出异常。线程调用运行该任务的 execute 本身。此策略提供简单的反馈控制机制，能够减缓新任务的提交速度。

- `DiscardOldestPolicy`：

抛弃队列中等待最久的任务，然后把当前任务加入队列中尝试再次提交（如果再次失败，则重复此过程）。

- `DiscardPolicy`：

直接丢弃任务，不予任何处理也不抛出异常，如果允许任务丢失，这是最好的拒绝策略。

#### 7）为何不用JDK创建线程池的方法
>阿里巴巴 java 开发手册
>`【强制】`线程资源必须通过线程池提供，不允许在应用中自行显示创建线程。说明：使用线程池的好处是**减少在创建和销毁线程上所消耗的时间以及系统资源的开销，解决资源不足的问题。如果不使用线程池，有可能造成系统创建大量同类线程而导致消耗完内存或者“过度切换”的问题。**
>`【强制】` 线程池不允许使用Executors去创建，而是通过ThreadPoolExecutor的方式，这样的处理方式让写的同学更加明确线程池的运行规则，规避资源耗尽的风险。
>
> 1.  `FixedThreadPool` 和 `SingleThreadPool`：允许的请求队列长度为Integer.MAX_VALUE，可能会堆积大量的请求，从而导致OOM。
>  2.  `CacheThreadPool` 和 `ScheduledThreadPool` ：允许创建线程的数量为Integer.MAX_VALUE，可能会创建大量的线程，从而导致OOM。
*例子：*

![](https://ae01.alicdn.com/kf/H5860d60cef174628b8a7a84a5533e6564.jpg)

#### 8）合理配置线程池
*CPU密集型*：

+  查看本机CPU核数：`Runtime.getRuntime().availableProcessors() `
+ CPU密集的意思是该任务需要大量的运算，而没有阻塞，CPU需一直全速运行。
+ CPU密集任务只有在真正的多核CPU上才可能得到加速（通过多线程）
+ CPU密集型任务配置尽可能少的线程数量 => 公式：CPU核数+1个线程的线程池

*IO密集型*：

+ 由于IO密集型任务线程并不是一直在执行任务，则应配置尽可能多的线程，如CPU核数 * 2 
+ IO密集型，是说明该任务需要大量的IO，即大量的阻塞。所以在单线程上运行IO密集型的任务会导致浪费大量的CPU运算能力浪费在等待上，所以要使用多线程可以大大的加速程序运行，即使在单核CPU上，这种加速主要就是利用了被浪费掉的阻塞时间。
+ 配置线程公式：CPU核数 / 1-阻塞系数（0.8~0.9） =>如8核CPU：8 / 1 - 0.9 = 80个线程数

### 七、死锁编码及定位分析

#### 1）什么是死锁

>死锁是指两个或两个以上的进程在执行过程中，因争夺资源而造成的一种互相等待的现象，如果无外力的干涉那么它们将无法推进下去，如果系统的资源充足，进程的资源请求都能够得到满足，死锁出现的可能性就很低，否则会因争夺有限的资源而陷入死锁。

![](https://user-gold-cdn.xitu.io/2020/4/5/1714a4760baaec9c?w=672&h=354&f=png&s=106295)
#### 2)造成原因
 - `资源系统不足`
 - `进程运行推进的顺序不合适`
 - `资源分配不当`

*例子*：

![](https://ae01.alicdn.com/kf/H996717b4120e46568341e500ea5a6d30k.jpg)

**打印结果**
陷入死锁状态：
![](https://user-gold-cdn.xitu.io/2020/4/5/1714a4761335004e?w=657&h=91&f=png&s=4228)
#### 3)解决方法
 - ##### `jps` 命令定位进程编号

  ![](https://user-gold-cdn.xitu.io/2020/4/5/1714a476178f7296?w=437&h=148&f=png&s=3806)
 - ##### `jstack` 找到死锁查看

  ![](https://user-gold-cdn.xitu.io/2020/4/5/1714a4761e446b53?w=1002&h=543&f=png&s=86313)



**[END]**



以上便是 **Java中高并发** 的大概知识点啦！路漫漫，小菜与你一同求索~ 

![看完不赞，都是坏蛋](https://imgconvert.csdnimg.cn/aHR0cHM6Ly93d3cuNTJkb3V0dS5jbi9zdGF0aWMvdGVtcC9waWMvOWJkNjhkMTUwZjA3ODdjNTYwYTQzOWRhMzU5YTU4MGEucG5n?x-oss-process=image/format,png#pic_center)

> 今天的你多努力一点，明天的你就能少说一句求人的话！
>
> *我是小菜，一个和你一起学习的男人。* `💋`
>
>
> 微信公众号已开启，**小菜良记**，没关注的同学们记得关注哦！