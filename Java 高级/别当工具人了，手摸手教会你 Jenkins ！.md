大家好，我是小菜，一个渴望在互联网行业做到蔡不菜的小菜。可柔可刚，点赞则柔，白嫖则刚！
**死鬼~看完记得给我来个三连哦！**


![](https://user-gold-cdn.xitu.io/2020/4/11/17169c46045528af?w=240&h=224&f=jpeg&s=7529)


>本文主要介绍 `Jenkins`
>
>如有需要，可以参考
>
>如有帮助，不忘 **点赞** ❥
>
>
>微信公众号已开启，**小菜良记**，没关注的同学们记得关注哦！

“唉，每天提交完代码都得自己打包再部署到测试环境和开发环境，好麻烦啊！都快变成运维了”

“啊？哦！确实，每天打包部署确实都成为了工具人了”

一段简白的对话快速的隐灭在办公室中，却引发了我的思考，“这么麻烦的过程肯定已经有了很好的解决方案，毕竟程序员是`面向懒惰编程`，自己对 **Jenkins** 这个工具有所耳闻已经很久了，看来今天得对它`下手`了”

说干就干，今天咱们就来求索一下 **JenKins**，看完你不妨也给你们项目整一个，给自己多增加点`划水`的时间！

![](https://cbuc.top/1607594479667.gif)

> **读前须知：** 本文较长，从安装到使用，一步步带你超神！
>
> **微信公众号关注：** `[ 小菜良记 ]` ，带你领略技术风骚！

## 一、Jenkins 是什么

> Jenkins是一个`开源软件`项目，是基于`java`开发的一种`持续集成`工具，用于监控持续重复的工作，旨在提供一个开放易用的软件平台，使软件的持续集成变成可能。

简单来说，它就是一个 **持续集成** 的工具！

### 1. 持续集成

**持续集成**（Continuous Integration），简称 **CI**。频繁地将代码集成到主干之前，必须通过自动化测试，只要有一个测试用例失败，就不能集成。通过持续集成，团队可以快速从一个功能到另外一个功能。

![](https://cbuc.top/1607063047968.png)

**好处：**

- 降低风险，由于持续集成不断去构建，编译和测试，可以很早发现问题
- 减少重复性的工作
- 持续部署，提供可部署单元包
- 持续交付可供使用的版本

### 2. Jenkins 持续集成

![](https://cbuc.top/1607065613065.png)

我们先通过这张图来看到 **Jenkins** 在其中起到的作用：

- 首先，开发人员将代码提交到 **Git** 仓库
- 然后 **Jenkins** 使用 **Git** 插件来拉取 **Git** 仓库的代码，然后配合 **JDK、Maven** 等软件完成代码编译，测试、审查、、测试和打包等工作
- 最后 **Jenkins** 将生成的 **jar/war** 推送到 **测试/生产 服务器** ，供用户访问

整套步骤下来，作为开发人员我们只需要提交下代码，剩下的工作都交给了 **Jenkins** ，真是美滋滋，怎么没有早点上这个工具的车！

## 二、Jenkins 安装

磨刀不误砍柴工，没刀的情况下说再多都是虚的。我们就先来看下 **Jenkins** 是如何安装的吧！

### 1. 安装JDK

因为 **Jenkins** 是 **java** 写的，所以要运行起来必须要配置 **java** 运行环境。这里就不赘诉 JDK 的安装过程了

### 2. 下载安装 Jenkins

- **下载**

我们可以进入下载页面选择我们要安装的版本：[下载地址](https://www.jenkins.io/zh/download/)， 我们这里使用的版本是 ：`jenkins-2.190.3-1.1.noarch.rpm`

- **安装**

然后把下载好的 **rpm** 包上传到我们的服务器，通过 `rpm -ivh jenkins-2.190.3-1.1.noarch.rpm` 进行安装，然后编辑 `etc` 目录下的 **jenkins** 配置文件：`vim /etc/sysconfig/jenkins`，需要改的地方如下（也可以选择不改）：

```properties
JENKINS_USER="root"
JENKINS_PORT="8888"
```

- **启动**

`systemctl start jenkins`

- **访问**

通过浏览器访问 `http://服务器IP:8888/`，看到以下页面说明启动成功了

![](https://cbuc.top/1607067896468.png)

然后我们在服务器上从指定文件中获取密码，进行下一步。

这一步我们可以先跳过插件安装，因为**Jenkins**插件需要连接默认官网下载，速度非常慢：

![](https://cbuc.top/1607070823549.png)

然后我们添加一个管理员账号来管理：

![](https://cbuc.top/1607070841631.png)

看到以下页面就说明设置成功了：

![](https://cbuc.top/1607264518370.png)

> 微信公众号关注：**小菜良记** ，带你领略技术风骚！

## 三、Jenkins 使用

### 1. 插件加速

> 工欲善其事，必先利其器

贴心的小菜是不会让你遭受等待的痛苦的，首先我们进入 `Jenkins -> Manage Jenkins -> Manage Plugins` ，点击 `install` 

![](https://cbuc.top/1607653359244.png)

然后我们在安装 **Jenkins** 的服务器上进入 `/var/lib/jenkins/updates` 目录，可以看到有个 `default.json` 文件，**第一步：**我们需要替换里面的部分字段，输入命令如下：

```shell
sudo sed -i 's#updates.jenkins.io/download/plugins#mirrors.tuna.tsinghua.edu.cn/jenkins/plugins#g' default.json && sudo sed -i 's#www.google.com#www.baidu.com#g' default.json
```

**第二步：**我们进入到 `/var/lib/jenkins`目录，编辑 ` hudson.model.UpdateCenter.xm`，将里面的 `https://updates.jenkins.io/update-center.json`修改为 `http://mirrors.tuna.tsinghua.edu.cn/jenkins/updates/update-center.json`

**最后一步：** 输入以下命令进行重启 **Jenkins** ：

```shell
systemctl restart jenkins
```

通过以上步骤，我们就可以愉快的安装插件了！

### 2. 用户管理

在 **Jenkins** 中我们也可以进行用户权限管理，这个时候我们需要借助插件 `Role-based Authorization Strategy` 

- **首先安装 `Role-based Authorization Strategy` 插件**

![](https://cbuc.top/1607087167914.png)

- **开启全局安全配置**

![](https://cbuc.top/1607265662959.png)

将授权策略切换为 `"Role-Based Strategy"`

![](https://cbuc.top/1607266045131.png)



- **创建用户**

更改完授权策略，我们就可以来创建用户了，进入系统管理页面中的`Manage Users`

![](https://cbuc.top/1607266133097.png)

这里我们创建了两个用户，分别是 `cbuc1` 和 `cbuc2`

![](https://cbuc.top/1607267870545.png)

- **创建角色**

创建好用户，我们就可以来创建角色了，在系统管理页面进入 `Manage and Assign Roles`

![](https://cbuc.top/1607267910157.png)

角色主要分为 **Global roles（全局角色）** 和 **Item roles（项目角色）**

**Global roles（全局角色）：** 管理员等高级用户可以创建基于全局的角色

**Item roles（项目角色）：** 针对某个或者某些项目的角色

![](https://cbuc.top/1607267910157.png)

我们系统现在已经存在了两个用户，然后我们就可以给这两个用户绑定对应的角色

![](https://cbuc.top/1607268164551.png)

### 3. 凭证管理

什么是凭证呢？ **凭证** 可以用来存储需要密文保护的数据库密码，**GitLab** 密码信息，**Docker** 私有仓库的登录密码。保存了这些信息后，**Jenkins** 就可以和这些第三方的应用进行交互。当然，这还是得借助 **Jenkins** 的插件！

#### 1）安装

首先安装 `Credentials Binding` 插件

![](https://cbuc.top/1607088143834.png)

安装好插件后，在系统首要的菜单栏中就会多了个 **凭证** 菜单

![](https://cbuc.top/1607266556053.png)

点击进去，我们可以看到可以添加的凭证有 **5** 种：

![](https://cbuc.top/1607088287034.png)

1. **Username with password** ：用户名和密码
2. **SSH Username with private key：** 使用 **SSH** 用户和密钥
3. **Secret file：** 需要保密的文本文件，使用时 **Jenkins** 会将文件复制到一个临时目录中，再将文件路径设置到一个变量中，等构建结束后，所复制的 **Secret file** 就会被删除
4. **Secret text：** 需要保存的一个加密的文本串，如钉钉机器人或 **GitHub** 的 **api token**
5. **Certificate：** 通过上传证书文件的方式

我们平时比较常用的类型为：`Username with password` 和 `SSH Username with private key`

#### 2）Git 凭证管理

我们如果要使用 **Jenkins** 从 **GitLab** 拉取项目代码，我们就得使用凭证来验证。

- 安装 **Git** 插件

我们需要在 **Jenkins** 中安装 **Git插件** 来拉取项目代码

![](https://cbuc.top/1607266887281.png)

然后我们在服务器上也需要安装 **Git 工具**：

```shell
# 安装命令
yum install git -y
# 验证命令
git --version
```

##### 1. 方式1：用户密码类型

我们可以使用 **用户密码** 登录后拉取项目代码，这个时候我们需要用到 **凭证的 Username with password 类型**：

![](https://cbuc.top/1607266676993.png)

![](https://cbuc.top/1607266697804.png)

创建成功我们就可以测试是否可用，我们先创建一个 **FreeStyle 项目**

![](https://cbuc.top/1607306912845.png)

然后在 **GitLab** 中复制我们项目的 **URL**

![](https://cbuc.top/1607594017275.png)

在 **Credentials** 中选择我们刚刚创建的凭证，保存配置后，我们点击 **Build Now** 来构建项目：

![](https://cbuc.top/1607307063370.png)

这个时候在控制台可以看到输出

![](https://cbuc.top/1607307086430.png)

然后在进入服务器的 `/var/lib/jenkins/workspace` 目录中看到我们拉取的项目：

![](https://cbuc.top/1607307212593.png)

说明我们已经成功使用 **用户密码** 凭证模式拉取到 **Git项目了**

##### 2. 方式2：SSH密钥类型

除了用账号密码方式来验证 **Git** ，我们还可以用 **SSH密钥** 来验证，步骤流程如下：

![](https://cbuc.top/1607308765334.png)

从图上我们可以得知，第一步需要生成 **公私钥**，我们在 **Jenkins服务器** 上输入以下指令生成：

`ssh-keygen -t rsa` 输入指令后，一路回车，便可在 `/root/.ssh/` 目录下生成公私钥：

 ![](https://cbuc.top/1607307581897.png)

- **id_rsa**：私钥文件
- **id_rsa.pub**：公钥文件

然后我们把生成的公钥放在 **GitLab** 中，`root账户登录->点击头像->Settings->SSH Keys`，复制 **id_rsa.pub** 中的内容，点击 **"Add key"**

![](https://cbuc.top/1607307814247.png)

然后我们再回到 **Jenkins** 系统页面中添加凭证，选择 `SSH Username with private key` ，把刚刚生成的私有文件内容复制过来

![](https://cbuc.top/1607307983449.png)

添加后就会生成一条凭证

![](https://cbuc.top/1607308020593.png)

创建成功我们就可以测试是否可用，我们先创建一个 **FreeStyle 项目**

![](https://cbuc.top/1607308070922.png)

然后在 **GitLab** 中复制我们项目的 **URL**

![](https://cbuc.top/1607308410091.png)

在 **Credentials** 中选择我们刚刚创建的凭证，保存配置后，我们点击 **Build Now** 来构建项目：

![](https://cbuc.top/1607308489735.png)

这个时候在控制台可以看到输出

![](https://cbuc.top/1607308526228.png)

然后在进入服务器的 `/var/lib/jenkins/workspace` 目录中看到我们拉取的项目：

![](https://cbuc.top/1607308613232.png)

说明我们已经成功使用 **SSH Username with private key** 凭证模式拉取到 **Git项目了**

### 4. 项目管理

#### 1）Maven 安装

我们现在开发中的项目大部分都是 **Maven** 项目，使用 **Maven** 项目，我们就需要进行 **依赖管理**，因此我们应当在服务器上安装 **Maven** 来下载项目依赖。

- **安装 Maven**

我们可以从 **Maven** 官网上下载压缩包，然后上传到服务器上进行解压

`tar -xzf apache-maven-3.6.0-bin.tar.gz`

- **配置环境变量**

`vim /etc/profile`

```shell
export JAVA_HOME=/usr/lib/jvm/java-1.8.0-openjdk
export MAVEN_HOME=/home/maven/apache-maven-3.6.2
export PATH=$PATH:$JAVA_HOME/bin:$MAVEN_HOME/bin
```

编辑后使配置文件生效：

```shell
source /etc/profile
```

验证：

```shell
mvn -v
```

然后设置 **Maven** 的 `settings.xml`

```shell
# 创建本地仓库目录
mkdir /data/localRepo
vim /home/maven/apache-maven-3.6.2/conf/settings.xml
```

将本地仓库改为： `/root/repo/`

添加阿里云私服地址：`alimaven aliyun maven http://maven.aliyun.com/nexus/content/groups/public/ central`

- **Jenkins配置**

在 **Jenkins** 我们也需要配置 **JDK** 和 **Maven** 的关联.

进入 `Jenkins -> Global Tool Configuration -> JDK`

![](https://cbuc.top/1607593766261.png)

进入 `Jenkins -> Global Tool Configuration -> Maven`

![](https://cbuc.top/1607309560875.png)

**添加全局变量**

进入`Manage Jenkins->Configure System->Global Properties`，添加三个全局变量

**JAVA_HOME、M2_HOME、PATH+EXTRA**

![](https://cbuc.top/1607311536652.png)

然后我们进入项目中点击 **configure**

![](https://cbuc.top/1607311591261.png)

然后添加 **shell** 执行脚本：

![](https://cbuc.top/1607311622735.png)

保存后重新构建，查看控制台，可以看到 **mvn** 构建成功：

![](https://cbuc.top/1607311700829.png)

#### 2）war 包部署

如果我们的项目是打成 war 包的形式，那么我们需要借助 **tomcat** 容器来运行，那么我们首先便是要先安装一个 **tomcat**

#####  Tomcat 安装

我们将事先下载好的 **Tomcat** 安装包上传到服务器上，通过 `tar -xzf apache-tomcat-8.5.47.tar.gz` 解压，然后运行 `bin`目录下的 `start.sh`启动 **Tomcat** ，看到以下结果则说明启动成功：

![](https://cbuc.top/1607312864719.png)

下一步我们需要配置Tomcat用户角色权限，默认情况下Tomcat是没有配置用户角色权限的 

首先我们需要修改 `tomcat/conf/tomcat-users.xml` 文件：

![](https://cbuc.top/1607317198007.png)

（复制）内容如下：

```xml
<role rolename="tomcat"/>
<role rolename="role1"/>
<role rolename="manager-script"/>
<role rolename="manager-gui"/>
<role rolename="manager-status"/>
<role rolename="admin-gui"/>
<role rolename="admin-script"/>
<user username="tomcat" password="tomcat" roles="manager-gui,manager-script,tomcat,admin-gui,admin-script"/>
```

然后修改 `/tomcat/webapps/manager/META-INF/context.xml` 文件，将以下内容注释：

![](https://cbuc.top/1607317386575.png)

然后进入**tomcat** 页面，点击进入：

![](https://cbuc.top/1607312972616.png)

账号密码都是 **tomcat**

![](https://cbuc.top/1607317434159.png)

成功页面如下：

![](https://cbuc.top/1607317499447.png)

这样子我们就完成了 **tomcat** 的安装，然后接下来就可以进行部署了

##### Tomcat 部署

- 在 **jenkins** 中安装 `Deploy to container` 插件
- 添加 **Tomcat** 凭证

![](https://cbuc.top/1607318181089.png)

- 构建配置

在项目的 **configure** 中配置

![](https://cbuc.top/1607318417492.png)

然后点击构建，查看控制台输出：

![](https://cbuc.top/1607318939149.png)

显示已经部署成功，然后访问项目页面，可以看到 **war** 包项目部署成功：

![](https://cbuc.top/1607318964360.png)

#### 3）jar 包部署

上面说完了 **war** 包项目是如何部署的，但是我们现在项目用到比较多的还是 **SpringBoot** ，这个时候打出来的是 **jar** 类型，但是 **SpringBoot** 里面内置了 **tomcat** 容器，这样子我们就不需要借助外部 **tomcat** 容器的使用了。

![](https://cbuc.top/1607430640484.png)

- 首先我们在 **Jenkins** 中下载 **Maven** 插件，这个时候新建项目的时候会有个 **Maven** 项目的选项

![](https://cbuc.top/1607419258237.png)

然后在项目的 **configure** 中作如下配置：

![](https://cbuc.top/1607419613740.png)

>**Repository URL**：库地址
>**Credentials**：凭证
>**Branch Specifier (blank for ‘any’)**：分支

![](https://cbuc.top/1607421585005.png)

> **Run only if build succeeds**：在构建成功时执行后续步骤
> **Add post-build step**：添加构建后的步骤
> **Send files or execute commands over SSH**：通过ssh发送文件或执行命令

- 安装 `Publish Over SSH` 插件

因为我们要部署的服务器与 **Jenkins** 不在同一个服务器上，所以我们需要这个插件来远程部署

安装好插件后我们需要先配置远程服务器，在 **Jenkins** 服务器上输入 `ssh-copy-id 远程服务器IP` 将公钥拷贝到远程服务器上，然后在 **Jenkins** 系统配置中添加服务器信息，如下：

![](https://cbuc.top/1607429559421.png)

完成以上步骤后，我们就可以回到项目的 **configure** 中添加我们刚刚配置的服务器信息：

![](https://cbuc.top/1607419855828.png)

>**Name**：SSH Servers中配置的服务器
>**Source files**：源文件
>**Remove prefix**：删除前缀
>**Remote directory**：上传到服务器的目录
>**Exec command**：执行的脚本

完成以上步骤，我们就可以愉快的点击 **Build Now** 了！

![](https://cbuc.top/1607481783254.png)

#### 4）流水线项目

**Jenkins** 中自动构建项目的类型有很多，常用的有以下三种：

- **自由风格软件项目（FreeStyle Project）**

- **Maven 项目（Maven Project）**

- **流水线项目（Pipeline Project）**

每种类型的构建其实都可以完成一样的构建过程与结果，只是在操作方式、灵活度等方面有所区别，其中流水线类型灵活度比较高，其他两种类型我们在上面的例子中都已经尝试过了，下面我们就来介绍如何构建流水线项目。

##### 1. 概念

**Pipeline** 就是一套运行在 **Jenkins** 上的工作流框架，将原来独立运行与单个或者多个节点的任务连接起来，实现单个任务难以完成的复杂流程编排和可视化工作

##### 2. 优点

- **代码**：**Pipeline** 以代码的形式实现，通常被检入源代码控制，使团队能够编辑，审查和迭代其传送流程。
- **持久性：** 无论是计划内的还是计划外的服务器重启，**Pipeline** 都是可恢复的
- **可停止：** **Pipeline** 可接收交互式输入，以确定是否继续执行 **Pipeline**
- **多功能：** **Pipeline** 支持现实世界中复杂的持续交付要求，它支持 **fork/join** 、循环执行、并行执行任务的功能
- **可扩展：** **Pipeline** 插件支持其 **DSL** 的自定义扩展，以及与其他插件集成的多个选项

##### 3. 创建

创建 **Pipeline** 项目之前我们需要安装 **Pipeline** 插件：

![](https://cbuc.top/1607476590066.png)

然后在创建项目的时候便会多了 **Pipeline** 类型：

![](https://cbuc.top/1607476661542.png)

选择好项目类型之后我们就可以在项目中的 **configure** 进行配置了：

- 首先老样子配置好 **git** 地址，跟上面一样，这里不多作赘诉
- 然后配置 **Pipeline** 脚本

**Pipeline** 项目是统一通过 **Pipeline** 脚本来管理，这样也更好的提高灵活性

`Hello World` 模板：

```java
pipeline {
    agent any
    stages {
        stage('Hello') {
            steps {
                echo 'Hello World'
            }
        }
    }
}
```

> **stages：** 代表整个流水线的所有执行阶段，通常 **stages** 只有1个，里面包含多个 stage
>
> **stage：** 代表一个阶段内需要执行的逻辑，**steps** 里面是 **shell** 脚本，**git** 拉取代码，**ssh** 远程发布等任意内容

`声明式 Pipeline` 模板：

```java
pipeline {
    agent any
    stages {
        stage('拉取代码') {
            steps {
                echo '拉取代码'
            }
        }
        stage('编译构建') {
            steps {
                echo '编译构建'
            }
        }
        stage('项目部署') {
            steps {
                echo '项目部署'
            }
        }
    }
}
```

你也完全不用担心不会书写 **Pipeline** 脚本，我们可以点击 `[Pipeline Syntax]` 跳转到 **Pipeline 代码生成页面**

![](https://cbuc.top/1607490697891.png)

![](https://cbuc.top/1607490860500.png)

书写好脚本后点击构建，可以看到整个构建过程：

![](https://cbuc.top/1607575072554.png)

如果我们需要部署到不同环境，比如生产环境和开发环境，我们还可以在项目的 **configure** 中进行配置：

- 首先需要安装 **Extended Choice Parameter** 插件
- 然后在配置中添加 **Extended Choice Parameter** 参数

![](https://cbuc.top/1607575370700.png)

完成以上配置后，点击保存，这个时候我们就可以在构建的时候选择需要部署的服务器了

![](https://cbuc.top/1607575461956.png)

然后我们就可以从 **Pipeline** 脚本中读取我们选择的参数，贴上该项目的构建脚本，如下：

![](https://cbuc.top/1607575755033.png)

```shell
node {
    //git凭证ID
    def git_auth = "7fdb3fa3-74eb-4862-b36f-c03701f71250"
    //git的url地址
    def git_url = "git@192.168.100.131:cbuc_group/cbuc_web.git"
    //获取当前选择的服务器名称
    def selectedServers = "${publish_server}".split(",")

    stage('开始拉取代码') {
        checkout([$class: 'GitSCM', branches: [[name: '*/v3.0']], doGenerateSubmoduleConfigurations: false, extensions: [], submoduleCfg: [], userRemoteConfigs: [[credentialsId: git_auth, url: git_url]]])
    }
    stage('开始打包') {
        sh "mvn -Dmaven.test.skip=true clean package"
    }
    stage('开始远程部署') {
        //遍历所有服务器，分别部署
        for(int j=0;j<selectedServers.length;j++){
            //获取当前遍历的服务器名称
            def currentServerName = selectedServers[j]
            //生产环境部署目录
            def pro_address = "/home/pro/java"
            //开发环境部署目录
            def dev_address = "/home/dev/java"

            //根据不同的profile来部署服务器
            if(currentServerName=="pro"){
                sshPublisher(publishers: [sshPublisherDesc(configName: 'pro_server', transfers: [sshTransfer(cleanRemote: false, excludes: '', execCommand: 'sh build.sh', execTimeout: 120000, flatten: false, makeEmptyDirs: false, noDefaultExcludes: false, patternSeparator: '[, ]+', remoteDirectory: pro_address, remoteDirectorySDF: false, removePrefix: 'target', sourceFiles: 'target/cbuc_web-0.0.1-SNAPSHOT.jar')], usePromotionTimestamp: false, useWorkspaceInPromotion: false, verbose: false)])
            }else if(currentServerName=="dev"){
                sshPublisher(publishers: [sshPublisherDesc(configName: 'dev_server', transfers: [sshTransfer(cleanRemote: false, excludes: '', execCommand: "sh build.sh", execTimeout: 120000, flatten: false, makeEmptyDirs: false, noDefaultExcludes: false, patternSeparator: '[, ]+', remoteDirectory: dev_address, remoteDirectorySDF: false, removePrefix: 'target', sourceFiles: 'target/cbuc_web-0.0.1-SNAPSHOT.jar')], usePromotionTimestamp: false, useWorkspaceInPromotion: false, verbose: false)])
            }
        }
    }
}
```

还有一种情况就是如果部署 **Jenkins** 的服务器宕机了，这个时候就会丢失 **Pipeline** 脚本文件，重新书写是一件很麻烦的事情，那么我们就可以将脚本文件放到我们的项目的根目录下，然后在 **configure** 中配置 **Pipeline** 脚本文件的位置：

![](https://cbuc.top/1607576171799.png)

![](https://cbuc.top/1607576845509.png)

然后我们点击构建，可以看到结果也是成功的：

![](https://cbuc.top/1607576884664.png)

#### 5）构建触发器

上面我们讲完了几种项目的构建方式，其中都是通过手动点击构建进行构建的，我们也可以通过触发器来构建

![](https://cbuc.top/1607577021134.png)

常用的有：

##### 1. Build After Other Projects Are Built

![](https://cbuc.top/1607577810259.png)

其他工程构建后触发。在选项中填写我们关注的项目，其中也支持3个选择以供选择：

> **Trigger only if build is stable：** 仅在项目稳定构建时执行
>
> **Trigger even if the build is unstable：** 即使项目构建不稳定也执行
>
> **Trigger even if the build fails：** 即使项目构建失败也执行

##### 2. Build Periodically 

![](https://cbuc.top/1607578099791.png)

定时构建。语法类型如 **cron** 表达式，定时字符串从左往右分别为： **分 时 日 月 周**

##### 3. Poll SCM

 轮询 SCM。指定时间扫描本地代码仓库的代码是否有变更，如果代码有变更就触发项目构建。

![](https://cbuc.top/1607578220076.png)

##### 4. Trigger builds remotely

![](https://cbuc.top/1607578345486.png)

远程触发构建。通过使用我们定义的密钥，然后访问构建地址：`http://192.168.100.131:8888/job/test01/build?token=123123`

##### 5. 自动触发构建

刚才我们看到在**Jenkins**的内置构建触发器中，**轮询SCM**可以实现**Gitlab**代码更新，项目自动构建，但是该方案的性能不佳。那有没有更好的方案呢？ 有的。就是利用**Gitlab**的**webhook**实现代码**push**到仓库，立即触发项目自动构建。

![](https://cbuc.top/1607587885407.png)

完成自动触发构建我们需要在 **Jenkins** 安装插件：`GitLab Hook` 和 `GitLab`

![](https://cbuc.top/1607590193712.png)

然后我们在 **Build Trigger** 中就可以看到多了一个选项：

![](https://cbuc.top/1607590434659.png)

复制这串 **WebHook** 地址，跟着到 **GitLab** 页面进行设置：

路径步骤：`Admin Area -> Settings -> Network`

![](https://cbuc.top/1607591771899.png)

然后我们在对应的项目中进行设置：

![](https://cbuc.top/1607592410163.png)

最后再回到 **Jenkins** 页面中做以下配置：`Manage Jenkins->Configure System`

![](https://cbuc.top/1607593556818.png)

做完以上配置，我们就可以愉快的代码进行自动触发构建了！



**END**

这篇文章较长，都是满满的干货，从安装到使用，一步步带你入 `运维` 的坑，学完这篇快给你的项目用上吧！路漫漫，小菜与你一同求索！

![看完不赞，都是坏蛋](https://imgconvert.csdnimg.cn/aHR0cHM6Ly93d3cuNTJkb3V0dS5jbi9zdGF0aWMvdGVtcC9waWMvOWJkNjhkMTUwZjA3ODdjNTYwYTQzOWRhMzU5YTU4MGEucG5n?x-oss-process=image/format,png#pic_center)
> 今天的你多努力一点，明天的你就能少说一句求人的话！
>
> *我是小菜，一个和你一起学习的男人。* `💋`
>
>
> 微信公众号已开启，**小菜良记**，没关注的同学们记得关注哦！