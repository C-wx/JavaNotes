大家好，我是小菜。
一个希望能够成为 **吹着牛X谈架构** 的男人！如果你也想成为我想成为的人，不然点个关注做个伴，让小菜不再孤单！


>本文主要介绍 `SpringCloud中Sentinel`
>
>如有需要，可以参考
>
>如有帮助，不忘 **点赞** ❥
>
>
>微信公众号已开启，**小菜良记**，没关注的同学们记得关注哦！

上篇我们已经了解到微服务中重要的组件之一 --- **服务网关Gateway** 。我们在取精排糠的同时，不可否认微服务给我们带来的好处。其中承载高并发的好处更是让各大公司趋之若鹜！

但是想要接收高并发，自然要接收它带来的一系列问题。在微服务架构中，我们将业务拆分成了一个个服务，这样的好处之一便是分担压力，一个服务顶不住了，下一个服务顶上去。但是服务与服务之间可以相互调用，由于网络原因或自身的原因，服务并不能保证百分百可用，也就是各大公司现在追寻的几个9（99.99%，99.999%）可用！如果单个服务出现问题，调用这个服务就会出现网络延迟，此时如果正好有大量的网络涌入，势必会形成任务堆积，导致服务瘫痪！

空口无凭，小菜给你找点示例看看：

`OrderController`

![](https://gitee.com/cbuc/picture/raw/master/typora/image-20210612213501386.png)

这里我们通过接口模拟了下单的场景，其中通过线程休眠的方式模拟了网络延迟的场景。接下来我们还需要改一下 **tomcat** 中并发的线程数

`applicatio.yaml`

```yaml
server:
  tomcat:
    max-threads: 10  # 默认的并发数为200
```

当这一切准备就绪好，我们这个时候还需要压测工具 **Jmeter** 的帮助（不会操作的同学具体看以下使用）

1. 首先打开Jmeter软件，选择新建线程组

![](https://gitee.com/cbuc/picture/raw/master/typora/image-20210612220127501.png)

2. 设置请求线程数

![](https://gitee.com/cbuc/picture/raw/master/typora/image-20210612220225419.png)

3. 设置HTTP请求取样器

![](https://gitee.com/cbuc/picture/raw/master/typora/image-20210612220303764.png)

4. 设置请求的接口

![](https://gitee.com/cbuc/picture/raw/master/typora/image-20210612221837711.png)

完成上面步骤就可以点击开始了。不过测试之前我们确保两个API都是可以访问的：

![image-20210612220817883](https://gitee.com/cbuc/picture/raw/master/typora/image-20210612220817883.png)

![](https://gitee.com/cbuc/picture/raw/master/typora/image-20210612220731159.png)

然后开始压力测试，当大量请求发送到创建订单的接口时，我们这时候通过网页访问 `detail` **API** 发现请求一直在阻塞，过一会才联通！

![](https://gitee.com/cbuc/picture/raw/master/typora/image-20210612221901567.png)

这无疑是一个开发炸弹，而这便是高并发带来的问题。看到这里，恭喜你成功见证了一场服务雪崩的问题。那不妨带着这份兴趣继续往下看，会给你惊喜的。

### Sentinel

### 一、服务雪崩

我们开头直接用服务雪崩勾引你，不知道你心动了没有，如果不想你的项目在线上环境面临同样的问题，赶紧为项目搭线起来，不经意的举动往往能让你升职加薪！

在分布式系统中，由于网络原因或自身的原因。服务一般无法保证 100% 可用，如果一个服务出现了问题，调用这个服务就会出现线程阻塞的情况，此时若有大量的请求涌入，就会出现多条线程阻塞等待，进而导致服务瘫痪。而由于服务与服务之间的依赖性，故障会进行传播，相互影响之下，会对整个微服务系统造成灾难性的严重后果，这就是服务故障的 **“雪崩效应”**！

最开始的时候，服务**A~C** 三个服务其乐融融的相互调用，响应也很快，为主人工作也很卖力

![](https://gitee.com/cbuc/picture/raw/master/typora/image-20210612222220078.png)

好景不长，主人火了，并发量上来了。可能因为服务C还不够健壮的原因，服务C在某一天宕机了，但是服务B还是依赖服务C，不停的进行服务调用

![](https://gitee.com/cbuc/picture/raw/master/typora/image-20210612222449372.png)

这个时候可能大家都还没意识到问题的严重性，只是怀疑可能请求太多了，导致服务器变卡了。请求继续发送，服务A这个时候也未知问题，一边觉得奇怪服务B是不是偷懒了，怎么还不把响应返回给它，一边继续发送请求。但是这个时候请求都在服务B堆积着，终于有一天服务B也累出问题了

![](https://gitee.com/cbuc/picture/raw/master/typora/image-20210612222705969.png)

这个时候人们开始抱怨服务A了，却不知道服务A底层原来还依赖服务B和服务C，而这时服务B和服务C都挂了。服务A这时才想通为什么服务B之前那么久没返回响应，原来服务B也依赖服务C啊！但是这个时候已经晚了，请求不断接收，不断堆积，下场都是惊人的相似，也走向了宕机的结果。不过有一点不同的是，服务A宕机后需要承载了用户各种的骂声~

![](https://gitee.com/cbuc/picture/raw/master/typora/image-20210612222931117.png)

可悲的故事警惕了我们，微服务架构之间并没有那么可靠。有时候真的是说挂就挂，原因各种各样，不合理的容量设计，高并发情况下某个方法响应变慢，亦或是某台机器的资源耗尽。我们如果不采取措施，只能坐以待毙，不断的在重启服务器中循环。但是我们可能无法杜绝雪崩的源头，但是如果我们在问题发生后做好容错的准备，保证下一个服务发生问题，不会影响到其他服务的正常运行，各个服务自扫家门雪，做到独立，**雪落而不雪崩**！

### 二、容错方案

想要防止雪崩的扩散，就要做好服务的容错，容错说白了就是保护自己不被其他队友坑，带进送人头的行列！那我们有哪些容错的思路呢？

#### 1）隔离方案

它是指将系统按照一定的原则划分为若干个服务模块，各个模块之间相互独立，无强依赖。当有故障发生时，能将问题和影响隔离在某个模块内部，而不扩散风险，不涉及其他模块，不影响整体的系统服务。常见的隔离方式有：**线程隔离** 和信号量隔离：

![](https://gitee.com/cbuc/picture/raw/master/typora/image-20210612224919529.png)

#### 2）超时方案

在上游服务调用下游服务的时候，设置一个最大响应时间，如果超过这个时间下游服务还没响应，那么就断开连接，释放掉线程

![](https://gitee.com/cbuc/picture/raw/master/typora/image-20210612225057673.png)

#### 3）限流方案

限流就是限制系统的输入和输出流量已达到保护系统的目的。为了保证系统的稳固运行，一旦达到需要限制的阈值，就需要限制流量并采用少量措施完成限制流量的目的

![](https://gitee.com/cbuc/picture/raw/master/typora/image-20210612225432554.png)

> 限流策略有很多，后期也会考虑出一篇专门将如何进行限流

#### 4）熔断方案

在互联网系统中，当下游服务因访问压力过大而相应变慢或失败的时候，上游服务为了保护系统整体的可用性，可以暂时切断对下游服务的调用。这种牺牲局部，保全整体的措施就叫做熔断

![](https://gitee.com/cbuc/picture/raw/master/typora/image-20210612230133516.png)

其中熔断有分为三种状态：

- 熔断关闭状态（Closed）

服务没有故障时，熔断器所处的状态，对调用方的调用不做任何限制

- 熔断开启状态（Open）

后续对该服务接口的调用不再经过网络，直接执行本地的 **fallback** 方法

- 半熔断状态（Half-Open）

尝试恢复服务调用，允许有限的流量调用该服务，并监控成功率。如果成功率达到预期，则说明服务已经恢复，进入熔断关闭状态；如果成功率依然很低，则重新进入熔断关闭状态

#### 5）降级方案

降级其实就是为服务提供一个 **B计划**，一旦服务无法正常，就启用 **B计划**

![](https://gitee.com/cbuc/picture/raw/master/typora/image-20210612230548872.png)

方案其实有很多，但是很难说明那种方案是最好的。在开发者的世界中，没有最好，只有最适合。那如果自己写一个容错方案往往是比较容易出错的（功力高深者除外），那么为了解决这个问题，我们不妨用第三方已经为我实现好的组件！

### 三、容错组件

#### 1）Hystrix

**Hystrix** 是 Netflix 开源的一个延迟和容错库，用于隔离访问远程系统，服务或者第三方库，防止级联失败，从而提升系统的可用性和容错性

#### 2）Resilience4J

**Resilience4J**是一款非常轻量，简单，并且文档非常清晰，丰富的熔断工具，这是 **Hystrix** 官方推荐的替代品。它支持 **SpringBoot 1.x/2.x** 版本，而且监控也支持和 **prometheus** 等多款主流产品进行整合

#### 3）Sentinel

**Sentinel** 是阿里开源的一款断路器的实现，在阿里巴巴内部也已经大规模采用，可以说是非常稳定

##### 不同之处

容错组件其实有很多，但各有风骚，下面分别说明这这三种组件的不同之处，如何抉择，仔细斟酌！

|      功能      |                        Sentinel                        |          Hystrix           |           resilience4j           |
| :------------: | :----------------------------------------------------: | :------------------------: | :------------------------------: |
|    隔离策略    |              信号量隔离（并发线程数限流）              |   线程池隔离/信号量隔离    |            信号量隔离            |
|  熔断降级策略  |             基于响应时间，异常比率，异常数             |        基于异常比率        |      基于异常比率，响应时间      |
|  实时统计实现  |               时间滑动窗口（LeapArray）                | 时间滑动窗口（基于Rxjava） |         Ring Bit Buffer          |
|  动态规则配置  |                     支持多种数据源                     |       支持多种数据源       |             有限支持             |
|     扩展性     |                       多个扩展点                       |         插件的形式         |            接口的形式            |
| 基于注解的支持 |                          支持                          |            支持            |               支持               |
|      限流      |            基于 QPS，支持基于调用关系的限流            |         有限的支持         |           Rate LImiter           |
|    流量整形    |         支持预热模式，匀速器模式，预热排队模式         |           不支持           |     简单的 Rate Limiter模式      |
| 系统自适应保护 |                          支持                          |           不支持           |              不支持              |
|     控制台     | 提供即用的控制台，可配置规则，查看秒级监控，机器发现等 |       简单的监控查看       | 不提供控制台，可对接其他监控系统 |

之前有说过，一个新秀想让大伙接受并广泛使用，肯定得具备良好的特性才能冲出老牌的包围圈。那么 **Sentinel** 作为一个微服务中的新秀，只有具备让人满意的功能，才能被大伙接受。因此，这篇的主角便是 **Sentinel** ，不妨深入了解一番！

### 四、认识Sentinel

学会用一个组件之前，我们先需要知道这个组件是什么。

#### 1）什么是Sentinel

**Sentinel**（分布式系统的流量防卫兵）是阿里开源的一套用于 `服务容错` 的综合性解决方案。它以流量为切入点，从 **流量控制**、**熔断降级**、**系统负载保护**等多个维度来保护服务的稳定性。

#### 2）特性

- **丰富的应用场景**：Sentinel 承接了阿里巴巴近10年的双十一大促的流量的核心场景。在秒杀、消息削峰填谷，集群流量控制、实时熔断下游不可用应用等场景游刃有余
- **完备的实时监控：** Sentinel 提供了实时的监控功能。通过控制台可以看到接入应用的单台机器的数据，甚至500台以下规模的集群的汇总情况
- **广泛的开源生态：** Sentinel 提供开箱即用的与其他开源框架整合模块，只需要引入相关的依赖进行简单的配置即可快速接入
- **完善的 SPI 扩展点：** Sentinel 提供简单易用、完善的 SPI 扩展接口。可以通过扩展接口来快速定制逻辑。例如定制规则管理，适配动态数据源等

#### 3）组成部分

- **核心库（Java客户端）**：不依赖任何框架/库，能够运行于所有的Java运行环境，同时对 Dubbo和SpringCloud 有很好的支持
- **控制台（Dashboard）**：基于SpringBoot开发， 打包后可以直接运行，不需要额外的 Tomcat 等应用容器

### 五、上手 Sentinel

既然 Sentinel 有两个组成部分，我们分别介绍

#### 1） 核心库使用

最关键的一步便是引入依赖

```xml
<dependency>
    <groupId>com.alibaba.cloud</groupId>
    <artifactId>spring-cloud-starter-alibaba-sentinel</artifactId>
</dependency>
```

然后编写一个测试控制器

```java
@RestController
@RequestMapping("order")
public class OrderController {

    private final static Logger LOGGER = LoggerFactory.getLogger(OrderController.class);

    @GetMapping("/create/{id:.+}")
    public String createOrder(@PathVariable String id) {
        LOGGER.info("准备下单ID为 [{}] 的商品", id);
        LOGGER.info("成功下单了ID为 [{}] 的商品", id);
        return "success";
    }

    @GetMapping("/{id:.+}")
    public String detail(@PathVariable String id) {
        return StringUtils.join("获取到了ID为", id, "的商品");
    }
}
```

#### 2）控制台使用

**Sentinel** 具备完善的控制台， 其实就抓住了国人开发的命点。很多人看到有控制台的使用，毫不犹豫的选择了它！

- 首先我们需要下载控制台的 **Jar** 包启动运行，[下载地址](https://github.com/alibaba/Sentinel/releases)

- 下载结束进入到下载目录中通过以下命令启动，然后访问 `localhost:8080`，即可看到页面

```shell
java -Dserver.port=8080 -Dcsp.sentinel.dashboard.server=localhost:8080 -Dproject.name=sentinel-dashboard -jar sentinel-dashboard-1.8.1.jar
```

![](https://gitee.com/cbuc/picture/raw/master/typora/image-20210613135157077.png)

- 登录控制台（sentinel/sentinel）

![](https://gitee.com/cbuc/picture/raw/master/typora/image-20210613135300835.png)

到这里我们就成功进入 **Sentinel**的控制台页面了，是不是上手十分简单。但这里控制台还是空荡荡的，那是因为我们项目还没进行控制台的相关配置。我们回到 **store-order**  订单服务中，在 **application.yaml** 中进行配置：

![](https://gitee.com/cbuc/picture/raw/master/typora/image-20210613135734244.png)

 `sertinel.transport.port`为随意端口号，用来跟控制台交流的端口；`sentinel.transport.dashboard`  为控制台的访问地址

配置完成后，我们便可以启动 **store-order** 服务，此时再看控制台可以发现已经有了 **store-order** 这个服务了

![](https://gitee.com/cbuc/picture/raw/master/typora/image-20210613143024363.png)

可能有些小伙伴会觉得奇怪，上面说到用来跟控制台交流的端口是干嘛用的？有问题，便有进步！这里我们可以顺带了解一下控制台的使用原理

![](https://gitee.com/cbuc/picture/raw/master/typora/image-20210613141846162.png)

当 Sentinel应用启动后，我们需要将我们的微服务程序注册到控制台上，也就是在配置文件中指定控制台的地址，这个是肯定的。但是所谓用来跟控制台交流的端口，也就是我们每个服务都会通过这个端口跟控制台传递数据，控制台也可以通过此端口调用微服务中的监控程序来获取微服务的各种信息。**因此这个端口必不可少，而且每个服务都需要具备独立的端口号**。

#### 3）基本概念

- **资源**

所谓的资源就是 **Sentinel** 要保护的东西。资源是 Sentinel 的关键概念，它可以使 Java 应用程序中的任何内容，可以是一个服务，也可以是一个方法，甚至是一段代码。

- **规则**

所谓的规则就是用来定义如何进行保护资源的，它是作用于资源之上的，定义以什么样的方式保护资源，主要包括了流量控制规则，熔断降级规则以及系统保护规则

---

我们来看个简单的例子，我们设置 `order/{id}`这个API 的 QPS 为1

![](https://gitee.com/cbuc/picture/raw/master/typora/image-20210613143059080.png)

当我们不断刷新页面就会发现，已经进行了流控

![](https://gitee.com/cbuc/picture/raw/master/typora/image-20210613143337003.png)

在这里面 `order/{id}`指的就是 **资源**，我们设置的QPS阈值就是 **规则**

#### 4）重要功能

学会用 **Sentinel** 之前，我们需要清楚 **Sentinel** 能为我们干点什么

##### （1）**流量控制**

流量控制在网络传输中是一个常用的概念，它用于调整网络包的数据。任意时间到来的请求往往是随机不可控的，而系统的处理能力是有限的。我们需要根据系统的处理能力对流量进行控制，Sentinel 作为一个调配器，可以根据需要把随机的请求调整成合适的形状。

##### （2）**熔断降级**

当检测到调用链路中某个资源出现不稳定的表现，例如请求响应时间长或者异常比例升高的时候，则对这个资源的调用进行限制，让请求快速失败，避免影响到其他资源而导致级联故障

Sentinel 采用了两种手段进行解决

1. **通过并发线程数进行限制**

**Sentinel** 通过限制资源并发线程的数量，来减少不稳定资源对其他资源的影响。当某个资源出现不稳定的情况时，例如响应时间变长，对资源的直接影响就是会造成线程数的逐步堆积。当 线程数在特定资源上堆积到一定的数量之后，对该资源的新请求就会拒绝。堆积的线程完成任务后才会开始继续接受请求

2. **通过响应时间对资源进行降级**

除了对并发线程数进行控制之外，**Sentinel** 还可以通过响应时间来快速降级不稳定的资源。当依赖的资源出现响应时间过长后，所有对该资源的访问都会被直接拒绝，直到过了指定的时间窗口之后才会重新恢复

> 这里提一嘴和 Hystrix 的区别
>
> 两者的原则实际上都是一致的。都是当一个资源出现问题时，让其快速失败，不会波及到其他资源服务。但是在限制的实现上是不一样的
>
> - Hystrix 采用的是线程池隔离方式，优点是做到了资源之间的隔离，缺点是增加了线程上下文切换的成本
> - Sentinel 采用的是通过并发线程的数量和响应时间来对资源做限制的
>
> 个人认为 Sentinel 处理限制的方式更好一些

##### （3）**系统负载保护**

Sentinel 同时提供系统维度的自适应保护能力。当系统负载较高的时候，如果还持续让请求进入可能会导致系统崩溃，无法响应。在集群环境下，会把本应这台机器承载的流量转发到其他机器上去。如果这个时候其他的机器也处在一个崩溃的边缘状态，Sentinel 提供了对应的保护机制，让系统的入口流量和负载达到一个平衡，保证系统在能力方位之内处理最多的请求。

#### 5）流控规则

流控规则，在我们上面说明 Sentinel 的基本概念时简单演示了一下。流量控制就是用来监控应用流量的 QPS（每秒查询率）或并发线程数等指标，当达到指定的阈值时对流量进行控制，以避免被瞬时的流量高峰冲垮，从而保障应用的高可用性。

`简单配置`

**簇点链路 ---> 选择对应资源 ---> 添加流控**

![](https://gitee.com/cbuc/picture/raw/master/typora/image-20210613150518975.png)

- **资源名**：唯一名称，默认就是请求路径，支持自定义
- **针对来源：** 指定对哪个微服务进行限流，默认为 default（不区分来源，全部限制）

- **阈值类型/单机阈值**：
  1. **QPS (每秒请求数)**：当调用该接口的QPS达到阈值的时候进行限流
  2. **线程数**：当调用该接口的线程数达到阈值的时候进行限流
- **是否集群：** 这里暂时不演示集群

`高级配置`

我们点开 **高级选项** 可以看到多出了两个额外功能

![](https://gitee.com/cbuc/picture/raw/master/typora/image-20210613173631131.png)

其中 **流控模式** 分为 三种

- **直接（默认）**：接口达到限流条件时，开启限流
- **关联**：当关联的资源达到限流条件是，开启限流（适合做应用让步）

- **链路：** 当从某个接口过来的资源达到限流条件时，开启限流

`1、关联流控`

**直接流控** 的方式我们在上面已经演示过了，我们这里直接说 **关联流控** 如何使用

![](https://gitee.com/cbuc/picture/raw/master/typora/image-20210613175027763.png)

使用方式也很简单，只要添加相关联的资源即可。只要关联资源 `/order/create/{id}`的 QPS 每秒超过 1。那么 `/order/{id}` 就会触发流控。这就是 **你冲动，我买单**

设置完后，我们需要请我们的老帮手 **Jmeter** 帮忙测试一下：

![](https://gitee.com/cbuc/picture/raw/master/typora/image-20210613180357333.png)

这个时候 `/order/create/{id}` 的QPS已经远远超过1了，然后我们再试着访问 `/order/{id}`，发现已经被限流了！

![](https://gitee.com/cbuc/picture/raw/master/typora/image-20210613175546447.png)

`2、链路流控`

链路流控模式指的是：当从某个接口过来的资源达到限流条件的时候，开启限流。它的功能有点类似于针对来源配置项，区别在于： **针对来源是针对上级微服务，而链路流控是针对上级接口，也就是说它的粒度更细**

该模式使用麻烦一些，我们需要改造下代码：

`OrderService`

![](https://gitee.com/cbuc/picture/raw/master/typora/image-20210613182951389.png)

`OrderController`

![](https://gitee.com/cbuc/picture/raw/master/typora/image-20210613190430908.png)

`application.yaml`

![](https://gitee.com/cbuc/picture/raw/master/typora/image-20210613190530918.png)

然后自定义 **Sentinel** 上下文过滤类 `FilterContextConfig`：

![](https://gitee.com/cbuc/picture/raw/master/typora/image-20210613190637080.png)

接下来我们在 **Sentinel** 控制台流控中添加配置：

![](https://gitee.com/cbuc/picture/raw/master/typora/image-20210613190733796.png)

然后我们看测试结果，发现以` /order/_datail02`为入口访问，会进行流控，而`/order/_datail01`访问便不会进行流控

![](https://gitee.com/cbuc/picture/raw/master/typora/image-20210613190405256.png)

因此我们清楚了**链路模式**的入口资源是针对方法接口的

#### 6）降级规则

降级规则指的就是当满足什么条件的时候，对服务进行降级。Sentinel 提供了三个衡量条件：

- **慢调用比例**

 当资源的平均相应时间超过阈值（单位 ms）之后，资源进入准降级的状态。如果接下来`1s`内持续进入`5`个请求，它们的RT都持续超过这个阈值，那么在接下来的时间窗口（单位 s）之内，就会对这个方法进行降级。

![](https://gitee.com/cbuc/picture/raw/master/typora/image-20210613202602164.png)

- **异常比例**

当资源的`每秒异常总数/占通过量`的比率超过阈值之后，资源就会进入降低状态，即在接下的时间窗口（单位 s）之内，对这个方法的调用都会自动的返回。异常比率的赋值范围为 `[0.0, 1.0]`

![](https://gitee.com/cbuc/picture/raw/master/typora/image-20210613203127440.png)

- **异常数**

当资源近1分钟的异常数目超过阈值之后就会直接进行降级。但是这里需要注意的是，由于统计时间窗口是分钟级别的，若时间窗口小于60s，则结束熔断状态后仍可能再进入熔断状态

![](https://gitee.com/cbuc/picture/raw/master/typora/image-20210613203142727.png)

#### 7）热点规则

热点参数流控规则是一种更加细粒度的流控规则，它允许将规则具体到参数上。这里我们可以在代码里面具体看下怎么使用

```java
@SentinelResource("order")  // 不添加该注解标识, 热点规则不生效
@GetMapping("/_datail03")
public String detail03(String arg1, String arg2) {
    return StringUtils.join(arg1, arg2);
}
```

该API接收两个参数`arg1`和`arg2`，这个时候我们对这个资源添加参数流控

![](https://gitee.com/cbuc/picture/raw/master/typora/image-20210613203639873.png)

弄完上面配置后，我们就可以在浏览器进行测试了

当参数为第二个的时候，无论一秒刷新几次都不会触发流控

![](https://gitee.com/cbuc/picture/raw/master/typora/image-20210613205722644.png)

当参数为第一个的时候，只要QPS超过了1，就会触发流控

![](https://gitee.com/cbuc/picture/raw/master/typora/image-20210613205846158.png)

这个配置也有高级选项，可以更细颗粒的对参数进行限制，这里就不再演示了。

![](https://gitee.com/cbuc/picture/raw/master/typora/image-20210613205951276.png)

#### 8）系统规则

系统保护规则是从应用级别的入口流量进行控制，从单台机器总体的 **Load、RT、线程数、入口QPS、CPU 使用率**五个维度监控应用数据，让系统尽可能跑在最大吞吐量的同时保证系统整体的稳定性

![image-20210613220336221](https://gitee.com/cbuc/picture/raw/master/typora/image-20210613220336221.png)

- **Load：** 仅对 Linux/Unix 有效。当系统的 load 超过阈值时，且系统当前的并发线程数超过系统容量时才会触发系统保护。系统容量是由系统的 `maxQPS * minRT` 计算而出，设定的参考值可以参考 **CPU 核数 * 2.5**
- **RT：** 当单台机器上所有入口流量的平均 RT 达到阈值就会触发系统保护，单位是毫秒
- **线程数：** 当单台机器上所有入口流量的并发线程数达到阈值是就会触发保护
- **入口QPS：** 当单台机器上所有入口流量的QPS达到阈值就会触发系统保护
- **CPU使用率：** 当单台机器上所有入口流量的CPU使用率达到阈值就会触发系统保护

#### 9）授权规则

在某些场景下，我们需要根据调用来源来判断该次请求是否允许放行，这个时候我们可以使用Sentinel的来源访问控制的功能。**来源访问控制根据资源的请求来源判断资源是否能够通过**。

![image-20210613211659301](https://gitee.com/cbuc/picture/raw/master/typora/image-20210613211659301.png)

- **白名单：** 只有请求来源位于白名单内才能通过
- **黑名单：** 请求来源位于黑名单时不予通过，其余的则放行通过

那么问题来了，`流控应用`是啥玩意？要用这个流控应用，我们还需要借助 **Sentinel** 中的 **RequestOriginParser** 接口来处理来源。只要 **Sentinel** 保护的接口资源被访问，**Sentinel** 就会调用 **RequestOriginParser** 的实现类去解析访问源

`CustomRequestOriginParser`

```java
public class CustomRequestOriginParser implements RequestOriginParser {
    @Override
    public String parseOrigin(HttpServletRequest httpServletRequest) {
        return httpServletRequest.getParameter("api");
    }
}
```

然后我们添加授权规则

![](https://gitee.com/cbuc/picture/raw/master/typora/image-20210613212657645.png)

该规则的作用是，只有当请求URL中带有参数 `api=detail03` 才能访问成功，否则失败。以下便是测试结果

![](https://gitee.com/cbuc/picture/raw/master/typora/image-20210613212822282.png)

![](https://gitee.com/cbuc/picture/raw/master/typora/image-20210613212911162.png)

### 六、扩展 Sentinel

#### 1）@SentinelResource

这个注解我们上面已经用过，不知道小伙伴们有没有注意到，上面避开没讲就是为了在这详细的介绍下！

该注解的作用就是用来定义资源点。当我们定义了资源点之后，就可以通过 **Sentinel** 控制台来设置限流和降级策略来对资源点进行保护。同时还可以通过该注解来指定出现异常时候的处理策略。

我们点进注解可以看到该注解中存在许多属性

![](https://gitee.com/cbuc/picture/raw/master/typora/image-20210613221229527.png)

| 属性               | 作用                                                         |
| ------------------ | ------------------------------------------------------------ |
| value              | 资源点名称                                                   |
| blockHandle        | 处理BlockException的函数名称，函数要求：<br />1. 必须是 `public`<br />2. 返回类型参数与原方法要一致<br />3. 默认需和原方法在同一个类中。如果希望使用其他类的函数，可以配置 blockHandlerClass，并制定blockHandlerClass 里面的方法 |
| blackHandlerClass  | 存放 blockHandler 的类，对应的处理函数必须用 `static`  修饰  |
| fallback           | 用于在抛出异常时候提供 fallback 处理逻辑。fallback 函数可以针对所有类型的异常（除了exceptionsToIgnore 中排除的异常），函数要求：<br />1. 返回类型与原方法一致<br />2. 参数类型需和原方法匹配<br />3. 默认需和原方法在同一个类中。若希望使用其他类的函数，可配置 fallbackClass，并指定对应的方法 |
| fallbackClass      | 存放 fallback 的类，对应的处理函数必须用 `static`   修饰     |
| defaultFallback    | 用于通用的 fallback 逻辑。默认fallback函数可以针对所有类型的异常进行处理。若同时配置了 fallback 和 defaultFallback，以fallback为准。函数要求：<br/>1. 返回类型与原方法一致<br/>2. 方法参数列表为空，或者有一个 Throwable 类型的参数。<br/>3. 默认需要和原方法在同一个类中。若希望使用其他类的函数，可配置 fallbackClass ，并指定 fallbackClass 里面的方法。 |
| exceptionsToIgnore | 指定排除掉哪些异常。排除的异常不会计入异常统计，也不会进入fallback逻辑，而是原样抛出。 |
| exceptionsToTrace  | 需要trace的异常                                              |

我们这里简单使用演示一下

- **将限流和降级方法定义在原方法同一个类中**

![](https://gitee.com/cbuc/picture/raw/master/typora/image-20210613223408976.png)

- **限流和降级方法定义不在原方法同一个类中**

![](https://gitee.com/cbuc/picture/raw/master/typora/image-20210613223345261.png)

然后我们做个简单的流控设置：

![](https://gitee.com/cbuc/picture/raw/master/typora/image-20210613223240236.png)

访问结果：

![](https://gitee.com/cbuc/picture/raw/master/typora/image-20210613223854558.png)

![](https://gitee.com/cbuc/picture/raw/master/typora/image-20210613223908571.png)

这种提示的方式显然更加友好！

#### 2）Sentinel 规则持久化

已经上手尝试的同学可能会发现一个问题，当我们的项目重启，或者 Sentinel 控制台重启都会导致配置被清空了！这是因为这些规则默认是存放在内存中，这可是很大的问题！因此**规则持久化**是一个必不可少的工作！当然在 **Sentinel** 也已经很好的支持了这项功能，处理逻辑如下：

![](https://gitee.com/cbuc/picture/raw/master/typora/image-20210613224611238.png)

实话说配置类代码有点长，这里直接贴代码了，有需要的小伙伴可以拷过去直接用！

```java
public class FilePersistence implements InitFunc {

    @Override
    public void init() throws Exception {
        String ruleDir = new File("").getCanonicalPath() + "/sentinel-rules";
        String flowRulePath = ruleDir + "/flow-rule.json";
        String degradeRulePath = ruleDir + "/degrade-rule.json";
        String systemRulePath = ruleDir + "/system-rule.json";
        String authorityRulePath = ruleDir + "/authority-rule.json";
        String paramFlowRulePath = ruleDir + "/param-flow-rule.json";
        this.mkdirIfNotExits(ruleDir);
        this.createFileIfNotExits(flowRulePath);
        this.createFileIfNotExits(degradeRulePath);
        this.createFileIfNotExits(systemRulePath);
        this.createFileIfNotExits(authorityRulePath);
        this.createFileIfNotExits(paramFlowRulePath);
        // 流控规则sentinel
        ReadableDataSource<String, List<FlowRule>> flowRuleRDS = new
                FileRefreshableDataSource<>(
                flowRulePath,
                flowRuleListParser
        );
        FlowRuleManager.register2Property(flowRuleRDS.getProperty());
        WritableDataSource<List<FlowRule>> flowRuleWDS = new
                FileWritableDataSource<>(
                flowRulePath,
                this::encodeJson
        );
        WritableDataSourceRegistry.registerFlowDataSource(flowRuleWDS);
        // 降级规则
        ReadableDataSource<String, List<DegradeRule>> degradeRuleRDS = new
                FileRefreshableDataSource<>(
                degradeRulePath,
                degradeRuleListParser
        );
        DegradeRuleManager.register2Property(degradeRuleRDS.getProperty());
        WritableDataSource<List<DegradeRule>> degradeRuleWDS = new
                FileWritableDataSource<>(
                degradeRulePath,
                this::encodeJson
        );
        WritableDataSourceRegistry.registerDegradeDataSource(degradeRuleWDS);
        // 系统规则
        ReadableDataSource<String, List<SystemRule>> systemRuleRDS = new
                FileRefreshableDataSource<>(
                systemRulePath,
                systemRuleListParser
        );
        SystemRuleManager.register2Property(systemRuleRDS.getProperty());
        WritableDataSource<List<SystemRule>> systemRuleWDS = new
                FileWritableDataSource<>(
                systemRulePath,
                this::encodeJson
        );
        WritableDataSourceRegistry.registerSystemDataSource(systemRuleWDS);
        // 授权规则
        ReadableDataSource<String, List<AuthorityRule>> authorityRuleRDS = new
                FileRefreshableDataSource<>(
                authorityRulePath,
                authorityRuleListParser
        );
        AuthorityRuleManager.register2Property(authorityRuleRDS.getProperty());
        WritableDataSource<List<AuthorityRule>> authorityRuleWDS = new
                FileWritableDataSource<>(
                authorityRulePath,
                this::encodeJson
        );
        WritableDataSourceRegistry.registerAuthorityDataSource(authorityRuleWDS);
// 热点参数规则
        ReadableDataSource<String, List<ParamFlowRule>> paramFlowRuleRDS = new
                FileRefreshableDataSource<>(
                paramFlowRulePath,
                paramFlowRuleListParser
        );
        ParamFlowRuleManager.register2Property(paramFlowRuleRDS.getProperty());
        WritableDataSource<List<ParamFlowRule>> paramFlowRuleWDS = new
                FileWritableDataSource<>(
                paramFlowRulePath,
                this::encodeJson
        );
        ModifyParamFlowRulesCommandHandler.setWritableDataSource(paramFlowRuleWDS);
    }

    private final Converter<String, List<FlowRule>> flowRuleListParser = source ->
            JSON.parseObject(
                    source,
                    new TypeReference<List<FlowRule>>() {
                    }
            );

    private final Converter<String, List<DegradeRule>> degradeRuleListParser = source
            -> JSON.parseObject(
            source,
            new TypeReference<List<DegradeRule>>() {
            }
    );

    private final Converter<String, List<SystemRule>> systemRuleListParser = source ->
            JSON.parseObject(
                    source,
                    new TypeReference<List<SystemRule>>() {
                    }
            );
    private final Converter<String, List<AuthorityRule>> authorityRuleListParser =
            source -> JSON.parseObject(
                    source,
                    new TypeReference<List<AuthorityRule>>() {
                    }
            );
    private final Converter<String, List<ParamFlowRule>> paramFlowRuleListParser =
            source -> JSON.parseObject(
                    source,
                    new TypeReference<List<ParamFlowRule>>() {
                    }
            );

    private void mkdirIfNotExits(String filePath) throws IOException {
        File file = new File(filePath);
        if (!file.exists()) {
            file.mkdirs();
        }
    }

    private void createFileIfNotExits(String filePath) throws IOException {
        File file = new File(filePath);
        if (!file.exists()) {
            file.createNewFile();
        }
    }

    private <T> String encodeJson(T t) {
        return JSON.toJSONString(t);
    }
}
```

然后在 `resources` 下创建配置目录 `META-INF/services` ,然后添加文件`com.alibaba.csp.sentinel.init.InitFunc`在文件中添加配置类的全路径

![](https://gitee.com/cbuc/picture/raw/master/typora/image-20210613230725206.png)

![](https://gitee.com/cbuc/picture/raw/master/typora/image-20210613230748843.png)

这样子我们启动项目的时候就会生成 Sentinel 的配置文件了

![](https://gitee.com/cbuc/picture/raw/master/typora/image-20210613230858494.png)

当我们在控制台中添加一条流控规则后，对应的 json 文件就会有对应的配置

![](https://gitee.com/cbuc/picture/raw/master/typora/image-20210613231042833.png)

![](https://gitee.com/cbuc/picture/raw/master/typora/image-20210613231025922.png)

到这里我们就完成了 Sentinel 的持久化功能，到这里我们也完成了对 SpringCloud 中Sentinel 的介绍！

> 后面会继续整理关于 SpringCloud 组件的文章，敬请关注！

不要空谈，不要贪懒，和小菜一起做个`吹着牛X做架构`的程序猿吧~点个关注做个伴，让小菜不再孤单。咱们下文见！

> 今天的你多努力一点，明天的你就能少说一句求人的话！
>
> *我是小菜，一个和你一起变强的男人。* `💋`
>
>
> 微信公众号已开启，**小菜良记**，没关注的同学们记得关注哦！
