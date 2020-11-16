大家好，我是小菜，一个渴望在互联网行业做到蔡不菜的小菜。可柔可刚，点赞则柔，白嫖则刚！
**死鬼~看完记得给我来个三连哦！**


![](https://user-gold-cdn.xitu.io/2020/4/11/17169c46045528af?w=240&h=224&f=jpeg&s=7529)


>本文主要介绍 `ThreadLocal 的使用`
>
>如有需要，可以参考
>
>如有帮助，不忘 **点赞** ❥
>
>
>微信公众号已开启，**小菜良记**，没关注的同学们记得关注哦！

之前我们有在并发系列中提到 `ThreadLocal` 类和基本使用方法，那我们就来看下 `ThreadLocal` 究竟是如何使用的！

## ThreadLocal 简介

### 概念

**ThreadLocal** 类是用来提供线程内部的局部变量。这种变量在多线程环境下访问（**get** 和 **set** 方法访问）时能保证各个线程的变量相对独立于其他线程内的变量。 **ThreadLocal** 实例通常来说都是 **private static** 类型的，用于关联线程和上下文。

### 作用

- **传递数据** 

提供线程内部的局部变量。可以通过 **ThreadLocal** 在同一线程，不同组件中传递公共变量。

- **线程并发**

适用于多线程并发情况下。

- **线程隔离**

每个线程的变量都是独立的，不会相互影响。

## ThreadLocal 实战

### 1. 常见方法

- **`ThreadLocal ()`**

**构造方法，创建一个 ThreadLocal 对象**

- **`void set (T value)`**

设置当前线程绑定的局部变量

- **`T get ()`**

**获取当前线程绑定的局部变量**

- **`void remove ()`**

**移除当前线程绑定的局部变量**

### 2. 为什么要使用 ThreadLocal

首先我们先看一组并发条件下的代码场景：

```java
@Data
public class ThreadLocalTest {
    private String name;

    public static void main(String[] args) {
        ThreadLocalTest tmp = new ThreadLocalTest();
        for (int i = 0; i < 4; i++) {
            Thread thread = new Thread(() -> {
                tmp.setName(Thread.currentThread().getName());
                System.out.println(Thread.currentThread().getName() +
                                   "\t 拿到数据：" + tmp.getName());
            });
            thread.setName("Thread-" + i);
            thread.start();
        }
    }
}
```

我们理想中的代码输出结果应该是这样的：

```java
/** OUTPUT **/
Thread-0	 拿到数据：Thread-0
Thread-1	 拿到数据：Thread-1
Thread-2	 拿到数据：Thread-2
Thread-3	 拿到数据：Thread-3
```

但是实际上输出的结果却是这样的：

```java
/** OUTPUT **/
Thread-0	 拿到数据：Thread-1
Thread-3	 拿到数据：Thread-3
Thread-1	 拿到数据：Thread-1
Thread-2	 拿到数据：Thread-2
```

顺序乱了没有关系，但是我们可以看到 **Thread-0** 这个线程拿到的值却是 **Thread-1**

从结果中我们可以看出多个线程在访问同一个变量的时候会出现异常，这是因为线程间的数据没有隔离！

并发线程出现的问题？那加锁不就完事了！这个时候你三下五除二的写下了以下代码：

```java
@Data
public class ThreadLocalTest {

    private String name;

    public static void main(String[] args) {
        ThreadLocalTest tmp = new ThreadLocalTest();
        for (int i = 0; i < 4; i++) {
            Thread thread = new Thread(() -> {
                synchronized (tmp) {
                    tmp.setName(Thread.currentThread().getName());
                    System.out.println(Thread.currentThread().getName() 
                                       + "\t" + tmp.getName());
                }
            });
            thread.setName("Thread-" + i);
            thread.start();
        }
    }
}
/** OUTPUT **/
Thread-2	Thread-2
Thread-3	Thread-3
Thread-1	Thread-1
Thread-0	Thread-0
```

从结果上看，加锁好像是解决了上述问题，但是 **synchronized** 常用于多线程数据共享的问题，而非多线程数据隔离的问题。这里使用 **synchronized** 虽然解决了问题，但是多少有些不合适，并且 **synchronized** 属于重量级锁，为了实现多线程数据隔离贸然的加上 **synchronized**，也会影响到性能。

加锁的方法也被否定了，那么该如何解决？不如用 **ThreadLocal** 牛刀小试一番：

```java
public class ThreadLocalTest {

    private static ThreadLocal<String> threadLocal = new ThreadLocal<>();

    public String getName() {
        return threadLocal.get();
    }

    public void setName(String name) {
        threadLocal.set(name);
    }

    public static void main(String[] args) {
        ThreadLocalTest tmp = new ThreadLocalTest();
        for (int i = 0; i < 4; i++) {
            Thread thread = new Thread(() -> {
                tmp.setName(Thread.currentThread().getName());
                System.out.println(Thread.currentThread().getName() + 
                                   "\t 拿到数据：" + tmp.getName());
            });
            thread.setName("Thread-" + i);
            thread.start();
        }
    }
}
```

在查看输出结果之前，我们先来看看代码发生了那些变化

首先多了一个 `private static` 修饰的 **ThreadLocal** ，然后在 `setName` 的时候，我们实际上是往 **ThreadLocal** 里面存数据，在 `getName` 的时候，我们是在 **ThreadLocal** 里面取数据。感觉操作上也是挺简单的，但是这样真的能做到线程间的数据隔离吗，我们再来看一看结果：

```java
/** OUTPUT **/
Thread-1	 拿到数据：Thread-1
Thread-2	 拿到数据：Thread-2
Thread-0	 拿到数据：Thread-0
Thread-3	 拿到数据：Thread-3
```

从结果上可以看到每个线程都能取到对应的数据。**ThreadLocal** 也已经解决了多线程之间数据隔离的问题。

那么我们来小结一下，**为什么需要使用ThreadLocal，与 synchronized 的区别是什么**

- **synchronized**

**原理：** 同步机制采用 "以时间换空间" 的方式，只提供了一份变量，让不同线程排队访问

**侧重点：** 多个线程之间同步访问资源

- **ThreadLocal**

**原理：** ThreadLocal 采用 "以空间换时间" 的方式，为每个线程都提供了一份变量的副本，从而实现同时访问而互不干扰

**侧重点：** 多线程中让每个线程之间的数据相互隔离

### 3. 内部结构

从上面的案例中我们可以看到 **ThreadLocal** 的两个主要方法分别是 `set()` 和 `get()`

那我们不妨猜想一下，如果让我们来设计 **ThreadLocal** ，我们该如何设计，是否会有这样的想法：每个 **ThreadLocal** 都创建一个 **Map**，然后用线程作为 **Map** 的 **key**，要存储的局部变量作为 **Map** 的 **value** ，这样就能达到各个线程的局部变量隔离的效果。

![](https://cbucbm.club/8972894e)

这个想法也是没错的，早期的 **ThreadLocal** 便是这样设计的，但是在 **JDK 8** 之后便更改了设计，如下：

![](https://cbucbm.club/b092134b)

设计过程：

1. 每个 **Thread** 线程内部都有一个 **ThreadLocalMap**

2. **ThreadLocalMap** 中存储着以 **ThreadLocal** 对象为 **key** ，线程变量为 **value**
3. **Thread** 内部的 **Map** 是由 **ThreadLocal** 维护的，由 **ThreadLocal** 负责向 **Map** 设置和获取线程的变量值
4. 对于不同的线程，每次获取副本值时，别的线程并不能获取到线程的副本值，这样就会形成副本的隔离，互不干扰

**注：** 每个线程都要有自己的一个 **map**，但是这个类就是一个普通的 **Java** 类，并没有实现 **Map** 接口，但是具有类似 **Map** 类似的功能。

![](https://cbucbm.club/986800c1)

通过这样实现看起来貌似会比之前我们猜想的更加复杂，这样做的好处是什么呢？

- 每个 **Map** 存储的 **Entry** 数量就会变少，因为之前的存储数量由 **Thread** 的数量决定，现在是由 **ThreadMap** 的数量决定，在实际开发中，**ThreadLocal** 的数量要更少于 **Thread** 的数量。
- 当 **Thread** 销毁之后，对应的 **ThreadLocalMap** 也会随之销毁，能减少内存的使用

### 4. 源码分析

![](https://cbucbm.club/66e6d553)

首先我们先看 **ThreadLocalMap** 中有哪些成员：

![](https://cbucbm.club/6e465eef)

如果你看过 **HashMap** 的源码，肯定会觉得这几个特别熟悉，其中：

- **INITIAL_CAPACITY**：初始容量，必须是 2 的整次幂
- **table**：存放数据的**table**
- **size**：数组中 **entries** 的个数，用于判断 **table** 当前使用量是否超过阈值
- **threshold**：进行扩容的阈值，表使用量大于它的时候会进行扩容

#### ThreadLocals

**Thread** 类中有个类型为 **ThreadLocal.ThreadLocalMap** 类型的变量 **ThreadLocals** ，这个就是用来保存每个线程的私有数据。

![](https://cbucbm.club/986800c1)

#### ThreadLocalMap

**ThreadLocalMap**是**ThreadLocal**的内部类，每个数据用**Entry**保存，其中的**Entry**用一个键值对存储，键为**ThreadLocal**的引用。

![](https://cbucbm.club/f4daf835)

我们可以看到 **Entry** 继承于**WeakReference**，这是因为如果是强引用，即使把 **ThreadLocal** 设置为 **null**，**GC** 也不会回收，因为 **ThreadLocalMap** 对它有强引用。

在没有手动删除这个**Entry**以及**CurrentThread**依然运行的前提下，始终有强引用链 **threadRef** `->` **currentThread** `->` **threadLocalMap** `->` **entry**，**Entry**就不会被回收（**Entry**中包括了**ThreadLocal**实例和**value**），导致**Entry**内存泄漏。

![](https://cbucbm.club/d2d0f5f2)

那是不是就是说如果使用了**弱引用**，就不会造成**内存泄露** 呢，这也是不正确的。

因为如果我们没有手动删除 **Entry** 的情况下，此时 **Entry** 中的 **key == null**，这个时候没有任何强引用指向 **threaLocal** 实例，所以 **threadLocal** 就可以顺利被 **gc** 回收，但是 **value** 不会被回收，而这块的 **value** 永远不会被访问到，因此会导致**内存泄露**

![](https://cbucbm.club/c9a34cde)

接下来我们看下 **ThreadLocalMap** 的几个核心方法：

#### set 方法

首先我们先看下源码：

```java
public void set(T value) {
    // 获取当前线程对象
    Thread t = Thread.currentThread();
    // 获取此线程对象中维护的ThreadLocalMap对象
    ThreadLocalMap map = getMap(t);
    // 判断map是否存在
    if (map != null)
        // 存在则调用map.set设置此实体entry
        map.set(this, value);
    else
        // 如果当前线程不存在ThreadLocalMap对象则调用createMap进行ThreadLocalMap对象的初始化
        // 并将 t(当前线程)和value(t对应的值)作为第一个entry存放至ThreadLocalMap中
        createMap(t, value);
}

ThreadLocalMap getMap(Thread t) {
    return t.threadLocals;
}

void createMap(Thread t, T firstValue) {
    //这里的this是调用此方法的threadLocal
    t.threadLocals = new ThreadLocalMap(this, firstValue);
}
```

执行流程：

- 首先获取当前线程，并根据当前线程获取一个 **map**
- 如果获取的 **map** 不为空，则将参数设置到 **map** 中（当前 **ThreadLocal** 的引用作为 **key** ）

- 如果 **Map** 为空，则给该线程创建 **map** ，并设置初始值

#### get 方法

源码如下：

```java
public T get() {
    // 获取当前线程对象
    Thread t = Thread.currentThread();
    // 获取此线程对象中维护的ThreadLocalMap对象
    ThreadLocalMap map = getMap(t);
    // 如果此map存在
    if (map != null) {
        // 以当前的ThreadLocal 为 key，调用getEntry获取对应的存储实体e
        ThreadLocalMap.Entry e = map.getEntry(this);
        // 对e进行判空 
        if (e != null) {
            @SuppressWarnings("unchecked")
            // 获取存储实体 e 对应的 value值
            // 即为我们想要的当前线程对应此ThreadLocal的值
            T result = (T)e.value;
            return result;
        }
    }
    return setInitialValue();
}

private T setInitialValue() {
    // 调用initialValue获取初始化的值
    // 此方法可以被子类重写, 如果不重写默认返回null
    T value = initialValue();
    // 获取当前线程对象
    Thread t = Thread.currentThread();
    // 获取此线程对象中维护的ThreadLocalMap对象
    ThreadLocalMap map = getMap(t);
    // 判断map是否存在
    if (map != null)
        // 存在则调用map.set设置此实体entry
        map.set(this, value);
    else
        // 如果当前线程不存在ThreadLocalMap对象则调用createMap进行ThreadLocalMap对象的初始化
        // 并将 t(当前线程)和value(t对应的值)作为第一个entry存放至ThreadLocalMap中
        createMap(t, value);
    // 返回设置的值value
    return value;
}
```

执行流程：

- 首先获取当前线程，根据当前线程获取一个 **map**
- 如果获取的 **map** 不为空，则在 **map** 中以 **ThreadLocal** 的引用作为 **key** 来在 **map** 中获取对应的 **Entry entry**  ，否则跳转到**第四步**
- 如果 **Entry entry** 不为空 ，则返回 **entry.value** ，否则跳转到**第四步**
- **map** 为空或者 **entry** 为空，则通过 **initialValue** 函数获取初始值 **value** ，然后用 **ThreadLocal** 的引用和 **value** 作为 **firstKey** 和 **firstValue** 创建一个新的 **map**

#### remove 方法

源码如下：

```java

public void remove() {
    // 获取当前线程对象中维护的ThreadLocalMap对象
    ThreadLocalMap m = getMap(Thread.currentThread());
    // 如果此map存在
    if (m != null)
        // 存在则调用map.remove
        m.remove(this);
}
// 以当前ThreadLocal为key删除对应的实体entry
private void remove(ThreadLocal<?> key) {
    Entry[] tab = table;
    int len = tab.length;
    int i = key.threadLocalHashCode & (len-1);
    for (Entry e = tab[i];
         e != null;
         e = tab[i = nextIndex(i, len)]) {
        if (e.get() == key) {
            e.clear();
            expungeStaleEntry(i);
            return;
        }
    }
}
```

执行流程：

- 首先获取当前线程，并根据当前线程获取一个 **map**
- 如果获得的**map** 不为空，则移除当前 **ThreadLocal** 对象对应的 **entry**

#### initialValue 方法

源码如下：

```java
protected T initialValue() {
    return null;
}
```

在源码中我们可以看到这个方法仅仅简单的返回了 **null** ，这个方法是在线程第一次通过 **get ()** 方法访问该线程的 **ThreadLocal** 时调用的，只有在线程先调用了 **set ()** 方法才不会调用 **initialValue ()** 方法，通常情况下，这个方法最多被调用一次。

如果们想要 **ThreadLocal** 线程局部变量有一个除 **null** 以外的初始值，那么就必须通过子类**继承** **ThreadLocal** 来重写此方法，可以通过匿名内部类实现。



**【END】**

这篇 **ThreadLocal** 就介绍到这里啦，希望读到这里的小伙伴能够有所收获。

路漫漫，小菜与你一同求索！

![看完不赞，都是坏蛋](https://imgconvert.csdnimg.cn/aHR0cHM6Ly93d3cuNTJkb3V0dS5jbi9zdGF0aWMvdGVtcC9waWMvOWJkNjhkMTUwZjA3ODdjNTYwYTQzOWRhMzU5YTU4MGEucG5n?x-oss-process=image/format,png#pic_center)
> 今天的你多努力一点，明天的你就能少说一句求人的话！
>
> *我是小菜，一个和你一起学习的男人。* `💋`
>
>
> 微信公众号已开启，**小菜良记**，没关注的同学们记得关注哦！