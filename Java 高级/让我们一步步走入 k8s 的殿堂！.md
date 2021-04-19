大家好，欢迎来到小菜个人 **solo** 学堂。在这里，知识免费，不吝吸收！关注免费，不吝动手！
**死鬼~看完记得给我来个三连哦！**


![](https://gitee.com/cbuc/picture/raw/master/typora/20210411110759.jpeg)


>本文主要介绍 `kubernetes 的使用`
>
>如有需要，可以参考
>
>如有帮助，不忘 **点赞** ❥
>
>
>微信公众号已开启，**小菜良记**，没关注的同学们记得关注哦！

上篇文章我们说到如何搭建 **k8s** 集群，不知道看完的小伙伴有没有自己去尝试一下呢！

[不要让贫穷扼杀了你学 k8s 的兴趣](https://mp.weixin.qq.com/s/iQXX6Xm4SKJkTCKRN0qDzA)

这篇我们本着善始善终的原则，一文教会你如何使用 k8s ，成为别人家的程序员~

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

通过上面那张图我们差不多就可以将 kubernetes 中的重点资源理解一遍了，大概明白每个资源在集群中起到的作用。

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

我们之前也已经使用过一次了：`kubectl get nodes` 。这个命令便是用来获取集群中各个节点状态的，那么不难以想象，这个命令的语法：

```shell
kubectl [command] [TYPE] [NAME] [flags]
```

- **command：**指定对资源执行的操作、例如：`create、get、describe、delete...`

- **TYPE：** 指定资源类型。资源类型是大小写敏感的，开发者能以单数、复数和缩写的形式，例如：`pod、pods、po`

- **NAME：** 指定资源的名称。名称也是大小写敏感的，如果省略名称，则会显示所有的资源，例如 ：`kubectl get pods`

- **flags：** 指定可选的参数。例如：`-s 、-server`

**k8s** 支持的 **command** 有很多，我们可以跟 docker 一样使用 `kubectl --help` 来获取帮助文档：

![](https://gitee.com/cbuc/picture/raw/master/typora/20210411160248.png)

文档很多，当然有些是常用的，有些是不常用的，小菜在这里给你们简单分个类，下次有需要可以直接查看分类中的结果~

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

```shell
# 创建一个namespace
[root@master ~]# kubectl create namespace dev
namespace/dev created
# 获取namespace
[root@master ~]# kubectl get namespace
NAME              STATUS   AGE
aaa-test          Active   3m26s
cbuc              Active   2d
cbuc-ns           Active   5m36s
cbuc-test         Active   11d
default           Active   11d
dev               Active   7s
kube-node-lease   Active   11d
kube-public       Active   11d
kube-system       Active   11d
# 在此namespace下创建并运行一个nginx的Pod
[root@master ~]# kubectl run pod --image=nginx -n dev
kubectl run --generator=deployment/apps.v1 is DEPRECATED and will be removed in a future version. Use kubectl run --generator=run-pod/v1 or kubectl create instead.
deployment.apps/pod created
# 查看新创建的pod
[root@master ~]# kubectl get pod -n dev
NAME                  READY  STATUS   RESTARTS   AGE
pod-864f9875b9-pcw7x   1/1   Running      0      21s
# 删除指定的pod
[root@master ~]# kubectl delete pod pod-864f9875b9-pcw7x
pod "pod-864f9875b9-pcw7x" deleted
# 删除指定的namespace
[root@master ~]# kubectl delete ns dev
namespace "dev" deleted
```

#### 2）资源清单

不管是 **命令式对象配置** 还是 **声明式对象配置** 我们都需要借助 **yaml** 资源清单创建。

我们先来看看一个 pod controller(控制器)  的yaml 文件中有哪些内容：

```yaml
apiVersion: v1
kind: Deployment
metadata: <Object>
  namespace: test_ns #命名空间名称
  name: nginx #资源名称
  labels:
    app: nginx #资源标签
spec: <Object>
  minReadySeconds: <integer> #设置pod准备就绪的最小秒数
  paused: <boolean> #表示部署已暂停并且deploy控制器不会处理该部署
  progressDeadlineSeconds: <integer>
  strategy: <Object> #将现有pod替换为新pod的部署策略
    rollingUpdate: <Object> #滚动更新配置参数，仅当类型为RollingUpdate
      maxSurge: <string> #滚动更新过程产生的最大pod数量，可以是个数，也可以是百分比
      maxUnavailable: <string> #
    type: <string> #部署类型，Recreate，RollingUpdate
  replicas: <integer> #pods的副本数量
  selector: <Object> #pod标签选择器，匹配pod标签，默认使用pods的标签
    matchLabels: <map[string]string> 
      key1: value1
      key2: value2
    matchExpressions: <[]Object>
      operator: <string> -required- #设定标签键与一组值的关系，In, NotIn, Exists and DoesNotExist
      key: <string> -required-
      values: <[]string>   
  revisionHistoryLimit: <integer> #设置保留的历史版本个数，默认是10
  rollbackTo: <Object> 
    revision: <integer> #设置回滚的版本，设置为0则回滚到上一个版本
  template: <Object> -required-
    metadata:
    spec:
      containers: <[]Object> #容器配置
      - name: <string> -required- #容器名、DNS_LABEL
        image: <string> #镜像
        imagePullPolicy: <string> #镜像拉取策略，Always、Never、IfNotPresent
        ports: <[]Object>
        - name: #定义端口名
          containerPort: #容器暴露的端口
          protocol: TCP #或UDP
        volumeMounts: <[]Object>
        - name: <string> -required- #设置卷名称
          mountPath: <string> -required- #设置需要挂载容器内的路径
          readOnly: <boolean> #设置是否只读
        livenessProbe: <Object> #就绪探测
          exec: 
            command: <[]string>
          httpGet:
            port: <string> -required-
            path: <string>
            host: <string>
            httpHeaders: <[]Object>
              name: <string> -required-
              value: <string> -required-
            scheme: <string> 
          initialDelaySeconds: <integer> #设置多少秒后开始探测
          failureThreshold: <integer> #设置连续探测多少次失败后，标记为失败，默认三次
          successThreshold: <integer> #设置失败后探测的最小连续成功次数，默认为1
          timeoutSeconds: <integer> #设置探测超时的秒数，默认1s
          periodSeconds: <integer> #设置执行探测的频率（以秒为单位），默认1s
          tcpSocket: <Object> #TCPSocket指定涉及TCP端口的操作
            port: <string> -required- #容器暴露的端口
            host: <string> #默认pod的IP
        readinessProbe: <Object> #同livenessProbe
        resources: <Object> #资源配置
          requests: <map[string]string> #最小资源配置
            memory: "1024Mi"
            cpu: "500m" #500m代表0.5CPU
          limits: <map[string]string> #最大资源配置
            memory:
            cpu:         
      volumes: <[]Object> #数据卷配置
      - name: <string> -required- #设置卷名称,与volumeMounts名称对应
        hostPath: <Object> #设置挂载宿主机路径
          path: <string> -required- 
          type: <string> #类型：DirectoryOrCreate、Directory、FileOrCreate、File、Socket、CharDevice、BlockDevice
      - name: nfs
        nfs: <Object> #设置NFS服务器
          server: <string> -required- #设置NFS服务器地址
          path: <string> -required- #设置NFS服务器路径
          readOnly: <boolean> #设置是否只读
      - name: configmap
        configMap: 
          name: <string> #configmap名称
          defaultMode: <integer> #权限设置0~0777，默认0664
          optional: <boolean> #指定是否必须定义configmap或其keys
          items: <[]Object>
          - key: <string> -required-
            path: <string> -required-
            mode: <integer>
      restartPolicy: <string> #重启策略，Always、OnFailure、Never
      nodeName: <string>
      nodeSelector: <map[string]string>
      imagePullSecrets: <[]Object>
      hostname: <string>
      hostPID: <boolean>
status: <Object>
```

上面便是一个完整的 **deployment** 资源配置清单。老样子混个眼熟，不是每个 **deployment** 都需要这么多的配置，以下是必须存在的字段属性介绍：

|          名称          |  类型  |                   描述                   |
| :--------------------: | :----: | :--------------------------------------: |
|        version         | String |       属于 k8s 哪一个API 版本或组        |
|          kind          | String |  资源类型，如`pod、deployment、service`  |
|        metadata        | Object |           元数据对象，嵌套字段           |
|     metadata.name      | String |             元数据对象的名字             |
|          spec          | Object | 定义容器的规范，创建的容器应该有哪些特性 |
|    spec.container[]    |  List  |              定义容器的列表              |
| spec.container[].name  | String |                容器的名称                |
| spec.container[].image | String |           定义要用到镜像的名称           |

#### 3）命令式对象配置

我们结合以上必存的字段，可以简单写出一个 yaml （test.yaml） 文件：

```shell
apiVersion: v1
kind: Namespace
metadata:
  name: cbuc-ns
  
# 此处三个 - 表示分隔多个配置文件
---

apiVersion: v1
kind: Pod
metadata:
  name: nginx-pod
  namespace: cbuc-ns
spec:
  containers:
  - name: nginx01
    image: nginx:1.19.0
```

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

接下来的阶段便是我们针对 k8s 中每个资源展开说明了，小伙伴们打起精神了哦！

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
  name：aaa-test
```

然后执行`kubectl create -f namespace.yml`， 这样子我们同样也可以获得一个名称为 **aaa-test** 的命名空间。

然后我们就可以在资源创建的时候使用了：

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: test-pod
  namespace: aaa-test
spec:
  containers:
  - image: nginx:1.19.0
    name: nginx01
```

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

这种就是我们日常中经常用到的。一旦被创建就会放入 **etcd** 中存储，接着就会被调度到任一节点上运行，当 Pod 里某个容器停止时，Kubernetes 会自动检测到这个问题并且重新启动这个 Pod 里某所有容器， 如果 Pod 所在的 Node 宕机，则会将这个 Node 上的所有 Pod 重新调度到其它节点上。

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

之前在 **docker** 我们有进行测试没有对 **docker** 资源进行限额的时候，运行一个 **elasticSearch** 镜像的时候服务器直接卡死。那么在 **docker** 能做到资源限额，**pod** 自然也可以。

**Kubernetes** 中可以设置限额的计算资源有 **CPU** 和 **Memory** 两种。**Kubernetes** 我们想要进行配额限定需要设定两个参数：`Request` 和 `Limits` 

- **Request**：表示该资源最小的申请量，系统必须满足要求
- **Limits**：表示该资源最大允许使用量，不能超出这个量，当容器试图使用超过这个量的资源时，就会被 **Kubernetes** kill 掉并重启

```shell
spec:
  containers:
  - name: test01
    image: nginx:1.19.0
    resources: 
      limits: 
        cpu: "500m"
        memory: "128Mi"
      request:
        cpu: "250m"
        memory: "64Mi"
```

上面表示一个 **nginx** 容器最少需要 0.25个CPU和 64 MB内存，最多只能使用  0.5个CPU和 128 MB内存。

##### ㈡ pod 使用

我们先来看一份 **pod** 资源清单：

![](https://gitee.com/cbuc/picture/raw/master/typora/image-20210416133540276.png)

这份清单大部分看起来会比较陌生，但是有部分关键属性我们在上面已经讲过了，当我们实际要用的时候如果记不起那么多我们可以使用指令 `kubectl explain pod.xxx`  的方式来查看每个属性的含义，例如

![](https://gitee.com/cbuc/picture/raw/master/typora/20210418225352.png)

###### ① 简单创建

我们如果想要创建一个 pod ，只需要简单准备一份 **test.yml** 文件即可：

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: nginxPod
  namespace: cbuc-test
spec: 
  containsers:
  - name: nginx
    image: nginx:1.19.0
  - name: java
    image: java:1.8
```

然后通过 **命令式对象配置** 的指令 `kubectl create -f test.yml` 就可以获取到一个 nginx **pod**。这只是一个简单的pod 配置，我们在里面声明了两个容器：`nginx` 和 `java`。通过指令`kubectl get pod -n cbuc-test` 查看当前 **pod** 的状态。

docker 可以用 `docker exec -it` 进入容器，k8s 也是类似此命令：

```shell
kubectl exec -it pod名称 -n 命名空间 bash
```

通过以上命令就可以进入到我们的pod中

// todo

###### ② 属性说明

上面我们已经成功的创建了一个 pod，但是这只是一个简单的 pod 配置，我们可以针对该 **yaml** 文件展开扩展~

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

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: pod-command
  namespace: aaa-test
spec:
  containers:
  - name: centos
    image: centos:7.0
    command: ["/bin/sh","-c","touch /mnt/test.txt;while true;do /bin/echo $(date +%T) >> /mnt/test.txt; sleep 3; done;"]
```

我们在上面创建了一个 centos的pod，然后在pod初始化完成后，便会执行 command 中的命令，我们可以通过 `kubectl exec -it pod名称 -n 命名空间 bash` 然后进入到 `/mnt/test.txt`

或者我们可以在pod外部执行命令：

// todo

```shell
kubectl exec -it pod名称 -n 命名空间 -c centos /bin/sh / # tail -f /mnt/test.txt
```

**3. args** 

我们上面说到的 command 已经可以完成启动命令和传递参数的功能，但是我们 k8s 中还提供了一个 `args` 选项，用于传递参数。k8s 中使用 command 和 args 两个参数可以实现覆盖 Dockerfile 中的 **ENTRYPOINE** 的功能。

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: pod-command
  namespace: aaa-test
spec:
  containers:
  - name: centos
    image: centos:7.0
    command: ["/bin/sh"]
    args: ["-c","touch /mnt/test.txt;while true;do /bin/echo $(date +%T) >> /mnt/test.txt; sleep 3; done;"]
```

`注意:`

1. 如果 command 和 args 均没有写，那么是使用 Dockerfile 的配置
2. 如果 command 写了，args 没有写，那么 Dockerfile 默认的配置会被忽略，执行输入的 command
3. 如果 command 没写，args 写了， 那么 Dockerfile 中配置的 ENTRYPOINT 的命令会被执行，使用当前 args 的参数
4. 如果 command 和 arg 都写了，那么 Dockerfile 的配置就会被忽略，执行 command 命令加上 args 参数

**4. env**

用于在 pod 中的容器设置环境变量

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: pod-command
  namespace: aaa-test
spec:
  containers:
  - name: centos
    image: centos:7.0
    env:
    - name: "username"
      value: "cbuc"
    command: ["/bin/sh", "-c", "/bin/echo $username >> /mnt/test.txt;"]
```

执行命令查看：

//todo

```shell
kubectl exec -it pod名称 -n 命名空间 -c centos /bin/sh / # tail -f /mnt/test.txt
```

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

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: nginx-pod
  namespace: aaa-test
spec:
  containers:
  - name: nginx01
    image: nginx:1.19.0
    ports:
      name: nginx-port
      containerPost: 80
      protocol: TCP
```

创建方式可以选择 3 中创建方式任意一种，然后创建完成后我们可以通过 `podIp+containerPort` 来访问到 nginx 资源

**6. resources**

容器中运行的程序需要占用一定的资源（CPU和内存），在运行的时候如果不对某个容器的资源进行限制，那么它可能会耗尽服务器的大量资源，防止这种情况的发生，k8s 中提供了 `resource` 属性，对资源进行限制。这个属性下有两个子选项：

- **limits：** 用于限制运行容器的最大占用资源，当容器占用资源超过 limit 时会被终止，并进行重启
- **requests：**  用于设置容器需要的最小资源，如果环境资源不够，容器将会无法启动

看个使用例子：

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: nginx-pod
  namespace: aaa-test
spec:
  containers:
  - name: nginx01
    image: nginx:1.19.0
    resources:
      limits: # 上限
        cpu: "2" # 单位 core 数
        memory: "500Mi"
      requests: # 下限
        cpu: "1"
        memory: "100Mi"
```

- **cpu：** core数，可以为整数或小数
- **memory：** 内存大小，可以使用 Gi， Mi， G，M 等形式

