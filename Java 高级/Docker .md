**大家好**，我是小菜，一个渴望在互联网行业做到蔡不菜的小菜。可柔可刚，点赞则柔，白嫖则刚！
**死鬼~看完记得给我来个三连哦！**


![](https://user-gold-cdn.xitu.io/2020/4/11/17169c46045528af?w=240&h=224&f=jpeg&s=7529)


>本文主要介绍 `Docker 的基本使用`
>
>如有需要，可以参考
>
>如有帮助，不忘 **点赞** ❥
>
>
>微信公众号已开启，**小菜良记**，没关注的同学们记得关注哦！

## Docker 简介

> Docker 是一个开源的应用容器引擎，基于 [Go 语言](https://www.runoob.com/go/go-tutorial.html) 并遵从 Apache2.0 协议开源。
> Docker 可以让开发者打包他们的应用以及依赖包到一个轻量级、可移植的容器中，然后发布到任何流行的 Linux 机器上，也可以实现虚拟化。
> 容器是完全使用沙箱机制，相互之间不会有任何接口（类似 iPhone 的 app）,更重要的是容器性能开销极低。

### Docker 应用场景

- **Web** 应用的自动化打包和发布

- 自动化测试和持续集成、发布
- 在服务型环境中部署和调整数据库或其他的后台应用
- 从头编译或者扩展现有的 OpenShift 或 Cloud Foundry 平台来搭建自己的 PaaS 环境。

### Docker 的优点

- 快速、一致地交付应用程序
- 响应式部署和扩展
- 在同一硬件上运行更多的工作负载

### Docker 的三个核心

- **镜像（Image）**

是创建容器的基础，类似虚拟机的快照

- 容器

从镜像创建的运行实例，它可以被启动、停止和删除。每个容器之间是相互隔离，互不可见，保证了平台的安全性

- 仓库

集中保存镜像的地方

![](https://cbucbm.club/d59147ef)

上图便是 **Docker** 的 LOGO，也诠释了 **Docker** 的概念。

**Docker** 借鉴了集装箱的概念， **Docker** 便是用来运输软件和应用程序的。

### Docker 架构

![](https://cbucbm.club/257322a8)

- **Client**

**Docker** 客户端。是许多 **Docker** 用户与 **Docker** 交互的主要方式，**Docker** 客户端可以与多个守护进程进行通信。

- **Docker Daemon**

**Docker** 守护程序。负责侦听 **Docker API** 请求并管理 **Docker** 对象，如 图像、容器、网络和卷。守护程序还可以与其他守护程序通信以管理**Docker**服务。

- **Registry**

**Docker**注册表存储**Docker**镜像。**Docker Hub**是任何人都可以使用的公共注册中心。

### VM vs 容器

|  **特性**  |      **VM**      |     **容器**     |
| :--------: | :--------------: | :--------------: |
|  隔离级别  |    操作系统级    |      进程级      |
|  隔离策略  |    Hypervisor    |     CGroups      |
|  系统资源  |      5~15%       |       0~5%       |
|  启动时间  |      分钟级      |       秒级       |
|  镜像存储  |      GB~TB       |      KB~MB       |
|  集群规模  |       上百       |       上万       |
| 高可用策略 | 备份、容灾、迁移 | 弹性、负载、动态 |

- **容器：** 是一个应用层的抽象，用于将代码和依赖资源打包在一起，多个容器可以在同一台机器上运行，共享操作系统内核，但各自作为独立的进程在用户空间中运行 。与虚拟机相比， 容器占用的空间较少（容器镜像大小通常只有几十兆），瞬间就能完成启动。
- 虚拟机：是一个物理硬件层抽象，用于将一台服务器变成多台服务器。 管理程序允许多个VM在一台机器上运行。每个VM都包含一整套操作系统、一个或多个应用、必要的二进制文件和库资源，因此占用大量空间。

![](https://cbucbm.club/54a03d6c)

## Docker 上手

### 一、 镜像操作

#### 1. 列出镜像

**语句：** `docker images`

- **REPOSITORY**：表示镜像的仓库源
- **TAG**：镜像的标签
- **IMAGE ID**：镜像ID
- **CREATED**：镜像创建时间

- **SIZE**：镜像大小

#### 2. 查找镜像

**语句：** `docker search ${image_name}`

- **NAME**：镜像仓库源的名称
- **DESCRIPTION**：镜像的描述
- **starts**：用户评价，反应一个镜像的受欢迎程度
- **OFFICIAL**：是否docker官方发布
- **auto commit**：自动构建，表示该镜像由 *Docker Hub* 自动构建流程创建的

#### 3. 拉取镜像

**语句：** `docker pull ${image_name}:${image_version}`

#### 4. 删除镜像

- **删除单个：** 

  `docker rmi ${image_name} (or ${id})`

- **删除多个：**

   `docker rmi ${image_name}/${id} ${image_name}/${id} ...`

- **删除所有：**

   `docker rmi ` **\`docker images -q\`** 

#### 5. 查看镜像元数据

`docker inspect ${image_name}`

`docker inspect -f ='{{.NetworkSettings.IPAddress}}' ${image_name} `

- **-f**： 可用 **-format** 代替

### 二、容器操作

#### 1. 创建容器

 `docker run [option] --name=${name} image command [args...]`

**option 选项**：

- `-i`： 交互式容器
- `-t`：tty，终端
- `-d`：后台运行，并且打印出容器 **id**

**示例**：

`docker run -i -t -d --name=centOS1 centos /bin/bash`

**=**

`docker run -itd --name=centOS1 centos /bin/bash`

**注：** 创建的容器名称不能重复

#### 2. 进入容器

**方式1：**

`docker attach ${name}/${id}`

- 示例：

`docker attach centOS1`

**注：** 在容器内使用 **exit** 退出容器时，***容器会停止***

**方式2：**

`docker exec -it ${name}/${id} /bin/bash`

- 示例：

`docker exec -it centOS1 /bin/bash`
**注：** 在容器内使用 **exit** 退出容器时，***容器不会停止***

#### 3. 查看容器

- **查看正在运行的容器**

`docker ps`

- **查看历史运行过的容器**

`docker ps -a`

- **查看最后一次运行的容器**

`docker ps -l`

#### 4. 停止容器

`docker stop ${name}/${id}`

#### 5. 启动容器

`docker start ${name}/${id}`

#### 6. 删除容器

- **删除一个容器**

`docker rm ${name}/${id}`

- **删除多个容器**

`docker rm ${name1}/${id1}  ${name2}/${id2} ...`

- **删除多个容器**

`docker rm` **\` docker ps -a -q\`**

#### 7. 查看容器元数据

`docker inspect ${name}`

`docker inspect -f ='{{.NetworkSettings.IPAddress}}' ${name} `

- **-f**： 可用 **-format** 代替

#### 8. 查看容器日志

`docker logs ${name}/${id}`

#### 9. 文件拷贝

`docker cp 需要拷贝的文件或目录 容器名称:容器目录`

**示例：**
`docker cp 1.txt c2:/root`

#### 10. 目录挂载

目录挂载就是将宿主机的目录与容器内的努力进行映射，这样我们改变宿主机挂载目录下的内容时，容器内对应挂载目录里面的目录也会改变

**语句：** 使用 **-v** 进行挂载
`docker run ‐id ‐‐name=centOS1 ‐v /opt/:/usr/local centos`

如果权限不足，我们应当使用：

`docker run ‐id ‐‐privileged=true ‐‐name=c4 ‐v /opt/:/usr/local/myhtml centos`

### 三、镜像制作

我们不仅仅可以从 **Docker Hub** 上拉取镜像进行创建容器，我们还可以手动定制 **docker** 系统镜像，目前构建镜像的方式有两种：

- 使用 `docker commit` 命令
- 使用 `docker build` 配合 `Dockerfile` 文件

####  1. docker commit

##### 1）**制作镜像**

使用 `docker commit` 制作镜像前，我们需要一个正在运行的容器：

- **步骤1** 

我们需要先拉取一个 **centOS** 作为我们的基础镜像： `docker pull centos`

- **步骤2**

运行刚刚拉取到的镜像：`docker run -it --name=centOS1 centos:latest`

- **步骤3**

在容器中安装环境：

1. **tomcat**

上传 **tomcat** 安装包：

 `docker cp apache‐tomcat‐8.5.54.tar.gz centOS1:/root/`

安装 **tomcat**：

`tar ‐zxvf apache‐tomcat‐8.5.54.tar.gz ‐C /usr/local/`

编辑**tomcat** 下的 **/bin/setclsspath.sh** 文件，添加如下内容：

```she
export JAVA_HOME=/usr/local/jdk1.8.0_161
export JRE_HOME=/usr/local/jdk1.8.0_161/jre
```

2. **JDK**

上传 **JDK** 安装包：

 `docker cp jdk‐8u161‐linux‐x64.tar.gz centOS1:/root/`

安装 **JDK**：

`tar ‐zxvf jdk‐8u161‐linux‐x64.tar.gz ‐C /usr/local/`

编辑**/etc/profile** 文件，添加如下内容：

```she
JAVA_HOME=/usr/local/jdk1.8.0_161
export PATH=$JAVA_HOME/bin:$PATH
```

- 步骤4

将我们正在运行的容器提交为一个新的镜像

`docker commit centOS1 cbucImage`



##### 2）**端口映射**

- **步骤1**

启动容器：`docker run ‐itd ‐‐name=t1 ‐p 8888:8080 cbucImage /bin/bash`

- **步骤2**

运行tomcat：`docker exec t1 /usr/local/apache‐tomcat‐7.0.47/bin/startup.sh`

这样子我们就可以通过 `http://ip:port` 来访问页面了



##### 3）**镜像打包**

**打包镜像**：

`docker save ‐o /mnt/myImage.tar cbucImage`

**在其他服务器中使用镜像：**

`docker load -i myImage.tar`



##### 4）**容器打包**

打包容器：

`docker export ‐o /mnt/mycentos.tar centOS1`

导入容器：

`docker import mycentos.tar centOS2:latest`

#### 2. docker builder

**Dockerfile**使用基本的基于**DSL**语法的指令来构建一个Docker镜像，之后使用**docker**
**builder**命令基于该**Dockerfile**中的指令构建一个新的镜像

##### 1）**DSL 语法**

|   关键词   |           解释           |
| :--------: | :----------------------: |
|    FROM    |         基础镜像         |
| MAINTAINER |        维护者信息        |
|    RUN     |         安装软件         |
|    ADD     |  COPY 文件，会自动解压   |
|  WORKEDIR  |     cd 切换工作目录      |
|   VOLUME   |         目录挂载         |
|   EXPOSE   |       内部服务端口       |
|    CMD     | 执行 Dockerfile 中的命令 |
|    ENV     |       设置环境变量       |

**解析：**

- **1. FROM**

指定基础 **image**。必须指定且需要在**Dockerfile**其他指令的前面。后续的指令都依赖于该指令指定的**image**。**FROM**指令指定的基础image可以是官方远程仓库中的，也可以位于本地仓库。FROM命令告诉**docker**我们构建的镜像是以哪个(发行版)镜像为基础的。如果在同一个**Dockerfile**中创建多个镜像时，可以使用多个 **FROM** 指令。

**格式：** 

`FROM <image>` 或者 `FROM <image>:<tag>`

- **2. MAINTAINER**

指定镜像创建者信息。用于将**image**的制作者相关的信息写入到**image**中。当我们对该**image**执行**docker inspect**命令时，输出中有相应的字段记录该信息。

**格式：**

`MAINTAINER <name>`

- **3. RUN**

安装软件使用。可以运行任何被基础image**支持的命令**。如基础**image**选择了**ubuntu**，那么软
件管理部分只能使用**ubuntu**的命令。

**格式：**

`RUN <command>`

- **4. CMD**

设置 **container** 启动时执行的操作。该操作可以是执行自定义脚本，也可以是执行系统命令。该指令只能在文件中存在一次，如果有多个，则只执行最后一条。

**格式：**

`CMD command param1 param2`

- **5. ENTRYPOINT**

设置**container**启动时执行的操作，可以多次设置，但是只有最后一个有效。

**格式：**
`ENTRYPOINT command param1 param2`

**场景1：**

独自使用时，如果你还使用了**CMD**命令且**CMD**是一个完整的可执行的命令，那么**CMD**指令和**ENTRYPOINT**会互相覆盖，只有最后一个**CMD**或者**ENTRYPOINT**有效。

例： 这个时候只有 **ENTRYPOINT** 会执行

```shell
CMD ls -l
ENTRYPOINT ls ‐l
```

**场景2：**

和**CMD**指令配合使用来指定**ENTRYPOINT**的默认参数，这时**CMD**指令不是一个完整的可执行命令，仅仅是参数部分。**ENTRYPOINT**指令只能使用**JSON**方式指定执行命令，而不能指定参数。

例：

```shell
CMD ["‐l"]
ENTRYPOINT ["/usr/bin/ls"]
```

- **6. USER**

设置 **container** 容器的用户，默认是 **root** 用户

**格式：**

```shell
# 指定memcached的运行用户
ENTRYPOINT ["memcached"]
USER daemon
或者
ENTRYPOINT ["memcached", "‐u", "daemon"]
```

- **7. EXPOSE**

指定容器需要映射到宿主机器的端口。当你需要访问容器的时候，可以不是用容器的**IP**地址而是使用宿主机器的**IP**地址和映射后的端口。要完成整个操作需要两个步骤，首先在Dockerfile使用**EXPOSE**设置需要映射的容器端口，然后在运行容器的时候指定 **‐p** 选项加上**EXPOSE**设置的端口，这样**EXPOSE**设置的端口号会被随机映射成宿主机器中的一个端口号。也可以指定需要映射到宿主机器的那个端口，这时要确保宿主机器上的端口号没有被使用。**EXPOSE**指令可以一次设置多个端口号，相应的运行容器的时候，可以配套的多次使用 **‐p** 选项。

**格式：**

```shell
EXPOSE <port> [<port>...]
# 映射一个端口
EXPOSE port1
# 相应的运行容器使用的命令
docker run ‐p port1 image
# 映射多个端口
EXPOSE port1 port2 port3
# 相应的运行容器使用的命令
docker run ‐p port1 ‐p port2 ‐p port3 image
# 还可以指定需要映射到宿主机器上的某个端口号
docker run ‐p host_port1:port1 ‐p host_port2:port2 ‐p host_port3:port3 image
```

- **8. ENV**

用于设置环境变量。设置了后，后续的**RUN**命令都可以使用，**container**启动后，可以通过**docker inspect** 查看这个环境变量，也可以通过在**docker run ‐‐env key=value**时设置或修改环境变量。

**格式：**

```shell
ENV <key> <value>
# 假如你安装了JAVA程序，需要设置JAVA_HOME，那么可以在Dockerfile中这样写：
ENV JAVA_HOME /path/java/jdk
```

- **9. ADD**

从**src**复制文件到**container**的 **dest** 路径。主要用于将宿主机中的文件添加到镜像中。

**格式：**

```shell
# <src> 是相对被构建的源目录的相对路径，可以是文件或目录的路径，也可以是一个远程的文件url; <dest> 是container中的绝对路径
ADD <src> <dest>
```

- **10. VOLUMN**

指定挂载点。使容器中的一个目录具有持久化存储数据的功能，该目录可以被容器本身使用，
也可以共享给其他容器使用。

**格式：**

```shell
VOLUME ["<mountpoint>"]
# 例：VOLUME ["/tmp/data"]
```

运行通过该**Dockerfile**生成**image**的容器，**/tmp/data**目录中的数据在容器关闭后，里面的数据还存在。

- **11. WORKDIR**

切换目录，可以多次切换(相当于**cd**命令)，对**RUN，CMD，ENTRYPOINT**生效。

**格式：**

```shell
WORKDIR /path/to/workdir
# 在/p1, /p2下执行vim a.txt
WORKDIR /p1 WORKDIR p2 RUN vim a.txt
```

- **12. ONBUILD**

在子镜像中执行

**格式：**

```shell
# 指定的命令在构建镜像时并不执行，而是在它的子镜像中执行
ONBUILD <Dockerfile关键字>
```



##### 2）**创建镜像**

我们编辑好 **Dockerfile** 文件后，在 **Dockerfile** 所在目录输入指令：

```shell
docker build ‐t cbucImage:v1.0.0 ‐‐rm=true .
```

**注：**

- **‐t** 表示选择指定生成镜像的用户名，仓库名和tag
- **‐‐rm=true** 表示指定在生成镜像过程中删除中间产生的临时容器。

- 上面构建命令中最后的 **.** 符号不要漏了，表示使用当前目录下的**Dockerfile**构建镜像

#### 3. 运行镜像

我们创建好镜像后，便可以使用以下指令运行：

```shell
docker run ‐itd ‐‐name centos1 ‐p 8888:80 cbucImage /bin/bash
```

使用以下命令进入容器：

```shell
docker exec -it centos1 /bin/bash
```



**[END]**



以上便是 **Docker** 的大概知识点啦，看完试着将自己的项目打包成 **docker** 镜像练一练哦！路漫漫，小菜与你一同求索~ 

![看完不赞，都是坏蛋](https://imgconvert.csdnimg.cn/aHR0cHM6Ly93d3cuNTJkb3V0dS5jbi9zdGF0aWMvdGVtcC9waWMvOWJkNjhkMTUwZjA3ODdjNTYwYTQzOWRhMzU5YTU4MGEucG5n?x-oss-process=image/format,png#pic_center)
> 今天的你多努力一点，明天的你就能少说一句求人的话！
>
> *我是小菜，一个和你一起学习的男人。* `💋`
>
>
> 微信公众号已开启，**小菜良记**，没关注的同学们记得关注哦！