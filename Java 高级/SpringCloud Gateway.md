大家好，我是小菜。


>本文主要介绍 `SpringCloud之服务网关Gateway`
>
>如有需要，可以参考
>
>如有帮助，不忘 **点赞** ❥
>
>
>微信公众号已开启，**小菜良记**，没关注的同学们记得关注哦！


前段时间与小伙伴闲聊时说到他们公司的现状，近来与将来，公司将全面把单体服务向微服务架构过渡。这里面我们听到了关键词 --- **微服务**。乍听之下，觉得也很合理。互联网在不断的发展，这里不只是行业的发展，更是系统架构的发展。现在市面上架构大致也经历了这几个阶段：

![](https://gitee.com/cbuc/picture/raw/master/typora/image-20210608202313008.png)

这是好事吗？是好事，毋庸置疑。因为不跟上时代的浪潮，总会被拍死在沙滩上。但完全是好事吗？那也不见得。

我们先要明白微服务解决了什么问题？大方面上应该就是应用层面和人层面的问题

- **应用层面：** 单体服务的架构很简单，项目开发和维护成本低是它无争议的优点。但是臃肿耦合又会给基础设施带来了过重的负担。如果某个应用处理资源占用了大量的CPU，就会导致其他处理资源饿死的现象，系统延迟增高，直接影响系统的可用性。

- **人层面：**` 独立`、`多语言生态` 也是微服务的标签。在单体服务中，投入的人力资源越多不见得越高效，反而越容易出错。但微服务不同，每个服务都是独立出来的，团队可以更加容易的协作开发，甚至一套系统，多个服务，多种语言，毫无冲突。

![](https://gitee.com/cbuc/picture/raw/master/typora/image-20210608204158387.png)

但是我们凡事不能被好处蒙蔽。微服务的缺点也是一直存在的：

- 需要考虑各个服务之间的容错性问题
- 需要考虑数据一致性问题
- 需要考虑分布式事务问题
- ...

很多人认为微服务的核心就是在于 `微`。服务分的越细越好，就好像平时写代码的时候丝毫不考虑的单一原则，反而在服务拆分上用到淋漓尽致。这里就需要明白一个核心概念：`微服务的关键不在微，而是在于合适的大小`

这句话好像很简单的样子，但是多大才算合适的大小？这可能据不同团队，不同项目而定。合适的大小可能取决于更少的代码仓库，更少的部署队列，更少的语言... 这里更无法一锤定音！

如果没法做到合适的大小，而无脑的拆分服务，那么微服务可能反而成为你项目的累赘。因此，有时全面转型微服务反而不是好事，你觉得呢？

话题有点跑远了，咱们努力扯回来。既然微服务已经成为主流，那么如何设计微服务便是我们应该做的事，而不是谈及微服务之时，想到的只是与人如何争论如何拒用微服务。那么这篇我们要讲的是`SpringCloud之服务网关Gateway`

### SpringCloud之服务网关Gateway

#### 一、什么是服务网关

什么是服务网关？不要给自己当头一棒。我们换个问法，为什么需要服务网关？

> 服务网关是跨一个或多个服务节点提供单个统一的访问入口

它的作用并不是可有可无的存在，而是至关重要。我们可以在服务网关做`路由转发`和`过滤器`的实现。优点简述如下：

- 防止内部服务关注暴露给外部客户端
- 为我们内部多个服务添加了额外的安全层
- 减低微服务访问的复杂性

![](https://gitee.com/cbuc/picture/raw/master/typora/image-20210608210820196.png)

根据图中内容，我们可以得出以下信息：

- 用户访问入口，统一通过网关访问到其他微服务节点
- 服务网关的功能有`路由转发`，`API监控`、`权限控制`、`限流`

而这些便是 **服务网关** 存在的意义！

#### 二、认识Gateway

**SpringCloud Gateway** 是 SpringCloud 的一个全新项目，目标是取代Netflix Zuul。它是基于 `Spring5.0 + SpringBoot2.0 + WebFlux` 等技术开发的，性能高于 Zuul，据官方提供的信息来看，性能是 Zuul 的1.6倍，意在为微服务架构提供一种简单有效的统一的 API 路由管理方式。

**SpringCloud Gateway** 不仅提供了统一的路由方式（反向代理），并且基于 Filter 链（定义过滤器对请求过滤）提供了网关基本的功能，例如：鉴权、流量控制、熔断、路径重写、日志监控等

##### 1）Zuul 比较

其实说到 Netflix Zuul，在使用或准备使用微服务架构的小伙伴应该并不陌生，毕竟Netflix 是一个老牌微服务开源者。新秀与老牌之间的争夺，如果新秀没有点硬实力，如何让人安心转型！

这里我们可以顺带了解一下 **Weflux**，**Webflux** 的出现填补了 Spring 在响应式编程上的空白。

> 可能有很多小伙伴并不知道 Webflux，小菜接下来也会出一篇关于 Webflux 的讲解，实则真香！

Webflux 的响应式编程不仅仅是编程风格上的改变，而是对于一系列著名的框架都提供了响应式访问的开发包，比如 **Netty**、**Redis**（如果不知道 Netty 的实力，可以想想为什么 Nginx 可以承载那么大的并发，底层就是基于Netty）

那么说这么多，跟 **Zuul** 有什么关系呢？我们可以看下 **Zuul** 的 IO 模型

SpringCloud 中所集成的 Zuul 版本，采用的是 Tomcat 容器，使用的是传统的 Servlet IO 处理模型。Servlet 是由 Servlet Container 管理生命周期的。

![](https://gitee.com/cbuc/picture/raw/master/typora/image-20210608220440384.png)

但问题就在于 **Servlet** 是一个简单的网络IO模型，当请求进入到 **ServletContainer**就会为其绑定一个线程，在并发不高的场景下这种模型是没有问题的，但是一旦并发上来了，线程数量就会增加。那导致的问题就是频繁进行上下文切换，内存消耗严重，处理请求的时间就会变长。正所谓牵一发而动全身！

而 SpriingCloud Zuul 便是基于 servlet 之上的一个阻塞式处理模型，即Spring实现了处理所有 request 请求的一个 servlet（DispatcherServlet），并由该 Servlet 阻塞式处理。虽然 SpringCloud Zuul 2.0 开始，也是用了 Netty 作为并发IO框架，但是 SpringCloud 官方已经没有集成该版本的计划！

> 注：这里没有推崇 Gateway 的意思，具体使用依具体项目而定

##### 2）Gateway 的处理流程

![](https://gitee.com/cbuc/picture/raw/master/typora/image-20210608222440937.png)

1. 客户端向 SpringCloud Gateway 发出请求
2. SpringCloud Gateway 会在 Gateway Handler Mapping 中找到与请求相匹配的路由
3. 找到相匹配的路由会将其发送到 Gateway Web Mapping 
4. Gateway Web Mapping 收到路由后再通过指定的过滤器将请求发送到我们实际的服务执行业务逻辑上
5. 具体的服务处理完请求返回

#### 三、掌握 Gateway

##### 1. Gateway 依赖

最关键的一步便是引入网关的依赖

```xml
<!--gateway网关-->
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-starter-gateway</artifactId>
</dependency>
```

##### 2. 项目结构

![](https://gitee.com/cbuc/picture/raw/master/typora/image-20210609232945176.png)

我这里简单的创建了一个微服务项目，项目里有一个 `store-gateway` 服务网关 和一个 `store-order` 订单服务。因为我们这篇只说明服务网关的作用，不需要太多服务提供者和消费者！

在`store-order`订单服务中只有一个控制器`OrderController`，里面也只有一个简单到发指的API

```java
@RestController
@RequestMapping("order")
public class OrderController {
    
    @GetMapping("/{id:.+}")
    public String detail(@PathVariable String id) {
        return StringUtils.join("获取到了ID为", id, "的商品");
    }
    
}
```

我们分别启动两个服务，然后访问订单服务的API：

![](https://gitee.com/cbuc/picture/raw/master/typora/image-20210609233249610.png)



结果肯定是符合预期的，不至于翻车。`8001` 是订单服务的接口，这个时候我们可以了解到，原来微服务架构每个服务独立启动，都是可以独立访问的，也就相当于传统的单体服务。

我们想想看，如果用端口来区分每个服务，是否也可以达到微服务的效果？理论上好像是可以的，但是如果成百上千个服务呢？端口爆炸，维护爆炸，治理爆炸... 不说别的，心态直接爆炸了！这个时候我们就想着如果只用统一的一个端口访问各个服务，那该多好！端口一致，路由前缀不一致，通过路由前缀来区分服务，这种方式将我们带入了服务网关的场景。是的，这里就说到了服务网关的功能之一 --- `路由转发`。

##### 3. 网关出现

既然要用到网关，那我们上面创建的服务之一 `store-gateway` 就派上用场了！怎么用？我们在配置文件做个简单的修改：

```yaml
spring:
  application:
    name: store-gateway
  cloud:
    gateway:
      routes: 
        - id: store-order 
          uri: http://localhost:8001 
          order: 1
          predicates:
            - Path=/store-order/** 
          filters:
            - StripPrefix=1
```

不多废话，我们直接启动网关，通过访问`http://localhost:8000/store-order/order/123` 看是否能获取到订单？

![](https://gitee.com/cbuc/picture/raw/master/typora/image-20210609234125248.png)

很顺利，我们成功拿到了ID 为 123 的订单商品！

我们看下 URL 的组成：

