大家好，我是小菜，一个渴望在互联网行业做到蔡不菜的小菜。可柔可刚，点赞则柔，白嫖则刚！
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

![](https://gitee.com/cbuc/picture/raw/master/17169c46045528af)

阅读这篇文章先需要对 **docker** 的基本知识有所了解！相关阅读请移步：[Docker上手，看完觉得自己又行了！](https://mp.weixin.qq.com/s/BViQO6ZBZJ0VTsgi3HZeSw)

相信点进来的小伙伴大部分都对 **k8s** 有所耳闻，甚至于已经使用上了。而如果是因为对标题感到好奇的小伙伴也别急着划走，因为我劝你一定要学习 **Kubernetes（k8s）**。而标题也并非标题党，由于 **k8s** 集群大体上分为两大类：

- **一主多从**：一台 master 节点和多台 node 节点，搭建比较简单，但是有可能出现 master 单机故障
- **多主多从：** 多台 master 节点和多台 node 节点，搭建比较麻烦，但是安全性高

不管是 **一主多从** 异或者是 **多主多从** ，这里至少都是需要三台服务器，而且每台服务器的规格至少得在 `2G内存 2颗CPU` 配置起步，而我们如果纯属为了平时练习使用，花费一笔钱去投资服务器，可能有部分人是不愿意的，所以这里就呼应了标题~接下来小菜将带给你比较节省的方案去学习 **k8s集群的搭建**！

开头说到，**我劝你一定要学习 k8s** 这并非是一句空话。当下，云原生也并非是一个新名词，它已经指定了一条新的开发道路，**敏捷、可扩展、可复制、最大化利用...**便是这条道路的代名词！这篇文章不单单介绍 **Kubernetes** 的搭建，如果对 **Kubernetes** 有所熟悉的同学可以直接跳转到 **Kubernetes 集群搭建** 的部分，如果不熟悉的同学建议先看看前半部分，先对 **Kubernetes** 有所了解一下。

## Kubernetes

#### K8s了解

有些同学可能感到有点奇怪，为什么一会说 **kubernetes**，一会说 **k8s**，这两个是同一个东西吗？答案是肯定的

> Kubernetes 简称 k8s，是用 8 来代替 8 个字符 **“ubernete”**  的缩写

这个一个用来管理云平台中多个主机上的容器化应用，它的目的便是让部署容器化的应用简单且高效，它提供了应用部署，规划，更新，维护的一种机制。

我们先来看看部署应用的迭代过程：

- **传统部署：** 直接将应用程序部署在物理机上

- **虚拟化部署：** 可以在一台物理机上运行多个虚拟机，每个虚拟机都是独立的一个环境
- **容器化部署：** 与虚拟机类似， 但是共享了操作系统

![](https://i.loli.net/2021/04/09/5JgQibPtBXMjEno.png)

看了以上部署方式，想想看你们公司现在是用的哪一种~说到容器化部署，学过docker的同学肯定第一时间想到**docker** ，**docker** 的容器化部署方式确实给我们带来了很多便利，但是问题也是存在的，有时候这些问题会被我们刻意性的回避，因为 **docker** 实在是有点好用，让人有点不忍心对它产生质疑，但是又不得不面对：

- 一个容器故障停机了，怎么样保证高可用让另外一个容器立刻启动去替补上停机的容器

- 当并发访问量上来的时候，是否可以做到自动扩容，并发访问量下去的时候是否可以做到自动缩容
- ...

容器的问题确实有时候挺值得深思的，而这些容器管理的问题统称为 **容器编排** 问题，我们能想到的问题，自然有人去解决，例如 **docker** 自家就推出了 **Docker Swarm** 容器编排工具，**Apache** 退出了 **Mesos** 资源统一管控工具，**Google** 推出了`Kubernetes`容器编排工具，而这也是我们要说到的主角！

##### K8s优点

- **自我修复**：一旦某一个容器崩溃，能够在1秒左右迅速启动新的容器
- **弹性伸缩**：可以根据需要，自动对集群中正在运行的容器数量进行调整
- **服务发现**：服务可以通过自动发现的形式找到它所依赖的服务
- **负载均衡**：如果一个服务启动了多个容器，能够自动实现请求的负载均衡
- **版本回退**：如果发现新发布的程序版本有问题，可以立即回退到原来的版本
- **存储编排**：可以根据容器自身的需求自动创建存储卷



##### K8s 构成组件

一个完整的 **Kubernetes** 集群是由 **控制节点 master** 、**工作节点 node** 构成的，因此这种集群方式也分为 **一主多从** 和 **多主多从**，而每个节点上又会安装不同组件以提供服务。

###### **Master**

集群的控制平面，负责集群的决策（管理）。它旗下存在以下组件

- **ApiServer** ：资源操作的唯一入口，接收用户输入的命令，提供认证、授权、Api 注册和发现等机制
- **Scheduler**：负责集群资源调度，按照预定的调度策略将 **pod** 调度到相应的 **node** 节点上
- **ControllerManager**：负责维护集群的状态，比如程序部署安排，故障检测，自动扩展，滚动更新等
- **Etcd**：负责存储集群中各种资源对象的信息

###### Node

集群的数据平面，负责为容器提供运行环境（干活）。它旗下存在以下组件

- **Kubelet**：负责维护容器的生命周期，即通过控制 docker 来创建、更新、销毁容器
- **KubeProxy**：负责提供集群内部的服务发现和负载均衡
- **Docker**：负责节点上容器的各种操作









![](https://gitee.com/cbuc/picture/raw/master/typora/20210406225042.png)

![](https://gitee.com/cbuc/picture/raw/master/typora/20210406225104.png)

![](https://gitee.com/cbuc/picture/raw/master/typora/20210406225113.png)

![](https://gitee.com/cbuc/picture/raw/master/typora/20210406225127.png)

![](https://gitee.com/cbuc/picture/raw/master/typora/20210406225139.png)

![](https://gitee.com/cbuc/picture/raw/master/typora/20210406225148.png)

![](https://gitee.com/cbuc/picture/raw/master/typora/20210406225159.png)

![](https://gitee.com/cbuc/picture/raw/master/typora/20210406225209.png)

![](https://gitee.com/cbuc/picture/raw/master/typora/20210406225217.png)

![](https://gitee.com/cbuc/picture/raw/master/typora/20210406225227.png)

![](https://gitee.com/cbuc/picture/raw/master/typora/20210406225239.png)

![](https://gitee.com/cbuc/picture/raw/master/typora/20210406225255.png)

![](https://gitee.com/cbuc/picture/raw/master/typora/20210407001347.png)

![](https://gitee.com/cbuc/picture/raw/master/typora/20210407001347.png)

![](https://gitee.com/cbuc/picture/raw/master/typora/20210407001347.png)









![](https://gitee.com/cbuc/picture/raw/master/typora/20210407001347.png)

![](https://gitee.com/cbuc/picture/raw/master/typora/20210407001347.png)

![](https://gitee.com/cbuc/picture/raw/master/typora/20210407001347.png)

![](https://gitee.com/cbuc/picture/raw/master/typora/20210406224402.png)

![](https://gitee.com/cbuc/picture/raw/master/typora/20210406224544.png)

![](https://gitee.com/cbuc/picture/raw/master/typora/20210406224700.png)

![](https://gitee.com/cbuc/picture/raw/master/typora/20210406224745.png)

![](https://gitee.com/cbuc/picture/raw/master/typora/20210406224857.png)

![](https://gitee.com/cbuc/picture/raw/master/typora/20210406224917.png)





![](https://gitee.com/cbuc/picture/raw/master/typora/20210406231337.png)

![](https://gitee.com/cbuc/picture/raw/master/typora/20210406231544.png)





```shell
wget https://mirrors.aliyun.com/docker-ce/linux/centos/docker-ce.repo -O /etc/yum.repos.d/docker-ce.repo
```

![](https://gitee.com/cbuc/picture/raw/master/typora/20210406232018.png)

![](https://gitee.com/cbuc/picture/raw/master/typora/20210406232055.png)





kubectl 

![](https://gitee.com/cbuc/picture/raw/master/typora/20210406234940.png)







![](https://gitee.com/cbuc/picture/raw/master/typora/20210407000601.png)

![](https://gitee.com/cbuc/picture/raw/master/typora/20210407000758.png)