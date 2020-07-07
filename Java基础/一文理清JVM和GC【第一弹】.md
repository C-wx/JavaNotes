大家好，我是小菜，一个渴望在互联网行业做到蔡不菜的小菜。可柔可刚，点赞则柔，白嫖则刚！
**死鬼~看完记得给我来个三连哦！**

![](https://mmbiz.qlogo.cn/mmbiz_jpg/P7WuIzkp9iaVqg4wKjOlo2cia6ibCCRkwD1HDVQavMXmkLTqSh5uIxcvJW4Mb7DHWm7q2b5zgbluU6vGhBsia7vGEw/0?wx_fmt=jpeg)

> 本文主要介绍 `JVM和GC解析`
> 如有需要，可以参考
> 如有帮助，不忘 **点赞** ❥
>
> 创作不易，白嫖无义！

### 一、JVM内存体系

其中*方法区*和*堆*被JVM中多个`线程共享`，比如类的静态常量就被存放在方法区，供类对象之间共享。

*虚拟机栈*、*本地方法栈*、*程序计数器*是每个`线程独立`拥有的，不会与其他线程共享。

所以Java在通过new创建一个类对象实例的时候，一方面会在虚拟机栈中创建一个对该对象的引用，另一方面会在堆上创建类对象的实例，然后将对象引用指向该对象的实例。对象引用存放在每一个方法对应的栈帧中。

![img](https://mmbiz.qlogo.cn/mmbiz_png/P7WuIzkp9iaVqg4wKjOlo2cia6ibCCRkwD1p69H8z8CDzbPtmYMqn0ScaAuD4EV9OtbBgoByQpcYZHqQ1Ju8IInKA/0?wx_fmt=png)

- `虚拟机栈：`虚拟机栈中执行每个方法的时候，都会创建一个栈帧用于存储局部变量表，操作数栈，动态链接，方法出口等信息。
- `本地方法栈：`与虚拟机栈发挥的作用相似，相比于虚拟机栈为Java方法服务，本地方法栈为虚拟机使用的Native方法服务，执行每个本地方法的时候，都会创建一个栈帧用于存储局部变量表，操作数栈，动态链接，方法出口等信息。
- `方法区：`它用于存储已被虚拟机加载的*类信息，常量，静态变量*，即时编译器编译后的代码等数据，方法区在JDK1.7版本及之前称为永久代，从JDK1.8之后永久代被移除。
- `堆：`堆是Java对象的存储区域，任何new字段分配的*Java对象实例和数组*，都被分配在了堆上，Java堆可使用 `- Xms` 和`-Xmx` 进行内存控制，从JDK1.7版本之后，*运行时常量池*从方法区移到了堆上。
- `程序计数器：`指示Java虚拟机下一条需要执行的字节码指令。

### 二、JAVA8之后的JVM

*从图中我们可以看出JAVA8的JVM 用元空间取代了永久代*

![img](https://mmbiz.qlogo.cn/mmbiz_png/P7WuIzkp9iaVqg4wKjOlo2cia6ibCCRkwD1yLQCSpYHmCuiaJwGorb251hE07cZlZvk4p5TsgAsdiaE0h84Ds2WRdDw/0?wx_fmt=png)



![img](https://mmbiz.qlogo.cn/mmbiz_png/P7WuIzkp9iaVqg4wKjOlo2cia6ibCCRkwD1WibQ4jmmK9ia6BbI0aUoibCiax8383oR7v3cCJT9Rt6ZS3qGzmwffsmLtw/0?wx_fmt=png)



![img](https://mmbiz.qlogo.cn/mmbiz_png/P7WuIzkp9iaVqg4wKjOlo2cia6ibCCRkwD19Ky786FzjyT5REWibQOia17HqPOafknfj9XZFMNIyT2DZJAuWddNToVw/0?wx_fmt=png)

### 三、GC作用域

![img](https://mmbiz.qlogo.cn/mmbiz_png/P7WuIzkp9iaVqg4wKjOlo2cia6ibCCRkwD10xNaUaLDufjIvF9YOrqII96HfJE1SPFzZnjviaE6a6LW1gv2fWFNDvQ/0?wx_fmt=png)

### 四、常见垃圾回收算法

#### 1）引用计数法：

*JVM的实现一般不采用这种方式*

![img](https://mmbiz.qlogo.cn/mmbiz_png/P7WuIzkp9iaVqg4wKjOlo2cia6ibCCRkwD12icIrh6pKnK3R7vJ9HcJCf0JvSq11Y64ocUZqYQCn0fZEpqBwdy1RlA/0?wx_fmt=png)


*缺点*：

- 每次对对象赋值时均要维护引用计数器，且计数器本身也有一定的消耗；
- 较难处理循环引用；

#### 2）复制算法：

Java 堆从GC的角度可以细分为：**新生代**（Eden区、From Survivor区 和 To Survivor区）和 老年代。
*特点*：
复制算法不会产生内存碎片，但会占用空间。用于新生代。

![ ](https://mmbiz.qlogo.cn/mmbiz_png/P7WuIzkp9iaVqg4wKjOlo2cia6ibCCRkwD1EubGve4kezb4DaXGatSRMg7CYuOiaXVF7gFPLuYsxqSsrjW3ejsQ2tw/0?wx_fmt=png)


*MinorGC的过程（复制 --> 清空 --> 互换）*：

1. **复制：** （Eden、SurvivorFrom 复制到 SurvivorTo，年龄加1）
   首先，当Eden区满的时候会触发第一次GC，把还活着的对象拷贝到SurvivorFrom区，当Eden区再次触发GC的时候会扫描Eden区域和From区域，对这两个区域进行垃圾回收，经过这次回收后还存活的对象，则直接复制到To区域（如果有对象的年龄已经到达了老年的标准，则复制到老年代区），同时把这些对象的年龄加1。
2. **清空：**（清空Eden、SurvivorFrom）
   清空Eden和SurvivorFrom中的对象，也即复制之后有交换，谁空谁是to。
3. **互换：**(SurvivorTo和SurvivorFrom 互换)
   最后，SurvivorTo和SurvivorFrom 互换，原SurvivorTo成为下一次GC是的SurvivorFrom区。

#### 3）标记清除法

算法分成`标记`和`清除`两个阶段，*先标记出要回收的对象，然后统一回收这些。*
*特点*：
不会占用额外空间，但会扫描两次，耗时，容易产生碎片，用于老年代

![ ](https://mmbiz.qlogo.cn/mmbiz_png/P7WuIzkp9iaVqg4wKjOlo2cia6ibCCRkwD1icpficFshU8ZicDfavAvXhnWNehKmZQibbaSgW3dWcRMkOelQQRtOS7Q2w/0?wx_fmt=png)

#### 4）标记压缩法

*优点*：
没有内存碎片，可以利用bump
*缺点*：
需要移动对象的成本，用于老年代
*原理*：

标记：与标记清除一样

![ ](https://mmbiz.qlogo.cn/mmbiz_png/P7WuIzkp9iaVqg4wKjOlo2cia6ibCCRkwD1JKIMnDPtPr6LcqBzWT7Z4Dj27j7VpsibpbZgF5tnQFJnAMW3dB9ruEw/0?wx_fmt=png)


压缩：再次扫描，并往一段滑动存活对象

![ ](https://mmbiz.qlogo.cn/mmbiz_png/P7WuIzkp9iaVqg4wKjOlo2cia6ibCCRkwD1FFNvJlEj0dBBpKjdkTfRY0tdL85fmx0k8nclP1ib0icG6EGE5ia3BNMqw/0?wx_fmt=png)

### 五、判断对象是否可回收

#### 1）引用计数法

Java中，*引用和对象是有关联的*。如果要操作对象则必须用`引用进行`。
因此，很显然的一个方法就是通过`引用计数`来判断一个对象是否可以回收。简单来说就是给对象添加一个引用计数器。*每当有一个地方引用它，计数器的值加1，每当有一个引用失效时，计数器的值减1。*
任何时刻计数器值为0的对象就是不可能再被使用的，那么这个对象就是可回收对象。
*缺点*：

很难解决对象之间相互循环引用的问题

#### 2）枚举根节点做可达性分析(根搜索路径)

所谓`GC roots`或者说`tracing GC` 的 *根集合* 就是一组必须活跃的引用。
基本思路就是通过一系列名为`GC Root` 的对象作为起始点，从这个被称为`GC Roots`的对象*开始向下搜索*。

*如GC Roots没有任何引用链相连是，则说明此对象不可用。也即给定一个集合的引用作为根出发，通过引用关系*

![ ](https://mmbiz.qlogo.cn/mmbiz_png/P7WuIzkp9iaVqg4wKjOlo2cia6ibCCRkwD1ZE94wqQju7JBcX1yWNOJoVoO3BxcmGCnklKTcJhxz4Ea8DP60LDFbQ/0?wx_fmt=png)

#### 哪些可以做GCRoots对象

- `虚拟机栈`（栈帧中的局部变量区，也叫做局部变量表）
- `方法区中的类静态属性引用的对象`
- `方法区中常量引用的对象`
- `本地方法栈中N（Native方法）引用的对象`

### 六、JVM的参数类型

#### 1）标配参数

- `java -version`

- `java -help`

  ![ ](https://mmbiz.qlogo.cn/mmbiz_png/P7WuIzkp9iaVqg4wKjOlo2cia6ibCCRkwD15usSWIz9nEj0Vv2dQ0ic8xZoQ96cd7qibVR7x8CsGyLZ6LNkedrjwdSA/0?wx_fmt=png)

#### 2）X参数

- `java -Xint -version` ：解释执行

- `java -Xcomp -version` ：第一次使用就编译成本地代码

- `java -Xmixed` ：混合模式

  ![ ](https://mmbiz.qlogo.cn/mmbiz_png/P7WuIzkp9iaVqg4wKjOlo2cia6ibCCRkwD1TRTCia0T5atiasWocswS7Qn0EzYA8jrzP41ITErYvDCgnO2ib76icZBZBA/0?wx_fmt=png)

#### 3）XX参数

- *Boolean类型*

`-XX`：*`+` 或者 `-` 某个属性值（`+`：表示开启，`-`：表示关闭）*
***例子\***：
`-XX: +PrintGCDetails`： 开启打印GC收集细节
`-XX: -PrintGCDetails`： 关闭打印GC收集细节
`-XX: +UseSerialGC`： 开启串行垃圾收集器
`-XX: -UseSerialGC`：关闭串行垃圾收集器

- *KV设置类型*

`-XX`: *属性key = 属性value*
***例子\***：
`-XX: MetaspaceSize = 128m`：设置元空间大小为128m 
`-XX:MaxTenuringThreshold = 15`：控制新生代需要经历多少次GC晋升到老年代中的最大阈值

- *jinfo -查看当前运行程序的配置*

***公式\***：`jinfo -flag` 配置项  进程编号
***例子\***：

1. 查看初始堆大小：

   ![ ](https://mmbiz.qlogo.cn/mmbiz_png/P7WuIzkp9iaVqg4wKjOlo2cia6ibCCRkwD1jiazN0YQSDK5er6G5IAHdfPUfj5hicG0XUkNIgbgWl1pBnFuSLJ2xe5A/0?wx_fmt=png)

2. 查看其他参数

   ![img](https://mmbiz.qlogo.cn/mmbiz_png/P7WuIzkp9iaVqg4wKjOlo2cia6ibCCRkwD1rLrK0fsJsIOydPEp7KI9avW9ZvFMVAebJ8epCGkKoBrLwbym2XeHicg/0?wx_fmt=png)

3. 查看使用哪种垃圾回收器

   ![ ](https://mmbiz.qlogo.cn/mmbiz_png/P7WuIzkp9iaVqg4wKjOlo2cia6ibCCRkwD1uO6YTiawycTdqRxrGueyHFybFzG2ya9V7kIonRh5tibFGibGEUGRNJVoQ/0?wx_fmt=png)

```
两个经典参数
```

- `-Xms` 等价于 `-XX: InitialHeapSize`
- `-Xmx` 等价于 `-XX: MaxHeapSize`

### 七、查看JVM默认值

- `-XX:+PrintFlagsInitial`： 查看默认初始值

- - `java -XX: +PrintFlagsInitial -version`

  - `java -XX: +PrintFlagsInitial`

    ![ ](https://mmbiz.qlogo.cn/mmbiz_png/P7WuIzkp9iaVqg4wKjOlo2cia6ibCCRkwD17lpKmGEKOFbroNYz43P7IAsKry1bANx02fic8iaCAIrw4U33Iymjbwibw/0?wx_fmt=png)

- `-XX:+PrintFlagsFinal` ：查看修改更新

- - `java -XX:+PrintFlagsFinal`

  - `java -XX:+PrintFlagsFinal -version`

  - `java -XX:+PrintCommandedLineFlags`

    ![ ](https://mmbiz.qlogo.cn/mmbiz_png/P7WuIzkp9iaVqg4wKjOlo2cia6ibCCRkwD1a6tf79XHhV2BG6ibGUovmc2BCYbcrfoZibVwa5BwNlFp0HQ5fwF1wibVw/0?wx_fmt=png)

### 八、常用的配置参数

*经典案例设置*：
`-Xms128m -Xmx4096m -Xss1024k -XX:Metaspacesize=512m -XX:+PrintCommandLineFlags -XX:PrintGCDetails -XX:UseSerialGC`

- `-Xms`

*初始化大小内存，默认为物理内存1/64*
等价于 `-XX:InitialHeapSize`

- `-Xmx`

*最大分配内存，默认为物理内存1/4*
等价于 `-XX:MaxHeapSize`

- `-Xss`

*设置单个线程的大小，一般默认为5112K~1024K*
等价于 `-XX:ThreadStackSize`

- `-Xmn`

*设置年轻代大小*

- `-XX:MetaspaceSize`

*设置元空间大小*

元空间的本质和永久代类似，都是对JVM规范中方法区的实现，不过元空间与永久代之间最大的区别在于：**元空间并不在虚拟机中，而是使用本地内存**。因此，默认情况下，**元空间的大小仅受本地内存限制**。

- `-XX:+PrintGCdetails`

*输出详细的GC收集日志信息*

- `-XX:SurvivorRatio`

*设置新生代中eden和S0/S1空间的比例*
默认：
`-XX:SurvivorRatio=8` --> `Eden:S0:S1=8:1:1`
修改：
`-XX:SurvivorRatio=4` --> `Eden:S0:S1=4:1:1`
*SurvivorRatio值就是设置eden区的比例占多少，S0/S1相同*

- `-XX:NewRatio`

*设置年轻代与老年代在堆结构的占比*
默认：
`-XX:NewRatio=2`： 新生代占1，老年代占2，年轻代占整个堆的1/3
修改：
`-XX:NewRatio=4`： 新生代占1，老年代占4，年轻代占整个堆的1/5
*NewRatio值就是设置老年代的占比，剩下的1给新生代*

- `-XX:MaxTenuringThreshold`

*设置垃圾最大年龄*
`-XX:MaxTenuringThreshold=0`：设置垃圾最大年龄。

如果设置为0的话，则年轻代对象不经过Survivor区，直接进入老年代。对于老年代比较多的应用，可以提高效率。如果将此值设置为一个较大值，则年轻代对象会在Survivor区进行多次复制，这样可以增加对象在年轻代的存活时间，增加年轻代被回收的概论。

### 九、强软弱虚

![ ](https://mmbiz.qlogo.cn/mmbiz_png/P7WuIzkp9iaVqg4wKjOlo2cia6ibCCRkwD1sTa8G0nEdm79OomicG2QkBKgC7j8m82iamHeFnsFfIdxUng7aptvKdRQ/0?wx_fmt=png)



![ ](https://mmbiz.qlogo.cn/mmbiz_png/P7WuIzkp9iaVqg4wKjOlo2cia6ibCCRkwD1rsGtiae5UwsmrEf9O56xj8js4T0yTpd2QoLj5erraXHzkW9kmm9Unkw/0?wx_fmt=png)

#### 1）强引用

- 当内存不足，JVM开始垃圾回收，对于强引用的对象，*就算出现了OOM也不会对该对象进行回收，`死都不收`*
- 强引用是我们最常见的普通对象引用，只要还有强引用指向一个对象，就能表名对象还`活着`，垃圾收集器不会碰这种对象。在Java中最常见的就是强引用，把一个对象赋给一个引用变量，这个引用变量就是一个强引用。当一个对象被强引用变量引用时，它处于可达状态，它是不可能被垃圾回收机制回收的。*即使该对象以后永远都不会被用到，JVM也不会回收。* **因此强引用是造成Java内存泄漏的主要原因之一。**
- 对于一个普通的对象，如果没有其他的引用关系，只要超过了引用的作用域或者显式地将相应（强）引用赋值为**null**，一般就是认为可以被垃圾收集（具体看垃圾收集策略）

```java
public static void main(String[] args) {
        Object o1 = new Object();   //默认为强引用
        Object o2 = o1;     //引用赋值
        o1 = null;          //置空 让垃圾收集
        System.gc();
        System.out.println(o1);     // null
        System.out.println(o2);     // java.lang.Object@1540e19d
    }
```

#### 2）软引用

- 软引用就是一种相对强引用弱化了一些的引用。需要用java.lang.ref.SoftReference类来实现，可以让对象豁免一些垃圾收集。
- 系统内存`充足` -> 不会回收
- 系统内存`不足` -> 会回收
- 软引用通常用在对内存敏感的程序中，比如高速缓存就有用到软引用，*内存够用的时候就保留，不够用就回收*

```java
    public static void main(String[] args) {
        Object o1 = new Object();
        SoftReference softReference = new SoftReference(o1);
        o1 = null;
        System.gc();
        System.out.println(o1);
        System.out.println(softReference.get());
    }
```

#### 3）弱引用

- 弱引用需要用`java.lang.ref.WeakReference`类来实现，它比软引用的生存期更短
- 对于弱引用的对象，只要垃圾回收机制一运行，不管JVM的内存空间是否足够，都会回收该对象占用的内存。

```java
    public static void main(String[] args) {
        Object o1 = new Object();
        WeakReference weakReference = new WeakReference(o1);
        o1 = null;
        System.gc();
        System.out.println(o1);                     //null
        System.out.println(weakReference.get());    //null
    }
```

#### 4）虚引用

- 虚引用需要`java.lang.ref.PhantomReference`类来实现。
- *形如虚设*，它不会决定对象的生命周期。
- *如果一个对象持有虚引用，那么它就和没有任何一样，在任何时候都可能被垃圾回收器回收*，它不能单独使用也不能通过它来访问对象，虚引用必须和`引用队列（ReferenceQueue）`联合使用。
- 虚引用的主要作用是跟踪对象被垃圾回收的状态，仅仅是提供了一种确保对象被`finalize`以后，做某些事情的机制。`PhantomReference`的`get()`方法总是返回null，因此无法访问对应的引用对象。其意义在于*说明一个对象已经进入finalization阶段，可以被gc回收，用来实现比finalization机制更灵活的回收操作。*

```java
public static void main(String[] args) {
        Object o1 = new Object();
        ReferenceQueue<Object> referenceQueue = new ReferenceQueue<>();
        PhantomReference<Object> phantomReference = new PhantomReference<>(o1,referenceQueue);
        System.out.println(o1);                         //java.lang.Object@1540e19d
        System.out.println(phantomReference.get());     //null
        System.out.println(referenceQueue.poll());      //null
    }
```

*扩展*：软弱引用适用场景

假如有一个引用需要读取大量的本地图片
**存在问题**：

1. 如果每次读取图片都从硬盘读取则会严重影响性能。
2. 如果一次性全部加载到内存中有可能造成内存溢出。

**解决思路**：
用一个HashMap来保存图片的路径和相应图片对象关联的软引用之间的映射关系，在内存不足时，JVM会自动回收这些缓存图片对象所占用的空间，从而有效地避免了OOM的问题。
`Map imgMap = new HashMap()`

`WeakHashMap`：

```java
public static void main(String[] args) {
        WeakHashMap<Integer,String> weakHashMap = new WeakHashMap<>();
        Integer key = new Integer(1);
        weakHashMap.put(key,"测试1");
        System.out.println(weakHashMap);    //{1=测试1}
        key=null;
        System.out.println(weakHashMap);    //{1=测试1}
        System.gc();
        System.out.println(weakHashMap+"\t"+weakHashMap.size());    //{} 0
    }
```

![看完不赞，都是坏蛋](https://mmbiz.qlogo.cn/mmbiz_png/P7WuIzkp9iaVqg4wKjOlo2cia6ibCCRkwD1Rv4C3icjAnDGgz8dzqpBpjDKfE6OGiaCWI6jBR0XOfcMsJjIibRZBPicXw/0?wx_fmt=png)

> 今天的你多努力一点，明天的你就能少说一句求人的话！
>
> *我是小菜，一个和你一起学习的男人。* `💋`