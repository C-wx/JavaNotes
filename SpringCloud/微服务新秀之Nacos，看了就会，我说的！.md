大家好，我是小菜，一个渴望在互联网行业做到蔡不菜的小菜。可柔可刚，点赞则柔，白嫖则刚！
**死鬼~看完记得给我来个三连哦！**


![](https://user-gold-cdn.xitu.io/2020/4/11/17169c46045528af?w=240&h=224&f=jpeg&s=7529)


>本文主要介绍 `微服务中的Nacos`
>
>如有需要，可以参考
>
>如有帮助，不忘 **点赞** ❥
>
>
>微信公众号已开启，**小菜良记**，没关注的同学们记得关注哦！

在讲 **Nacos** 之前，我们需要了解什么是 `Nacos`：Nacos 是阿里的一个开源产品，它是针对微服务架构中的 **服务发现**、**配置管理**、**服务治理** 的综合性解决方案。

官网给出的回答：

> Nacos 致力于帮助您发现、配置和管理微服务。Nacos 提供了一组简单易用的特性集，帮助您实现动态服务发现、服务配置管理、服务及流量管理。
> Nacos 帮助您更敏捷和容易地构建、交付和管理微服务平台。Nacos 是构建以“服务”为中心的现代应用架构(例如微服务范式、云原生范式)的服务基础设施。

综上所述，得出 `Nacos` 的四大特性：

- 服务发现与服务健康检查
- 动态配置管理
- 动态DNS服务
- 服务和元数据管理

**附图：**

![源网侵删](https://cbucbm.club/3e92db03)

看到`Nacos`支持这么多主流的开源生态，是心动的感觉！

### 一、入门基操

#### 使用方式

`Nacos`的使用方式也极其简单，以下为 **windows** 下安装方式

##### 步骤1

点击[下载地址](https://github.com/alibaba/nacos/releases) 下载最新稳定版本

##### 步骤2

双击 **bin** 目录下的 `startup.cmd` 启动服务器

##### 步骤3

通过浏览器访问 `http://127.0.0.1:8848/nacos` 打开 **nacos** 控制台登录页面，默认用户名密码皆为：`nacos`，登录成功后便可访问主页面。

![](https://cbucbm.club/1d2d23ae)

#### 扩展使用

##### 发布配置

我们可以通过 **地址** 的方式发布配置：`http://127.0.0.1:8848/nacos/v1/cs/configs`，使用 **postman** 进行测试：

![](https://cbucbm.club/9c7e23d9)

![](https://cbucbm.club/ab6eabdd)

##### 获取配置

我们可以通过 **地址** 的方式获取配置：`http://127.0.0.1:8848/nacos/v1/cs/configs`，使用 **postman** 进行测试：

![](https://cbucbm.club/f29b2bae)

##### 发布服务

我们可以通过 **地址** 进行服务注册：`http://127.0.0.1:8848/nacos/v1/ns/instance`，使用 **postman** 进行测试：

![](https://cbucbm.club/e04c7d2a)

![](https://cbucbm.club/746be310)

##### 服务发现

我们可以通过 **地址** 发现服务：`http://127.0.0.1:8848/nacos/v1/ns/instance/list`，

使用 **postman** 进行测试：

![](https://cbucbm.club/dcc5325e)

##### 外部数据库支持

`nacos`默认是使用嵌入式数据库实现数据的存储，如果我们要使用外部 **mysql** 存储 `nacos`数据，进行以下步骤：

- **步骤1**

安装`Mysql`（5.6.5 ~ 8 之间的版本）

- **步骤2**

初始化 **mysql** 数据库，新建数据库 `nacos`，然后加载 `conf/nacos-mysql.sql` 

- **步骤3**

修改 `conf/application.properties`文件，添加 `mysql` 数据源的配置，然后重启，便可生效

![](https://cbucbm.club/2d17e532)

### 二、配置管理

在上述中我们已经知道`Nacos`其中的一个功能便是用于配置中心。配置中心是在微服务架构中，当系统从一个单体应用被拆分为分布式系统上一个个服务节点时，配置文件也必须随着迁移而分割，这样配置就分散了，而且各个配置中也存在互相冗余的部分。

![](https://cbucbm.club/b1177bdc)

配置中心所担任的角色：

![](https://cbucbm.club/6cbb616f)

配置中心将配置从各应用中剥离出来，对配置进行统一管理，应用自身不需要自己去管理配置

从图中我们总结流程如下：

- 用户在配置中心更新配置信息
- A 服务和 B 服务及时得到配置更新通知，从配置中心获取更新

#### 发布配置

![](https://cbucbm.club/559b3091)

- 步骤1中我们可以创建命名空间，命名空间（NameSpace）是用于隔离多个环境的（如开发、测试、生产），而每个应用在不同环境的同一配置（如数据库配置）的值是不一样的。
- 步骤2中我们可以切换不同命名空间来发布不同配置，命名空间下类似 UUID 的一串便是每个命名空间的唯一ID。
- 步骤3中我们可以点击发布配置，其中 **DataId** 和 **group** 是必填项

![](https://cbucbm.club/6b41a162)

**完成上面三个步骤后我们便可以看到生成了一条刚刚配置过的信息**

#### 获取配置

然后我们在项目中便可读取配置中的内容，步骤如下：

- 步骤1

在 `pom` 文件中引入 `nacos-client`包：

```xml
<dependency>
    <groupId>com.alibaba.nacos</groupId>
    <artifactId>nacos-client</artifactId>
    <version>1.1.3</version>
</dependency>
```

- 步骤2

通过 `nacos-client` 包下提供的 API，来获取配置：

```java
public static void main(String[] args) throws NacosException {
    //使用nacos client远程获取nacos服务上的配置信息
    //nacos server地址
    String serverAddr = "127.0.0.1:8848";
    //data id
    String dataId = "application-dev.properties";
    //group
    String group = "DEFAULT_GROUP";

    //namespace
    String namespace = "dfa1c276-69f7-47d6-9903-6850b9c248f7";
    Properties properties =new Properties();
    properties.put("serverAddr",serverAddr);
    properties.put("namespace",namespace);
    
    //获取配置
    ConfigService configService = NacosFactory.createConfigService(properties);
    
    // String dataId, String group, long timeoutMs
    String config = configService.getConfig(dataId, group, 5000);
    System.out.println(config);
}
/* OUTPUT:
spring.datasource.mysql.driverClassName = com.mysql.cj.jdbc.Driver
*/
```

配置的管理模型如下图所示：

![](https://cbucbm.club/2f862520)

- **命名空间（NameSpace）**

命名空间（NameSpace）用于不同环境（开发环境、测试环境和生产环境）的配置隔离。不同的命名空间下，可以存在相同名称的配置分组（Group）或配置集。

- **配置分组（Group）**

配置分组是对配置集进行分组，不同的配置分组下可以有相同的配置集（DateId）。默认的配置分组名称为 **DEFAULT_GROUP**。用于区分不同的项目或应用。

- **配置集（DataId）**

在系统中，一个配置文件通常就是一个 **配置集**，一个配置集可以包含了系统的各种配置信息，例如一个配置集可能包含了数据源、线程池、日志级别等配置项。每个配置集都可以定义一个有意义的名称。

![](https://cbucbm.club/b9e93965)

#### 分布式配置

在了解通过 `Nacos` 集中管理多个服务的配置之前，我们先大概了解下以下概念：

##### **传统单体架构**

![](https://cbucbm.club/02108db7)

所有功能模块打包到一起并放在一个 web 容器中运行，所有功能模块使用同一个数据库。

**特点**：

- 开发效率高
- 容易测试
- 容易部署

**缺点：**

- 复杂性会逐渐变高，维护性逐渐变差
- 版本迭代逐渐变慢
- 阻碍技术创新
- 无法按需伸缩

##### 微服务架构

![](https://cbucbm.club/3b464c6a)

微服务简单来说就是将一个项目拆分成多个服务。每一个微服务都是完整的应用，都有自己的业务逻辑和数据库。每一个业务模块都是用独立的服务完成，这种微服务架构模式也影响了应用和数据库之间的关系，不像传统多个业务模块共享一个数据库，微服务加购每个服务都有自己的数据库。

**优点：**

- 分而治之，职责单一
- 可伸缩
- 局部容易修改、替换、部署，有利于持续集成和快速迭代
- 不会受限于任何技术栈

##### Nacos

![](https://cbucbm.club/6cbb616f)

话不多说，我们直接用代码来演示配置中心的用法：

- **步骤1 - 发布配置**

我们在`Nacos`主页中创建两个配置文件：

`service_a.properties`：

![](https://cbucbm.club/b16c3d31)

`service_b.properties`：

![](https://cbucbm.club/637d4927)

- **步骤2 - 创建父工程**

`pom.xml` 如下：

![](https://cbucbm.club/7d73853c)

- **步骤3 - 创建子模块**`service-a`

`pom.xml` 如下：

![](https://cbucbm.club/e5b83468)

`bootstrap.yml`如下：

![](https://cbucbm.club/517bdf00)

- **步骤4 - 创建子模块**`service-b`

`pom.xml` 如下：

![](https://cbucbm.club/db5758c1)

`bootstrap.yml`如下：

![](https://cbucbm.club/ef5253de)

工程目录结构如下：

![](https://cbucbm.club/d920f2c4)

`ConfigController`如下：

![](https://cbucbm.club/d4874a02)

`service-a`运行结果为：

![](https://cbucbm.club/c1107e8e)

`service-b`运行结果为：

![](https://cbucbm.club/fd1481ce)

可以看到通过以上步骤成功获取到了我们在`nacos`中创建配置文件的内容。其中我们需要注意关键的步骤为：**1.** 引入 `spring-cloud-alibaba-dependencies` 和 `spring-cloud-starter-alibaba-nacos-config` 的 **jar包**。 **2.** 我们在 `resources` 下创建的配置文件必须是 `bootstrap` 而不能是 `application` **3.** `bootstrap.yml`中的配置

**bootstrap.yml另有玄机？**

我们在上面看到配置核心点在于：

```yaml
spring:
  application:
    name: service_a
  cloud:
    nacos:
      config:
        server-addr: 127.0.0.1:8848                     # 配置中心地址
        # spring.application.name + file-extension = service_a.properties
        file-extension: properties                      # dataid名称的后缀
        namespace: dfa1c276-69f7-47d6-9903-6850b9c248f7 # 指定具体的namespace
        group: TEST_GROUP
```

这个是读取指定配置组下的指定配置，我们都知道开发讲究高内聚低耦合，如果有相同的配置项我们可以独立抽取成一个文件，这样我们就得引入多个配置文件，当然`nacos`也是支持的，配置如下：

```yaml
spring:
  application:
    name: service_a
  cloud:
    nacos:
      config:
        server-addr: 127.0.0.1:8848                     # 配置中心地址
        # spring.application.name + file-extension = service_a.properties
        file-extension: properties                      # dataid名称的后缀
        namespace: dfa1c276-69f7-47d6-9903-6850b9c248f7 # 指定具体的namespace
        group: TEST_GROUP
        # 通过 ext-config 来配合使用
        ext-config[0]:
          data-id: service-common_1.properties
        ext-config[1]:
          data-id: service-common_2.properties
          group: GLOBALE_GROUP
        ext-config[2]:
          data-id: service-common_3.properties
          group: REFRESH_GROUP
          refresh: true  #动态刷新配置
```

注意`ext-config` 得从 0 开始，其中 **refresh** 标签用来实现动态刷新，就是配置文件修改后，项目不用重启也能实时读取最新的配置文件。

可能你会觉得通过 `ext-config` 有点麻烦，需要写那么多，为了简化我们还可以使用 `shared-dataids` 和 `refreshable-dataids` 实现同上一样的功能，如下：

```yaml
spring:
  application:
    name: service_a
  cloud:
    nacos:
      config:
        server-addr: 127.0.0.1:8848                     # 配置中心地址
        # spring.application.name + file-extension = service_a.properties
        file-extension: properties                      # dataid名称的后缀
        namespace: dfa1c276-69f7-47d6-9903-6850b9c248f7 # 指定具体的namespace
        group: TEST_GROUP
        
        shared-dataids: service-common_1.properties,service-common_2.properties,service-common_3.properties
        refreshable-dataids: service-common_3.properties
```

通过 `shared-dataids` 来支持多个共享 Data Id 的配置，多个之间用逗号隔开。
通过 `refreshable-dataids` 来支持哪些共享配置的 Data Id 在配置变化时，应用中是否可动态刷新，感知到最新的配置值，多个 Data Id 之间用逗号隔开。如果没有明确配置，默认情况下所有共享配置的 Data Id 都不支持动态刷新。

**配置项的优先级**

```yaml
#方式1
file-extension: properties                      # dataid名称的后缀
namespace: dfa1c276-69f7-47d6-9903-6850b9c248f7 # 指定具体的namespace
group: TEST_GROUP

#方式2
ext-config[0]:
data-id: service-common_1.properties
ext-config[1]:
data-id: service-common_2.properties
group: GLOBALE_GROUP
ext-config[2]:
data-id: service-common_3.properties
group: REFRESH_GROUP
refresh: true  #动态刷新配置

#方式3
shared-dataids: service-common_1.properties,service-common_2.properties,service-common_3.properties
refreshable-dataids: service-common_3.properties
```

以上我们了解到了 `nacos` 有三种配置方式，其中优先级：

**方式1 > 方式2（内部比较：n越大，优先级越高） > 方式3**

以上我们已经了解完了`Nacos`作为配置中心的使用，接下来我们来看看`Nacos`作为服务的注册中心有什么奥秘！

### 三、服务发现

#### 什么是服务发现

在微服务架构中，整个系统会按职责划分为多个服务，通过服务之间且做来实现业务目标。这样在我们的代码中免不了要进行服务间的远程调用，服务的消费方要调用服务的生产方，为了完成这一次请求，消费方需要知道服务生产方的网络位置（**IP地址和端口号**）

![](https://cbucbm.club/6a7ad419)

##### **服务发现中心对比**

|  **对比项目**   |         **Nacos**          | **Eureka**  |    **Consul**     | **ZooKeeper** |
| :-------------: | :------------------------: | :---------: | :---------------: | :-----------: |
|   一致性协议    |     支持 AP 和 CP 模型     |   AP 模型   |      CP模型       |    CP模型     |
|    健康检查     | TCP/HTTP/MYSQL/Client Beat | Client Beat | TCP/HTTP/gRPC/Cmd |  Keep Alive   |
|   负载均衡器    |   权重/metadate/Selector   |   Ribbon    |       Fabio       |       -       |
|    雪崩保护     |             有             |     有      |        无         |      无       |
|  自动注销实例   |            支持            |    支持     |      不支持       |     支持      |
|    访问协议     |          HTTP/DNS          |    HTTP     |     HTTP/DNS      |      TCP      |
|    监听支持     |            支持            |    支持     |       支持        |     支持      |
|   多数据中心    |            支持            |    支持     |       支持        |    不支持     |
| 跨注册中心同步  |            支持            |   不支持    |       支持        |    不支持     |
| SpringCloud集成 |            支持            |    支持     |       支持        |    不支持     |
|   Dubbo 集成    |            支持            |   不支持    |      不支持       |     支持      |
|    K8s 集成     |            支持            |   不支持    |       支持        |    不支持     |

#### 服务发现入门

百说不如一练，咱们话不多说，直接上代码：

- **步骤1 - 新建父工程**

`pom.xml`如下：

![](https://cbucbm.club/719f9aba)

- **步骤2 - 新建服务生产者**

`pom.xml`如下：

![](https://cbucbm.club/e37353cb)

`application.yml`如下：

![](https://cbucbm.club/40700e95)

`启动类`如下：

![](https://cbucbm.club/a5c2fb30)

`ProviderController.java`如下：

![](https://cbucbm.club/6af2baf6)

以上便是生成者的代码，其中关键点在于：**1.** 引入 `spring-cloud-starter-alibaba-nacos-discovery` jar包 **2.** 在启动类标注 `@EnableDiscoveryClient` 注解 **3.** 在 `application.yml` 中配置`nacos`服务中心的地址 **4.** 在 `controller` 中暴露服务

- **步骤3 - 新建服务消费者**

`pom.xml`如下：

![](https://cbucbm.club/1e29af50)

`application.yml`如下：

![](https://cbucbm.club/7ffc65c0)

`启动类`如下：

![](https://cbucbm.club/0a8850b1)

`ConsumerController.java`如下：

![](https://cbucbm.club/0f9d2ea5)

以上便是消费者的代码，其中关键点在于：**1.** 引入 `spring-cloud-starter-alibaba-nacos-discovery` jar包 **2.** 在启动类标注 `@EnableDiscoveryClient` 注解 **3.** 在 `application.yml` 中配置`nacos`服务中心的地址 **4.** 在 `controller` 中使用`RestTemplate` 调用服务。

以上我们可以看到在`Nacos`中注册了两个服务，分别是 `service-provider` 和 `service-consumer`，我们也可以在`Nacos`控制台看到：

![](https://cbucbm.club/43abb490)

同样，服务注册也支持命名空间的隔离，我们只需在`application.yml`中添加配置：

```yaml
server:
  port: 8083

spring:
  application:
    name: service-consumer
  cloud:
    nacos:
      discovery:
        server-addr: 127.0.0.1:8848
        # 命名空间
        namespace: dfa1c276-69f7-47d6-9903-6850b9c248f7
        cluster-name: DEFAULT
```

##### Feign 的使用

`Feign`是`Netflix`开发的声明式、模板化的HTTP客户端，`Feign`可以帮助我们更快捷、优雅地调用`HTTP API`。

`Feign`的使用方式也十分简单，几个步骤如下：

- **步骤1**

声明 `Feign` 客户端：

```java
@FeignClient(value = "service-provider") //生产者名称
public interface ConsumerService {

    @GetMapping("/getData")
    String getDate();
}
```

- **步骤2**

在 **启动类** 添加 `@EnableFeignClients` 注解

- **步骤3**

在 `controller` 层进行调用：

```java
@RestController
public class ConsumerController {

    @Autowired
    private ConsumerService consumerService;

    @GetMapping("/getData")
    public String getData() {
        String date = consumerService.getDate();
        return "consumer consumer ---" + date;
    }
}
```

**结果**：

![](https://cbucbm.club/f00c92ed)

简单的使用，减少了与业务无关的 HTTP 请求相关代码的编写，使业务逻辑清晰。



**END**



以上便是 微服务中 `Nacos` 的大概介绍啦，希望看到这里的你也有所收获！路漫漫，小菜与你一同求索~



![看完不赞，都是坏蛋](https://imgconvert.csdnimg.cn/aHR0cHM6Ly93d3cuNTJkb3V0dS5jbi9zdGF0aWMvdGVtcC9waWMvOWJkNjhkMTUwZjA3ODdjNTYwYTQzOWRhMzU5YTU4MGEucG5n?x-oss-process=image/format,png#pic_center)
> 今天的你多努力一点，明天的你就能少说一句求人的话！
>
> *我是小菜，一个和你一起学习的男人。* `💋`
>
>
> 微信公众号已开启，**小菜良记**，没关注的同学们记得关注哦！