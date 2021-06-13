大家好，我是小菜。

一个希望能够成为 **吹着牛X谈架构** 的男人！如果你也想成为我想成为的人，不然点个关注做个伴，让小菜不再孤单！


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

#### 一、认识网关

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

##### 1）Zuul 比较

**SpringCloud Gateway** 是 SpringCloud 的一个全新项目，目标是取代Netflix Zuul。它是基于 `Spring5.0 + SpringBoot2.0 + WebFlux` 等技术开发的，性能高于 Zuul，据官方提供的信息来看，性能是 Zuul 的1.6倍，意在为微服务架构提供一种简单有效的统一的 API 路由管理方式。

**SpringCloud Gateway** 不仅提供了统一的路由方式（反向代理），并且基于 Filter 链（定义过滤器对请求过滤）提供了网关基本的功能，例如：鉴权、流量控制、熔断、路径重写、日志监控等。

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

#### 三、掌握网关

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

![](https://gitee.com/cbuc/picture/raw/master/typora/image-20210610210147390.png)

能够访问到我们的服务，说明网关配置生效了，我们再来看下这么配置项是怎么一回事！

`spring.cloud.gateway` 这个是服务网关 Gateway 的配置前缀，没什么好说的，自己需要记住就是了。

`routes` 以下就是我们值得关注的了，`routes` 是个复数形式，那么可以肯定，这个配置项是个数组的形式，因此意味着我们可以配多个路由转发，当请求满足什么条件的时候转到哪个微服务上。

- **id：** 当前路由的`唯一`标识
- **uri：** 请求要转发到的地址
- **order**：路由的优先级，数字越小级别越高
- **predicates：** 路由需要满足的条件，也是个数组（这里是`或`的关系）
- **filters：** 过滤器，请求在传递过程中可以通过过滤器对其进行一定的修改

了解完必要的参数，我们也高高兴兴去部署使用了，但是好景不长，我们又迎来了新的问题。我订单服务原先使用的 `8001` 端口，因为某些原因给其他服务使用了，这个时候小脑袋又大了，这种情况肯定不会出现 `上错花轿嫁对郎` 的结果！

咱们想想看这种问题要怎么解决比较合适？既然都采用微服务了，那我们能不能采用服务名的方式跳转访问，这样子无论端口怎么变，都不会影响到我们的正常业务！那如果采用服务的方式，就需要一个`注册中心`，这样子我们启动的服务可以同步到`注册中心`的 **注册表** 中，这样子网关就可以根据 `服务名` 去注册中心中寻找服务进行路由跳转了！那咱们就需要一个注册中心，这里就采用 **Nacos** 作为注册中心.

> 关于 **Nacos** 的了解，可以空降 [微服务新秀之Nacos，看了就会，我说的！](https://mp.weixin.qq.com/s/WzbLt3hS6iCgEzDInpn70g)

我们分别在服务网关 和 订单服务的配置文件都做了以下配置：

![](https://gitee.com/cbuc/picture/raw/master/typora/image-20210610224807229.png)

启动两个服务后，我们可以在 **Nacos** 的控制台服务列表中看到两个服务：

![](https://gitee.com/cbuc/picture/raw/master/typora/image-20210610225437073.png)

这个时候可以看到 **订单服务** 的服务名为：`store-order`，那我们就可以在网关配置文件部分做以下修改：

![](https://gitee.com/cbuc/picture/raw/master/typora/image-20210610230029080.png)

这里的配置与上述不同点之一 `http` 换成了 `lb`（**lb** 指的是从nacos中按照名称获取微服务，并遵循负载均衡策略），之二 `端口` 换成了 `服务名`

那我们继续访问上述URL看是否能够成功访问到订单服务：

![](https://gitee.com/cbuc/picture/raw/master/typora/image-20210610225816722.png)

结果依然没有翻车！这样子，不管订单服务的端口如何改变，只要我们的服务名不变，那么始终都可以访问到我们的对应的服务！

日子一天一天的过去~ 服务也一点一点的增加！终于有一天小菜烦了，因为每次增加服务都得去配置文件中增加一个`routes` 的配置列，虽然也只是 **CV** 的操作，但是，哪一天小菜不小心手一抖，那么~~~ 算了算了，找找看有没有什么可以偷懒的写法，终于不负小菜心，找到了一种简化版！配置如下：

![](https://gitee.com/cbuc/picture/raw/master/typora/image-20210610230521282.png)

就这？是的，就这！管你服务再怎么多，我配置文件都不用修改。这样子配置的目的便是请求统一方式都是变成了 `网关地址:网关端口/服务名/接口名` 的方式访问。再次访问页面，依然行得通！

![](https://gitee.com/cbuc/picture/raw/master/typora/image-20210610230726845.png)

但是方便归方便，在方便的同时也限制了很多扩展功能，因此使用需三思！不可`贪懒`！

#### 四、掌握核心

上面已经说完了网关的简单使用，看完的小伙伴肯定已经可以上手了！接下来我们继续趁热打铁，了解下 **Gateway** 网关的核心。不说别的，**路由转发**  肯定是网关的核心！我们从上面已经了解到一个具体路由信息载体，主要定义了以下几个信息（**回忆下**）：

- **id：** 路由的唯一标识，区别于其他Route
- **uri：** 路由指向目的地 uri，即客户端请求最终被转发到的微服务

- **order：** 用于多个 Route 之间的排序，数值越小排序越靠前，匹配优先级越高
- **predicate：** 用来进行条件判断，只有断言都返回真，才会真正的执行路由
- **filter：** 过滤器用于修改请求和响应信息

这里来梳理一下访问流程：

![](https://gitee.com/cbuc/picture/raw/master/typora/image-20210611220649499.png)

这张图很清楚的描述服务网关的调用流程（**盲目自信**）

1. **GatewayClient** 向 **GatewayServer** 发出请求
2. 请求首先会被 **HttpWebHandlerAdapter** 进行提取组转成网关上下文
3. 然后网关的上下文会传递到 **DispatcherHandler**，它负责将请求分发给 **RoutePredicateHandlerMapping**
4. **RoutePredicateHandlerMapping**负责路由查找，并更具路由断言判断路由是否可用
5. 如果断言成功，由 **FilteringWebHandler** 创建过滤器链并调用
6. 请求会一次经过 **PreFilter** ---> **微服务** ---> **PostFilter** 的方法，最终返回响应

过程了解了，我们抽取一下其中的关键！`断言` 和 `过滤器`

##### 1.  断言

**Predicate** 也就是断言，主要适用于进行条件判断，`只有断言都返回真，才会真正执行路由`

###### 1）断言工厂

SpringCloud Gateway 中内置了许多断言工厂，所有的这些断言都和 HTTP 请求不同的属性相匹配，具体如下；

- **基于 Datetime 类型的断言工厂**

该类型的断言工厂是根据时间做判断的

1、**AfterRoutePredicateFactory：** 接收`一个`日期参数，判断请求日期是否晚于指定日期

2、**BeforeRoutePredicateFactory：**接收`一个`日期参数，判断请求日期是否早于指定日期

3、**BetweenRoutePredicateFactory：**接收`两个`日期参数，判断请求日期是否在指定时间段内

- **基于远程地址的断言工厂 RemoteAddrRoutePredicateFactory**

该类型的断言工厂是接收`一个参数`，**IP** 地址端，判断请求主机地址是否在地址段中。（eq：-RemoteAddr=192.168.1.1/24）

- **基于Cookie的断言工厂 CookieRoutePredicateFactory**

该类型的断言工厂接收`两个参数`，Cookie 名字和一个正则表达式，判断请求 cookie 是否具有给定名称且值与正则表达式匹配。（eq：-Cookie=cbuc）

- **基于Header的断言工厂HeaderRoutePredicateFactory**

该类型的断言工厂接收`两个参数`，标题名称和正则表达式。判断请求 Header 是否具有给定名称且值与正则表达式匹配。（eq：-Header=X-Request）

- **基于Host的断言工厂 HostRoutePredicateFactory**

该类型的断言工厂接收`一个参数`，主机名模式。判断请求的**host** 是否满足匹配规则。（eq：-Host=**.cbuc.cn）

- **基于Method请求方法的断言工厂 MethodRoutePredicateFactory**

该类型的断言工厂接收`一个参数`，判断请求类型是否跟指定的类型匹配。（eq：-Method=GET）

- **基于Path请求路径的断言工厂 PathRoutePredicateFactory**

该类型的断言工厂接收`一个参数`，判断请求的URI部分是否满足路径规则。（-eq：-Path=/order/）

- **基于Query请求参数的断言工厂 QueryRoutePredicateFactory**

该类型的断言工厂接收`两个参数`，请求 **Param** 和 正则表达式。判断请求参数是否具有给定名称且值与正则表达式匹配。（eq：-Query=cbuc）

- **基于路由权重的断言工厂 WeightRoutePredicateFactory**

该类型的断言工厂接收`一个[组名，权重]`，然后对于同一个组内的路由按照权重转发

###### 2）使用

这么多断言工厂，这里就不一一使用演示了，我们结合几个断言工厂的使用演示一下。

我们老样子不多废话，直接上代码：

`CustomPredicateRouteFactory`

![](https://gitee.com/cbuc/picture/raw/master/typora/image-20210612154031414.png)

`配置文件`

![](https://gitee.com/cbuc/picture/raw/master/typora/image-20210612154450256.png)

**测试结果**

`success`

![](https://gitee.com/cbuc/picture/raw/master/typora/image-20210612154542954.png)

`fail`

![](https://gitee.com/cbuc/picture/raw/master/typora/image-20210612154528586.png)

惊呼 **Amazing** 的同时，不要着急的往下看，我们回归代码，看看，为什么一个可以访问成功，一个却访问失败了。两个方面：1. 两者访问的URL有哪些不同 2. 代码哪部分对 URL 做出了处理

> 先养成独立思考，再去看解决方法

当你思考完后，可能部分同学已经有结果了，那让我们继续往下看！首先是一个 `CustomRoutePredicateFactory`类，这个类的作用有点像拦截器，在做请求转发的时候进行了拦截，我们请求的时候可以打个断点：

![](https://gitee.com/cbuc/picture/raw/master/typora/image-20210612155008753.png)

可以看到，确实是拦截器的功能，在每个请求发起的时候做了拦截。那问题2 的结果就出来了，原来URL处理是在 **RoutePredicateFactory** 中做了处理，在 apply 方法中可以通过 `exchange.getRequest()` 拿到 **ServerHttpRequest** 对象，从而可以获取到请求的参数、请求方式、请求头等信息。 `shortcutFieldOrder()`方法也是重写的关键之一，我们需要这里返回，我们实体类中定义的属性，然后在`apply()`方法中才能接收到我们赋值的属性参数！

> 注意：如果自定义的实体中有多个属性需要判断，`shortcutFieldOrder()`方法中的顺序要跟配置文件中的参数顺序一致

那么当我们编写了该断言工厂后，如果让之生效？`@Component` 这个注解肯定必不可少了，目的就是让 Spring 容器管理。那么已经注册的断言工厂如何声明使用呢？那就得回到配置文件了！

![](https://gitee.com/cbuc/picture/raw/master/typora/image-20210612154450256.png)

我们这里重点看 `predicates` 这个配置项下的配置，分别有三个配置，一个是我们已经熟悉的 `Path` ，其他两个有点陌生，但是这里再看看 `Custom` 是不是又有点眼熟？是的，我们在上面好像定义了一个叫 `CustomRoutePredicate` 的断言工厂，两者有点相似，又好像差点什么。那我就再给你一个提示：

![](https://gitee.com/cbuc/picture/raw/master/typora/image-20210612155802770.png)

我们看下抽象的断言工厂有哪些自实现的类！其中是不是有 `PathRoutePredicateFactory`，没错，就是你想的那样！有没有一种**拨开雨雾见青天**的感觉！原来我们配置文件的 key 是以类名的前缀声明的，也就是说断言工厂类的格式必须是：` 自定义名称`+ **RoutePredicateFactory** 为后缀，然后在配置文件中声明。这样子举一反三，我们自然而然的就清楚了 ` - Before` 的作用，该作用便是：**限制请求时间在 xxx 之前**。

而 **- Custom=**`cbuc`，这个 cbuc 便是我们限制的规则，只有 name 为 cbuc 的用户才能请求成功。如果有多个参数，可以用` ,` 隔开，顺序需要与断言工厂中`shortcutFieldOrder()` 返回参数的顺序一致！

> 如果在自定义断言工厂的途中遇到了什么阻碍，不然看看内置的断言工厂是如何实现的。`多看源码总没错！`

##### 2. 过滤器

接下来进入第二个核心，也就是过滤器。该核心的作用也挺简单，就是在请求的传递过程中，对请求和响应做一系列的手脚。为了怕你划回去看请求流程过于麻烦，小菜贴心的再贴一遍流程图：

![](https://gitee.com/cbuc/picture/raw/master/typora/image-20210612161323080.png)

在 **Gateway** 的过滤器中又可以分为 **局部过滤器** 和 **全局过滤器**。听名称就知道其作用，**局部** 是用于某一个路由上的，**全局** 是用于所有路由上的。不过不管是 **局部** 还是 **全局**，生命周期都分为 `pre` 和 `post`。

- **pre：** 作用于路由到微服务之前调用。我们可以利用这种过滤器实现身份验证、在集群中选择请求的微服务，记录调试记录等
- **post：** 作用于路由到微服务之后执行。我们可以利用这种过滤器用来响应添加标准的 HTTP Header，收集统计信息和指标、将响应从微服务发送到客户端。

###### 1）局部过滤器

局部过滤器是针对于单个路由的过滤器。同样 Gateway 已经内置了许多过滤器

![](https://gitee.com/cbuc/picture/raw/master/typora/image-20210612162141254.png)

我们选几种常用的过滤器进行说明：（下列过滤器省略后缀 `GaewayFilterFactory`，完整名称为 前缀+后缀）

|     过滤器前缀      |             作用             |                        参数                        |
| :-----------------: | :--------------------------: | :------------------------------------------------: |
|     StripPrefix     |    用于截断原始请求的路径    |            使用数字表示要截断的路径数量            |
|  AddRequestHeader   |    为原始请求添加 Header     |                 Header 的名称及值                  |
| AddRequestParameter |    为原始请求添加请求参数    |                    参数名称及值                    |
|        Retry        |    针对不同的响应进行重试    |         reties、statuses、methods、series          |
|     RequestSize     | 设置允许接收最大请求包的大小 |            请求包大小，单位字节，默认5M            |
|       SetPath       |      修改原始请求的路径      |                    修改后的路径                    |
|     RewritePath     |      重写原始的请求路径      |    原始路径正则表达式以及重写后路径的正则表达式    |
|     PrefixPath      |    为原始请求路径添加前缀    |                      前缀路径                      |
| RequestRateLimiter  | 对请求限流，限流算法为令牌桶 | KeyResolver、reteLimiter、statusCode、denyEmptyKey |

> 内置的过滤器小伙伴们可以自己尝试一番，有问题欢迎提问！

我们接下来讲讲如何自定义过滤器工厂。`don't say so much`，我们上代码

`CustomGatewayFilterFactory`

![](https://gitee.com/cbuc/picture/raw/master/typora/image-20210612165204768.png)

`配置文件`

![](https://gitee.com/cbuc/picture/raw/master/typora/image-20210612165253716.png)

当我们开启请求计数的时候，可以看到控制台对于请求次数作了统计：

![](https://gitee.com/cbuc/picture/raw/master/typora/image-20210612165339372.png)

因此我们可以通过这种方式轻松实现局部过滤器

###### 2）全局过滤器

全局过滤器作用于所有路由，无需配置。通过全局过滤器可以实现对权限的统一校验，安全性验证等功能

老样子，我们先看看 **Gateway** 中存在哪些全局过滤器：

![](https://gitee.com/cbuc/picture/raw/master/typora/image-20210612170000028.png)

相对于局部过滤器，全局过滤器的命名就没有太多约束了，毕竟不需要在配置文件中进行配置。

我们熟悉一下经典的全局过滤器

|                          过滤器名称                          |                             作用                             |
| :----------------------------------------------------------: | :----------------------------------------------------------: |
|         **ForwardPathFilter / ForwardRoutingFilter**         |                      路径转发相关过滤器                      |
|                **LoadBalanceerClientFilter**                 |                   负载均衡客户端相关过滤器                   |
|      **NettyRoutingFilter / NettyWriteResponseFilter**       |                    Http 客户端相关过滤器                     |
|                 **RouteToRequestUrlFilter**                  |                     路由 URL 相关过滤器                      |
| **WebClientHttpRoutingFilter  / WebClientWriteResponseFilter** | 请求 WebClient 客户端转发请求真实的URL并将响应写入到当前的请求响应中 |
|                  **WebsocketRoutingFilter**                  |                     websocket 相关过滤器                     |

了解完内置的过滤器，我们再看看如何定义全局的过滤器！

`CustomerGlobalFilter`

![](https://gitee.com/cbuc/picture/raw/master/typora/image-20210612171405718.png)

> 对于全局过滤器，我们不需要在配置文件中配置，因为是作用于所有路由

`测试结果`

**success**

![](https://gitee.com/cbuc/picture/raw/master/typora/image-20210612171613455.png)

**fail**

![](https://gitee.com/cbuc/picture/raw/master/typora/image-20210612171548739.png)

可以看到，我们使用全局过滤器进行了`鉴权`处理，如果没有携带 **token** 将无法访问！

---

![](https://gitee.com/cbuc/picture/raw/master/typora/image-20210612171939444.png)

到这里我们已经了解到了服务网关的路由转发，权限校验甚至于可以基于`断言和过滤器`做出粗略简单的 API监控和限流

但其实对于 **API监控**和 **限流**，SpringCloud 中已经有了更好的组件完成这两项工作。毕竟单一原则，做的越多往往错的也越多！

> 后面会继续整理关于 SpringCloud 组件的文章，敬请关注！

对于微服务的框架，孰好孰坏由我们自己判定。但是不管孰好孰坏，面对一门新技术的产生，我们最需要做的便是接收它，包容它，然后用好它，是骡子是马，自己溜溜就知道了。

不要空谈，不要贪懒，和小菜一起做个`吹着牛X做架构`的程序猿吧~点个关注做个伴，让小菜不再孤单。咱们下文见！

![看完不赞，都是坏蛋](https://imgconvert.csdnimg.cn/aHR0cHM6Ly93d3cuNTJkb3V0dS5jbi9zdGF0aWMvdGVtcC9waWMvOWJkNjhkMTUwZjA3ODdjNTYwYTQzOWRhMzU5YTU4MGEucG5n?x-oss-process=image/format,png#pic_center)
> 今天的你多努力一点，明天的你就能少说一句求人的话！
>
> *我是小菜，一个和你一起变强的男人。* `💋`
>
>
> 微信公众号已开启，**小菜良记**，没关注的同学们记得关注哦！

