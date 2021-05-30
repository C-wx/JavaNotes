大家好，我是小菜，一个渴望在互联网行业做到蔡不菜的小菜。可柔可刚，点赞则柔，白嫖则刚！
**死鬼~看完记得给我来个三连哦！**


![](https://user-gold-cdn.xitu.io/2020/4/11/17169c46045528af?w=240&h=224&f=jpeg&s=7529)


>本文主要介绍 `K8s中数据存储的使用`
>
>如有需要，可以参考
>
>如有帮助，不忘 **点赞** ❥
>
>
>微信公众号已开启，**小菜良记**，没关注的同学们记得关注哦！

k8s 的进程到这里我们已经完成了 **Namespace**、**Pod**、**PodController** 几种资源的使用方式，已经过大半了哦~这篇文章我们就继续来了解一下在**k8s**  中怎么进行数据存储！

`kubernetes的最小控制单元，容器都是运行在 pod 中的，一个pod中可以有 1 个或多个容器`

这个概念我们早已了解，不难明白容器的生命周期会很短暂，当pod出现问题的时候，pod控制器会频繁的创建和销毁，而每个pod都是独立的，因此存储在容器中的数据也会被清除，这种结果无疑是致命打击。这时，可能就会有小伙伴说了，**docker** 中存在数据挂载，**k8s** 肯定也存在，我们可以利用数据挂载来解决该问题~那么，恭喜你答对了，**k8s** 中不仅支持数据挂载，而且支持的功能还相当强大，话不多说，我们接下来就进入数据世界~

## 数据存储

**k8s**中有个 **Volume** 的概念，**Volumn** 是 **Pod** 中能够被多个容器访问的共享目录，**K8s** 的 **Volume** 定义在 **pod** 上，然后被一个 pod里的多个容器挂载到具体的文件目录下，**k8s**通过 **Volume** 实现同一个 pod 中不同容器之间的数据共享以及数据的持久化存储，**Volume**的生命周期不与pod中单个容器的生命周期相关，当容器终止或重启的时候，**Volume**中的数据也不会被丢失。

**Volume** 支持常见的类型如下：

![](https://gitee.com/cbuc/picture/raw/master/typora/image-20210504144146336.png)

除了上面列出来的这些，还有 **gcePersistentDisk、awsElasticBlockStore、azureFileVolume、azureDisk** 这些存储，但是因为使用较少，所以不做过多了解。下面我们就来详细看看每个存储该如何使用！

### 一、基本存储

#### 1）EmptyDir

> 这是个最基础的 **Volume类型**，一个 **EmptyDir** 就是 **Host** 上的一个空目录。

##### **概念：**

它是在 **Pod** 被分配到 **Node** 节点上时才会被创建，初始内容为空，并且无需指定宿主机上对应的目录文件，**它会自动在宿主机上分配一个目录**。

**值得关注的是**：

`Pod 销毁时，EmptyDir 中的数据也会被永久删除！`

**用处：**

1. 用作临时空间，比如 Web 服务器写日志或者 tmp 文件需要的临时目录。
2. 用作多容器之间的共享目录（一个容器需要从另一个容器中获取数据的目录）

##### **实战：**

我们以**nginx**为例，准备一份**资源清单**

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: nginx
  namespace: cbuc-test
  labels:
    app: nginx-pod
spec:
  containers:
  - name: nginx
    image: nginx:1.14-alpine
    ports:
   	- containerPort: 80
   	volumeMounts:	# 将 nginx-Log 挂载到nginx容器中，容器内目录为/var/log/nginx
   	- name: nginx-log
   	  mountPath: /var/log/nginx
  volumes:	#在此声明volume
  - name: nginx-log
    emptyDir: {}
```

然后我们创建后可以看看**emptyDir**存储卷在宿主机的位置。默认情况下宿主机中声明volume的目录位置是在 `/var/lib/kubelet/pods/<Pod 的 ID>/volumes/kubernetes.io~<Volume 类型 >/<Volume 名字 >` 中。

#### 2）HostPath

##### 概念：

**HostPath** 就是将 Node 节点上一个实际目录挂载到pod中，供容器使用，这种好处就是在 pod 销毁后，该目录下的数据依然存在！

##### 实战：

我们以 **nginx** 为例，准备一份资源清单：

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: nginx
  namespace: cbuc-test
  labels:
    app: nginx-pod
spec:
  containers:
  - name: nginx
    image: nginx:1.14-alpine
    ports:
    - containerPort: 80
    volumeMounts:
    - name: nginx-log
      mountPath: /var/log/nginx
  volumes:
  - name: nginx-log
    hostPath:           # 指定宿主机目录为 /data/nginx/log
      path: /data/nginx/log
      type: DirectoryOrCreate   # 创建类型
```

> `spec.volumes.hostPath.type`（创建类型）：
>
> - **DirectoryOrCreate**：目录存在就是用，不存在就先创建后使用
> - **Directory：** 目录必须存在
> - **FileOrCreate：** 文件存在就是用，不存在就创建后使用
> - **File：** 文件必须存在
> - **Socket：** unix 套接字必须存在
> - **CharDevice：** 字符设备必须存在
> - **BlockDevice：** 块设备必须存在

我们根据该资源清单可以创建出一个 pod，然后通过 **podIp** 访问该pod，再查看 `/data/nginx/log` 下的日志文件，发现已有日志产生

#### 3）NFS

**HostPath** 是我们日常中比较经常使用到的存储方式了，已经可以满足基本的使用场景。目前为止我们小小的总结一下：**EmptyDir** 是针对 `pod`，如果 pod 被销毁了，那么改数据就会丢失。针对该问题，**HostPath** 进行了改进，存储变成是针对 **Node** ，但是如果 **Node** 宕机了，那么数据还是会丢失。这个时候就需要准备单独的网络存储系统了，而比较常用的便是 **NFS、CIFS**

##### 概念：

**NFS** 是一个网络存储系统，可以搭建一台 **NFS** 服务器，然后将 Pod 中的存储直接连接到 **NFS** 系统上，这样的话，无论pod在节点上如何转移，只要 **Node** 节点和 **NFS**服务器对接没问题，数据就不会出现问题。

![](https://gitee.com/cbuc/picture/raw/master/typora/image-20210504160914401.png)

##### 实战：

既然需要 **NFS** 服务器，那肯定需要自己搭建一个。我们选取 **master** 节点来安装NFS 服务器

```shell
# 安装 nfs 服务器
yum install -y nfs-utils
# 准备共享目录
mkdir -p /data/nfs/nginx
# 将共享目录以读写权限暴露给 192.168.108.0/24 网段的所有主机
vim /etc/exports
# 添加以下内容
/data/nfs/nginx    192.168.108.0/24(rw,no_root_squash)
# 启动 nfs 服务器
systemctl start nfs
```

然后我们在各个节点上同样安装 NFS，以供驱动 NFS 设备

```shell
yum install -y nfs-utils
```

做好以上准备后我们就可以准备资源清单文件了：

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: nginx
  namespace: cbuc-test
  labels:
    app: nginx
spec:
  containers:
  - name: nginx-pod
    image: nginx:1.14-alpine
    ports:
    - containerPort: 80
    volumeMounts:
    - name: nginx-log
      mountPath: /var/log/nginx
    volumes:
    - name: nginx-log
      nfs:
        server: 192.168.108.100 	# NFS服务器地址，也就是master地址
        path: /data/nfs/nginx		# 共享文件路径
```

创建完pod后，我们可以进入到 `/data/nfs` 目录下查看到两个日志文件了

![](https://gitee.com/cbuc/picture/raw/master/typora/image-20210523173451789.png)

### 二、高级存储

管理存储是管理计算的一个明显问题，该部分需要抽象出如何根据消费方式来提供存储的详细信息。而 **k8s** 也很好的支持了，引入了两个新的 **API** 资源：**PersistenVolume** 和 **PersistentVolumeClaim**

**PersistenVolume**（PV）是持久化卷的意思，是对底层共享存储的一种抽象，一般情况下PV由 k8s 管理员进行创建和配置，它与底层具体的共享存储技术有关，并通过插件完成与共享存储的对象。

**PersistentVolumeClaim**（PVC）是持久卷声明的意思，是用户对存储需求的一种声明，换句话说，PVC其实就是用户向 k8s 系统发出的一种资源需求申请。

![](https://gitee.com/cbuc/picture/raw/master/typora/image-20210504175838461.png)

#### 1）PV

PV 是集群中由管理员配置的一段网络存储，它也是集群中的一种资源，资源清单模板如下：

```yaml
apiVersion: v1
kind: PersistentVolume
meatadata:
  name: pv
spec:
  nfs: 					# 存储类型，可以是 CIFS、GlusterFS
  capacity:				# 存储空间的设置
    storage: 2Gi
  accessModes:  		# 访问模式
  storageClassName:  	# 存储类别
  persistentVolumeReclaimPolicy:    # 回收策略
```

每个属性真的是又长又难记，我们先来看看每个属性的含义：

- **存储类型**

底层实际存储的类型，k8s 支持多种存储类型，每种存储类型的配置都有所差异

- **存储能力（capacity）**

目前只支持存储空间的设置，未来可能会加入 IOPS、吞吐量等指标的设置

- **访问模式（accessModes）**

用于描述用户应用对存储资源的访问权限，有以下几种访问权限：

1. ReadWriteOnce（RWO）：读写权限，但是只能被单个节点挂载
2. ReadOnlyMany（ROM）：只读权限，可以被多个节点挂载
3. ReadWriteMany（RWM）：读写权限，可以被多个节点挂载

- **存储类别**

PV 可以通过storageClassName参数指定一个存储类别

1. 具有特定类别的PV只能与请求了该类别的PVC进行绑定
2. 未设定类别的PV则只能与不请求任何类别的PVC进行绑定

- **回收策略（persistentVolumeReclaimPolicy）**

当PV没有再被使用的时候，需要对其处理的方式（不同的存储类型支持的策略也会不同），有以下几种回收策略：

1. Retain（保留）：保留数据。需要管理员手动清理数据
2. Recycle（回收）：清除PV中的数据，效果相当于 `rm -rf`

3. Delete（删除）：与 PV 相连的后端存储完成 volume 的删除操作，常见于云服务商的存储服务

##### 生命周期：

一个 PV 的生命周期可能会处于4种不同的阶段：

- **Available（可用）：** 表示可用状态，还未被任何PVC绑定
- **Bound（已绑定）：** 表示PV已经被PVC绑定
- **Released（已释放）：** 表示PVC已被删除，但是资源还未被集群重新声明
- **Failed（失败）：** 表示该PV的自动回收失败

##### 实战：

我们前面已经认识了NFS存储服务器，因此我们这里也依然使用 NFS 服务器做底层存储。首先我们需要创建1个PV，也对应着NFS中1个需要暴露的路径。

```shell
# 创建目录
mkdir /data/pv1 -pv

# 向NFS暴露路径
vim /etc/exports
/data/pv1   192.168.108.0/24(rw,no_root_squash)
```

完成以上步骤后我们就需要创建1个 PV：

```yaml
apiVersion: v1
kind: PersistentVolume
metadata:
  name: pv01
  namespace: cbuc-test
  labels:
    app: pv
spec:
  capacity:
    storage: 1Gi
  accessModes:
  - ReadWriteMany
  persistentVolumeReclaimPolicy: Retain
  nfs:
    path: /data/pv1
    server: 192.168.108.100
```

通过创建后我们可以查看到PV：

![](https://gitee.com/cbuc/picture/raw/master/typora/image-20210523173807183.png)

#### 2）PVC

PVC是资源的申请，用来声明对存储空间，访问模式，存储类别的需求信息，资源清单模板如下：

```yaml
apiVersion: v1
kind: persistentVolumeClaim
metadata:
  name: pvc
  namespace: cbuc-test
  labels:
    app: pvc
spec:
  accessModes:  		# 访问模式
  selector:				# 采用标签对PV选择
  storageClassName:		# 存储类别
  resources:				# 请求空间
    request:
      storage: 1Gi
```

很多属性我们在PV中已经了解到了，这里我们简单过一下~

- **访问模式（accessModes）**

用于描述用户应用对存储资源的访问权限

- **选择条件（selector）**

通过 Labels Selector的设置，对于系统中已经存在的PV进行筛选管理

- **资源类别（storageClassName）**

pvc在定义时可以设定需要的后端存储的类别，只有设置了该class的pv才能被系统选出

- **资源请求（resources）**

描述对存储资源的请求

##### 实战：

准备1份PVC的资源清单模板:

```yaml
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: pvc01
  namespace: cbuc-test
spec:
  accessModes:
  - ReadWriteMany
  resources:
    requests:
      storage: 1Gi
```

创建后我们先查看PVC是否创建成功

![](https://gitee.com/cbuc/picture/raw/master/typora/image-20210523174132168.png)

然后再查看pv是否已经被pvc绑定上

![](https://gitee.com/cbuc/picture/raw/master/typora/image-20210523174207142.png)

#### 3）实际使用

上面我们已经成功创建了 PV 和 PVC，但是还没说明如何使用，接下来我们就准备一份pod清单：

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: pod-pvc
  namespace: cbuc-test
spec:
  containers:
  - name: nginx01
    image: nginx:1.14-alpine
    volumeMounts:
    - name: test-pv
      mountPath: /var/log/nginx
  volumes:
  - name: test-pv
    persistentVolumeClaim:
      claimName: pvc01
      readOnly: true
```

#### 4）生命周期

万物皆有生命周期，PV与PVC也不例外，生命周期如下：

- **资源供应**

管理员手动创建底层存储和PV

- **资源绑定**

用户创建PVC，k8s负责根据PVC的声明去寻找PV，并绑定。

1. 如果找到，则会成功进行绑定，用户的应用就可以使用这个PVC了
2. 如果找不到，PVC则会无限处于Pending的状态，直到等到系统管理员创建了一个符合其要求的PV

`PV一旦绑定到某个PVC上，就会被这个PVC独占，不能再与其他PVC进行绑定了`

- **资源使用**

用户可在Pod中想 volume 一样使用pvc

- **资源释放**

用户通过删除PVC来释放PV，当存储资源使用完毕后，用户可以删除PVC，与该PVC绑定的PV将会标记为 `已释放`，但还不能立刻与其他PVC进行绑定，通过之前PVC写入的数据可能还留在存储设备上，只有在清除之后该PV才能再次使用

- **资源回收**

k8s 会根据pv设置的回收策略进行资源的回收

上面列出了 PV和PVC 的生命周期，与其说是生命周期，但不如是PV和PVC的使用过程！

### 三、配置存储

配置存储，顾名思义就是用来存储配置的，其中包括了两种配置存储，分别是 `ConfigMap` 和 `Secret`

#### 1）ConfigMap

**ConfigMap** 是一种比较特殊的存储卷，它的主要作用是用来存储配置信息的。资源清单模板如下：

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: cmp
  namespace: cbuc-test
data:
  info:
    username:cbuc
    sex:male
```

使用方式很简单，少了 `spec`，多了 `data.info`，只需在 `info` 下级以 `key: value` 的方式存储自己想要配置的配置文件即可

通过`kubectl create -f configMap.yaml`命令可创建出一个 **ConfigMap**

![](https://gitee.com/cbuc/picture/raw/master/typora/image-20210523174849358.png)

具体使用如下，我们需要创建一个Pod：

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: test-pod
  namespace: cbuc-test
spec:
  containers:
  - name: nginx
    image: nginx:1.14-apline
    volumeMounts:		# 将 configMap 挂载到目录中
    - name: config
      mountPath: /var/configMap/config
  volumes:
  - name: config
    configMap:
      name: cmp  # 上面我们创建 configMap 的名称
```

然后通过命令`kubectl create -f pod-cmp.yaml`创建出测试Pod，然后可查看pod中的配置文件：

![](https://gitee.com/cbuc/picture/raw/master/typora/image-20210523175229064.png)

#### 2）Secret

在 k8s 中，还存在一种和 ConfigMap 非常类似的对象，称之为 Secret 对象。它主要用于存储敏感信息，例如密码、秘钥、证书等信息。

我们首先对想要配置的数据进行 base64 加密：

```shell
# 加密用户名
[root@master test]# echo -n 'cbuc' | base64
Y2J1Yw==
# 加密密码
[root@master test]# echo -n '123456' | base64
MTIzNDU2
```

然后准备 Secret 资源清单文件

```yaml
apiVersion: v1
kind: Secret
metadata:
  name: secret
  namespace: cbuc-test
type: Opaque    # 表示base64编码格式的Secret
data:
  username: Y2J1Yw==
  password: MTIzNDU2
```

通过命令`kubectl create -f secret.yaml`创建 Secret，然后我们再准备一份Pod资源清单：

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: pod-secret
  namespace: cbuc-test
spec:
  containers:
  - name: nginx
    image: nginx:1.14-apline
    volumeMounts:
    - name: config
      mountPath: /var/secret/config
  volumes:
  - name: config
    secret:
      secretName: secret
```

![](https://gitee.com/cbuc/picture/raw/master/typora/image-20210524211556720.png)

创建后我们进入pod查看配置文件，可以发现配置文件的信息已经是解码后的

![](https://gitee.com/cbuc/picture/raw/master/typora/image-20210524211818975.png)

**END**

这篇我们就介绍了 k8s 的数据存储，篇幅较短，是不是意犹未尽，我们下篇再见（Server和Ingress）！路漫漫，小菜与你一同求索~

![看完不赞，都是坏蛋](https://imgconvert.csdnimg.cn/aHR0cHM6Ly93d3cuNTJkb3V0dS5jbi9zdGF0aWMvdGVtcC9waWMvOWJkNjhkMTUwZjA3ODdjNTYwYTQzOWRhMzU5YTU4MGEucG5n?x-oss-process=image/format,png#pic_center)
> 今天的你多努力一点，明天的你就能少说一句求人的话！
>
> *我是小菜，一个和你一起学习的男人。* `💋`
>
>
> 微信公众号已开启，**小菜良记**，没关注的同学们记得关注哦！

