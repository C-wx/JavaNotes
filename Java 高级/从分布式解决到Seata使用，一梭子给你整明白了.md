大家好，欢迎来到小菜同学的个人 **solo** 学堂，知识免费，不吝吸收！关注免费，不吝动手！


![](https://user-gold-cdn.xitu.io/2020/4/11/17169c46045528af?w=240&h=224&f=jpeg&s=7529)


>本文主要介绍 `分布式事务`
>
>如有需要，可以参考
>
>如有帮助，不忘 **点赞** ❥
>
>
>微信公众号已开启，**小菜良记**，没关注的同学们记得关注哦！

`生活可能对你耍无赖，但科技不能`

我去小卖部买东西，付完了钱，老板转身抽了口烟，却忘记了我付完钱？这种情况怎么办，发生在日常生活并不奇怪。但是你在网上下单，付完了钱，刚要查看订单，却提示你待支付，心中几万只草泥马跑过也不得而知！所以防止这种情况的发生，分布式事务也变得尤为重要。

有人纳闷了，付不付钱跟分布式事务有什么关系，这不是程序耍无赖吗？但是耍无赖的背后却是因为分布式事务在作祟！如果还不明白，那可能你还没明白什么是事务，什么是分布式事务~

### 分布式事务

#### 定义

事务提供一种机制将一个活动涉及的所有操作都纳入到一个不可分割的执行单元，组成事务的祝所有操作只有在操作均正常执行的情况下才能提交，只要其中任一操作执行失败，都会导致整个事务的回滚。简单来说，`要么做，要么不做`。

听起来有点 `man`，不要迷恋，先来了解一下事务的四大特性：`ACID`

- **A（Atomic）**：原子性，构成事物的所有操作，要么全部执行完成，要么全部不执行，不可能出现部分成功部分失败的情况。
- **C（Consistency）**：一致性，在事务执行前后，数据库的一致性约束没有被破坏。一旦所有事务动作完成，事务就被提交，数据和资源处于一种满足业务规则的一致性状态中。比如上面说的，我向商店老板付了钱，我这边扣除了100，而老板增加了100，这种就称为一致性。
- **I（Isolation）**：隔离性，数据库中的事务一般都是并发的，隔离性是指并发的两个事务互不干扰，一个事务不能看到其他事务运行过程的中间状态，通过配置事务隔离级别可以避免在脏读、幻读、不可重复读等问题。
- **D（Durability）**：持久性，事务完成之后，该事务对数据的更改会持久化到数据库中，并且不会被回滚

##### 单体事务

早期我们使用的还是单体架构，像是一个大家族，其乐融融的生活在一起日夜耕作。

![](https://gitee.com/cbuc/picture/raw/master/20210319124841.png)

时间久了，各种各样的问题自然而然的也出现了：`复杂性高，部署频率低，可靠性差，扩展能力受限...` 承受了太多不该承受的流言蜚语，而大家也逐渐找寻新的出路， 那微服务架构也便受应出现：`易于开发、扩展、理解和维护，不会受限于任何技术栈，易于和第三方应用系统集成...` 太多太多的优点，让单体系统也逐渐淡出人们的视角，好像如果现在不用微服务架构开发项目，就与社会脱节了~ 好处很多，但是问题也会变得更加复杂。这节我们不讲别的，就来看看分布式事务是咋回事。

事务无论在单体还是微服务中都肯定是存在，但是在 **单体** 架构中，我们通常是怎么解决事务的呢？ `@Transactional`，单靠这个注解就可以开启事务来保证整个操作的 `原子性`。

##### 分布式事务

微服务架构，其实就是将传统的单体拆分成多个服务，然后多个服务之间相互配合，来完成业务需求。

![](https://gitee.com/cbuc/picture/raw/master/20210319130155.png)

分布式事务就是指事务的参与者，支持事务的服务器，资源服务器以及事务管理器分别位于不同的分布式系统的不同节点之上。

既然说到分布式事务了，我们不妨一起了解一下微服务中的 **CAP理论**

- **C（Consistency）**：一致性。服务A、B、C三个节点都存储了用户数据，三个节点的数据都需要保持同一时刻数据一致性
- **A（Availability）**：可用性。服务A、B、C三个节点，其中一个节点如果宕机了，不能影响整个集群对外提供服务。
- **P（Partition Tolerance）**：分区容错性就是允许系统通过网络协同工作，分区容错性要解决由于网络分区导致数据的不完整及无法访问等问题。

我们都知道鱼和熊掌不可兼得，三者不能兼备择两者是也！**CAP** 目前来说无法都兼备，因此当前微服务策略中要么 `CA`，要么`CP`，不然就是`AP`。而这个时候又有一个理论出现了，那就是 **BASE理论** 。它是用来对 **CAP理论** 进行一些补充，它值得是：

- **BA（Basically Available）**：基本可用
- **S（Soft State）**：软状态
- **E（Eventually Consistent）**：最终一致性

这个理论的核心思想便是：如果我们如法做到强一致性，那么每个应用都应该根据自身的业务特点，采用适当的方式来使系统达到最终一致性。

#### 出现场景

让我们回到分布式事务中来，什么时候会出现分布式事务呢？

**场景1：** 虽然时单体的架构服务，但由于在分库的情况下，依然会导致分布式事务的情况，因此单体服务不会出现分布式事务的这种说法，**破**~

![](https://gitee.com/cbuc/picture/raw/master/typora/20210321213920.png)

**场景2：** 分布式架构下，两个服务之间相互调用，虽然使用的是同一个数据库，但是还是会出现分布式事务。谁让你使用的是分布式架构呢~

![](https://gitee.com/cbuc/picture/raw/master/typora/20210321214255.png)

**场景3：** 分布式架构下，两个服务之间相互调用，使用的是不用的数据库，这种情况下肯定会出现分布式事务的问题，想都不用想！

![](https://gitee.com/cbuc/picture/raw/master/typora/20210321214601.png)

#### 解决方法

有问题的地方便会有方法，当然也不一定，但是在这里，分布式事务问题确实有解决的方法， 如果没有，小菜也不会写这篇文章来自讨苦吃了！

##### 方法一：全局事务

不知道这里该说 **全局事务** 会让你比较熟悉，还是 **两阶段提交（2PC）** 会让你比较熟悉，还是说都不熟悉~，不熟悉也没关系，小菜带你熟悉熟悉！

全局事务是基于DTP模型实现的，它规定了要实现分布式事务需要三种角色：

- **AP（Application）**：应用系统（微服务）
- **TM（Transaction Manager）**：事务管理器（全局事务管理）
- **RM（Resource Manager）**：资源管理器（数据库）

除了 **AP** 这个角色，我们多认识了其他两个同学分别是`事务管理器`和`资源管理器`，那么他们起到什么作用呢，那我们就得看，这个两阶段提交是哪两阶段了！

###### 阶段1 ：表决阶段

所有参与者都将自己的事务进行预提交，并将能否成功的信息反馈给协调者

![](https://gitee.com/cbuc/picture/raw/master/20210322123418.png)

![](https://gitee.com/cbuc/picture/raw/master/20210322123621.png)

1. 事务管理器发一个 `prepare` 指令给 A 和 B 两个服务器
2. A 和 B 两个服务器收到消息后，根据自身情况，判断自己是否可以提交事务
3. 将处理结果记录到资源管理器中
4. 将处理结果返回给事务管理器

###### 阶段2 ：执行阶段

协调者根据所有参与者的反馈，通知所有参与者，步调一致地执行提交或者回滚

![](https://gitee.com/cbuc/picture/raw/master/20210322124150.png)

![](https://gitee.com/cbuc/picture/raw/master/20210322124212.png)

1. 事务管理器向 A 和 B 两个服务器发送提交指令
2. A 和 B 两个服务器收到指令后，将自己本身事务提交
3. 将处理结果记录到资源管理器
4. 将处理结果返回给事务管理器

这就是两阶段提交的大致过程，它提高了数据一致性的概率，实现成本较低。但是这种实现方式带来的缺点也是很明显的！

- **单点故障**：如果事务管理器出现了故障，整个系统将不可用
- **同步阻塞**：延迟了提交事件，加长了资源阻塞事件，不适合高并发的场景
- **数据不一致**：如果执行到第二阶段，依然存在**commit**结果未知的情况，只有部分参与者接收到 **commit** 消息，部分没有收到，那也只有部分参与者提交了事务，依然会导致数据不一致问题

##### 方法二：三阶段提交

既然两阶段提交解决不了问题，那我们就来三阶段提交。三阶段提交相对于两阶段提交来说增加了 `ConCommit` 阶段和超时机制。在一段规定时间内，如果服务器参与者没有接受到来自事务管理器的提交执行，那他们就会自己自动提交，这样子就能解决两阶段中单体故障问题。

我们来看看三阶段提交是哪三阶段：

- **CanCommit**：准备阶段。这个阶段要做的事就和两阶段提交一样，先去询问参与者是否有条件接收这个事务，这样子不会太暴力，一开始就直接干活锁死资源。

- **PreCommit**：这个阶段是事务管理器向各个参加者发送准备提交请求，各个参与者接到请求或，将处理结果记录到自己的资源管理器中，如果准备好了，就会想协调者反馈`ACK`表示我已经准备好提交了。
- **DoCommit**：这个就断就是从 **预提交状态** 转为 **提交**状态。事务管理器向各个参与者发送 **提交** 请求，参与者接收到请求后，就会各自执行自己事务的提交操作。将处理结果记录到自己的资源管理器中，并向协调者反馈 `ACK` 表示自己已经完成事务，如果有一个参与者未完成**PreCommit**的反馈或者反馈超时，那么协调者都会向所有的参与者节点发送abort请求，从而中断事务。

其实三阶段提交看起来就是把两阶段提交中的`提交阶段`变成了 **预提交阶段** 和 **提交阶段**。

![](https://gitee.com/cbuc/picture/raw/master/20210323135603.png)

那其实从上面可以看到，三阶段提交解决的只是两阶段提交中 **单体故障** 的问题，因为加入了超时机制，这里的超时的机制作用于 **预提交阶段** 和 **提交阶段**。如果等待 **预提交请求** 超时，那参与者相当于说啥都没干，直接回到准备阶段之前。如果等到**提交请求**超时，那参与者就会提交事务了。

所以可以看到其实 三阶段提交还是没根本解决问题，虽然比两阶段提交进步了一点点~

##### 方法三：TCC

**TCC（Try Confirm Cancel）** ，它是属于补偿型分布式事务。它的核心思想是 **针对每个操作，都要注册一个与其对应的确认和补偿（撤销）操作**。TCC 实现分布式事务一共有三个步骤：

- **Try**：尝试待执行的业务

这个过程并未执行业务，只是完成所有业务的一致性检查，并**预留**好执行所需的所有资源

- **Confirm**：确认执行业务

确认执行业务的操作，不做任何业务检查，只使用Try阶段预留的业务资源。通常情况下，采用TCC则会认为 Confirm 阶段是不会出错的。只要 Try 成功，则 Confirm 一定成功。如果 Confirm 出错了，则需要引入重试机制或人工处理

- **Cancel**：取消待执行的业务

取消 Try 阶段预留的业务资源。通常情况下，采用 TCC 则认为 Cancel 阶段也是一定能成功的，若 Cancel 阶段真的出错了，也要引入重试机制或人工处理

![](https://gitee.com/cbuc/picture/raw/master/20210324131258.png)

**TCC** 是业务层面的分布式事务，最终一致性，不会一直持有资源的锁。它的优缺点如下：

**优点：** 吧数据库层的二阶段提交上提到了应用层来实现，规避了数据库的 2PC 性能低下问题

**缺点**：TCC 的 Try、Confirm 和 Cancel 操作功能需业务提供，开发成本高。TCC 对业务的侵入较大和业务紧耦合，需要根据特定的场景和业务逻辑来设计相应的操作

##### 方法五：可靠消息事务

消息事务的原理是 **将两个事务通过消息中间件来进行异步解耦**。基于可靠消息服务的方案是通过消息中间件来保证上、下游应用数据操作的一致性。假设有 A、B两个服务，分布可以处理 A、B两个任务，此时需要存在一个业务流程，将任务 A和B 放到同一个事物中处理，这种方式就可以借助消息中间件来实现。

![](https://gitee.com/cbuc/picture/raw/master/20210324141608.png)

整体上可以分为两个大的步骤：`A服务向消息中间件发布消息` 和 `消息向B服务投递消息`

**步骤一：** A 服务向消息中间件发布消息

1. 在服务A处理任务A前，首先向消息中间件发送一条半信息
2. 消息中间件收到后将该消息持久化，但不进行投递。持久化成功后，向A服务返回确认应答
3. 服务A收到确认应答后，便可以开始处理任务A
4. 任务A处理完成后，服务A便会向消息中间件发送Commit 或者 Rollback 请求，该请求发送完成后，服务A的工作任务就结束了，该事务的处理过程也就结束了
5. 在消息中间件收到 **Commit** 后，便会向 B 服务投递消息，如果收到 **Rollback** 便会直接丢弃消息

如果消息中间件在最后的过程中，长时间没有收到服务A 发送的 **Commit** 或 **Rollback** 指令，这个时候就需要依靠 **超时询问机制**

> **超时询问机制**：
>
> 服务A除了实现正常的业务流程之外，还是需要提供一个可供消息中间件事务询问的接口。在消息中间件第一次收到消息后便会开始计时，如果超过规定的时间没有收到后续的指令，就会主动调用服务A提供的事务询问接口，询问当前服务的状态，通常来说该接口会返回三种结果，中间件需要根据这三种不同的结果做出不同的处理：
>
> - 提交：直接将该消息投递给服务B
> - 回滚：直接将该消息丢弃
> - 处理中：继续等待，重新计时

**步骤二：** 消息中间件向B服务投递消息

消息中间件收到A服务的提交 **Commit**指令后便会将该消息投递给B服务，然后将自己的状态置为阻塞等待状态。B服务收到消息中间件发送的消息后便开始处理任务B，处理完成后便会向消息中间件发出回应。但是在消息中间件阻塞等待的时候同样会出现问题

- 正常情况：消息中间件投递完消息后，进入阻塞等待状态，在收到确认应答后便认为事务处理完成，该流程结束
- 等待超时情况：在等待确认应答超时之后就会重新进行投递，直到B服务器返回消费成功响应为止。而消息重试的次数和时间间隔都可以设置，如果最终还是不能成功进行投递，则需要人工干预。

可靠消息服务方案是实现了 **最终一致性**。对比本地消息表实现方案，不需要再建立消息表。**不用依赖本地数据库事务**，适用于高并发的场景。**RocketMQ** 就很好的支持了消息事务。

##### 方法四：最大努力通知

最大努力通知也成为定期校对，是对可靠消息服务的进一步优化。它引入了本地消息表来记录错误消息，然后加入失败消息的定期校对功能，来进一步保证消息会被下游服务消费。

![](https://gitee.com/cbuc/picture/raw/master/20210325132356.png)

同样的这个跟消息事务一样可以分为两步：

**步骤一：** 服务A向消息中间件发送消息

1. 在处理业务的同一个事务中，向本地消息表写入一条记录
2. 消息发送者不断取出本地消息表中的消息发送到消息中间件，如果发送失败则进行重试

**步骤二：** 消息中间件向服务B投递消息

1. 消息中间件收到消息后便会将消息投递到下游服务B，服务B收到消息后便会执行自己的业务
2. 当服务B业务处理成功后，便会向消息中间件返回反馈应答，消息中间件便可将该消息删除，该流程结束
3. 如果消息中间件向服务B投递消息失败，便会尝试重试，如果重试失败，便会将该消息接入失败消息表中
4. 消息中间件同样需要提供查询失败消息的接口，服务B 定期查询失败信息，并进行消费

最大努力通知的方案实现比较简单，适用于一些最终一致性要求比较低的业务。

### Seata

#### Seata概念

既然分布式事务处理起来这么麻烦，那能不能让分布式事务处理起来像本地事务那么简单。当然这是我们的愿景。当然这个愿景是所有开发人员所希望的。而阿里巴巴团队就为这个愿景做出了行动，发起了开源项目 **Seata（Simple Extensible Autonomous Transaction Architecture）** 。这是一套分布式事务解决方案，意在解决开发人员遇到的分布式事务各方面的难题。

**Seata** 的设计目标是对业务无侵入，因此它是从业务无侵入的两阶段提交（全局事务）着手，在传统的两阶段上进行改进，他把一个分布式事务理解成一个包含了若干分支事务的全局事务。而全局事务的职责是协调它管理的分支事务达成一致性，要么一起成功提交，要么一起失败回滚。也就是一荣俱荣一损俱损~

![](https://gitee.com/cbuc/picture/raw/master/typora/20210325210426.png)

#### Seata 组成

我们看下 **Seata** 中存在几种重要角色：

- **TC（Transaction Coordinator）**：事务协调者。管理全局的分支事务的状态，用于全局性事务的提交和回滚。
- **TM（Transaction Manager）**：事务管理者。用于开启、提交或回滚事务。
- **RM（Resource Manager）**：资源管理器。用于分支事务上的资源管理，向 **TC** 注册分支事务，上报分支事务的状态，接收 **TC** 的命令来提交或者回滚分支事务。

这是一种很巧妙的设计，我们来看图：

![](https://gitee.com/cbuc/picture/raw/master/typora/20210325212926.png)

执行流程是这样的：

1.  服务A中的 **TM** 向 **TC** 申请开启一个全局事务，**TC** 就会创建一个全局事务并返回一个唯一的 **XID**
2.  服务A中的 **RM** 向 **TC** 注册分支事务，然后将这个分支事务纳入 **XID** 对应的全局事务管辖中
3.  服务A开始执行分支事务
4.  服务A开始远程调用B服务，此时 **XID** 会根据调用链传播
5. 服务B中的 **RM** 也向 **TC** 注册分支事务，然后将这个分支事务纳入 **XID** 对应的全局事务管辖中
6.  服务B开始执行分支事务
7. 全局事务调用处理结束后，**TM** 会根据有误异常情况，向 **TC** 发起全局事务的提交或回滚
8.  **TC** 协调其管辖之下的所有分支事务，决定是提交还是回滚

#### Seata使用

我们从上面了解到了 Seata 的组成和执行流程，我们接下来就来实际的使用下 **Seata**。

##### 示例演示

我们简单创建了一个微服务项目，其中有订单服务和库存服务。

![](https://gitee.com/cbuc/picture/raw/master/20210327201901.png)

我们这里采用了 **nacos** 作为注册中心，分别启动两个服务，我们在**nacos**控制台可以看到两个已经注册的服务：

![](https://gitee.com/cbuc/picture/raw/master/20210327205121.png)

> 号外：如果对**nacos**还不熟悉的小伙伴可以跳转查看 **nacos讲解**：[微服务新秀之Nacos](https://mp.weixin.qq.com/s/WzbLt3hS6iCgEzDInpn70g)

我们接着创建了一个数据库，其中有两张表：`c_order` 和 `c_product`，其中商品表中有一条数据，而订单表中还未有数据，接下来我们将要对其进行操作！

![](https://gitee.com/cbuc/picture/raw/master/20210327205802.png)

我们现在模拟一个下单的过程：

1.  请求进来，通过商品 **pid** 往数据库中查商品的信息
2.  创建一条该商品的订单
3.  对应扣减该商品的库存量
4.  流程结束

![](https://gitee.com/cbuc/picture/raw/master/20210327211028.png)

我们接下来就进入代码演示一下：

![](https://gitee.com/cbuc/picture/raw/master/20210327221106.png)

> 注意：这里 ProductService 并非是库存服务里面的类，而是利用 Feign 远程调用库存服务的接口
>
> ![](https://gitee.com/cbuc/picture/raw/master/20210327221453.png)

代码三步走，正常请况下肯定是没有问题的：

![](https://gitee.com/cbuc/picture/raw/master/20210327221324.png)

订单生成，库存也对应减少，感觉自己代码可以上线进入正轨的时候，我们来模拟一下库存中的异常，库存商品数量归为 **100**，订单表清空：

![](https://gitee.com/cbuc/picture/raw/master/20210327221629.png)

我们继续发送下单请求，可以看到库存服务已经抛出了异常

![](https://gitee.com/cbuc/picture/raw/master/20210327221841.png)

正常来说这个时候，库存表数量不应该减少，订单表不应该插入订单数据，但是事实真的是这样的吗？我们看数据：

![](https://gitee.com/cbuc/picture/raw/master/20210327222049.png)

库存数量没减，但是订单却增加了。好了，到这里，你就已经见识到了分布式事务的灾难性危害。接下来主角登场！

![](https://gitee.com/cbuc/picture/raw/master/20210328175616.gif)

##### Seata 安装

我们首先需要点击[下载地址](https://github.com/seata/seata/releases/)进行下载 **Seata**。

由于我们是使用 **nacos** 作为服务中心和配置中心，因此我们下载解压后需要做一些修改操作

- 进入 `conf` 目录编辑 `registry.conf` 和 `file.conf` 两个文件，编辑后内容如下：

![](https://gitee.com/cbuc/picture/raw/master/20210328171552.png)

![](https://gitee.com/cbuc/picture/raw/master/20210327231551.png)

- 由于新版 **Seata** 中没有 `nacos-conf.sh` 和 `config.txt` 两个文件，因此我们需要独立下载：

> [nacos-config.sh 下载地址](https://github.com/seata/seata/blob/develop/script/config-center/nacos/nacos-config.sh)
>
> [config.txt 下载地址](https://github.com/seata/seata/blob/develop/script/config-center/config.txt)

我们需要将 `config.txt` 文件放到 **seata** 目录下，而非 **conf** 目录下，并且需要修改 `config.txt` 内容

![](https://gitee.com/cbuc/picture/raw/master/20210327232033.png)

![](https://gitee.com/cbuc/picture/raw/master/20210327231912.png)

> `config.txt`就是seata各种详细的配置，执行 `nacos-config.sh` 即可将这些配置导入到**nacos**，这样就不需要将`file.conf`和`registry.conf`放到我们的项目中了，需要什么配置就直接从**nacos**中读取。

- 执行导入

在 **conf** 目录下打开 git bash 窗口，执行以下命令：

```shell
sh nacos-config.sh -h localhost -p 8848 -g SEATA_GROUP -t namespace-id(需要替换) -u nacos -w nacos
```

操作结束后，我们便可以在 **nacos** 控制台中看到配置列表，日后配置有需要修改便可以直接从这边修改，而不用修改目录文件：

![](https://gitee.com/cbuc/picture/raw/master/20210327232319.png)

- 数据库配置

在 **1.4.1** 最新版中依然没有 sql 文件，所以我们还是需要另外下载：[sql 下载地址](https://github.com/seata/seata/blob/1.4.1/script/server/db/mysql.sql)

在 **seata** 数据中执行这个文件，生成三张表：

![](https://gitee.com/cbuc/picture/raw/master/20210327232549.png)

在我们的业务数据库中执行 **undo_log** 这张表：

```sql
CREATE TABLE `undo_log`
(
    `id` BIGINT(20) NOT NULL AUTO_INCREMENT,
    `branch_id` BIGINT(20) NOT NULL,
    `xid` VARCHAR(100) NOT NULL,
    `context` VARCHAR(128) NOT NULL,
    `rollback_info` LONGBLOB NOT NULL,
    `log_status` INT(11) NOT NULL,
    `log_created` DATETIME NOT NULL,
    `log_modified` DATETIME NOT NULL,
    `ext` VARCHAR(100) DEFAULT NULL,
    PRIMARY KEY (`id`),
    UNIQUE KEY `ux_undo_log` (`xid`, `branch_id`)
) ENGINE = INNODB
AUTO_INCREMENT = 1
DEFAULT CHARSET = utf8;
```

-  添加 log 文件

如果我们没有log输出文件，启动 **seata** 可能会报错，因此我们需要在 **seata** 目录下创建 **logs** 文件夹，在 logs 文件下创建 `seata_gc.log` 文件

![](https://gitee.com/cbuc/picture/raw/master/20210327233055.png)

- 启动

做好了以上准备，我们便可以启动 **seata** 了，直接在 **bin** 目录下 cmd 执行 **bat** 脚本即可，启动结束便可在 **nacos** 中看到 **seata** 服务：

![](https://gitee.com/cbuc/picture/raw/master/20210327233230.png)

##### Seata 集成

在 Seata 安装的步骤中我们便完成了 **Seata 服务端** 的启动安装，接下来就是在项目中集成 **Seata 客户端**

- 第一步：我们需要在 **pom.xml**  文件中添加两个依赖：`seata 依赖` 和 `nacos 配置依赖`

```xml
<!--nacos-->
<dependency>
    <groupId>com.alibaba.cloud</groupId>
    <artifactId>spring-cloud-starter-alibaba-nacos-config</artifactId>
</dependency>

<!--seata-->
<dependency>
            <groupId>com.alibaba.cloud</groupId>
            <artifactId>spring-cloud-starter-alibaba-seata</artifactId>
            <exclusions>
                <!-- 排除依赖 指定版本和服务器端一致 -->
                <exclusion>
                    <groupId>io.seata</groupId>
                    <artifactId>seata-all</artifactId>
                </exclusion>
                <exclusion>
                    <groupId>io.seata</groupId>
                    <artifactId>seata-spring-boot-starter</artifactId>
                </exclusion>
            </exclusions>
        </dependency>

        <dependency>
            <groupId>io.seata</groupId>
            <artifactId>seata-all</artifactId>
            <version>1.4.1</version>
        </dependency>

        <dependency>
            <groupId>io.seata</groupId>
            <artifactId>seata-spring-boot-starter</artifactId>
            <version>1.4.1</version>
        </dependency>
```

**注意：** 这里需要排除 `spring-cloud-starter-alibaba-seata` 自带的 **seata** 依赖，然后引入我们自己需要的 **seata** 版本，如果版本不一致启动时可能会造成 `no available server to connect` 错误！

- 第二步：我们需要把 `restry.conf` 文件复制到项目的 resource 目录下

![](https://gitee.com/cbuc/picture/raw/master/20210328175208.png)

- 第三步：需要自己配置**seata**代理数据源

```java
@Configuration
public class DataSourceProxyConfig {
    @Bean
    @ConfigurationProperties(prefix = "spring.datasource")
    public DruidDataSource druidDataSource() {
        return new DruidDataSource();
    }

    @Primary
    @Bean
    public DataSourceProxy dataSource(DruidDataSource druidDataSource) {
        return new DataSourceProxy(druidDataSource);
    }
}
```

配置完数据源我们得在启动类的 `SpringBootApplication`  上排除Druid数据源依赖，否则可能会出现循环依赖的错误：

```java
@SpringBootApplication(exclude = {DataSourceAutoConfiguration.class})
```

- 第四步：在 **nacos** 的配置文件控制台中加入我们服务的事务组项：

```properties
service.vgroupMapping + 服务名称 = default
group为： SEATA_GROUP
```

![](https://gitee.com/cbuc/picture/raw/master/20210328172234.png)

- 第五步：项目中配置修改

![](https://gitee.com/cbuc/picture/raw/master/20210328172632.png)

- 第六步：开启全局事务

这步就是最终一步了，在我们需要开启事务的方法上添加 `@GlobalTransactional` 注解，类似于我们单体事务添加的`@Transactional`

![](https://gitee.com/cbuc/picture/raw/master/20210328173358.png)

##### Seata 测试

我们现在回到项目中，在上面的示例演示中，我们已经知道了如果库存服务发生异常，会出现的情况是，库存没有减少，而订单依然会生成。那我们如果增加了 **Seata** 来管理全局事务，情况是否会有所改变？我们测试如下：

**库存服务已经了异常：**

![](https://gitee.com/cbuc/picture/raw/master/20210328174731.png)

**看下数据库数据：**

![](https://gitee.com/cbuc/picture/raw/master/20210328174842.png)

看样子我们全局事务已经生效了，事务也已经完美的控制住了！

而我们创建的 **undo_log** 这张表在管理事务中也启动了重要的作用：

![](https://gitee.com/cbuc/picture/raw/master/20210328171354.png)

看完了以上操作，我们趁热打铁来梳理一下其中的执行流程，让你印象更加深刻些~

![](https://gitee.com/cbuc/picture/raw/master/typora/20210325222025.png)

相信看完这张图，你对 **Seata** 执行事务的流程也更加熟悉了吧！

这还没结束，我们接着来看看其中的一些要点：

1. 每个 **RM** 都需要使用 **DataSourceProxy** 连接数据库，这样是为了使用 **ConnectionProxy**，使用数据源和数据连接代理的目的就是在第一阶段将 **undo_log** 和业务数据放在一个本地事务提交，这样就保存了只要有业务操作就一定有 **undo_log** 产生！
2. 在第一阶段的 **undo_log** 中存放了数据修改前和修改后的值，为事务回滚做好准备，所以第一阶段就已经将分支事务提交，也就释放了锁资源！
3. **TM**  开启全局事务后，便会将 **XID** 放入全局事务的上下文中，我们通过 **feign** 调用也会将 **XID** 传入下游服务中，每个分支事务都会将自己的 **Branch ID** 与 **XID** 相关联！
4. 第二阶段如果全局事务是正常提交，那么**TC** 会通知各分支参与者提交分支事务，各参与者只需要删除对应的 **undo_log** 即可，并且可以异步执行！
5. 第二阶段如果全局事务需要回滚，那么 **TC** 会通知各分支事务参与者回滚分支事务，通过 **XID** 和 **Branch ID** 找到相应的 **undo_log** 日志，通过回滚日志生成反向 **SQL** 并执行，完成事务提交之前的状态，如果回滚失败便会重试回滚操作！

**END**

到这里，一篇分布式事务就讲完了，我们回顾下，从分布式事务的五种解决方案到引出 **Seata** 的使用，小菜同学真是用心良苦~  言归正传，看完后吸收了多少，动动小手，写写代码，让知识与你更亲近~

![看完不赞，都是坏蛋](https://gitee.com/cbuc/picture/raw/master/typora/20210325223703.png)
> 今天的你多努力一点，明天的你就能少说一句求人的话！
>
> *我是小菜，一个和你一起学习的男人。* `💋`
>
>
> 微信公众号已开启，**小菜良记**，没关注的同学们记得关注哦！