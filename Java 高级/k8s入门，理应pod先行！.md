大家好，欢迎来到小菜个人 **solo** 学堂。在这里，知识免费，不吝吸收！关注免费，不吝动手！
**死鬼~看完记得给我来个三连哦！**


![](https://gitee.com/cbuc/picture/raw/master/typora/20210411110759.jpeg)


>本文主要介绍 `kubernetes中pod的使用`
>
>如有需要，可以参考
>
>如有帮助，不忘 **点赞** ❥
>
>
>微信公众号已开启，**小菜良记**，没关注的同学们记得关注哦！

上篇文章我们说到如何搭建 **k8s** 集群，不知道看完的小伙伴有没有自己去尝试一下呢！

[不要让贫穷扼杀了你学 k8s 的兴趣](https://mp.weixin.qq.com/s/iQXX6Xm4SKJkTCKRN0qDzA)

这篇我们本着善始善终的原则，继续带你搞明白 k8s 中的**pod** ，成为别人家的程序员~

我们老样子，先回顾下 k8s 中存在的几种组件：

![](https://gitee.com/cbuc/picture/raw/master/typora/20210411111150.png)

那我们还得了解一下 k8s 中几种常见的资源：

- **Master：** 集群控制节点，每个集群都至少需要一个 **master** 节点负责集群的管控
- **Node：** 工作负载节点，由 **master** 分配容器到这些 **node** 节点上，然后 **node** 节点上的docker 负责容器的运行
- **Pod：** kubernetes的最小控制单元，容器都是运行在 pod 中的，一个pod中可以有 1 个或多个容器
- **Controller：** 控制器，通过它来实现对 pod 的管理，比如启动 pod，停止 pod，伸缩 pod 的数量等等
- **Service：** pod 对外服务的统一入口，可以维护同一类的多个 pod
- **Label：** 标签，用于对 pod 进行分类，同一类的 pod 会拥有相同的标签
- **NameSpace：** 命名空间，用来隔离 pod 的运行环境

这几个概念先初步有个了解即可，接下来便是对每个概念展开说明的时候~那么，正片开始！

## Kubernetes

### 一、资源管理

在 kubernetes 中，所有的内容都抽象为资源，用户需要通过操作资源来管理 **kubernetes**

关于对 **资源管理** 的理解，我们需要了解以下几个概念：

> - kubernetes 本质上是一个集群系统，用户可以在集群中部署各种服务
>
> - kubernetes 最小管理单元是 pod 而不是容器，所以我们需要将容器放在 pod 中运行，而 pod 一般是由 `pod控制器` 进行管理的
>
> - pod 提供服务后，我们需要借助 service 这个资源来实现访问 pod 中的服务
> - pod 也支持数据的持久化

然后我们通过下面图例梳理一下上面几个概念：

![](https://gitee.com/cbuc/picture/raw/master/typora/image-20210413131057604.png)

通过上面那张图我们差不多就可以将 **kubernetes** 中的重点资源理解一遍了，大概明白每个资源在集群中起到的作用。

我们有三种方式可以对资源对象进行管理：

- **命令式对象管理**

直接使用命令来操作 **kubernetes** 资源。例如：

```shell
kubectl run nginx --image=nginx:1.19.0 -port=80
```

- **命令式对象配置**

通过命令配置和配置文件来操作 **kubernetes** 资源。例如：

```shell
kubectl create/patch -f nginx.yml
```

- **声明式对象配置**

通过 **apply** 命令和配置文件来操作 **kubernetes** 资源。例如：

```shell
kubectl apply -f nginx.yml
```

在 k8s 中我们一般使用 **YAML** 格式的文件来创建符合我们预期期望的 pod，这样的 YAML 文件称为资源清单。我们也比较鼓励使用清单的方式来创建资源。

如果我们使用 **命令式对象管理**，这种方式虽然比较简单，可以直接创建一个 pod 出来，但是只能操作活动对象，无法进行审计和跟踪。

**命令式对象配置** 和 **声明式对象配置** 在我们日常中在创建资源服务中是比较经常用到。

#### 1）命令式对象管理

**kubectl** 这个是 **kubernetes** 集群的命令行工具，通过 **kubectl** 能够对集群本身进行管理，并能够在集群上进行容器化应用的安装部署。可以想象我们接下来的操作绝大部分都需要借助这个命令工具的帮助。

我们之前也已经使用过一次了：`kubectl get nodes` 。这个命令便是用来获取集群中各个节点状态的，那么不难想象，这个命令的语法：

```shell
kubectl [command] [TYPE] [NAME] [flags]
```

- **command：**指定对资源执行的操作、例如：`create、get、describe、delete...`

- **TYPE：** 指定资源类型。资源类型是大小写敏感的，开发者能以单数、复数和缩写的形式，例如：`pod、pods、po`

- **NAME：** 指定资源的名称。名称也是大小写敏感的，如果省略名称，则会显示所有的资源，例如 ：`kubectl get pods`

- **flags：** 指定可选的参数。例如：`-s 、-server、-o ...`

**k8s** 支持的 **command** 有很多，我们可以跟 docker 一样使用 `kubectl --help` 来获取帮助文档：

![](https://gitee.com/cbuc/picture/raw/master/typora/20210411160248.png)

文档很多，当然有些是常用的，有些是不常用的，小菜在这里给你们简单分个类，下次有需要可以直接查看分类中的结果！

##### 命令分类

**1、 基础命令**

|  名称   |                 描述                 |
| :-----: | :----------------------------------: |
| create  |     通过文件名或标准输入创建资源     |
| expose  |   将一个资源公开为一个新的 Service   |
|   run   |      在集群中运行一个特定的镜像      |
|   set   |        在对象上设置特定的功能        |
|   get   |          显示一个或多个资源          |
|  edit   |     使用默认的编辑器编辑一个资源     |
| delete  | 通过文件名、标准输入、资源名称或标签 |
| explain |           获取文档参考资料           |

**2、部署命令**

|      名称      |                             描述                             |
| :------------: | :----------------------------------------------------------: |
|    rollout     |                        管理资源的发布                        |
| rolling-update |                  对给定的控制器进行滚动更新                  |
|     scale      | 扩容或缩容 pod 数量，可对 `Deployment、ReplicaSet、RC或Job` 操作 |
|   autoscale    |          创建一个自动选择扩容或缩容并设置 pod 数量           |

**3、 集群管理命令**

|     名称     |                描述                 |
| :----------: | :---------------------------------: |
| certificate  |            修改证书资源             |
| cluster-info |            显示集群信息             |
|     top      | 显示资源使用情况，需要运行 Heapster |
|    cordon    |          标记节点不可调度           |
|   uncordon   |           标记节点可调度            |
|    drain     |   驱逐节点上的应用，准备下线维护    |
|    taint     |        修改节点的 taint 标记        |

**4、故障\调试命令**

|     名称     |                             描述                             |
| :----------: | :----------------------------------------------------------: |
|   describe   |                显示特定资源或资源组的详细信息                |
|     logs     | 在一个 pod 中打印容器日志，如果 pod 中只有多个容器，容器名称是可选的 |
|    attach    |                     附加到一个运行的容器                     |
|     exec     |                     执行一个命令到容器中                     |
| port-forward |               转发一个或多个本地端口到一个 pod               |
|    proxy     |            运行一个 proxy 到 kubernetes ApiServer            |
|      cp      |                    拷贝文件或目录到容器中                    |
|     auth     |                           检查授权                           |

**6、高级命令**

|  名称   |                描述                |
| :-----: | :--------------------------------: |
|  apply  | 通过文件名或标准输入对资源应用配置 |
|  patch  |    使用补丁修改、更新资源的字段    |
| replace |  通过文件名或标准输入替换一个资源  |
| convert |   不同的API 版本之间转换配置文件   |

**7、设置命令**

|    名称    |             描述              |
| :--------: | :---------------------------: |
|   label    |       更新资源上的标签        |
|  annotate  |       更新资源上的标签        |
| completion | 用于实现 kubectl 工具自动补全 |

**8、其他命令**

|     名称     |                          描述                          |
| :----------: | :----------------------------------------------------: |
| api-versions |                 打印受支持的 API 版本                  |
|    config    | 修改 kubeconfig 文件（用于访问 API，比如配置认证信息） |
|     help     |                    获取所有命令帮助                    |
|    plugin    |                   运行一个命令行插件                   |
|   version    |                打印客户端和服务版本信息                |

我们通过一些简单的例子来简单的认识一下这个命令工具：

![](https://gitee.com/cbuc/picture/raw/master/typora/image-20210423124705317.png)

#### 2）资源清单

不管是 **命令式对象配置** 还是 **声明式对象配置** 我们都需要借助 **yaml** 资源清单创建。

我们先来看看一个 **pod controller**(控制器)  的**yaml** 文件中有哪些内容：

![](https://gitee.com/cbuc/picture/raw/master/typora/image-20210423125521368.png)

上面便是一个完整的 **deployment** 资源配置清单。内容很多，老样子我们先混个眼熟，不是每个 **deployment** 都需要这么多的配置，以下便是必须存在的字段属性介绍：

|            名称            |  类型  |                   描述                   |
| :------------------------: | :----: | :--------------------------------------: |
|        **version**         | String |       属于 k8s 哪一个API 版本或组        |
|          **kind**          | String |  资源类型，如`pod、deployment、service`  |
|        **metadata**        | Object |           元数据对象，嵌套字段           |
|     **metadata.name**      | String |             元数据对象的名字             |
|          **spec**          | Object | 定义容器的规范，创建的容器应该有哪些特性 |
|    **spec.container**[]    |  List  |              定义容器的列表              |
| **spec.container[].name**  | String |                容器的名称                |
| **spec.container[].image** | String |           定义要用到镜像的名称           |

#### 3）命令式对象配置

我们结合以上必存的字段，可以简单写出一个 yaml （test.yaml） 文件：

![](https://gitee.com/cbuc/picture/raw/master/typora/image-20210423125559194.png)

然后我们可以通过 **命令式对象配置** 的方式创建出一个 pod：

```shell
kubectl create -f test.yaml
```

![](https://gitee.com/cbuc/picture/raw/master/typora/20210418224617.png)

#### 4）声明式对象配置

**声明式对象配置** 和 **命令式对象配置** 很相似，但是这种方式都是基于 `apply` 这一个命令来进行操作的。

我们这边复用一下上面创建的 `test.yaml` 文件

```shell
kubectl apply -f test.yaml
```

这种方式不能说是鸡肋，它有它的特点，比如说我们执行完上述命令后再执行一遍：

```shell
kubectl apply -f tset.yaml
```

![](https://gitee.com/cbuc/picture/raw/master/typora/20210418224805.png)

可以发现是没什么改动的，但是如果我们使用的是 `create` 来重复执行两遍呢？结果是`报错`了

![](https://gitee.com/cbuc/picture/raw/master/typora/20210418224912.png)

那么我们不难猜出 `apply`  这个命令就是对 `create` 和 `patch` 这两个命令的结合：

- 如果资源不存在，则执行创建 等同于  `create`
- 如果资源存在，则执行更新 等同于 `patch`

### 二、实战入门

接下来的阶段便是我们针对 **k8s** 中 **NameSpace 和 Pod** 资源展开说明了，小伙伴们打起精神了哦！

![image-20210414125544325](https://gitee.com/cbuc/picture/raw/master/typora/image-20210414125544325.png)

#### 1）Namespace

**Namespace（命名空间）** 的作用便是用来实现多用户之间的资源那个李的，通过将集群内部的资源对象分配到不同的 **Namespace** 中，形成逻辑上的分组，便于不同分组在共享使用整个集群资源的同时被分别管理。默认情况下，**kubernetes** 集群中的所有 pod 都是可以互相访问的，但是有些时候我们不想出现这种情况，那就可以借助于 **namespace**。

![](https://gitee.com/cbuc/picture/raw/master/typora/image-20210414132147164.png)

在集群内部有个默认的**Namespace - `default`** ，我们创建资源的时候如果不指定 **namespace**，那么就会将该资源分配到该 **default** 的命名空间之下。

```shell
[root@master test]# kubectl get namespace
```

![](https://gitee.com/cbuc/picture/raw/master/typora/20210418225024.png)

创建 **Namespace** 的方式也很简单，通过以下指令便可创建：

```shell
[root@master test]# kubectl create namespace aaa-test
```

![](https://gitee.com/cbuc/picture/raw/master/typora/20210418225252.png)

 这样子，我们就获得了一个名称为 **aaa-test** 的命名空间。或者我们也可以通过 **命令式对象配置** 的方式创建，先准备一个 **yaml** 文件：

```yaml
apiVersion: v1
kind: Namespace # yaml文件中大小写敏感
metadata：
  name：aaa-test  #命名空间的名称
```

然后执行`kubectl create -f namespace.yml`， 这样子我们同样也可以获得一个名称为 **aaa-test** 的命名空间。

然后我们就可以在资源创建的时候使用了：

![](https://gitee.com/cbuc/picture/raw/master/typora/image-20210423125649100.png)

然后执行 `kubectl create -f nginx.yml`，这样子我们就可以获取到一个 **pod** 资源，只有通过指定命名空间才能查看到我们的pod资源，这说明对其他用户是隔离的：

![](https://gitee.com/cbuc/picture/raw/master/typora/20210418225808.png)

如果我们想删除命名空间的话，可以使用指令：`kubectl delete -f namespace.yml`  或 `kubectl delete ns ns名称`

**注：** 如果将命名空间删除，那么存在于该命名空间下的资源会全部被删除

#### 2）Pod

**Pod** 是 k8s 中可以创建和管理的最小单元，是资源对象模型中由用户穿件或部署的最小资源对象模型，也是在 k8s 上运行容器化应用的资源对象。

 在使用 **docker** 的时候，我们清楚程序要运行就必须部署在容器中，而在 k8s 中，我们容器必须存在与 pod 中，**pod** 就可以认为是容器的封装，一个 **pod** 可以存在一个或多个容器。

![](https://gitee.com/cbuc/picture/raw/master/typora/image-20210414133057406.png)

##### ㈠ Pod 概念

###### ① pod 特性

**1. 资源共享**

在一个**pod** 中，多个容器之间可以共享存储和网络，相当于一个逻辑意义上的主机。

**2.  生命周期短暂**

pod 是一个具有生命周期的组件，如果 **pod** 所在的节点发生故障，那么该节点上的**pod** 都会被调度到其他节点上，而调度后的**pod**是一个全新的 **pod**，与之前没有任何关系。

**3. 平坦的网络 **

k8s 集群中的所有 **pod** 都在同一个网络地址空间中，也就是说每个 **pod** 都可以通过其他 pod 的IP地址来实现访问

###### ② pod 分类

- **普通 pod**

这种就是我们`日常中经常用到`的。一旦被创建就会放入 **etcd** 中存储，接着就会被调度到任一节点上运行，当 Pod 里某个容器停止时，Kubernetes 会自动检测到这个问题并且重新启动这个 Pod 里某所有容器， 如果 Pod 所在的 Node 宕机，则会将这个 Node 上的所有 Pod 重新调度到其它节点上。

- **静态 pod**

静态pod是由 **kubelet** 激进型管理的仅存在于特定 **node** 节点上的，它们不能通过 **API server** 进行管理，无法与 **controller 控制器** 进行管理，并且 **kubelet** 也无法对其进行健康检测。

###### ③ pod 声明周期

pod中有 5 中生命周期，我们都需要了解一下~

| 状态名称  |                             描述                             |
| :-------: | :----------------------------------------------------------: |
|  Pending  | API Server已经创建了 pod，但 pod 中的一个或多个容器的镜像还没有创建，包括镜像下载过程 |
|  Running  | Pod 内所有容器都已创建，且至少一个容器处于运行状态，正在启动状态或正在重启状态 |
| Completed |          Pod 内所有容器均成功执行退出，且不会再重启          |
|  Failed   |        Pod 内所有容器都已退出，但至少一个容器退出失败        |
|  Unknown  |         由于某种原因无法获取 Pod 状态，例如网络不通          |

###### ④ pod重启策略

| 策略名称  |                           描述                           |
| :-------: | :------------------------------------------------------: |
|  Always   |         当容器失效时，有 kubelet 自动重启该容器          |
| OnFailure | 当容器停止运行且退出码不为0时，由 kubelet 自动重启该容器 |
|   Never   |      不论容器运行状态如何，kubelet 都不会重启该容器      |

###### ⑤ pod 资源配置

之前在 **docker** 我们有进行测试没有对 **docker** 资源进行限额的时候，运行一个 **elasticSearch** 镜像的时候服务器直接卡死。那么在 **docker** 能做到资源限额，**k8s** 中自然也可以。

**Kubernetes** 中可以设置限额的计算资源有 **CPU** 和 **Memory** 两种。**Kubernetes** 我们想要进行配额限定需要设定两个参数：`Request` 和 `Limits` 

- **Request**：表示该资源最小的申请量，系统必须满足要求
- **Limits**：表示该资源最大允许使用量，不能超出这个量，当容器试图使用超过这个量的资源时，就会被 **Kubernetes** kill 掉并重启

![](https://gitee.com/cbuc/picture/raw/master/typora/image-20210423125708973.png)

上面表示一个 **nginx** 容器最少需要 0.25个CPU和 64 MB内存，最多只能使用  0.5个CPU和 128 MB内存。

##### ㈡ pod 基操

以下是一份 **pod** 的完整资源清单：

![](https://gitee.com/cbuc/picture/raw/master/typora/image-20210416133540276.png)

这份清单大部分看起来会比较陌生，但是有部分关键属性我们在上面已经讲过了，当我们实际要用的时候如果记不起那么多我们可以使用指令 `kubectl explain pod.xxx`  的方式来查看每个属性的含义，例如

![](https://gitee.com/cbuc/picture/raw/master/typora/20210418225352.png)

###### ① 简单创建

我们如果想要创建一个 pod ，只需要简单准备一份 **test.yml** 文件即可：

![](https://gitee.com/cbuc/picture/raw/master/typora/20210424173545.png)

然后通过 **命令式对象配置** 的指令 `kubectl create -f test.yml` 就可以获取到一个含有 **nginx 和 centos** 容器的pod。然后我们通过指令`kubectl get pod -n cbuc-test` 查看当前 **pod** 的状态。

![](https://gitee.com/cbuc/picture/raw/master/typora/20210424173052.png)

docker 可以用 `docker exec -it` 进入容器，k8s 也是类似此命令：

```shell
kubectl exec -it pod名称 -c 容器名称 -n 命名空间 bash
```

通过以上命令就可以进入到我们的pod中

![](https://gitee.com/cbuc/picture/raw/master/typora/20210424173253.png)

如果 pod 中只有一个容器，`-c` 可以不用指定容器名称

###### ② 属性说明

上面我们已经成功的创建了一个 pod，但是这只是一个简单的 pod 配置，我们可以针对该 **yaml** 文件进行扩展~

**1. imagePullPolicy**

![](https://gitee.com/cbuc/picture/raw/master/typora/image-20210416175253118.png)

这个属性用来设置镜像拉取策略，在 k8s 中支持三种镜像拉取策略：

- **Always：** 总是从远程仓库拉取镜像
- **IfNotPresent：** 本地有则使用本地镜像，本地没有则从远程仓库拉取镜像
- **Never：** 只使用本地镜像，从不去远程仓库拉取，本地如果不存在就会报错

> 注意：
>
> 如果镜像号为指定版本号，则默认策略为 ：IfNotPresent
>
> 如果镜像号为 latest ，则默认策略为： Always

**2. command**

command 是用于在 pod 中的容器初始化完毕之后运行一个命令。

![](https://gitee.com/cbuc/picture/raw/master/typora/20210424175318.png)

我们在上面创建了一个 centos的pod，然后在pod初始化完成后，便会执行 command 中的命令，我们可以通过 `kubectl exec -it pod名称 -n 命名空间 bash` 然后进入pod中查看 `/mnt/test.txt`

或者我们可以在pod外部执行命令：

```shell
kubectl exec pod名称 -n 命名空间 -c 容器名称 -- shell命令
```

![](https://gitee.com/cbuc/picture/raw/master/typora/20210424175416.png)

**3. args** 

我们上面说到的 command 已经可以完成启动命令和传递参数的功能，但是我们 k8s 中还提供了一个 `args` 选项，用于传递参数。k8s 中使用 command 和 args 两个参数可以实现覆盖 Dockerfile 中的 **ENTRYPOINE** 的功能。

![](https://gitee.com/cbuc/picture/raw/master/typora/image-20210423125814941.png)

`注意:`

1. 如果 command 和 args 均没有写，那么是使用 Dockerfile 的配置
2. 如果 command 写了，args 没有写，那么 Dockerfile 默认的配置会被忽略，执行输入的 command
3. 如果 command 没写，args 写了， 那么 Dockerfile 中配置的 ENTRYPOINT 的命令会被执行，使用当前 args 的参数
4. 如果 command 和 arg 都写了，那么 Dockerfile 的配置就会被忽略，执行 command 命令加上 args 参数

**4. env**

用于在 pod 中的容器设置环境变量

![](https://gitee.com/cbuc/picture/raw/master/typora/20210424180407.png)

进入pod查看：

![](https://gitee.com/cbuc/picture/raw/master/typora/20210424180318.png)

**5. ports**

ports 在 k8s 的属性类型是 Object，我们可以通过 `kubectl explain pod.spec.containers.ports` 查看该对象下的属性：

![](https://gitee.com/cbuc/picture/raw/master/typora/image-20210419140751013.png)

我们从图中可以发现该对象由5个属性：

- **containerPort：** 容器要监听的端口（0~65536）
- **hostIP：** 要将外部端口绑定到主机IP（一般省略）

- **hostPort：** 容器要在主机上公开的端口，如果设置，主机上只能运行容器的一个副本（一般省略）

- **name：** 端口名称，如果指定，必须保证name 在pod中是唯一的
- **protocol：** 端口协议，必须是 UDP、TCP或 SCTP，默认为 TCP

我们简单看个 **nginx** 的例子：

![](https://gitee.com/cbuc/picture/raw/master/typora/image-20210423125906758.png)

创建方式可以选择 3 中创建方式任意一种，然后创建完成后我们可以通过 `podIp+containerPort` 来访问到 nginx 资源

**6. resources**

容器中运行的程序需要占用一定的资源（CPU和内存），在运行的时候如果不对某个容器的资源进行限制，那么它可能会耗尽服务器的大量资源，防止这种情况的发生，k8s 中提供了 `resource` 属性，对资源进行限制。这个属性下有两个子选项：

- **limits：** 用于限制运行容器的最大占用资源，当容器占用资源超过 limit 时会被终止，并进行重启
- **requests：**  用于设置容器需要的最小资源，如果环境资源不够，容器将会无法启动

看个使用例子：

![](https://gitee.com/cbuc/picture/raw/master/typora/image-20210423125935052.png)

- **cpu：** core数，可以为整数或小数
- **memory：** 内存大小，可以使用 Gi， Mi， G，M 等形式

##### ㈢ pod 扩展

###### ① 生命周期

任何事物的创建过程都有属于它自己的生命周期，而 pod 对象从创建到销毁，这段的时间范围便称为 **pod** 的生命周期。生命周期一般包含下面几个过程：

`⒈` 运行初始化容器 （init container） 过程

`⒉` 运行主容器 （main container）

​     `2.1` 容器启动后钩子（post start），容器终止前钩子（pre stop）

​     `2.2` 容器存活性检测（liveness probe），就绪性检测（readiness probe）

`⒊` pod 终止过程

![](https://gitee.com/cbuc/picture/raw/master/typora/image-20210421125120629.png)

在整个生命周期中，**pod** 也会相应的出现 **5** 中状态，如下：

- **挂起（Pending）：** apiServer 已经创建 pod 资源对象，但它尚未被调度完成或者仍处于下载镜像的过程中
- **运行中（Running）：** pod 已经被调度至某节点，并且所用容器都已经被 kubelet 创建完成
- **成功（Succeeded）：** pod 中的所有容器都已经成功终止并且不会被重启
- **失败（Failed）：** 所有容器都已经终止，但至少有一个容器终止失败，即容器返回了非 0 值的退出状态
- **未知（UnKnown）：** apiServer 无法获取到 pod 对象的状态信息，通常是因为网络通信失败导致的

 **⑴ pod 的创建过程**

![](https://gitee.com/cbuc/picture/raw/master/typora/20210410181842.png)

**kubernetes** 启动后，无论是 **master** 节点 亦或者 **node** 节点，都会将自身的信息存储到 **etcd** 数据库中

1. 用户通过 **kubectl** 或其他 api 客户端提交需要创建的 pod 信息给 **apiServer**
2. **apiServer**  接收到信息后会生成 pod 对象信息，并存入 **etcd** 数据库中，返回确认消息给客户端
3. **apiServer** 开始反映 **etcd** 中 pod 对象的变化，其他组件会使用 **watch** 机制来跟踪检查 **apiServer** 上的变动
4. **scheduler** 发现如果有新的 pod 对象需要创建，便会为 pod 分配主机并将结果回送至 **apiServer**
5. **node** 节点上的 **kubectl** 发现有 pod 调度过来，会尝试调用 **docker** 启动容器，并将结果返回给 **apiServer**
6. **apiServer** 将接收到的 pod 状态信息存入 **etcd** 中

**⑵ pod 的终止过程** 

1. 用户首先向 **apiServer** 发送删除 pod 对象的命令
2. **apiServer** 中的pod对象信息会随着时间的推移而更新，在宽限期内（默认30s），pod 会被视为 **dead** 状态，并将 pod 标记为 **terminating** 状态
3. **kubelet** 在监控到 pod 对象转为 **terminating** 状态的同时启动 pod 关闭过程
4. 端点控制器监控到 pod 对象的关闭行为时将其从所有匹配到此端点的 **service** 资源的端点列表中移除
5. 如果当前 pod 对象定义了 `preStop` 钩子处理器，则在其标记为 **terminating** 后即会以同步的方式启动执行
6. pod 对象中的容器进程接收到停止信号，并停止容器
7. 宽限期结束后，如果 pod 中还存在仍在运行的进程，那么 pod 对象就会收到立即终止的信号
8. **kubelet** 请求 **apiServer** 将此 pod 资源的宽限期设置为 0 从而完成删除操作。

**⑶ 初始化容器** 

初始化容器，看名字也大致能够猜到初始化容器是在 pod 主容器启动之前要运行的容器，主要是做一些主容器的前置工作。

`特征：`

- 初始化容器必须运行完成直至结束，如果运行失败便会进行重启直至成功
- 初始化容器必须按照顺序执行，只有前一个成功后，后一个才能执行

这里简单看一个使用例子：

我们在初始化容器中定义了一个 **centos** 容器，只有 ping 通对应的地址才会启动成功，已知当前网络能通 `192.168.108.101`

![](https://gitee.com/cbuc/picture/raw/master/typora/20210424182412.png)

![](https://gitee.com/cbuc/picture/raw/master/typora/20210424182329.png)

可以看到，在初始化容器成功启动的情况下，我们的 **nginx** 容器也能运行成功，但是如果我们把 ping 的地址改一下，就会导致初始化容器启动失败，那么正常容器也是会启动失败的：

![](https://gitee.com/cbuc/picture/raw/master/typora/20210424181530.png)

**⑷ 钩子函数**

不知道你对钩子函数这个词是否有一些了解~ 钩子函数能够感知自身生命周期中的事件，在相应的时刻到来时就会运行用户指定的程序代码。

在 **k8s** 提供了两个钩子函数，分别是`启动之后`和`停止之前`

- **post start**：容器创建之后执行。如果失败了会重启容器
- **pre stop：** 容器终止之前执行。执行完成之后容器将成功终止，在其完成之前会阻塞删除容器的操作

那么钩子函数有了，我们该如何定义这个函数呢？在 **k8s** 中钩子函数支持使用三种方式定义动作：

- **exec 命令**

在容器中执行一次命令，如果命令执行的退出码为0，则认为程序正常，否则反之。

![](https://gitee.com/cbuc/picture/raw/master/typora/image-20210423130022484.png)

- **tcpSocket**

将会尝试访问一个用户容器的端口，如果能够建立这条连接，则认为程序正常，否则不正常

![](https://gitee.com/cbuc/picture/raw/master/typora/image-20210423130043203.png)

- **httpGet**

调用容器内Web应用的URL，如果返回的状态码在200和399之间，则认为程序正常，否则不正常

![](https://gitee.com/cbuc/picture/raw/master/typora/20210424182619.png)

###### ② 容器探测

容器探测是用来检测容器中的应用实例是否正常工作，是保障业务可用性的一种传统机制。如果经过探测，实例的状态不符合预期结果，那么 k8s 就会把这个实例删除。在 k8s 中也支持了两种探针来实现容器探测：

- **liveness probes：** 存活性探针，用于检测应用实例当前是否处于正常运行状态，如果不是，k8s 会重启容器
- **readiness probe：** 就绪性探针，用于检测应用实例当前是否可以接受请求，如果不能，k8s不会转发流量

> `注意：`
>
> **livenessProbe** 决定了容器是否需要重启
>
> **readinessProbe** 决定了是否将请求转发给容器

这两种探针支持的检测方式与上面生命周期检测的方式一样：

- **exec 命令**

![](https://gitee.com/cbuc/picture/raw/master/typora/image-20210423130132109.png)

- **TCPSocket**

![](https://gitee.com/cbuc/picture/raw/master/typora/image-20210423130146252.png)

- **httpGet**

![](https://gitee.com/cbuc/picture/raw/master/typora/20210424182644.png)

###### ③ 重启策略

在 **容器探测** 检测出容器有问题后， **k8s** 就会对容器所在的 pod 进行重启，而这些重启的定义便是由 pod 自身的重启策略决定的，pod 的重启策略有如下3种：

- **Always：** 容器失效时，自动重启该容器（默认值）

- **OnFailure：** 容器终止运行且退出码不为0时重启
- **Never：** 不论状态为何，都不重启该容器

首次需要重启的容器会立即进行重启，如果随后还需要重启，那么**kubectl** 便会延迟一段时间后才进行，反复重启的操作延迟时长为 `10s,20s,30s,40s,80s,160s和300s`，其中300s是最大的延迟时长

![](https://gitee.com/cbuc/picture/raw/master/typora/image-20210423130207824.png)

##### ㈣ pod 调度

上面说到过默认情况下，pod 在哪个 Node 节点上运行是由 **Scheduler**组件采用相应的算法计算出来的，这个过程是不受人工控制的。但是在实际的使用场景中我们有时候想要控制某些pod到达某些节点上，而针对于这种需求，**k8s** 当然也是可以满足的~ 在 k8s 中它提供了 `4` 中调度方式：

- **自动调度：** 由 **scheduler** 组件计算运行在哪个node节点上
- **定向调度：** 由用户自定义，需要用到 `NodeName`、`NodeSelector`  属性
- **亲和性调度：** 由用户自定义，需要用到 `NodeAffinity、PodAffinity、PodAntiAffinity` 属性
- **污点容忍调度：** 由用户自定义，需要用到 `Taints、Toleration`属性

###### ① 定向调度

我们可以利用 **nodeName** 或者 **nodeSelector** 来标记 pod 需要调度到期望的 node 节点上。这里的标记是强制性，不管 node 节点有没有宕机，都会往这个节点上面调度，因此如果node节点宕机的话，就会导致 pod 运行失败。

- **NodeName**

这个属性用于强制约束将 Pod 调度到指定名称的 node节点上，这种方式，其实就是直接跳过 **scheduler** 的调度逻辑。

![](https://gitee.com/cbuc/picture/raw/master/typora/20210424201058.png)

上面已经准备了一个 pod 的yaml文件，我们创建看下是否能够调度到我们想要的节点上

![](https://gitee.com/cbuc/picture/raw/master/typora/20210424201246.png)

可以看到 pod 节点已经成功的调度到名称为 **node02** 的节点上了

- **NodeSelector**

这个属性是用于将 pod 调度到添加了指定标签上的 node 节点上（k8s 中资源可以打标签，我们一样可以对 node 节点打标签）。它是通过 **k8s** 的 label-selector 机制实现的，就是说在 pod 创建之前，会由 **scheduler** 的使用 **MatchNodeSelector** 的调度策略进行 label 匹配，找出目标 node，然后将 pod 调度到目标节点，该匹配规则也是属于强制约束。

`测试：`

 首先对 node 节点打上标签：

```shell
kubectl label nodes node02 app=node-dev
```

查看是否打成功：

![](https://gitee.com/cbuc/picture/raw/master/typora/20210424201611.png)

然后准备一份 pod yaml文件：

![](https://gitee.com/cbuc/picture/raw/master/typora/image-20210423130252411.png)

然后我们创建后查看：

![](https://gitee.com/cbuc/picture/raw/master/typora/20210424203031.png)

###### ② 亲和度调度

上面介绍的定向调度是属于强制性约束，如果没有满足的node节点供运行的话，pod 就是启动失败，这样子就很大地限制了它的使用场景。所以我们接下来介绍的 **亲和度调度 (Affinity)** 便是用来解决这种问题的。

它是通过配置的形式，实现优先选择满足条件的 Node 进行调度，如果有就调度到对应节点，如果没有，也可以调度到不满足条件的节点上，这样可以使调度更加灵活

**Affinity分为三大类：**

- **nodeAffinity（node亲和性）**

以`node`为目标，解决 pod 可以调度到哪些 `node` 的问题

这个属性中又存在 `requiredDuringSchedulingIgnoredDuringExecution (硬限制)` 和 `preferredDuringSchedulingIgnoredDuringExecution (软限制)` 两种

Ⅰ、**requiredDuringSchedulingIgnoredDuringExecution** (硬限制)

这个限制和上面说到的定向调度有点像，只选择满足条件的 node 节点进行调度，使用例子如下：

![](https://gitee.com/cbuc/picture/raw/master/typora/20210424203951.png)

上面我们创建了一个 pod，会在标签 `key`为 **app**，且`value` 为 **node-pro 或 node-test** 的节点上选择，但是并不存在具备这个标签的节点，因此这个pod 一直处于挂起的状态~

![](https://gitee.com/cbuc/picture/raw/master/typora/20210424203832.png)

我们上面看到了一个新的属性 `matchExpressions`，这个是用来编写关系表达式的，具体使用方法如下：

```yaml
- matchExpressions:
  - key: app # 匹配存在标签的key为 app 的节点
    operator: Exists
  - key: app # 匹配标签的key为 app ,且value是"xxx"或"yyy"的节点
    operator: In
    values: ["xxx","yyy"]
  - key: app # 匹配标签的key为 app, 且value大于"xxx"的节点
    operator: Gt
    values: "xxx"
```

Ⅱ、 **preferredDuringSchedulingIgnoredDuringExecution (软限制)**

上面已经了解到了 **硬限制** 的使用，**软限制** 的使用如下，我们直接来看 yaml 文件：

![](https://gitee.com/cbuc/picture/raw/master/typora/20210424204545.png)

![](https://gitee.com/cbuc/picture/raw/master/typora/20210424204908.png)

这边可以看到虽然不存在满足条件的node，但是也是可以成功运行pod 的，只是调度到了不满足条件的 node 上！

我们来总结一下硬限制和软限制的用法：

![](https://gitee.com/cbuc/picture/raw/master/typora/20210424205459.png)

- **podAffinity（pod 亲和性）** 

以 pod 为目标，解决pod可以和哪些已存在的pod部署在同一个拓扑域中的问题。

**podAffinity** 同样也存在 **硬限制 和 软限制** ，我们接下来直接看下如何配置：

![image-20210423130432962](https://gitee.com/cbuc/picture/raw/master/typora/image-20210423130432962.png)

上面便是 podAffinity 硬限制的yaml文件，除了眼熟的属性之外，我们还看到了一个新的属性 `topologyKey`，那么这个属性是用来干嘛的呢？

> topologyKey 用于指定调度时作用域：
>
> - 如果值为 `kubernetes.io/hostname` ，说明是以 node 节点为区分范围
> - 如果值为 `kubernetes.io/os`， 则以 node 节点的**操作系统**来区分

了解完硬限制的编写，软限制也是与上面 node亲和度的用法相似，这里不再赘诉~

- **podAntiAffinity（pod反亲和性）**

以pod 为目标，解决pod不能和哪些已存在的pod部署在同一个拓扑域中的问题。

这个使用就是和上面基本一致了，就是和 **podAffinity** 要求反着来就是了，属性名换个就完事了~

![](https://gitee.com/cbuc/picture/raw/master/typora/image-20210423130449825.png)

这个yaml文件代表的含义便是选择不和标签带有 `app=test01` 或 `app=test02` 的pod "共处一室"。同样存在硬限制和软限制的配置

> **亲和性** 和 **反亲和性** 的使用场景
>
> **亲和性：** 如果两个应用交互频繁，那就有必要利用亲和性让两个应用尽可能的靠近，可以减少因为网络通信而带来的性能损耗
>
> **反亲和性：** 当应用采用多副本部署的时候，有必要采用反亲和性让各个应用实例打散分布在各个 node 上，这样可以提高服务的高可用性

###### ③ 污点(Taint)

我们先来看下目前 pod 存在于每个节点的情况，

![](https://gitee.com/cbuc/picture/raw/master/typora/20210424210138.png)

是否发现了一个问题，那就是 pod 基本都分布在了 node 节点上，而 master 节点却没有运行任何pod。而这个原因便是和我们要讲到的`污点` 有关系了！

我们前面说到的调度都是站在 pod 的角度，让 pod 选择 node 进行运行，而污点很好的进行了反转。**让node节点决定让哪些pod可以调度过来！**

Node 可以设置 `污点` ，设置上 `污点` 之后，就会和 pod 之间形成了一种排斥关系。这种关系存在的意义便是可以拒绝 pod 调度进来，也可以将已经存在的 pod 驱逐出去。

`污点格式：` **key=value:effect**

**key** 和 **value** 是污点的标签，而 **effect** 则是用来描述污点的作用，支持三种功能定义：

- **PreferNoSchedule**

 **k8s**将尽量避免把 Pod 调度到具有该污点的 node 节点上，除非没有其他节点可以调度。`（尽量不要来，除非没办法）`

- **NoScheduler**

 **k8s** 将不会把 Pod 调度到具有该污点的 node 节点上，但不会影响当前 Node 上已经存在的 pod。`(新的不要来，在这的就别动了)`

- **NoExecute**

 **k8s** 将不会把 Pod 调度到具有该污点的 node 节点上，同时也会将 Node 上已经存在的 Pod 驱逐。`（新的不要来，在这的赶紧走）`

**设置污点命令如下：**

```shell
# 设置污点
kubectl taint nodes node01 key=value:effect

# 去除污点
kubectl taint nodes node01 key:effect-

# 去除所有污点
kubectl taint nodes node01 key-
```

而 k8s中的 **master**节点之所以没有运行任何pod，那便是因为 **master** 节点上已经存在了污点：

![](https://gitee.com/cbuc/picture/raw/master/typora/20210424210426.png)

###### ④ 容忍(Toleration)

上面说到如果 node 节点存在污点，那么pod就会无法调度。那如果 pod 有时候就是想 "厚着脸皮"，哪怕你存在污点，也不嫌弃的想要调度进去有没有办法解决呢？

**k8s** 也是想到了这种情况的存在，因此便有了一个 `容忍` 的属性！

![](https://gitee.com/cbuc/picture/raw/master/typora/image-20210423114338264.png)

> 污点就是拒绝，容忍就是忽略，Node 通过污点来拒绝 pod 调度上去，pod 通过容忍忽略拒绝

我们先给 node01 打上 `NoExecute` 的污点，然后我们再给 pod 添加容忍，看下是否能够成功调度上去

`pod yaml：`

![](https://gitee.com/cbuc/picture/raw/master/typora/image-20210423130535989.png)

通过添加容忍后，我们可以发现pod 在 node01 的节点上成功运行了，说明容忍成功~

容忍的配置信息如下：

```yaml
tolerations:
- key 		# 对应着要容忍的污点的键，空意味着匹配所有的键
  value		# 对应着要容忍的污点值
  operator	# key-value 的运算符，支持 Equal 和 Exists（默认）
  effect    # 对应污点的effect，空意味着匹配所有的影响
  tolerationSeconds  # 容忍时间，当 effect 为NoExecute 时生效，表示 pod 在Node上的停留时间
```

**END**

关于 k8s 中 pod 的介绍到这里就结束啦~个人觉得还是挺详细的，如果能够认真看下来，相信对 pod 已经有足够了解了。但是你认为 k8s 到这里就结束了吗？那肯定不会的，碍于篇幅，所以其他资源组件留到下一节介绍~请动动小手，点点关注不迷路。路漫漫，小菜与你一同求索！

![](https://gitee.com/cbuc/picture/raw/master/typora/20210424210940.gif)

> 今天的你多努力一点，明天的你就能少说一句求人的话！
>
> *我是小菜，一个和你一起学习的男人。* `💋`
>
>
> 微信公众号已开启，**小菜良记**，没关注的同学们记得关注哦！

