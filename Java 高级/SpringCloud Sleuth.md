大家好，我是小菜。
一个希望能够成为 **吹着牛X谈架构** 的男人！如果你也想成为我想成为的人，不然点个关注做个伴，让小菜不再孤单！


>本文主要介绍 `SpringCloud中动态链路追踪`
>
>如有需要，可以参考
>
>如有帮助，不忘 **点赞** ❥
>
>
>微信公众号已开启，**小菜良记**，没关注的同学们记得关注哦！

关于微服务的概念我们在之前的两篇文章中都已经做出了相应的见解，没看过的小伙伴可以空降查看一番，不同见解欢迎后台留言！

- [《吃透微服务》 - 服务网关之Gateway](https://mp.weixin.qq.com/s/EPzc9bVhiqSZUSBygsI2AQ)

- [《吃透微服务》 - 服务容错之Sentinel](https://mp.weixin.qq.com/s/EPzc9bVhiqSZUSBygsI2AQ)

这么这篇我们继续我们的主题 **《吃透微服务》** ，继续了解分布式系统中解决链路追踪的方案 --- **Sleuth**。**吃透** 当然可以作为一个噱头，但也可以作为一个目标！

微服务的好坏我们不再争议，最近看到很多反面宣传微服务的特性，不知道是否大数据精准投喂~ `ps:估计后台人物画像已经标签为一个反微分子了`。这当然是一句玩笑话，结束玩笑，我们来为微服务的学习添砖加瓦！

微服务的关键在于拆分服务，但是关键不在 **微** ，而在于合适的大小。当我们跨出拆分服务的那一刻起，我们就得对我们拆分后的服务负责，这个是一个程序员应有的担当！当服务出现问题的时候，你是否还苦于排查，而无从定位。线上的代码我们无法 **debug**，找出问题的时刻往往出现以下对话：

![](https://gitee.com/cbuc/picture/raw/master/typora/image-20210626221327980.png)

除了无奈的苦笑，只能盲目的摸索，终于有一天程序员小菜不再妥协，力求解决该问题。咱们能不能搞个类似监控的功能，当请求进来的时候，分配一个身份令牌的给它，当它在微服务中游走的时候，每个服务都会留下它的痕迹，当它出现问题的时候，我们只需要检查游走到哪一个微服务，相当于画一个行为轨迹，通过行为轨迹追踪服务的调用问题！沾沾自喜的同时犯嘀咕了，这说起来容易，实现起来好像有点困难~**退堂鼓即将敲响**，便想到微服务中怎么可能没有人想到该问题，于是便游走于微服务组件之中，力求找到解决问题的实现。终于，**Sleuth** 出现了。

![](https://gitee.com/cbuc/picture/raw/master/typora/1624717255-70644.gif)

### Sleuth

#### 一、认识 Sleuth

前面做了那么多的铺垫，就是想引出 **Sleuth** 这玩意。洋洋洒洒说了一堆，那就简单用一句话描述一下它的作用：`在分布式系统中提供追踪解决方案`。它大量借用了 **Google Dapper**  的设计，难道又是个换壳的玩意？

我们先来了解一下 **Sleuth** 中的术语和相关概念，只为了让我们更加专业，更好 **吹着牛x谈架构**

- **Trace**

由一组 Trace Id 相同的 Span 串联形成一个树状结构。为了实现请求追踪，当请求到达分布式系统的入口端点时，只需要服务跟踪框架为该请求创建一个唯一的标识（即 **Trace Id**），同时在恩不是系统内部流转的时候，框架是中保持传递该唯一值，直到整个请求的返回。那么我们就可以使用该唯一标识将所有的请求串联起来，形成一条完整的请求链路。

- **Span**

代表了一组基本的工作单元。为了统计各处理单元的延迟，当请求到达各个服务组件的时候，也通过一个唯一标识（**SpanId**）来标记它的开始，具体过程和结束。通过 SpanId 的开始和结束时间戳，就能统计该 Span 的调用时间，除此之外，我们还可以获取如事件的名称，请求信息等元数据

- **Annotation**

用于记录一段时间内的事件。内部使用的重要注释如下：

1. **cs（Client Send）**：客户端发出请求，开始一个请求的事件
2. **sr（Server Received）** ：服务端接受到请求开始进行处理。`sr - cs = 网络延迟（服务调用的时间）`

3. **ss（Server Send）**：服务端处理完毕准备发送到客户端。`ss -sr = 服务器上的请求处理时间`

4. **cr（Client Reveived）**：客户端接受到服务端的响应，请求结束。`cr - sr = 请求的总时间`

![](https://gitee.com/cbuc/picture/raw/master/typora/image-20210626224734891.png)

理解完三个概念，我们下面就直接上手认识！

#### 二、掌握 Sleuth

我们老样子请出我们的微服务项目：

![](https://gitee.com/cbuc/picture/raw/master/typora/image-20210626225321779.png)

简简单单几个服务做个自我介绍

- **store-gateway：** 服务网关
- **store-order：** 订单服务
- **store-product：** 产品服务

然后我们需要在父工程中引入 **Sleuth** 依赖

```xml
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-starter-sleuth</artifactId>
</dependency>
```

>  直接在父工程的 dependencies 中引入，这样子每个子服务就可以不同重复引入该依赖

然后开始我们的简易代码模式：

**store-order服务**

`OrderController:`

![](https://gitee.com/cbuc/picture/raw/master/typora/image-20210626232522588.png)

`ProductService:`

![](https://gitee.com/cbuc/picture/raw/master/typora/image-20210626233425410.png)

**store-product 服务**

`ProductController:`

![](https://gitee.com/cbuc/picture/raw/master/typora/image-20210626233503726.png)

我们在**订单服务** 中使用 `Feign` 远程调用 **产品服务** 中的接口，然后启动服务调用后看控制台打印：

![](https://gitee.com/cbuc/picture/raw/master/typora/image-20210626233341688.png)

注意看我圈出来的部分，有没有发现了些许不同，没错！这一串字符中包含的信息有 **服务名 / TraceId / SpanId**。依次调用有一个全局的 **TraceId**，将调用链路穿起来。通过分析微服务的日志，不难看出请求的具体过程。但是有个弊端，但服务数量增多，或者日志数量增多，从日志文件中捞出某个请求的调用过程，可并非是件易事，那么有没有一个可以全文检索和可视化展示的插件帮助我们解决该问题？既然提出问题，小菜自然已经找到解决问题的方法！那就是 **ZipKin**

#### 三、使用 ZipKin

**ZipKin** 是 **Twitter** 开源的一个项目，它也是基于 **Google Dapper** 实现的，主要作用便是解决我们上面提到的问题：**收集服务的定时数据，以解决微服务架构中的延迟问题，包括数据的收集，存储，查找和展现**

我们可以使用它来收集各个服务器上请求链路的跟踪数据，并通过它提供的 REST API 接口来辅助我们查询跟踪数据以实现对分布式系统的监控程序，从而及时地发现系统中出现的延迟升高问题并找出系统性能瓶颈的根源！

我们除了面向开发的 API 接口之外， **ZipKin** 也提供了方便的 UI 组件来帮我们更加直观的搜索跟踪信息和分析请求链路明细，比如：可以查询某段时间内各用户请求的处理时间等。

> 听到 UI 组件是不是感到眼前一亮，说明我们可以通过控制台更好的管理链路跟踪

不仅如此，**ZipKin** 还提供了可插拔式的数据存储方式，例如：**In-Memory、MySQL、Cassandra以及Elasticsearch**。支持方式不算少，总有一种你喜欢的！

![](https://gitee.com/cbuc/picture/raw/master/typora/1624771866-31762.gif)

我们来看看 **ZipKin** 的基础架构图：

![](https://gitee.com/cbuc/picture/raw/master/typora/image-20210627134310854.png)

可以看出 **ZipKin** 结构并不复杂，有四个核心组件构成：

- **Collector**：收集器组件。主要用于处理外部系统发现过来的跟踪信息，然后将这些信息转换为 **ZipKin** 内部处理的 **Span** 格式，以便支持后续的存储、分析、展示等功能
- **Storage：** 存储组件。主要用于对处理收集器接收到的跟踪信息，默认会将这些信息存储到内存中，我们可以修改存储策略，将其存储到我们的其他存储仓库中
- **RestFul API**：API 组件。用于提供外部访问的接口，比如客户端的跟踪信息，或外接系统的访问信息
- **WebUI**：UI 组件。基于 API 组件实现的上层应用，通过 UI 组件用户可以方便而直观地查询和分析跟踪信息

因此 **ZiPKin** 使用起来有些类似我们上篇说到的 **Sentinel** ，它分为 **服务端** 和 **客户端**。

客户端就是指微服务中的应用，在客户端中配置服务端的 URL 地址，一旦发生服务间的调用的时候，会被配置在微服务中的 **Sleuth** 的监听器监听，并生相应的 **Trace** 和 **Span** 信息发送给服务端。

##### 1. ZipKin 服务端

服务端是一个现成的 SpringBoot 服务，我们只需要下载 **jar 包** ，便可以直接运行！[下载地址](https://search.maven.org/remote_content?g=io.zipkin.java&a=zipkin-server&v=LATEST&c=exec)

然后我们通过命令行的方式启动即可：

```shell
java -jar zipkin-server-2.12.9-exec.jar
```

成功启动后通过访问 `http://localhost:9411` 可以看到以下页面：

![](https://gitee.com/cbuc/picture/raw/master/typora/image-20210627135719889.png)

到这步为止，我们服务端就已经成功安装。然后回到我们的客户端集成上去

##### 2. ZipKin 客户端

我们需要在每个服务中引入 **ZipKin** 的依赖

```xml
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-starter-zipkin</artifactId>
</dependency>
```

然后在配置文件中进行配置：

```yaml
spring:
  zipkin:
    base-url: http://127.0.0.1:9411/ 	#zipkin 服务端地址
    discoveryClientEnabled: false 		#让nacos把它当成一个URL，而不要当做服务名
  sleuth:
    sampler:
      probability: 1.0 #采样的百分比
```

然后我们回到项目中，启动项目通过访问我们上面已经定义好的接口访问微服务，之后在 **ZipKin** 的UI界面进行观察

![](https://gitee.com/cbuc/picture/raw/master/typora/image-20210627155523399.png)

可以看到已经出现了我们刚刚访问微服务的请求链路了，点击其中一条记录可以查看具体的访问路线。

![](https://gitee.com/cbuc/picture/raw/master/typora/image-20210627155616004.png)

通过对请求链路进行追踪，就可以确定服务的哪一个模块更耗时，进而可以进行优化或者排查 **Bug**。

##### 3. ZipKin 持久化

在 ZipKin 中默认会将链路跟踪的数据保存到内存中，但是这种方式明显不适合于生产环境。因此在 **ZipKin** 中支持将追踪链路的数据持久化到 **mysql** 数据库中或 **elasticsearch** 中。

###### MySQL

- **首先我们需要配置数据库信息**

```sql
CREATE TABLE IF NOT EXISTS zipkin_spans (
    `trace_id_high` BIGINT NOT NULL DEFAULT 0 COMMENT 'If non zero, this means the trace uses 128 bit traceIds instead of 64 bit',
    `trace_id` BIGINT NOT NULL,
    `id` BIGINT NOT NULL,
    `name` VARCHAR(255) NOT NULL,
    `parent_id` BIGINT,
    `debug` BIT(1),
    `start_ts` BIGINT COMMENT 'Span.timestamp(): epoch micros used for endTs query and to implement TTL',
    `duration` BIGINT COMMENT 'Span.duration(): micros used for minDuration and maxDuration query'
) ENGINE=InnoDB ROW_FORMAT=COMPRESSED CHARACTER SET=utf8 COLLATE utf8_general_ci;

ALTER TABLE zipkin_spans ADD UNIQUE KEY(`trace_id_high`, `trace_id`, `id`) COMMENT 'ignore insert on duplicate';
ALTER TABLE zipkin_spans ADD INDEX(`trace_id_high`, `trace_id`, `id`) COMMENT 'for joining with zipkin_annotations';
ALTER TABLE zipkin_spans ADD INDEX(`trace_id_high`, `trace_id`) COMMENT 'for getTracesByIds';
ALTER TABLE zipkin_spans ADD INDEX(`name`) COMMENT 'for getTraces and getSpanNames';
ALTER TABLE zipkin_spans ADD INDEX(`start_ts`) COMMENT 'for getTraces ordering and range';
```

```sql
CREATE TABLE IF NOT EXISTS zipkin_annotations (
    `trace_id_high` BIGINT NOT NULL DEFAULT 0 COMMENT 'If non zero, this means the trace uses 128 bit traceIds instead of 64 bit',
    `trace_id` BIGINT NOT NULL COMMENT 'coincides with zipkin_spans.trace_id',
    `span_id` BIGINT NOT NULL COMMENT 'coincides with zipkin_spans.id',
    `a_key` VARCHAR(255) NOT NULL COMMENT 'BinaryAnnotation.key or Annotation.value if type == -1',
    `a_value` BLOB COMMENT 'BinaryAnnotation.value(), which must be smaller than 64KB',
    `a_type` INT NOT NULL COMMENT 'BinaryAnnotation.type() or -1 if Annotation',
    `a_timestamp` BIGINT COMMENT 'Used to implement TTL; Annotation.timestamp or zipkin_spans.timestamp',
    `endpoint_ipv4` INT COMMENT 'Null when Binary/Annotation.endpoint is null',
    `endpoint_ipv6` BINARY(16) COMMENT 'Null when Binary/Annotation.endpoint is null, or no IPv6 address',
    `endpoint_port` SMALLINT COMMENT 'Null when Binary/Annotation.endpoint is null',
    `endpoint_service_name` VARCHAR(255) COMMENT 'Null when Binary/Annotation.endpoint is null'
) ENGINE=InnoDB ROW_FORMAT=COMPRESSED CHARACTER SET=utf8 COLLATE utf8_general_ci;

ALTER TABLE zipkin_annotations ADD UNIQUE KEY(`trace_id_high`, `trace_id`, `span_id`, `a_key`, `a_timestamp`) COMMENT 'Ignore insert on duplicate';
ALTER TABLE zipkin_annotations ADD INDEX(`trace_id_high`, `trace_id`, `span_id`) COMMENT 'for joining with zipkin_spans';
ALTER TABLE zipkin_annotations ADD INDEX(`trace_id_high`, `trace_id`) COMMENT 'for getTraces/ByIds';
ALTER TABLE zipkin_annotations ADD INDEX(`endpoint_service_name`) COMMENT 'for getTraces and getServiceNames';
ALTER TABLE zipkin_annotations ADD INDEX(`a_type`) COMMENT 'for getTraces';
ALTER TABLE zipkin_annotations ADD INDEX(`a_key`) COMMENT 'for getTraces';
ALTER TABLE zipkin_annotations ADD INDEX(`trace_id`, `span_id`, `a_key`) COMMENT 'for dependencies job';
```

```sql
CREATE TABLE IF NOT EXISTS zipkin_dependencies (
    `day` DATE NOT NULL,
    `parent` VARCHAR(255) NOT NULL,
    `child` VARCHAR(255) NOT NULL,
    `call_count` BIGINT
) ENGINE=InnoDB ROW_FORMAT=COMPRESSED CHARACTER SET=utf8 COLLATE utf8_general_ci;
ALTER TABLE zipkin_dependencies ADD UNIQUE KEY(`day`, `parent`, `child`);
```

![](https://gitee.com/cbuc/picture/raw/master/typora/image-20210627155754805.png)

- 启动时指定 mysql 信息

```shell 
java -DSTORAGE_TYPE=mysql -DMYSQL_HOST=127.0.0.1 -DMYSQL_TCP_PORT=3306 -DMYSQL_DB=zipkin -DMYSQL_USER=root -DMYSQL_PASS=root -jar zipkin-server-2.12.9-exec.jar 
```

###### ElasticSearch

- 启动 ES
- 启动时指定 ES 信息

```shell
java -jar zipkin-server-2.12.9-exec.jar --STORAGE_TYPE=elasticsearch --ES-HOST=localhost:9200
```

**END**

到这里我们就完成了分布式系统中服务链路跟踪的解决！那为我们解决了问题呢？

- 跨微服务的API调用发生异常，快速定位出问题出在哪里。
- 跨微服务的API调用发生性能瓶颈，迅速定位出性能瓶颈。

不要空谈，不要贪懒，和小菜一起做个`吹着牛X做架构`的程序猿吧~点个关注做个伴，让小菜不再孤单。咱们下文见！

![看完不赞，都是坏蛋](https://imgconvert.csdnimg.cn/aHR0cHM6Ly93d3cuNTJkb3V0dS5jbi9zdGF0aWMvdGVtcC9waWMvOWJkNjhkMTUwZjA3ODdjNTYwYTQzOWRhMzU5YTU4MGEucG5n?x-oss-process=image/format,png#pic_center)

> 今天的你多努力一点，明天的你就能少说一句求人的话！
> *我是小菜，一个和你一起变强的男人。* `💋`
> 微信公众号已开启，**小菜良记**，没关注的同学们记得关注哦！
