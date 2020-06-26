> 大家好，我是小菜，一个渴望在互联网行业做到蔡不菜的小菜。可柔可刚，点赞则柔，白嫖则刚！
> **死鬼~看完记得给我来个三连哦！**
> > 本文主要介绍 `面试中高频出现的问题`
> > 如有需要，可以参考
> > 如有帮助，不忘 `点赞 ❥`
> > 创作不易，白嫖无义！
>
> ![](https://imgconvert.csdnimg.cn/aHR0cHM6Ly90dmF4NC5zaW5haW1nLmNuL2xhcmdlLzAwNlh6b3g0Z3kxZnFkZHRvYW1wbGczMDg4MDUyYWF1LmdpZg#pic_center)
>
> ### 一丶Java基础相关
>
>  `1）面向对象的特性有哪些`
>
> - **封装：** 封装是指将对象的实现细节隐藏起来，然后通过公共的方法来向外暴露出该对象的功能。使用封装不仅仅安全，而且可以简化操作。
> - **继承：** 继承是面向对象实现软件复用的重要手段，当子类继承父类后，子类是一种特殊的父类，能够直接或间接获得父类里的成员。**缺点：** `1.`强耦合，父类变子类也得变 `2.`破坏了封装性，实现细节对于子类都是透明的。
> - **多态：** 同一个行为具有多个不同表现形式或形态的能力。**条件：** `1.` 继承 `2.`重写 `3.`向上转型  **好处：** 可以屏蔽不同子类对象之间的实现差异。
> - **抽象：** 从特定的角度出发，从已经存在的一些事物中抽取我们所关注的 特性，行为，从而形成一个新的事物的思维过程。是一种从复杂到简洁的思维方式。
>
> `2）Java中的覆盖和重载`
>  - **覆盖：** 是指子类对父类方法的一种重写。**限制：** `1.`只能比父类抛出更少的异常 `2.`访问权限不能比父类的小 `3.` 被覆盖的方法不能是private 的。
>  - **重载：** 表示同一个类中可以有多个名称相同的方法。 **条件：** `1. `参数类型不同 `2.`参数个数不同 `3.`参数顺序不同. ***注意：*** 函数的返回值不同不能构成重载
>
> `3）抽象类和接口的区别`
>
> | 出发角度       | 抽象类                                     | 接口                                     |
> | -------------- | ------------------------------------------ | ---------------------------------------- |
> | 默认的实现方法 | 有默认的方法实现                           | 全部都是抽象的，不存在方法的实现         |
> | 构造器         | 可以有构造器                               | 不能有构造器                             |
> | 访问修饰符     | 可以有public、protected和default这些修饰符 | 默认是public，不可以使用其他修饰符       |
> | 继承方式       | 可以继承一个类和实现多个接口               | 可以继承一个或多个接口                   |
> | 添加新方法     | 可以提供默认的实现，而不需要修改原有的代码 | 添加方法后，必须修改实现该接口类中的方法 |
>
>  `4）Java 和 C++ 的区别`
>
> - 都是面向对象的语言
> - Java不提供指针来直接访问内存，比较安全
> - Java是单继承的，C++可以是多继承的
> - Java有自动内存管理机制
>
> `5）Java 是值传递还是引用传递`
>   ***Java内都是值传递***
> - **值传递：** 是针对基本类型变量，传递的是该变量的一个副本，而改变副本不会改变原有值的改变。
> - **引用传递：** 是针对对象型变量，传递的是该对象的引用地址，修改会引起原有对象的改变。
>
> `6）JDK、JRE、JVM`
> - **JDK：** 是 Java开发工具包，是Java开发环境的核心组件，并提供编译、调试和运行一个Java程序所需要的所有工具，可以执行文件和二进制文件，是一个平台特定的软件。
> - **JRE：** 是Java 运行时环境，是JVM 的实施实现，提供了运行Java 程序的平台，JRE 包含了 JVM，但是不包含 Java编译器/调试器之类的开发工具。
> - **JVM：** 是Java虚拟机，当我们运行一个程序时，JVM负责将字节码转换为特定机器代码，JVM提供了内存管理/垃圾回收和安全机制等。
>
> `7）Integer`
> ![](https://img-blog.csdnimg.cn/20200330100644654.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80MzI4NzIzOQ==,size_16,color_FFFFFF,t_70)
> 第一个和第二个的区别在于 Integer中存在缓存机制，JVM启动初期会缓存 **-128~127** 这个区间里面的所有数字，因此第一个位 true，第二个为false。第三个为 false 的原因在于使用了 new 关键字开辟了新空间 ， i 和 j 两个对象分别指向堆中的两块内存空间。
>  `8）String`
> ![](https://img-blog.csdnimg.cn/20200330101538157.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80MzI4NzIzOQ==,size_16,color_FFFFFF,t_70)
> - **情况1：** 最多创建一个String 对象，最少不创建String 对象，如果常量池中存在"test"，那么str1会直接引用，此时不会创建对象，否则会在常量池中创建 "test" 内存空间，然后再引用。
> - **情况2：** 最多创建两个String 对象，至少创建一个String 对象。new 关键字绝对会在堆空间中创建一个新的内存区域，所以至少会创建一个String对象。
>
> `9）Java 有没有 goto`
>   goto 是 Java 中的保留字，在目前版本的 Java 中没有使用。
> ` 10）final 的作用`
>
> - 被 final 修饰的类不可被继承
> - 被 final 修饰的方法不可被重写
> - 被 final 修饰的变量不可以被改变
> - 被final 修饰的方法，JVM会尝试与其关联，调高运行效率
>
> `11）final、finally和finalize的区别`
> - **final：** 可以修饰类、变量和方法。修饰类表示该类不能被继承，修饰方法表示该方法不能被重写，修饰变量表示该变量不可变。
> - **finally：** 一般作用在try-catch代码块中，在处理异常的时候，通常我们将一定要执行的代码方法放在finally代码块中，表示不管是否出现异常，该代码块都会执行，一般用来存放一些关闭资源的代码。
> - **finalize：** 是一个属于Object 类的方法，Object是所有类的父类，Java中允许使用finalize（）方法在垃圾收集器将对象从内存中清除出去之前做必要的清理工作。
>
> `12）Throwable`
>   Throwable 是Java语言中所有错误与异常的超类。包含两个子类：
> - `Error（错误）`
>   程序中无法处理的错误，表示运行应用程序中出现了严重的错误
> - `Exception（异常）`
>   程序本身可以捕获并且可以处理的异常
>   + `运行时异常`
>     Java编译器不会检查它，也就是说，当程序中可能出现这类异常时，倘若既“没有通过throws声明抛出它”，也“没有用try-catch语句捕获它”，还是会通过编译。
>   + `编译时异常`
>     Java编译器会检查它，如果程序中出现此类异常，要么通过throws进行声明抛出，要么通过try-catch进行捕获处理，否则不能通过编译。
>
> `13）常见的运行时异常`
> - NullPointException（空指针异常）
> - ClassNotCastException（类型转换异常）
> - IllegalArgumentException（非法参数异常）
> - IndexOutOfBoundException（下标越界异常）
> - ArrayStoreException（数组存储空间不够异常）
>
> `14）try-catch-finally中哪个部分可以省略`
>   catch可以省略
>
> ### 二丶集合容器相关
>
> > 集合这方面可以问的实在太多了，这一块可要好好把握住！
>
>
> ![](https://imgconvert.csdnimg.cn/aHR0cHM6Ly90dmF4NC5zaW5haW1nLmNuL2xhcmdlLzAwNlh6b3g0bHkxZnFkZGM2YXcxaWczMDhhMDU0YWF0LmdpZg#pic_center)
>  `1）集合的种类`
> ***Map接口和Collection接口是所有集合框架的父接口***
>
> - Collection：
>   + List接口
>     + ArrayList
>     + LinkedList
>     + Stack
>     + Vector
>   + Set接口
>     + HashSet
>     + TreeSet
>     + LinkedHashSet
>
> ![](https://img-blog.csdnimg.cn/20200329211931910.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80MzI4NzIzOQ==,size_16,color_FFFFFF,t_70)
>
> - Map：
>     + HashMap
>     + TreeMap
>     + Hashtable
>     + ConcurrentHashMap
>     + Properties
>
> ![](https://img-blog.csdnimg.cn/20200329212006878.png)
> ` 2）List 和 Set 的区别`
> **List：** 有序（元素存入集合的顺序和取出的顺序是一致的），可以重复，可以插入多个null元素，元素都有索引。
> **Set：** 无序（元素存入集合的顺序和取出的顺序有可能不一致），不可以重复，只允许存入一个null元素（元素的唯一性）
> ***`扩展：`*** **HashSet的底层实现**
> ![](https://img-blog.csdnimg.cn/20200329213327511.png)
>
> - HashSet实现Set接口，实际上是一个**HashMap** 实例支持。
> - 它不保证set 的迭代顺序，特别是它不保证该顺序恒久不变，此类允许使用null元素。 
> - 元素都存到HashMap键值对的**Key**上面，而Value是一个**统一的值**。
>   ![](https://img-blog.csdnimg.cn/20200329213625269.png)
>
> `3）Array 和 ArrayList 的区别`
> - 数组是固定长度的，集合是可变长度的
> - 数组可以存储基本数据类型也可以引用数据类型，集合只能存储引用数据类型
> - 数组存储的元素必须是用一个数据类型，集合存储的对象可以是不同数据类型
>
> `4）HashMap 和 HashTable的区别`
> - HashMap 继承自 **AbstractMap** 类；而 Hashtable 继承自 **Dictionary** 类
> - HashTable 是**线程安全**的，直接在方法上锁，并发度很低，虽多同时允许一个线程访问。相反HashMap效率比较高，但**线程不安全**。
> - HashTable 是**不允许**键或值为null的，HashMap的键值都**可以**是null，原因在于HashTable使用的是**安全失败机制（fail-fast）**，如果键或值为空会直接抛出异常；而HashMap在计算hash值的时候做了特殊处理如果键为空则赋值为0
>   ![](https://img-blog.csdnimg.cn/20200329214613440.png)
> - HashTable初始化的容量为**11**，HashMap初始化的容量为**16**。两者的负载因子都是**0.75**
> - HashTable的扩容机制是**2n+1**, HashMap的扩容机制是 **2n**
>
> ***`扩展：`*** **何为fail-fast**
>
> - fail-fast 是一种错误检测机制，并发情况下多个线程对集合的结构进行修改是就会触发 **fail-fast**机制，抛出 **ConcurrentModificationException**异常。
> - **原理：** 迭代器在遍历时直接访问集合中的内容，并且在遍历过程中使用一个 **modCount** 变量，遍历期间集合如果发生变化就会改变**modCount**的值，等下一个hasNext()/next()的时候就会比对 **modCount != expectedModCount** 这个判断条件，如果为true则会抛出异常，终止遍历，否则就返回遍历。
>
> `5）聊聊HashMap`
> - Java 1.8之前HashMap 是由 **数组+链表** 组合构成的数据结构。数组的长度是有限的，因此哈希会存在碰撞的情况，那么就会形成链表。如果链表长度过长就会造成查询效率过慢，时间复杂度为O(n)。
> - Java 1.8 之前采用的是 **头插法**，就是说新插入的值的时候，原来的值就会往后推一位，让新值放在头部位置，这样做的原因是因为后插入的值查找的可能性会更大一点，提高查找的效率。但是这样就会造成一个问题，在同一个位置上新元素总会被放在链表的头部位置，如果多线程put的情况下，就会改变数据的引用结构，那么就会造成**环形链表**，发生死循环！
> - Java 1.8 之后数据结构就变成了 **数组+链表+红黑树**。 当链表长度超过阈值（8）时，将链表转换为红黑树，这样大大减少了查找时间。
> - Java 1.8 之后采用的是 **尾插法**，这样扩容转移后前后链表顺序不变，保持之前节点的引用关系，就不会出现死循环的情况。
>
> `6）HashMap扩容是怎样扩容的`
>   当HashMap中的元素个数超过数组大小(数组总大小length,不是数组中个数size)*loadFactor时，就会进行数组扩容，loadFactor的默认值为0.75.HashMap不是无限扩容的，当达到了实现预定的MAXIMUM_CAPACITY，就不再进行扩容。
>   ![](https://img-blog.csdnimg.cn/20200329225108531.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80MzI4NzIzOQ==,size_16,color_FFFFFF,t_70)
> `7）HashMap 的长度为什么都是2的N次幂的大小`
> - 不会造成浪费，不随机分布问题。首先算得key得hashcode值，然后跟数组的长度-1做一次“与”运算（&）。如果length不为2的幂，那么length-1的2进制就会变成1110，在h为随机数的情况下，和1110做&操做，尾数永远是0，碰撞的几率大。
> - 提高性能。length为2的幂，那么扩容后元素新的位置就不需要重新计算，只需要看看当前值的hash&(扩容后的容量-1)是1还是0就好了，是0的话索引没变，是1的话索引变成“原索引+oldCap”
>
> `8）HashMap存在线程安全问题，那是怎么处理这种情况的？`
> - 使用 Collections.synchronizedMap(new HashMap<>())
> - HashTable
> - ConcurrentHashMap
>
> ***出于线程并发度的考虑，一般会选择ConcurrentHashMap，他的性能和效率都明显高于前两者***
>
> `9）ConcurrentHashMap`
>
> - Java 1.8 之前ConcurrentHashMap底层使用的是 **数组+链表** 
>   ![](https://ae01.alicdn.com/kf/H60dbfa5d139e4f6e815405799067ee23Z.png)
>   由图得知，底层的数据结构是由 **Segment 数组**，**HashEntry组成**，和HashMap一样，仍然是数据加链表的形式。HashEntry 和 HashMap 差不多，不同点是使用了 Volatile 修饰了 Value，在访问数据的时候不用加锁，只能保证了弱一致性。
> - ConcurrentHashMap 采用了 **分段锁** 技术，其中 **Segment** 继承于 **ReentrantLock**。不会像HashTable 那样直接在方法上加锁，太简单粗暴了...，如果容量大小是16，那个他的并发度就是16，相当于效率提高了16倍。
>   ![](https://ae01.alicdn.com/kf/Hd27e95a715dc4a7081bd3efad350615a6.png)
>   ![](https://ae01.alicdn.com/kf/H4ff2b66bb8e74308b1df27e84fe818f1i.png)
> - Java 1.8之后 ConcurrentHashMap 采用的是 **Node + CAS + Synchronized** 来保证并发安全性的，并且也引入了红黑树。跟HashMap的实现是一样的，当链表长度大于 7 的时候转换为红黑数。
>   ![](https://img-blog.csdnimg.cn/202003300923079.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80MzI4NzIzOQ==,size_16,color_FFFFFF,t_70)
> - ConcurrentHashMap 进行 put 的步骤：
>   + 如果相应位置的Node还没有初始化，则调用CAS插入相应的数据
>   + 如果相应位置的Node不为空，则对该节点加synchronized锁，遍历链表更新节点或插入新节点
>   + 如果该节点是TreeBin类型的节点，说明是红黑树结构，则通过putTreeVal方法往红黑树中插入节点；如果binCount不为0，说明put操作对数据产生了影响，如果当前链表的个数达到8个，则通过treeifyBin方法转化为红黑树，如果oldVal不为空，说明是一次更新操作，没有对元素个数产生影响，则直接返回旧值
>     ![](https://img-blog.csdnimg.cn/20200329230746593.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80MzI4NzIzOQ==,size_16,color_FFFFFF,t_70)
>
> `10）迭代器 Iterator `
>     Iterator 接口提供遍历任何 Collection 的接口。我们可以从一个 Collection 中使用迭代器方法来获取迭代器实例。迭代器取代了 Java 集合框架中的 Enumeration，迭代器允许调用者在迭代过程中移除元素。
> `11）Iterator 和 ListIterator 的区别`
> - Iterator 可以遍历 Set 和 List 集合，而 ListIterator 只能遍历 List。
> - Iterator 只能单向遍历，而 ListIterator 可以双向遍历（向前/后遍历）。
> - ListIterator 实现 Iterator 接口，然后添加了一些额外的功能，比如添加一个元素、替换一个元素、获取前面或后面元素的索引位置。
>
> `12）实现数组和 List 之间的转换`
> - 数组转 List：使用 Arrays. asList(array) 
> - List 转数组：使用 List 自带的 toArray() 
>
> `13）ArrayList 和 LinkedList 的区别`
> - ArrayList 底层是**动态数组**数据结构，LinkedList 底层是**双向链表**数据结构。
> - ArrayList 比 LinkedList 在随机访问的时候效率要高，因为 LinkedList 是线性的数据存储方式，所以需要移动指针从前往后依次查找。
> - 在非首尾的增加和删除操作，LinkedList 要比 ArrayList 效率要高，因为 ArrayList 增删操作要影响数组内的其他数据的下标。
> - LinkedList 比 ArrayList 更占内存，因为 LinkedList 的节点除了存储数据，还存储了两个引用，一个指向前一个元素，一个指向后一个元素。
>
> `14）ArrayList 和 Vector的区别`
> - Vector 使用了 Synchronized 来实现线程同步，是线程安全的，而 ArrayList 是非线程安全的。
> - ArrayList 在性能方面要优于 Vector。
> - ArrayList 和 Vector 都会根据实际的需要动态的调整容量，只不过在 Vector 扩容每次会增加 1 倍，而 ArrayList 只会增加 50%。
>
> `15）hashCode（）与equals（）的相关规定`
>
> - 两个对象相等，那么他们的hashCode也一定是相同的
> - 两个对象相等，那么equals方法返回的是true
> - 两个对象有相同的hashCode值，那他们也不一定相等
> - 如果重写了equals，那么它们的hashCode方法也必须被重写
>
> `16）极高并发下HashTable和ConcurrentHashMap哪个性能更好，为什么`
> - HashTable使用一把锁处理并发问题，当有多个线程访问时，需要多个线程竞争一把锁，导致阻塞
> - ConcurrentHashMap则使用分段，相当于把一个HashMap分成多个，然后每个部分分配一把锁，这样就可以支持多线程访问
>
> `17）HashMap在高并发下如果没有处理线程安全会有怎样的安全隐患`
> - 多线程put时可能会导致get无限循环，具体表现为CPU使用率100%
> - 多线程put时可能导致元素丢失
>
> `18）BlockingQueue是什么`
>   BlockingQueue是**JUC**包下的，是一个阻塞队列，在进行检索或移除一个元素的时候，它会等待队列变为非空；当在添加一个元素时，它会等待队列中的可用空间。BlockingQueue接口是Java集合框架的一部分，主要用于实现生产者-消费者模式。我们不需要担心等待生产者有可用的空间，或消费者有可用的对象，因为它都在BlockingQueue的实现类中被处理了。Java提供了集中BlockingQueue的实现，比如**ArrayBlockingQueue**、**LinkedBlockingQueue**、**PriorityBlockingQueue**、**SynchronousQueue**等。
>
> `19）ArrayList在循环过程中删除，会不会出问题，为什么。`
>   会出现问题。因为删除是利用复制和移动的方式，如果集合的值为{"11","22","22"},要删除22的话结果为{"11","22"}，第三个22就会前移，这样能循环过程中，就访问不到第三个22。
>   `解决：`
>  - 反向删除  
>  - 利用迭代器删除（多线程会报错）
>
> ### 三、Spring相关
>
>  `1）什么是Spring`
>
> - 是Java企业级应用的开源开发框架
> - 简化Java企业级应用开发，提高开发效率
> - 基于POJO为基础的编程模型促进良好的编程习惯
>
> `2）Spring的优缺点`
>   ***优点：***
> - **轻量级：** Spring从大小和透明度来说都是轻量级的
> - **控制反转（IOC）：** Spring通过控制反转实现了松散耦合，对象给出它们的依赖，而不是通过创建或查找依赖的对象
> - **面向切面（AOP）：** 支持面向切面的编程，实现应用业务逻辑和系统服务相分开
> - **容器：** Spring包含和管理应用中对象的生命周期和配置
> - **声明式事务管理：** 只需要通过配置就可以对事务进行管理，无需手动编程
>
> ***缺点：***
> - Spring 依赖反射，会影响性能
> - 使用门槛升高，入门Spring需要较长的时间
>
> `3）Spring的核心模块`
> - Spring core
> - Spring context
> - Spring bean
> - Spring orm
> - Spring aop
> - Spring web
> - Spring jdbc
> - Spring test
>
> `4）什么是Spring配置文件`
>   Spring配置文件是XML 文件。该文件主要包含类信息，它描述了这些类是如何配置以及相互引入的。但是XML配置文件冗长且更加干净，如果没有正确规划和编写，那么在大项目中管理会变得非常困难。
>
> `5）Spring框架中用到的设计模式`
> - **工厂模式：** BeanFactory 就是简单工厂模式的体现，用来创建对象的示例
> - **单例模式：** Bean默认为单例模式
> - **代理模式：** Spring的AOP功能用到了JDK的动态代理和CGLIB字节码生成技术
> - **模板方法：** 用来解决代码重复问题。比如RestTemplate，JmsTemplate，JpaTemplate
> - **观察者模式：** 当一个对象的状态发生改变时，所有依赖于它的对象都会得到通知并更新
>
> `6）IOC是什么`
>   IOC（控制反转），它把传统上由程序代码直接操控的对象的调用权交给容器，通过容器来实现对象组件的装配和管理。所谓“控制反转”概念就是对组件对象控制权的转移，从程序代码本身转移到了外部容器。Spring IOC负责创建对象，管理对象（通过依赖注入（DI）装配对象，配置对象，并且管理这些对象的整个生命周期）
>
> `7）IOC的优点`
> - IOC 或 依赖注入把应用的代码量降到最低。
> - 它使应用容易测试，单元测试不再需要单例和JNDI查找机制。
> - 最小的代价和最小的侵入性使松散耦合得以实现。
> - IOC容器支持加载服务时的饿汉式初始化和懒加载。
>
> `8）动态代理的两种方式以及区别`
> - **JDK动态代理：** 利用反射机制生成一个实现代理接口的匿名类，在调用具体方法前调用InvokeHandler来处理。如果被代理的类没有实现接口则无法实现。
> - **CGlib动态代理：** 针对类实现代理，对指定的类生成一个子类，并覆盖其中的方法，这种通过继承类的实现方式，不能代理final修饰的类。
>
> ***jdk动态代理的方式创建代理对象效率较高，执行效率较低，cglib创建效率较低，执行效率高***
>
> `9）可以通过多少种方法完成依赖注入`
>
> - 构造函数注入
> - setter 注入
> - 接口注入
>
> | 构造函数注入               | setter注入                 |
> | -------------------------- | -------------------------- |
> | 没有部分注入               | 有部分注入                 |
> | 不会覆盖setter属性         | 会覆盖setter属性           |
> | 任意修改都会创建一个新实例 | 任意修改不会创建一个新实例 |
> | 适用于设置很多属性         | 适用于设置少量属性         |
>
>
> `10）BeanFactory 和 ApplicationContext 的区别`
>
> | BEANFactory          | ApplicationContext |
> | -------------------- | ------------------ |
> | 懒加载               | 即时加载           |
> | 不支持国际化         | 支持国际化         |
> | 不支持基于依赖的注解 | 支持基于依赖的注解 |
>
>
> `11）ApplicationContext通常的实现是什么`
>
> - FileSystemXmlApplicationContext 
> - ClassPathXmlApplicationContext
> - WebXmlApplicationContext
>
> `12）Spring 支持的作用域`
> - **singleton：**  bean在每个Spring ioc 容器中只有一个实例。
> - **prototype：** 一个bean的定义可以有多个实例。
> - **request：** 每次http请求都会创建一个bean，该作用域仅在基于web的Spring ApplicationContext情形下有效。
> - **session：** 在一个HTTP Session中，一个bean定义对应一个实例。该作用域仅在基于web的Spring ApplicationContext情形下有效。
> - **global-session：** 在一个全局的HTTP Session中，一个bean定义对应一个实例。该作用域仅在基于web的Spring ApplicationContext情形下有效。
>
> `13）spring 自动装配 bean 有哪些方式`
> - **no：** 默认的方式是不进行自动装配的，通过手工设置ref属性来进行装配bean
> - **byName** 通过bean的名称进行自动装配，如果一个bean的 property 与另一bean 的name 相同，就进行自动装配
> - **byType：** 通过参数的数据类型进行自动装配
> - **constructor：** 利用构造函数进行装配，并且构造函数的参数通过byType进行装配
> - **autodetect：** 自动探测，如果有构造方法，通过 construct的方式自动装配，否则使用 byType的方式自动装配
>
> `14）怎样开启注解装配`
>   在Spring配置文件中配置 `<context:annotation-config/> `元素
>
> `15）@Autowired 和 @Resource 的区别`
> - **@Autowired：** 可用于构造函数、成员变量、Setter方法。默认是按照类型装配注入的，默认情况下它要求依赖对象必须存在（可以设置它required属性为false）
> - **@Resource：** 默认是按照名称来装配注入的，只有当找不到与名称匹配的bean才会按照类型来装配注入
>
> `16）spring 事务实现方式有哪些`
> - **编程式事务管理：** 你可以通过编程的方式管理事务，给你带来极大的灵活性，但是难维护。
> - **声明式事务管理：** 你可以将业务代码和事务管理分离，你只需用注解和XML配置来管理事务。
>
> `17）Spring的事务传播行为`
> - **REQUIRED：** 支持当前事务，当前存在事务就加入该事务，如果当前没有事务就创建一个事务
> - **SUPPORTS：** 支持当前事务，当前存在事务就加入该事务，如果当前没有事务就以非事务执行
> - **MANDATORY：** 支持当前事务，当前存在事务就加入该事务，如果当前没有事务就抛出异常
> - **REQUIRES_NEW：** 新建事务，无论当前有没有存在事务都会创建新事务
> - **NOT_SUPPORTED：** 以非事务方式执行，如果当前存在事务就把当前事务挂起
> - **NEVER：** 以非事务方式执行，如果当前存在事务就抛出异常
> - **NESTED：** 如果当前存在事务，则在嵌套事务内执行，如果当前没有事务，则按REQUIRED属性执行
>
> `18）spring 的事务隔离`
> - **READ_UNCOMMITTED：** 未提交读，最低隔离级别，事务未提交前，就可被其他事务读取（会出现幻读、脏读、不可重复读）
> - **READ_COMMITTED：** 提交读，一个事务提交后才能被其他事务读取到（会造成幻读、不可重复读），SQL Server的默认级别
> - **REPEATABLE_READ：** 可重复读，保证多次读取同一个数据时，其值都和事务开始时候的内容是一致的，禁止读取别的事务未提交的数据（会造成幻读），MySQL的默认级别
> - **SERIALIZABLE：** 序列化，代价最高最可靠的隔离级别，该隔离级别能防止脏读，不可重复读，幻读
>
> `脏读：` 表示一个事务能够读取另一个事务中还未提交的数据，比如：某个事务尝试插入记录A，此时该事务还未提交，然后另一个事务尝试读取到了记录A
> `不可重复读：` 是指一个事务内，多次读同一个数据，但是读出来的结果是不一样的
> `幻读：` 指同一个事务内多次查询返回的结果集不一样，比如：另外一个事务新增或删除第一个事务结果集里面的数据，同一个记录的数据内容别修改了，所有数据行的记录就变多或者变少了。
> **不可重复读一般是在修改数据的情况下，幻读一般是新增或者删除的情况下**
>
> `19）Spring AOP and AspectJ AOP 有什么区别`
> - Spring AOP 是为动态代理，AspectJ是为静态代理
> - Spring AOP使用的动态代理，所谓的动态代理就是说AOP框架不会去修改字节码，而是每次运行时在内存中临时为方法生成一个AOP对象，这个AOP对象包含了目标对象的全部方法，并且在特定的切点做了增强处理，并回调原对象的方法。
> - AspectJ是静态代理的增强，所谓静态代理，就是AOP框架会在编译阶段生成AOP代理类，因此也称为编译时增强，他会在编译阶段将AspectJ(切面)织入到Java字节码中，运行的时候就是增强之后的AOP对象。
>
> `20）Spring AOP里面的几个名词`
> - **切面（Aspect）：** 切面是通知和切点的结合。通知和切点共同定义了切面的全部内容。 在Spring AOP中，切面可以使用通用类（基于模式的风格） 或者在普通类中以 @AspectJ 注解来实现。
> - **连接点（Join point）：** 指方法，在Spring AOP中，一个连接点 总是 代表一个方法的执行。 应用可能有数以千计的时机应用通知。这些时机被称为连接点。连接点是在应用执行过程中能够插入切面的一个点。这个点可以是调用方法时、抛出异常时、甚至修改一个字段时。切面代码可以利用这些点插入到应用的正常流程之中，并添加新的行为。
> - **通知（Advice）：** 在AOP术语中，切面的工作被称为通知。
> - **切入点（Pointcut）：** 切点的定义会匹配通知所要织入的一个或多个连接点。我们通常使用明确的类和方法名称，或是利用正则表达式定义所匹配的类和方法名称来指定这些切点。
> - **引入（Introduction）：** 引入允许我们向现有类添加新方法或属性。
> - **目标对象（Target Object）：** 被一个或者多个切面（aspect）所通知（advise）的对象。它通常是一个代理对象。也有人把它叫做 被通知（adviced） 对象。 既然Spring AOP是通过运行时代理实现的，这个对象永远是一个 被代理（proxied） 对象。
> - **织入（Weaving）：** 织入是把切面应用到目标对象并创建新的代理对象的过程。在目标对象的生命周期里有多少个点可以进行织入：
>     + 编译期：切面在目标类编译时被织入。AspectJ的织入编译器是以这种方式织入切面的。
>     + 类加载期：切面在目标类加载到JVM时被织入。需要特殊的类加载器，它可以在目标类被引入应用之前增强该目标类的字节码。AspectJ5的加载时织入就支持以这种方式织入切面。
>     + 运行期：切面在应用运行的某个时刻被织入。一般情况下，在织入切面时，AOP容器会为目标对象动态地创建一个代理对象。SpringAOP就是以这种方式织入切面。
>
> `21）Spring通知有哪些类型`
> - **前置通知（Before）：** 在目标方法被调用之前调用通知功能；
> - **后置通知（After）：** 在目标方法完成之后调用通知，此时不会关心方法的输出是什么；
> - **返回通知（After-returning ）：** 在目标方法成功执行之后调用通知；
> - **异常通知（After-throwing）：** 在目标方法抛出异常后调用通知；
> - **环绕通知（Around）：** 通知包裹了被通知的方法，在被通知的方法调用之前和调用之后执行自定义的行为。
>
> `22）spring bean 容器的生命周期是什么样的`
> - Spring 容器根据配置中的 bean 定义中实例化 bean。
> - Spring 使用依赖注入填充所有属性，如 bean 中所定义的配置。
> - 如果 bean 实现 BeanNameAware 接口，则工厂通过传递 bean 的 ID 来调用 setBeanName()。
> - 如果 bean 实现 BeanFactoryAware 接口，工厂通过传递自身的实例来调用 setBeanFactory()。
> - 如果存在与 bean 关联的任何 BeanPostProcessors，则调用 preProcessBeforeInitialization() 方法。
> - 如果为 bean 指定了 init 方法（<bean> 的 init-method 属性），那么将调用它。
> - 最后，如果存在与 bean 关联的任何 BeanPostProcessors，则将调用 postProcessAfterInitialization() 方法。
> - 如果 bean 实现 DisposableBean 接口，当 spring 容器关闭时，会调用 destory()。
> - 如果为 bean 指定了 destroy 方法（<bean> 的 destroy-method 属性），那么将调用它。
>
> `23） DispatcherServlet 的工作流程`
>   ![](https://img-blog.csdnimg.cn/20200330225859706.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80MzI4NzIzOQ==,size_16,color_FFFFFF,t_70)
>
> 1. 用户发请求-->DispatcherServlet，前端控制器收到请求后自己不进行处理，而是委托给其他的解析器进行处理，作为统一访问点，进行全局的流程控制。
> 2. DispatcherServlet-->HandlerMapping，HandlerMapping将会把请求映射为HandlerExecutionChain对象（包含一个Handler处理器,多个HandlerInterceptor拦截器)。
> 3. DispatcherServlet-->HandlerAdapter,HandlerAdapter将会把处理器包装为适配器，从而支持多种类型的处理器。
> 4. HandlerAdapter-->处理器功能处理方法的调用，HandlerAdapter将会根据适配的结果调用真正的处理器的功能处理方法，完成功能处理，并返回一个ModelAndView对象(包含模型数据，逻辑视图名)
> 5. ModelAndView的逻辑视图名-->ViewResolver，ViewResoler将把逻辑视图名解析为具体的View。
> 6. View-->渲染，View会根据传进来的Model模型数据进行渲染，此处的Model实际是一个Map数据结构
> 7. 返回控制权给DispatcherServlet，由DispatcherServlet返回响应给用户。
>
> ![看完不赞，都是坏蛋](https://imgconvert.csdnimg.cn/aHR0cHM6Ly93d3cuNTJkb3V0dS5jbi9zdGF0aWMvdGVtcC9waWMvOWJkNjhkMTUwZjA3ODdjNTYwYTQzOWRhMzU5YTU4MGEucG5n?x-oss-process=image/format,png#pic_center)
>
> > 本文较长，覆盖范围较广，但深度不深，因此***先纵览斩offer，再融会而贯通***
> > 今天的你多努力一点，明天的你就能少说一句求人的话！
> > `很久很久之前，有个传说，据说：`
> > **看完不赞，都是坏蛋**