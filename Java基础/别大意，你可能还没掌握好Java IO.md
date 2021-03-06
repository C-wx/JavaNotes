大家好，我是小菜，一个渴望在互联网行业做到蔡不菜的小菜。可柔可刚，点赞则柔，白嫖则刚！
**死鬼~看完记得给我来个三连哦！**


![](https://gitee.com/cbuc/picture/raw/master/typora/20210222224137.jpeg)


>本文主要介绍 `Java中的I/O系统`
>
>如有需要，可以参考
>
>如有帮助，不忘 **点赞** ❥
>
>微信公众号已开启，**小菜良记**，没关注的同学们记得关注哦！

前言：

> 对程序语言的设计者来说，创建一个好的输入/输出 (I/O) 系统是一项艰难的任务

Java IO：即 Java 输入/输出系统。大部分程序都需要处理一些输入，并由输入产生一些输出，因此Java为我们提供了 `java.io` 包

作为一个合格的程序开发者，说到 `IO` 我们并不会陌生，JAVA IO 系统的知识体系如下：

![](https://gitee.com/cbuc/picture/raw/master/typora/20210222231050.png)

看完以上的图，才会恍然，原来 `Java.io` 包中为我们提供了这么多支持。而我们恍然的同时也不必感到惊慌，俗话说**万变不离其宗**，我们只需要根据源头进行扩展，相信就可以很好的掌握`IO`知识体系。

### File类

读写操作少不了与文件（`File`）打交道，因此我们想要掌握好 `IO` 流，不妨先从文件入手。

文件（`File`）这个词即非**单数**也非**复数**，它既能代表一个特殊的文件，又能表示一个目录下的文件集。

#### 列表

`File` 如果表示的是一个目录下的文件集的时候，我们想要得到一个目录可以怎么做？

![](https://gitee.com/cbuc/picture/raw/master/typora/20210223220844.png)

`File`已经为我们准备好了 **API**，根据返回值类型，我们不难猜到每个 API 方法的用处。

已知我们 D 盘目录下有个 `TestFile` 文件夹，该文件夹下有以下文件：

![](https://gitee.com/cbuc/picture/raw/master/typora/20210223221210.png)

##### 名称列表

如果我们想要获取指定目录下的名称列表，我们可以使用这两个API：

- `list()`
- `list(FilenameFilter filter)`

![](https://gitee.com/cbuc/picture/raw/master/typora/20210223221915.png)

不带参数的 `list()` 方法默认是列出指定目录下的所有文件名称。如果我们想要指定名称的目录名称列表我们便可以使用另一个方法：

![](https://gitee.com/cbuc/picture/raw/master/typora/20210224223916.png)

我们期望获取带有`test`关键字的文件名称，而结果也如我们所愿。 

##### 文件列表

有时候我们的很多操作不单单针对于某个文件，而是在整个文件集上做操作。要产生这个文件集，那我们就需要借助 `File` 的另外API方法了：

- `listFiles()`

- `listFiles(FilenameFilter filter)`

- `listFiles(FileFilter filter)`

有了以上经验，我们不难猜到 `listFiles()` 的作用便是列出所有的文件列表：

![](https://gitee.com/cbuc/picture/raw/master/typora/20210225231755.png)

图中我们已经获取到了文件集，该方法会返回的同样是一个数组，不过是一个 `File`类型的数组。

聪明的你肯定也已经知道了如果获取带指定关键字的文件集
![](https://gitee.com/cbuc/picture/raw/master/typora/20210225232318.png)

与上述列出文件名称如出一辙，真是个小机灵鬼~

但是`listFiles(FileFilter filter)` 这个方法传递的参数与上有何异？我们不妨一试：

![](https://gitee.com/cbuc/picture/raw/master/typora/20210225232641.png)

同样是一个接口，同样需要重写 `accept()` 方法，但是这个方法只有一个 `File` 的参数。因此这两个参数都是用于文件过滤的，功能大同小异~

#### 目录工具

##### 创建目录

`File` 类的好用之处不仅能让你对于已有目录文件的操作，还能让你无中生有！

文件的特性无外乎：名称，大小，最后修改日期，可读/写，类型等

![](https://gitee.com/cbuc/picture/raw/master/typora/20210225234507.png)

那么我们通过 API 也理应能够获得：

![](https://gitee.com/cbuc/picture/raw/master/typora/20210225235121.png)

以上什么类型都获取到了，唯独少了个类型，虽然说 `File` 没有提供直接获取类型的方法，但是我们可以通过获取文件的全名，然后通过裁剪获取到文件的后缀，便可获取到文件的类型：

![](https://gitee.com/cbuc/picture/raw/master/typora/20210225235727.png)

转手一操作，自给自足也能获取文件类型，真是个小机灵鬼~

以上我们都是基于文件目录存在的情况下操作的，那么如果我们想要操作的文件目录不存在。或者由于我们的粗心将文件目录名称输入错了，那么将会发生什么情况，操作进程是否能够正常进行？

![](https://gitee.com/cbuc/picture/raw/master/typora/20210226232700.png)

结果便是抛出异常了，的确抛出异常才是正常的现象，针对一个不存在的文件目录进行操作岂不是瞎胡闹

因此在我们不确定文件目录是否存在的情况下我们可以这样操作：

![](https://gitee.com/cbuc/picture/raw/master/typora/20210226235352.png)

在图中我们可以看到两个我们没见过的API方法，分别是 `exists()` 和 `mkdirs()`.

- `exists()`： 用于验证文件目录是否存在
- `mkdirs()`： 用于创建目录

通过以上先验证后操作，我们成功避免了异常。这里需要了解的是，除了 `mkdirs()` 可以创建目录之外，还有一个 `mkdir()` 也是可以创建目录的，这两个方法除了少了一个 `s` 之外，还有其他区别呢？

- `mkdir()`: 只能创建一层目录
- `mkdirs()`: 可以创建多层目录

![](https://gitee.com/cbuc/picture/raw/master/typora/20210226235925.png)

我们目前的场景是 `Test ` 目录不存在，`dir01` 这个目录自然也不存在，那么这个时候就得创建两层目录。但是我们使用 `mkdir()` 这个方法是行不通的，它无法创建。因此遇到这种情况我们应当使用 `mkdirs()`这个方法。

##### File类型

File 可以是一个文件也可以是一个文件集，文件集中可包含一个**文件**或者是一个文件夹，如果我们想要针对一个文件做肚读写操作，却无意对一个文件夹进行了操作，那就尴尬了，因此我们可以借助 `isDirectory` 来判断是否是文件夹：

![](https://gitee.com/cbuc/picture/raw/master/typora/20210227142813.png)

### 输入与输出

上面我们谈到 `File` 类的基本操作，接下来我们便进入了I/O模块。

输入和输出我们经常使用 **流** 这个概念，如输入流和输出流。这是个抽象的概念，代表任何与能力产出数据的数据源对象或是有能力接受数据的接收端对象。`流` 屏蔽了实际 I/O 设备找那个处理数据的细节！

`I/O` 可以分为 **输入** 和 **输出** 两部分。

输入流中又分为 **字节输入流（InputStream）** 和 **字符输入流（Reader）**，任何由 `InputStream` 或 `Reader` 派生而来的类都实现了 `read()` 这个方法，用来读取单个字节或字节数组。

输出流中又分为 **字节输出流（OutputStream）** 和 **字符输出流（Writer）**，任何由 `OutputStream` 或 `Writer` 派生而来的类都实现了 `write()` 这个方法，用来写入单个字节或字节数组。

因此我们可以看出 Java 中的规定：与输入有关的所有类都应该从 **InputStream** 继承，与输出有关的所有类都应该从 **OutputStream** 继承

#### InputStream

> 用来表示那些从不同数据源产生输入的类

那些不同数据源具体又是哪些？常见的有：**1.** 字节数组 **2.** String 对象 **3.** 文件 **4.** “管道”（一端输入，一端输出）

其中每一种数据源都有对应的 **InputStream** 子类可以操作：

|           类            |                             功能                             |
| :---------------------: | :----------------------------------------------------------: |
|  ByteArrayInputStream   |           允许将内存的缓冲区当作 InputStream 使用            |
| StringBufferInputStream |            **已废弃**，将String转换成 InputStream            |
|     FileInputStream     |                     用于从文件中读取信息                     |
|    PipedInputStream     | 产生用于写入相关 PipedOutPutStream的数据，实现 **管道化** 的概念 |
|   SequenceInputStream   |     将两个或多个 InputStream 对象转换成一个 InputStream      |
|    FilterInputStream    | 抽象类，作为`装饰器`的接口，为其他InputStream 提供有用的功能 |

#### OutPutStream

该类别的类决定了输出所要去往的目标：**1.** 字节数组 **2.** 文件 **3.** 管道

常见的 **OutPutStream** 子类有：

|          类           |                             功能                             |
| :-------------------: | :----------------------------------------------------------: |
| ByteArrayOutputStream |  在内存中创建缓冲区，所有送往 “流” 的数据都要放置在此缓冲区  |
|   FileOutputStream    |                      用于将信息写入文件                      |
|   PipedOutputStream   | 任何写入其中的信息都会自动作为相关 PipedInputStream 的输出，实现 **管道化** 的概念 |
|  FilterOutputStream   | 抽象类，作为`装饰器` 的接口，为其他 OutputStream 提供有用的功能 |

#### 装饰器

我们通过以上的认识，都看到了不管是输入流还是输出流，其中都有一个抽象类`FilterInputStream` 和 `FilterOutputStream`，这些类相当于是一个装饰器。在Java 中I/O 操作需要多种不同的功能组合，而这个便是使用装饰器模式的理由所在。

何为装饰器？装饰器必须具有和它所装饰对象的相同接口，但它也可以扩展接口，它可以给我们提供了相当多的灵活性，但它也会增加代码的复杂性。

`FilterInputStream` 和`FilterOutputStream` 是用来提供装饰器类接口以控制特定输入流（**InputStream**）和输出流（**OutputStream**）的两个类。

##### FilterInputStream

`InputStream` 作为字节输入流，那么读取的数据理应用字节数组接收，如下：

![](https://gitee.com/cbuc/picture/raw/master/typora/20210227131715.png)

我们得借助一个 `byte` 数组来接收读取到值，然后转为字符串类型。

既然我们有了装饰器`FilterInputStream` ，那是否可以借助装饰器的子类来帮我们实现读操作呢？我们先来看下常用的`FilterInputStream`子类有哪些：

|         类          |                             功能                             |
| :-----------------: | :----------------------------------------------------------: |
|   DataInputStream   | 与 DataOutputStream 搭配使用，我们可以按照可移植方式从流读取基本数据类型（int，char，long） |
| BufferedInputStream |   使用它可以防止每次读取时都得进行实际写操作。代表"缓冲区"   |

其中`DataInputStream`允许我们读取不同的基本数据类型数据以及String对象，搭配相应的`DataOutputStream`，我们就可以通过数据"**流**" 将基本类型的数据从一个地方迁移到另一个地方。

![](https://gitee.com/cbuc/picture/raw/master/typora/20210227151305.png)

然后说到`BufferedInputStream` 之前我们先看一组测试代码：

![](https://gitee.com/cbuc/picture/raw/master/typora/20210227151540.png)

现有三个文本文件，其中`test01.txt` 大小约为 **610M**，`test02/test03`均为空文本文件

那我们现在分别用普通的 `InputStream + OutputStream` 和装饰后的`BufferedInputStream + BufferedOutputStream` 写入文本

普通组合：

![](https://gitee.com/cbuc/picture/raw/master/typora/20210227155257.png)

缓冲区组合：

![](https://gitee.com/cbuc/picture/raw/master/typora/20210227155714.png)

可以看出两种方式的分别耗时，`4864 ms` 和 `1275 ms`。 使用普通组合相当于是缓冲区的 4 倍之久，如果文件更大的话，这个差异可是惊人的！惊讶的同时肯定也有所诧异，这是为什么呢？

如果用`read()`方法读取一个文件，每读取一个字节就要访问一次硬盘，这种读取的方式效率是很低的。即便使用`read(byte b[])`方法一次读取多个字节，当读取的文件较大时，也会频繁的对磁盘操作。

而`BufferedInputStream`的API文档解释为：在创建`BufferedInputStream`时，会创建一个内部缓冲区数组。在读取流中的字节时，可根据需要从包含的输入流再次填充该内部缓冲区，一次填充多个字节。也就是说，Buffered类初始化时会创建一个较大的byte数组，一次性从底层输入流中读取多个字节来填充byte数组，当程序读取一个或多个字节时，可直接从byte数组中获取，当内存中的byte读取完后，会再次用底层输入流填充缓冲区数组。因此这种从直接内存中读取数据的方式要比每次都访问磁盘的效率高很多。

![](https://gitee.com/cbuc/picture/raw/master/typora/20210227161153.png)

`BufferedInputStream/BufferedOutputStream`不直接操作数据源，而是对其他字节流进行包装，它们是 **处理流**。

程序把数据保存到 `BufferedOutputStream` 缓冲区中，并没有立即保存到文件里，缓冲区中的数组在以下情况会保存到文件中：

- 缓冲区已满
- `flush()` 清空缓冲区
- `close()` 关闭流

##### FilterOutputStream

`OutputStream` 的基本操作如下：

![](https://gitee.com/cbuc/picture/raw/master/typora/20210227170416.png)

通过调用`write()` 方法便可将值写入文件中，这里有两点需要注意：

- 写入文档默认是覆盖的方式

按我们理解调用两次该方法，文本文件中的内容应该是两行 `公众号：小菜良记`，但是实际上只用一行，这是因为后面写入的内容会覆盖前面已经存在的内容，解决方法便是在构造函数的时候加上`append = true`

![](https://gitee.com/cbuc/picture/raw/master/typora/20210227170634.png)

- 写入与读取的区别在于，读取的时候如果文件不存在会报错，但是写入的时候如果文件不存在，会默认帮你创建文件

`OutputStream`中同样存在装饰器类`FilterOutputStream`，以下便是装饰器类的常用子类：

|          类          |                             功能                             |
| :------------------: | :----------------------------------------------------------: |
|   DataOutputStream   | 与DATAInputStream搭配使用，可以按照可移植方式向流中写入基本类型数据（int，char，long等） |
| BufferedOutputStream | 使用它避免每次发送数据时都要进行实际的写操作，代表 **使用缓冲区**，可以调用`flush`清空缓冲区 |

`DataOutputStream` 和 `BufferedOutputStream` 在上面已经讲到，这里就不再赘述。

#### Reader 与 Writer

在 **Java 1.1** 的时候，对基本的I/O流类库进行了重大的修改，增添了 `Reader` 和 `Writer` 两个类。在我之前局限的认知中，会误以为这两个类的出现是为了替代 `InputStream` 和 `OutputStream` ，但事实也并非与我局限认知所似。

`InputStream` 和 `OutputStream`  是以面向字节的形式为 I/O 提供功能，而 `Reader` 和 `Writer`是提供兼容 `Unicode`于面向字符的形式为 I/O 提供功能

这两者共存，并提供了**适配器** - `InputStreamReader` 和 `OutputStreamWriter`

- `InputStreamReader` 可以把 **InputStream** 转换为 **Reader**
- `OutputStreamWriter` 可以把 **OutputStream** 转换为 **Writer**

这两者虽然不能说完全相同，但也是极为相似，对照如下：

|        字节流         |     字符流      |
| :-------------------: | :-------------: |
|      InputStream      |     Reader      |
|     OutputStream      |     Writer      |
|    FileInputStream    |   FileReader    |
|   FileOutputStream    |   FileWriter    |
| ByteArrayInputStream  | CharArrayReader |
| ByteArrayOutputStream | CharArrayWriter |
|   PipedInputStream    |   PipedReader   |
|   PipedOutputStream   |   PipedWriter   |

甚至装饰者类都几乎相似：

|        字节流        |     字符流     |
| :------------------: | :------------: |
|  FilterInputStream   |  FilterReader  |
|  FilterOutputStream  |  FilterWriter  |
| BufferedInputStream  | BufferedReader |
| BufferedOutputStream | BufferedWriter |
|     PrintStream      |  PrintWriter   |

使用**Reader** 和 **Writer** 的方式也十分简单：

![](https://gitee.com/cbuc/picture/raw/master/typora/20210301210824.png)

我们顺便看下装饰器的使用`BufferedReader` 与 `BufferedWriter`

![](https://gitee.com/cbuc/picture/raw/master/typora/20210301214803.png)

#### RandomAccessFile

**RandomAccessFile** 适用于由大小已知的记录组成的文件，所以我们可以使用 `seek()` 将记录从一处转移到另一处，然后读取或者修改记录。文件中记录的大小不一定都相同，只要我们能够确定哪些记录有多大以及它们在文件中的位置即可。

![](https://gitee.com/cbuc/picture/raw/master/typora/20210301211751.png)

我们从图中可以看到 **RandomAccessFile** 并非继承于 `InputStream` 和 `OutputStream` 两个接口，而是继承于有些陌生的`DateInput` 和 `DataOutput`。

真是个有点特立独行的类~我们继续来看下它的构造函数：

![](https://gitee.com/cbuc/picture/raw/master/typora/20210301220712.png)

我们这边只截取了构造函数的一部分，毕竟只截重点就行~

观察构造器可以发现，这里定义了四种模式：

|    r    | 以只读的方式打开文本，也就意味着不能用write来操作文件 |
| :-----: | :---------------------------------------------------: |
| **rw**  |               读操作和写操作都是允许的                |
| **rws** |  每当进行写操作，同步的刷新到磁盘，刷新内容和元数据   |
| **rwd** |      每当进行写操作，同步的刷新到磁盘，刷新内容       |

这有什么用呢？说白了就是 `RandomAccessFile` 这个类什么都要。**既能读，又能写**

从本质上来说，`RandomAccessFile` 的工作方式类似于把 **DataInputStream** 和 **DataOutputStream** 组合起来使用，还添加了一些方法，其中方法`getFilePointer()` 用于查找当前所处的文件位置，`seek()` 用于在文件内移至新的位置，`length()` 用于判断文件的最大尺寸。第二个参数用于表明我们是 **"随机读（r）"** 还是 **"既读又写（rw）"**，但它不支持单独 **写文件**。我们实际来操作一下：

获取只读`RandomAccessFile`：

![](https://gitee.com/cbuc/picture/raw/master/typora/20210301222231.png)

获取可读可写`RandomAccessFile`

![](https://gitee.com/cbuc/picture/raw/master/typora/20210301222838.png)

我们首先从向文件中写入了`test` 四个单词，然后将头指针移动3位后继续写入`File`四个单词，结果就变成了`testFile`，这是因为移动指针后是以第四个位置开始写入。

#### ZIP

看到`zip`这个词，我们理所应当的就会想到压缩文件，没错压缩文件在 `Java I/O`中也是极其重要的存在。也许更应该说对文件的压缩在我们的开发中也是极其重要的存在。

在 **Java** 内置类中提供了需要关于`ZIP` 压缩的类，可以使用 `java.util.zip` 包中的`ZipOutuputStream` 和 `ZipInputStream` 来实现文件的 **压缩** 和 **解压缩**。我们先来看下如何对文件进行压缩~

##### ZipOutputStream

**ZipOutputStream** 的构造方法如下：

```java
public ZipOutputStream(OutputStream out) {/* doSomething */}
```

我们需要传入一个 `OutputStream` 对象。因此我们也大致可以认为 **压缩文件** 相当于是向一个 **压缩文件中写入数据**，听起来可能会有点绕。我们先看下`ZipOutputStream`中有哪些API：

![](https://gitee.com/cbuc/picture/raw/master/typora/20210307134852.png)

|               方法                | 返回值 |                             说明                             |
| :-------------------------------: | :----: | :----------------------------------------------------------: |
|     putNextEntry(ZipEntry e)      |  void  | 开始写一个新的 ZipEntry，并将流内的位置移至此 entry 所值数据的开头 |
| write(byte[] b, int off, int len) |  void  |               将字节数组写入当前 ZIP 条目数据                |
|    setComment(String command)     |  void  |                  设置此 ZIP 文件的注释文字                   |
|             finish()              |  void  |  完成写入ZIP 输出流的内容，无须关闭它所配合的 OutputStream   |

我们来演示一下如何压缩文件：

场景：我们需要将D盘目录下的 `TestFile`文件夹压缩到 D盘下的 `test.zip` 中

![](https://gitee.com/cbuc/picture/raw/master/typora/20210307174531.png)

![](https://gitee.com/cbuc/picture/raw/master/typora/20210307150355.png)

具体的操作逻辑如下：

![](https://gitee.com/cbuc/picture/raw/master/typora/20210307151657.png)

通过以上步骤我们便可以很顺利的将一个文件压缩

##### ZipInputStream

说完如何将文件压缩，那自然要会如何将文件解压缩！

```java
public ZipInputStream(InputStream in) {/* doSomethings */}
```

**ZipInputStream** 与压缩流类似，构造函数同样需要传入一个 `InputStream` 对象，毋庸置疑，API肯定也是一一对应的：

![](https://gitee.com/cbuc/picture/raw/master/typora/20210307152843.png)



|               方法               |  返回值  |                             说明                             |
| :------------------------------: | :------: | :----------------------------------------------------------: |
| read(byte[] b, int off, int len) |   int    |     读取目标 b 数组内 off 偏移量的位置，长度是 len 字节      |
|            avaiable()            |   int    | 判断是否已读完目前 entry 所指定的数据，已读完返回 0，否则返回 1 |
|           closeEntry()           |   void   |          关闭当前 ZIP 条目并定位流以读取下一个条目           |
|           skip(long n)           |   long   |               跳过当前 ZIP 条目中指定的字节数                |
|          getNextEntry()          | ZipEntry | 读取下一个ZipEntry，并将流内的位置移至该 entry 所指数据的开头 |
|   createZipEntry(String name)    | ZipEntry |             以指定的name参数新建一个ZipEntry对象             |

那下面我们便动手操作一下如何解压一个文件：

![](https://gitee.com/cbuc/picture/raw/master/typora/20210307174814.png)

不必被代码长度吓到，认真阅读便会发现解压文件也很简单：

我们通过 `getNextEntry()` 方法来获取到一个`ZipEntry`，这里取到文件方式类似于深度遍历，每次返回的目录大致如下：

![](https://gitee.com/cbuc/picture/raw/master/typora/20210307175008.png)

每次都会遍历完一个目录下的所有文件，例如 `dir01` 文件夹下的所有文件，才会继续遍历 `dir02` 文件夹，所以我们不必使用递归的方式去获取所有文件。取到每一个文件后，通过 `ZipFile`获取输出流，然后写入到解压后的文件中。大致流程如下：

![](https://gitee.com/cbuc/picture/raw/master/typora/20210307175335.png)

### 新 I/O

**JDK1.4的java.nio.*** 包中引入了新的 **JavaI/O** 类库，其目的也简单，就是`提高速度`。实际上，旧的I/O包已经使用 `nio` 重新实现过，以便充分利用这种速度提高。

只要使用的结构更接近于操作系统执行I/O的方式，那么速度自然也会提高，因此就产生了两个概念：`通道` 和 `缓冲器`。

我们该怎么理解 **通道** 和 **缓冲器** 两个概念呢。我们可以认为缓冲器相当于是一辆煤矿中的小火车，通道相当于火车的轨道，小火车载着满满的煤矿从矿源运往它处。因此我们并没有直接和通道交互，而是和缓冲器交互，并把缓冲器派送到通道。通道要么从缓冲器获得数据，要么向缓冲器发送数据。

`ByteBuffer`是唯一直接与通道直接交互的缓冲器，可以存储未加工字节的缓冲器。

```java
ByteBuffer buffer = ByteBuffer.allocate(1024);
```

`ByteBuffer` 的创建方式通常可以通过`allocate()`方法来指定大小创建。同时`ByteBuffer`中支持 **4** 中创建 **ByteBuffer**

![](https://gitee.com/cbuc/picture/raw/master/typora/20210303215828.png)

为了更好支持 **新I/O** ，**旧 I/O** 类库中有三个类被修改了，用以产生`FileChannel`。这个被修改的类分别的：`FileInputStream`，`FileOutputStream`以及用于读写兼备的 `RandomAccessFile`。这里值得注意的是这些都是字节操作流，因为字符流不能用于产生通道，但是 `Channels` 中提供了实用的方法，用于在通道中产生 **Reader** 和 **Writer**

#### 获取通道

我们在上面已经了解到了有三个类支持产生通道，具体产生通道的方法如下：

![](https://gitee.com/cbuc/picture/raw/master/typora/20210303221739.png)

![](https://gitee.com/cbuc/picture/raw/master/typora/20210303221809.png)

以上便是创建通道的三种方式，并且进行了读写操作的测试。我们看一下图中的测试代码，然后总结一下：

- `getChannel()`方法将会产生一个 `FileChannel`。我们可以向它传送可用于读写的`ByteBuffer`。我们将字节存放于 `ByteBuffer` 的方法之一是：使用 `put()`方法直接对它们进行填充，填入一个或多个字节，或基本数据类型的值。不过，也可以使用 `wray()`方法将已存在的字节数组 **"包装"** 到 ByteBuffer 中。这样子就可以不用在复制底层的数组，而是把它作为所产生的 **ByteBuffer** 的存储器，可以称之为 **数组支持的ByteBuffer**

- 我们还可以看到 `FileChannel` 使用到的 `position()` 方法，这个方法可以在文件内随处移动`FileChannel`，在这里，我们把它移动到最后，然后进行其他的读写操作。

- 对于只读访问，我们必须显式地使用静态的`allocate()` 方法来分配 `ByteBuffer`。如果我们想要获取更好的速度我们也可以使用 `allocateDirect()` ，以产生一个与操作系统有更高耦合性的 **"直接"** 缓冲器。但是这种分配的开支会更大，并且具体实现也随操作系统的不同而不同。

- 如果我们想要的调用 `read()` 来向`ByteBuffer` 存储字节，就必须调用缓冲器上的`flip()` 方法，这是用来告知 `FileChannel` ，让它准备好让别人读取字节的准备，当然，**这也是为了获取最大速度**。这里我们用 **ByteBuffer** 来接收字节后就没有继续使用缓冲器来进一步操作，如果需要继续`read()` 的话，我们就必须得调用 `clear()` 方法来为每个 `read()` 方法做准备。

#### 通道相连

程序员往往都是比较懒惰的，上面那种读取后再通知 `FileChannel` 的方式似乎有些麻烦。那么有没有更加简单的方法？肯定是有的，不然我也不会问是吧~ 那就是让一个通道与另外一个通道直接相连接，这就得借助特殊的方法`transferTo()` 和 `transferFrom()` 。具体使用如下：

![](https://gitee.com/cbuc/picture/raw/master/typora/20210304205444.png)

借助**方法1** 或 **方法2** 都可以成功将文件写入到 `test03.txt` 文件中

**END**

I/O 操作是我们日常开发中或不可缺的知识，所以这块我们也得好好掌握！

![看完不赞，都是坏蛋](https://gitee.com/cbuc/picture/raw/master/typora/20210304210411.png)
> 今天的你多努力一点，明天的你就能少说一句求人的话！
>
> *我是小菜，一个和你一起学习的男人。* `💋`
>
>
> 微信公众号已开启，**小菜良记**，没关注的同学们记得关注哦！