大家好，欢迎来到小菜个人 **solo** 学堂。在这里，知识免费，不吝吸收！关注免费，不吝动手！
**死鬼~看完记得给我来个三连哦！**


![](https://gitee.com/cbuc/picture/raw/master/17169c46045528af)


>本文主要介绍 `kubernetes集群的搭建`
>
>如有需要，可以参考
>
>如有帮助，不忘 **点赞** ❥
>
>
>微信公众号已开启，**小菜良记**，没关注的同学们记得关注哦！

阅读这篇文章先需要对 **docker** 的基本知识有所了解！相关阅读请移步：[Docker上手，看完觉得自己又行了！](https://mp.weixin.qq.com/s/BViQO6ZBZJ0VTsgi3HZeSw)

相信点进来的小伙伴应该都对 **k8s** 有所耳闻，甚至于已经使用上了。而如果是因为对标题感到好奇的小伙伴也别急着划走，因为我劝你一定要学习 **Kubernetes（k8s）**。而标题也并非标题党，由于 **k8s** 集群大体上分为两大类：

- **一主多从**：一台 master 节点和多台 node 节点，搭建比较简单，但是有可能出现 master 单机故障
- **多主多从：** 多台 master 节点和多台 node 节点，搭建比较麻烦，但是安全性高

不管是 **一主多从** 异或者是 **多主多从** ，这里至少都是需要三台服务器，而且每台服务器的规格至少得在 `2G内存 2颗CPU` 配置起步，而我们如果纯属为了平时练习使用，花费一笔钱去投资服务器，可能有部分人是不愿意的，所以这里就呼应了标题~接下来小菜将带给你比较节省的方案去学习 **k8s集群的搭建**！

开头说到，**我劝你一定要学习 k8s** 这并非是一句空话。当下，云原生也并非是一个新名词，它已经指定了一条新的开发道路，**敏捷、可扩展、可复制、最大化利用...**便是这条道路的代名词！这篇文章不单单介绍 **Kubernetes** 的搭建，如果对 **Kubernetes** 有所熟悉的同学可以直接跳转到 **Kubernetes 集群搭建** 的部分，如果不熟悉的同学建议先看看前半部分，先对 **Kubernetes** 有所了解一下。

## Kubernetes

#### 一、K8s 事前了解

有些同学可能感到有点奇怪，为什么一会说 **kubernetes**，一会说 **k8s**，这两个是同一个东西吗？答案是肯定的

> Kubernetes 简称 k8s，是用 8 来代替 8 个字符 **“ubernete”**  的缩写

这个一个用来管理云平台中多个主机上的容器化应用，它的目的便是让部署容器化的应用简单且高效，它提供了应用部署，规划，更新，维护的一种机制。

我们先来看看部署应用的迭代过程：

- **传统部署：** 直接将应用程序部署在物理机上

- **虚拟化部署：** 可以在一台物理机上运行多个虚拟机，每个虚拟机都是独立的一个环境
- **容器化部署：** 与虚拟机类似， 但是共享了操作系统

![](https://gitee.com/cbuc/picture/raw/master/typora/20210410180502.png)

看了以上部署方式，想想看你们公司现在是用的哪一种~说到容器化部署，学过docker的同学肯定第一时间想到**docker** ，**docker** 的容器化部署方式确实给我们带来了很多便利，但是问题也是存在的，有时候这些问题会被我们刻意性的回避，因为 **docker** 实在是有点好用，让人有点不忍心对它产生质疑，但是又不得不面对：

- 一个容器故障停机了，怎么样保证高可用让另外一个容器立刻启动去替补上停机的容器

- 当并发访问量上来的时候，是否可以做到自动扩容，并发访问量下去的时候是否可以做到自动缩容
- ...

容器的问题确实有时候挺值得深思的，而这些容器管理的问题统称为 **容器编排** 问题，我们能想到的问题，自然有人去解决，例如 **docker** 自家就推出了 **Docker Swarm** 容器编排工具，**Apache** 退出了 **Mesos** 资源统一管控工具，**Google** 推出了`Kubernetes`容器编排工具，而这也是我们要说到的主角！

##### 1）K8s优点

- **自我修复**：一旦某一个容器崩溃，能够在1秒左右迅速启动新的容器
- **弹性伸缩**：可以根据需要，自动对集群中正在运行的容器数量进行调整
- **服务发现**：服务可以通过自动发现的形式找到它所依赖的服务
- **负载均衡**：如果一个服务启动了多个容器，能够自动实现请求的负载均衡
- **版本回退**：如果发现新发布的程序版本有问题，可以立即回退到原来的版本
- **存储编排**：可以根据容器自身的需求自动创建存储卷

##### 2）K8s 构成组件

一个完整的 **Kubernetes** 集群是由 **控制节点 master** 、**工作节点 node** 构成的，因此这种集群方式也分为 **一主多从** 和 **多主多从**，而每个节点上又会安装不同组件以提供服务。

###### 1、**Master**

集群的控制平面，负责集群的决策（管理）。它旗下存在以下组件

- **ApiServer** ：资源操作的唯一入口，接收用户输入的命令，提供认证、授权、Api 注册和发现等机制
- **Scheduler**：负责集群资源调度，按照预定的调度策略将 **pod** 调度到相应的 **node** 节点上
- **ControllerManager**：负责维护集群的状态，比如程序部署安排，故障检测，自动扩展，滚动更新等
- **Etcd**：负责存储集群中各种资源对象的信息

###### 2、Node

集群的数据平面，负责为容器提供运行环境（干活）。它旗下存在以下组件

- **Kubelet**：负责维护容器的生命周期，即通过控制 docker 来创建、更新、销毁容器
- **KubeProxy**：负责提供集群内部的服务发现和负载均衡

![](https://gitee.com/cbuc/picture/raw/master/typora/20210410180554.png)

看完了以上介绍，那我们接下来就开始进行 **k8s** 集群的搭建！

#### 二、k8s 集群搭建

##### 1）Centos7 安装

首先我们需要软件：

- VMware Workstation Pro 
- Centos 7.0 镜像

虚拟机软件可百度查找下载，如若没有联系小菜，小菜给你提供

镜像可访问阿里云进行下载，如下图：[下载地址](http://mirrors.aliyun.com/centos/7/isos/x86_64/)

![](https://gitee.com/cbuc/picture/raw/master/typora/20210410191612.png)

完成虚拟机的安装后我们便可在 **VMware** 中安装 **Centos7**

- 我们选择 **创建新的虚拟机**

![](https://gitee.com/cbuc/picture/raw/master/typora/20210410192057.png)

- 选择自定义安装

> 典型安装：VMware会将主流的配置应用在虚拟机的操作系统上，对于新手来很友好。
>
> 自定义安装：自定义安装可以针对性的把一些资源加强，把不需要的资源移除。避免资源的浪费。

![](https://gitee.com/cbuc/picture/raw/master/typora/20210406225042.png)

- 兼容性一般向下兼容

![](https://gitee.com/cbuc/picture/raw/master/typora/20210410231559.png)

- 选择我们下载好的 **centos** 镜像

![](https://gitee.com/cbuc/picture/raw/master/typora/20210410231559.png)

- 给自己的虚拟机分配名称和安装地址，我们需要安装三台，所以名称我这里分别命名为（master、node01、node02）

![](https://gitee.com/cbuc/picture/raw/master/typora/20210410231559.png)

- 给自己的虚拟机分配资源，最低要求一般是 2核2G内存

![](https://gitee.com/cbuc/picture/raw/master/typora/20210410231559.png)

![](https://gitee.com/cbuc/picture/raw/master/typora/20210410231559.png)

- 这里使用 **NAT** 网络类型

> 桥接：选择桥接模式的话虚拟机和宿主机在网络上就是平级的关系，相当于连接在同一交换机上。
>
> NAT：NAT模式就是虚拟机要联网得先通过宿主机才能和外面进行通信。
>
> 仅主机：虚拟机与宿主机直接连起来

![](https://gitee.com/cbuc/picture/raw/master/typora/20210410231559.png)

- 接下来一直下一步，然后点击完成即可

![](https://gitee.com/cbuc/picture/raw/master/typora/20210406225209.png)

![](https://gitee.com/cbuc/picture/raw/master/typora/20210406225217.png)

![](https://gitee.com/cbuc/picture/raw/master/typora/20210406225227.png)

![](https://gitee.com/cbuc/picture/raw/master/typora/20210406225239.png)

![](https://gitee.com/cbuc/picture/raw/master/typora/20210406225255.png)

![](https://gitee.com/cbuc/picture/raw/master/typora/20210410195538.png)

- 安装完后我们便可以在页面看到以下结果，点击开启此虚拟机：

![](https://gitee.com/cbuc/picture/raw/master/typora/20210410195907.png)

- 选择 **install CentOS7**

![image-20210410200006452](https://gitee.com/cbuc/picture/raw/master/typora/20210410200006.png)

- 然后就可以看到安装过程：

![](https://gitee.com/cbuc/picture/raw/master/typora/20210410200037.png)

- 过一会便会看到让我们选择语言的界面，这里选择中文并继续

![](https://gitee.com/cbuc/picture/raw/master/typora/20210410200137.png)

- 软件选择我们可以选`基础设施服务器`，安装位置可选 `自动分区`

![](https://gitee.com/cbuc/picture/raw/master/typora/20210410200250.png)

- 然后我们需要点击 **网络和主机名** 进入网络配置

![](https://gitee.com/cbuc/picture/raw/master/typora/20210406224402.png)

- 我们在 tarbar 栏点击 编辑 -> 虚拟网络编辑器 查看虚拟机的子网IP

![](https://gitee.com/cbuc/picture/raw/master/typora/20210410200507.png)

![](https://gitee.com/cbuc/picture/raw/master/typora/20210410200502.png)

- 这边我们手动自定义添加 Ipv4 的地址，DNS服务器可填阿里云的

我们分配的地址需要排除 `255` 和 `02` 这两个地址，分别是广播和网关地址。

我是这样配置的：

> **master 节点** ： `192.168.108.100`
>
> **node01 节点** ：`192.168.108.101`
>
> **node02 节点** ：`192.168.108.102`

![](https://gitee.com/cbuc/picture/raw/master/typora/20210406224700.png)

- 配置完选择保存并点击完成，然后设置主机名

我是这样配置的：

> **master 节点** ： `master`
>
> **node01 节点** ：`node01`
>
> **node02 节点** ：`node02`

![](https://gitee.com/cbuc/picture/raw/master/typora/20210406224745.png)

- 完成以上配置后，大致是如下样子

![](https://gitee.com/cbuc/picture/raw/master/typora/20210406224857.png)

- 点击开始安装后，我们来到了以下页面，然后配置以下两个信息

![](https://gitee.com/cbuc/picture/raw/master/typora/20210406224917.png)

完成以上配置后，重启便可以使用，其他两个节点也是同样的配置，可以直接选择克隆，网络配置和主机名 记得改~ 然后我们便得到以下配置的三个服务器：

| 主机名 | IP              | 配置                |
| ------ | --------------- | ------------------- |
| master | 192.168.108.100 | 2 核 2G内存 30G硬盘 |
| node01 | 192.168.108.101 | 2 核 2G内存 30G硬盘 |
| node02 | 192.168.108.102 | 2 核 2G内存 30G硬盘 |

##### 2）环境配置

完成以上服务器的搭建后，我们可以利用 **shell 工具** 进行连接，开始搭建 k8s 环境

- 主机名解析

为了集群节点间的直接调用，我们需要配置一下主机名解析，分别在三台服务器上编辑 `/etc/hosts`

![](https://gitee.com/cbuc/picture/raw/master/typora/20210410202145.png)

- 同步时间

集群中的时间必须要精确一致，我们可以直接使用`chronyd`服务从网络同步时间，三台服务器需做同样的操作

![](https://gitee.com/cbuc/picture/raw/master/typora/20210410202344.png)

- 禁用**iptables**和**firewalld**服务

kubernetes和docker在运行中会产生大量的**iptables**规则，为了不让系统规则跟它们混淆，直接关闭系统的规则。三台虚拟机需做同样操作：

```shell
# 1 关闭firewalld服务
[root@master ~]# systemctl stop firewalld
[root@master ~]# systemctl disable firewalld
# 2 关闭iptables服务
[root@master ~]# systemctl stop iptables
[root@master ~]# systemctl disable iptables
```

- 禁用**selinux**

**selinux**是**linux**系统下的一个安全服务，如果不关闭它，在安装集群中会产生各种各样的奇葩问题

```shell
# 永久关闭
[root@master ~]# sed -i 's/enforcing/disabled/' /etc/selinux/config
# 临时关闭
[root@master ~]# setenforce 0
```

- 禁用swap分区

swap分区指的是虚拟内存分区，它的作用是在物理内存使用完之后，将磁盘空间虚拟成内存来使用启用swap设备会对系统的性能产生非常负面的影响，因此kubernetes要求每个节点都要禁用swap设备但是如果因为某些原因确实不能关闭swap分区，就需要在集群安装过程中通过明确的参数进行配置说明

```shell
# 临时关闭
[root@master ~]# swapoff -a
# 永久关闭
[root@master ~]# vim /etc/fstab
```

注释掉swap分区那一行

![](https://gitee.com/cbuc/picture/raw/master/typora/20210410203051.png)

- 修改**linux**的内核参数

我们需要修改**linux**的内核参数，添加网桥过滤和地址转发功能，编辑`/etc/sysctl.d/kubernetes.conf`文件，添加如下配置:

```shell
net.bridge.bridge-nf-call-ip6tables = 1
net.bridge.bridge-nf-call-iptables = 1
net.ipv4.ip_forward = 1
```

添加后进行以下操作：

```shell
# 重新加载配置
[root@master ~]# sysctl -p
# 加载网桥过滤模块
[root@master ~]# modprobe br_netfilter
# 查看网桥过滤模块是否加载成功
[root@master ~]# lsmod | grep br_netfilter
```

同样是在三台服务器都进行操作，成功信息如下：

![](https://gitee.com/cbuc/picture/raw/master/typora/20210406231337.png)

- 配置 **ipvs** 功能

在**kubernetes**中**service**有两种代理模型，一种是基于**iptables**的，一种是基于**ipvs**的
相比较的话，**ipvs**的性能明显要高一些，但是如果要使用它，需要手动载入**ipvs**模块

```shell
# 安装ipset和ipvsadm
[root@master ~]# yum install ipset ipvsadmin -y

# 添加需要加载的模块写入脚本文件
[root@master ~]# cat <<EOF > /etc/sysconfig/modules/ipvs.modules
#!/bin/bash
modprobe -- ip_vs
modprobe -- ip_vs_rr
modprobe -- ip_vs_wrr
modprobe -- ip_vs_sh
modprobe -- nf_conntrack_ipv4
EOF
# 为脚本文件添加执行权限
[root@master ~]# chmod +x /etc/sysconfig/modules/ipvs.modules
# 执行脚本文件
[root@master ~]# /bin/bash /etc/sysconfig/modules/ipvs.modules
# 查看对应的模块是否加载成功
[root@master ~]# lsmod | grep -e ip_vs -e nf_conntrack_ipv4
```

![](https://gitee.com/cbuc/picture/raw/master/typora/20210406231544.png)

- 完成以上配置后重启服务器

```shell
[root@master ~]# reboot
```

##### 3）docker安装

第一步：

```shell
# 获取镜像源
[root@master ~]# wget https://mirrors.aliyun.com/docker-ce/linux/centos/docker-ce.repo -O /etc/yum.repos.d/docker-ce.repo
```

第二步：

```shell
# 安装特定版本的docker-ce
# 必须指定--setopt=obsoletes=0，否则yum会自动安装更高版本
[root@master ~]# yum install --setopt=obsoletes=0 docker-ce-18.06.3.ce-3.el7 -y
```

第三步：

```shell
# 添加一个配置文件
# Docker在默认情况下使用的Cgroup Driver为cgroupfs，而kubernetes推荐使用systemd来代替cgroupfs
[root@master ~]# mkdir /etc/docker
```

第四步：

```shell
# 添加阿里云 yum 源, 可从阿里云容器镜像管理中复制镜像加速地址
[root@master ~]# cat <<EOF > /etc/docker/daemon.json
{
"registry-mirrors": ["https://xxxx.mirror.aliyuncs.com"]
}
EOF
```

 第五步：

```shell
# 启动docker
[root@master ~]# systemctl enable docker && systemctl start docker
```

完成以上5步，也就完成了 docker 的安装，离成功更近一步~

##### 4）集群初始化

1、由于 **kubernetes** 的镜像源在国外，速度比较慢，因此我们需要切换成国内的镜像源

```shell
# 编辑 /etc/yum.repos.d/kubernetes.repo 添加一下配置
[root@master ~]# vim /etc/yum.repos.d/kubernetes.repo
[kubernetes]
name=Kubernetes
baseurl=http://mirrors.aliyun.com/kubernetes/yum/repos/kubernetes-el7-x86_64
enabled=1
gpgcheck=0
repo_gpgcheck=0
gpgkey=http://mirrors.aliyun.com/kubernetes/yum/doc/yum-key.gpg
http://mirrors.aliyun.com/kubernetes/yum/doc/rpm-package-key.gpg
```

![](https://gitee.com/cbuc/picture/raw/master/typora/20210410224144.png)

2、然后安装**kubeadm**、**kubelet**和**kubectl** 三个组件

```shell
yum install --setopt=obsoletes=0 kubeadm-1.17.4-0 kubelet-1.17.4-0
kubectl-1.17.4-0 -y
```

![](https://gitee.com/cbuc/picture/raw/master/typora/20210406234940.png)

3、配置 kubelet 的group

```shell
# 编辑 /etc/sysconfig/kubelet，添加下面的配置
KUBELET_CGROUP_ARGS="--cgroup-driver=systemd"
KUBE_PROXY_MODE="ipvs"
```

![](https://gitee.com/cbuc/picture/raw/master/typora/20210410224600.png)

4、这步是来初始化集群的，`因此只需在 master 服务器上执行即可`，上面那些是每个服务器都需要执行！

```shell
# 创建集群
# 由于默认拉取镜像地址 k8s.gcr.io 国内无法访问，这里指定阿里云镜像仓库地址
[root@master ~]# kubeadm init \
--apiserver-advertise-address=192.168.108.100 \
--image-repository registry.aliyuncs.com/google_containers \
--kubernetes-version=v1.17.4 \
--pod-network-cidr=10.244.0.0/16 \
--service-cidr=10.96.0.0/12 

#使用 kubectl 工具
[root@master ~]# mkdir -p $HOME/.kube
[root@master ~]# sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
[root@master ~]# sudo chown $(id -u):$(id -g) $HOME/.kube/config
```

![](https://gitee.com/cbuc/picture/raw/master/typora/20210407000601.png)

然后我们需要将**node** 节点加入集群中，在 `node 服务器` 上执行上述红框的命令：

```shell
[root@master ~]# kubeadm join 192.168.108.100:6443 --token xxx \ 
--discovery-token-ca-cert-hash sha256:xxx
```

便可在 **master 节点** 获取到节点信息：

![](https://gitee.com/cbuc/picture/raw/master/typora/20210407000758.png)

但是我们这个时候查看集群状态都是为`NotReady`，这是因为还没有配置网络插件

5、安装网络插件

**kubernetes**支持多种网络插件，比如**flannel**、**calico**、**canal**等等，这里选择使用**flanne**

下载 **flanneld-v0.13.0-amd64.docker**  ：[下载地址](https://github.com/flannel-io/flannel/releases/)

![](https://gitee.com/cbuc/picture/raw/master/typora/20210410230531.png)

下载完成后，上传至 **master 服务器** 执行以下命令

```shell
docker load < flanneld-v0.13.0-amd64.docker
```

执行完成后便可看到多了个 **flannel** 镜像：

![](https://gitee.com/cbuc/picture/raw/master/typora/20210410230737.png)

然后我们需要获取**flannel**的配置文件来部署 **flannel** 服务

```shell
[root@master ~]# wget https://raw.githubusercontent.com/coreos/flannel/master/Documentation/kube-flannel.yml

# 使用配置文件启动fannel
[root@master ~]# kubectl apply -f kube-flannel.yml

# 再次查看集群节点的状态
[root@master ~]# kubectl get nodes
```

![](https://gitee.com/cbuc/picture/raw/master/typora/20210410180812.png)

这个时候所有节点的状态都是`Ready` 的状态，到此为止，我们的 **k8s** 集群就算搭建完成了！

##### 5）集群功能验证

接下来就是我们的验证时间，之前我们学 **docker** 的时候往往会启动一个 **nginx** 容器来测试是否可用，**k8s** 我们也同样来部署一个 **nginx** 来测试下服务是否可用~ 

（下面例子为测试例子，如果不清楚每个指令的作用也不要紧，后面我们会出篇 k8s 的教学文章来说明 **k8s** 如果使用！）

- 首先我们创建一个 deployment

```shell
[root@master ~]# kubectl create deployment nginx --image=nginx:1.14-alpine
deployment.apps/nginx created

[root@master ~]# kubectl get deploy
NAME    READY   UP-TO-DATE   AVAILABLE   AGE
nginx   1/1     1            1           31s
```

- 然后创建一个 service 来让外界能够访问到我们 nginx 服务

```shell
[root@master ~]# kubectl expose deploy nginx --port=80 --target-port=80 --type=NodePort
service/nginx exposed

[root@master ~]# kubectl get svc 
NAME         TYPE        CLUSTER-IP       EXTERNAL-IP   PORT(S)        AGE
nginx        NodePort    10.110.224.214   <none>        80:31771/TCP   5s
```

然后我们通过 node 节点的 IP 加上service 暴露出来的 nodePort 来访问我们的 nginx 服务：

![](https://gitee.com/cbuc/picture/raw/master/typora/20210410180733.png)

也可以直接在集群中通过 service 的 IP 加上映射出来的 port 来访问我们的服务：

![](https://gitee.com/cbuc/picture/raw/master/typora/20210410180753.png)

从结果上看两种访问都是可用的，说明我们的 **nginx** 服务部署成功，不妨点个关注助助兴~

> 公众号搜索：小菜良记
>
> 更多干货值得阅读哦！

那么为什么我们可以访问到 **nginx**？我们不妨结合上面说到的 k8s 组件来梳理一下各个组件的调用关系：

![](https://gitee.com/cbuc/picture/raw/master/typora/20210410181842.png)

1. **kubernetes** 启动后，无论是 **master** 节点 亦或者 **node** 节点，都会将自身的信息存储到 **etcd** 数据库中

2. 创建 **nginx** 服务，首先会将安装请求发送到 **master** 节点上的 **apiServer** 组件中

3. **apiServer** 组件会调用 **scheduler** 组件来决定应该将该服务安装到哪个 **node** 节点上。这个时候就需要用到 **etcd** 数据库了，**scheduler**会从 **etcd** 中读取各个 **node** 节点的信息，然后按照一定的算法进行选择，并将结果告知给 **apiServer**

4. **apiServer** 调用 **controllerManager** 去调度 **node** 节点，并安装 **nginx** 服务

5. **node** 节点上的 **kubelet** 组件接收到指令后，会通知**docker**，然后由 **docker** 来启动一个 **nginx** 的**pod**

   > **pod** 是 **kubernetes** 中的最小操作单元，容器都是跑在 **pod** 中

6. 以上步骤完成后，**nginx** 服务便运行起来了，如果需要访问 **nginx**，就需要通过 **kube-proxy** 来对 **pod** 产生访问的代理，这样外部用户就能访问到这个 **nginx** 服务

以上便是运行一个服务的全过程，不知道看完之后有没有一种 `肃然起敬` 的感觉，设计是在太巧妙了，因此到这里，难道就不准备看 k8s 使用下文！如果准备看的话，小手将关注点起来哦！

**END**

以上便是 **k8s** 集群的搭建过程，有了 **k8s** 的环境，你还怕学不会 **k8s** 的使用吗！在自己的虚拟机上尽情折腾，弄坏了也就一个恢复快照的事~ 我是小菜，路漫漫，与你一同求索！

![看完不赞，都是坏蛋](https://gitee.com/cbuc/picture/raw/master/typora/20210410232040.png)

> 今天的你多努力一点，明天的你就能少说一句求人的话！
>
> *我是小菜，一个和你一起学习的男人。* `💋`
>
>
> 微信公众号已开启，**小菜良记**，没关注的同学们记得关注哦！