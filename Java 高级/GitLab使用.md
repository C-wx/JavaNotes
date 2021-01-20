大家好，我是小菜，一个渴望在互联网行业做到蔡不菜的小菜。可柔可刚，点赞则柔，白嫖则刚！
**死鬼~看完记得给我来个三连哦！**


![](https://user-gold-cdn.xitu.io/2020/4/11/17169c46045528af?w=240&h=224&f=jpeg&s=7529)


>本文主要介绍 ``
>
>如有需要，可以参考
>
>如有帮助，不忘 **点赞** ❥
>
>
>微信公众号已开启，**小菜良记**，没关注的同学们记得关注哦！

你可能对 **Gitlab** 有点陌生，听过说 **GitHub** ，也听说过 **Gitee** 。那 **Gitlab** 又是啥东西。

## 一、Gitlab 介绍

![](https://cbuc.top/1607073970023.jpg)

GitLab 是一个用于仓库管理系统的开源项目，使用[Git](https://baike.baidu.com/item/Git)作为代码管理工具，并在此基础上搭建起来的web服务。

相比较 **GitHub** ，它的特色在于：

1. 允许免费设置仓库权限
2. 允许用户选择分享一个 **project** 的部分代码
3. 允许用户设置 **project** 的获取权限，进一步提升安全性
4. 可以设置获取团队整体的改进进度
5. 通过 **innersourcing** 让不在权限范围内的人访问不到该资源

**GitLab** 和 **GitHub** 一样都是属于第三方基于 **Git** 开发的作品，免费且开源（基于 MIT 协议），与 **GitHub**类似，可以注册用户，任意提交你的代码，添加 **SSHKey**  等。不同的是， **GitLab 是可以部署到自己的服务器上，数据库等一切信息都掌握在自己手上，适合团队内部协作开发** 

## 二、GitLab 安装

#### 1 . 安装相关依赖

`yum -y install policycoreutils openssh-server openssh-clients postfix`

#### 2. SSH 设置

启动 **SSH** 服务并设为开机启动

`systemctl enable sshd && sudo systemctl start sshd`

#### 3. postfix 设置

设置**postfix**开机自启，**postfix**可以支持 **GitLab** 发信功能

`systemctl enable postfix && systemctl start postfix`

#### 4. 防火墙设置

如果已经关闭了防火墙，则不用以下操作

`firewall-cmd --add-service=ssh --permanent`

`firewall-cmd --add-service=http --permanent`

`firewall-cmd --reload`

#### 5. 下载

`wget https://mirrors.tuna.tsinghua.edu.cn/gitlab-ce/yum/el6/gitlab-ce-12.4.2-ce.0.el6.x
86_64.rpm`

#### 6. 安装

`rpm -i gitlab-ce-12.4.2-ce.0.el6.x86_64.rpm`

#### 7. 修改 GitLab 配置

`vim /etc/gitlab/gitlab.rb`

**GitLab** 的默认端口是 80，我们可以将其改为 **82** ，修改部分如下：

```properties
external_url 'http://服务器IP:82'
nginx['listen_port'] = 82
```

#### 8.  重启

通过 `gitlab-ctl reconfigure` 重新加载配置，再通过 `gitlab-ctl restart` 重启 **GitLab** 服务

#### 9. 开放端口

如果已经关闭了防火墙，则不用以下操作

`firewall-cmd --zone=public --add-port=82/tcp --permanent`

`firewall-cmd --reload`

#### 10. 访问

通过浏览器访问 `http://服务器IP:82/`，登录后成功访问页面如下：

![](https://cbuc.top/1607663196420.png)

## 三、GitLab 使用

### 1. 组管理

使用管理员 **root** 账号创建 **组** ，一个组里面可以有多个不同的项目分支，可以将开发人员添加到组里面设置权限，不同的组就是公司不同的开发项目或者服务模块，不同的组添加不同的开发人员即可实现对开发人员设置权限的管理。

具体步骤如下：

![](https://cbuc.top/1607663559116.png)

![](https://cbuc.top/1607663728910.png)

点击`Create group`，我们就成功创建了一个组，便可以在当前组下添加上项目了

![](https://cbuc.top/1607663902337.png)

以上我们说到可以对开发人员进行权限设置，我们可以点击 `Members` 来到成员管理页面

![](https://cbuc.top/1607664000605.png)

我们可以添加相关的开发人员到该组中，这样子被添加进该组的成员拥有对该组下所有项目的访问权限，当然，没有被添加进来的成员是访问不到该项目的！

这个时候我们可以看到，下拉框中只有一个 **root** 账户，为了不让你成为 `"光杆司令"`，你可以进行添加用户

### 2.用户管理

跟着小菜的步骤，顺次点击：`Admin Area -> Overview -> Users -> New user`

![](https://cbuc.top/1607664500685.png)

![](https://cbuc.top/1607673451155.png)

填写必要的参数，就可以创建用户了，其中 `Access level` 等级如下：

> **Regular：** 普通用户，只能访问属于他的组和项目
>
> **Admin：** 管理员，可以访问所有组和项目

创建好用户之后，我们需要点击 **Edit** 进行密码修改：

![](https://cbuc.top/1607674513352.png)

![](https://cbuc.top/1607674648887.png)

然后我们就可以进行登录了，也可以使用管理员账号将该用户归属到对应 **项目组** 下

![](https://cbuc.top/1607674779468.png)

其中我们可以赋予该用户不同的权限，权限一共有 **5** 种：

> **Guest：** 可以创建 issue，发表评论，不能读写版本库 **Report**，可以克隆代码，不能提交。**QA**、**PM** 可以赋予这个权限
>
> **Developer：** 可以克隆代码，开发，提交，push。普通开发人员可以赋予这个权限
>
> **Maintainer：** 可以创建项目，添加tag，保护分支，添加项目成员，编辑项目。核心开发人员可以赋予这个权限
>
> **Owner：** 可以设置项目访问权限，删除项目，迁移项目，管理组成员。开发组负责人可以赋予这个权限

### 3. 项目管理

一个组里面可以有多个项目，创建如下：

![](https://cbuc.top/1607675257545.png)

填写好项目名和访问权限就可以点击创建了，成功创建如下：

![](https://cbuc.top/1607675281003.png)

创建好项目，我们就可以将本地项目源码上传到 **GitLab** 仓库中

#### 1）开启版本控制

首先我们用 **Idea** 开发工具打开项目，在 **Idea** 工具栏，依次点击 `VCS -> 启用版本控制合并（Enable Version Control Integration）-> 选择 Git -> 点击确定`

![](https://cbuc.top/1607675650449.png)

![](https://cbuc.top/1607675705919.png)

#### 2）提交代码到本地仓库

`右键点击项目 -> Git -> 提交目录（Commit Directory）`

![](https://cbuc.top/1607676053328.png)

![](https://cbuc.top/1607676217432.png)

#### 3）推送到远程仓库

![](https://cbuc.top/1607676384877.png)

点击推送后会出现下面这个弹窗，我们需要配置远程项目的仓库地址，然后点击确定

![](https://cbuc.top/1607676404818.png)

![](https://cbuc.top/1607678508843.png)

配置好远程仓库后我们就可以将本地代码推送到远程仓库了

![](https://cbuc.top/1607676459275.png)

回到我们的 **GitLab** 页面，进入到对应的项目下，就可以看到我们刚刚上传的记录了：

![](https://cbuc.top/1607678483252.png)



**END**

以上便是 **GitLab** 的搭建过程，从安装到简单实用，在看的你赶紧动手起来给自己的项目搭一个 **GitLab** 吧！路漫漫，小菜与你一同求索

![看完不赞，都是坏蛋](https://imgconvert.csdnimg.cn/aHR0cHM6Ly93d3cuNTJkb3V0dS5jbi9zdGF0aWMvdGVtcC9waWMvOWJkNjhkMTUwZjA3ODdjNTYwYTQzOWRhMzU5YTU4MGEucG5n?x-oss-process=image/format,png#pic_center)
> 今天的你多努力一点，明天的你就能少说一句求人的话！
>
> *我是小菜，一个和你一起学习的男人。* `💋`
>
> 微信公众号已开启，**小菜良记**，没关注的同学们记得关注哦！