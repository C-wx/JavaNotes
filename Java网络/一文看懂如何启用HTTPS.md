大家好，我是小菜，一个渴望在互联网行业做到蔡不菜的小菜。可柔可刚，点赞则柔，白嫖则刚！
**死鬼~看完记得给我来个三连哦！**![](https://user-gold-cdn.xitu.io/2020/4/11/17169c46045528af?w=240&h=224&f=jpeg&s=7529)

> 本文主要介绍 `如何启用HTTPS`
> 如有需要，可以参考
> 如有帮助，不忘 **点赞** ❥

### 一、准备工作

- 服务器一台（可以购买阿里云轻量应用服务器，比较便宜）
- SSL证书 （可以注册阿里云免费证书，安全性较差）
- 域名一个 （可以在万网上购买并要进行备案）
- 本地打包好的项目（博主是使用springboot开发，所以打包好的是jar包而不是war包）
- `ftp`客户端

*首先在服务器上搭建好环境（数据库，jdk之类的），因为演示的项目是由`SpringBoot`搭建，有内置运行容器，所以不用Tomcat。*

#### 1）SSL证书

可以上阿里云申请免费版的`SSL`证书，也可以访问`FreeSSL`网站进行注册免费的证书
![ ](https://img-blog.csdnimg.cn/20200308123216857.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80MzI4NzIzOQ==,size_16,color_FFFFFF,t_70)

#### 2）域名备案成功后需要进行解析
到阿里云控制台，进入域名管理
  ![ ](https://img-blog.csdnimg.cn/20200308124447722.png)
  ![ ](https://img-blog.csdnimg.cn/2020030812450570.png)
  ![ ](https://img-blog.csdnimg.cn/20200308124534492.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80MzI4NzIzOQ==,size_16,color_FFFFFF,t_70)

#### 3）解析完成后在这块点击证书申请，填写相关信息
![ ](https://img-blog.csdnimg.cn/20200308123451479.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80MzI4NzIzOQ==,size_16,color_FFFFFF,t_70)
![ ](https://img-blog.csdnimg.cn/20200308123551767.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80MzI4NzIzOQ==,size_16,color_FFFFFF,t_70)
申请好后经过审核 ，然后便可以点击下载
![ ](https://img-blog.csdnimg.cn/20200308123711279.png)

#### 4）注入`ServletWebServerFactory`

在我们`SpringBoot`项目中的**启动类**中注入`ServletWebServerFactory`：

```java
@Bean
public ServletWebServerFactory servletContainer(){
    TomcatServletWebServerFactory tomcat = new TomcatServletWebServerFactory();
    tomcat.addAdditionalTomcatConnectors(createHTTPConnector());
    return tomcat;
}

private Connector createHTTPConnector() {
    Connector connector = new Connector("org.apache.coyote.http11.Http11NioProtocol");
    //同时启用http（8080）、https（8866）两个端口
    connector.setScheme("http");
    connector.setSecure(false);
    connector.setPort(8080);
    connector.setRedirectPort(8866);
    return connector;
}
```
然后在`application.properties`配置文件中添加
![ ](https://img-blog.csdnimg.cn/20200308124149288.png)
*这里注意是`server.ssl.key-store-password`而不是 `server.ssl.key-password`*

#### 5）打包项目
将`自己打包好的项目和下载下来的证书`放到`usr/develop/project` 文件夹下，文件夹目录可以自己选择。

为了方便我自己建了几个脚本方便运行。

- `vim start.sh` 

  建立启动脚本，内容如下：
```shell
nohup java -jar 自己的项目名称.jar &
```
- `vim stop.sh `

  建立停止脚本，内容如下：
```shell
PID=$(ps -ef | grep 自己的项目名称.jar | grep -v grep | awk '{ print $2 }')
if [ -z "$PID" ]
then
    echo Application is already stopped
else
    echo kill $PID
    kill -9 $PID
fi
```
- `vim run.sh`

   建立运行脚本，内容如下
```shell
echo stop application
source stop.sh
echo start application
source start.sh
```
然后在终端输入 `./run.sh`
如果提示没有权限，则输入

```shell
chmod u+x *.sh
```
然后再输入 `./run.sh`
这样我们的程序就启动了，然后我们在浏览器上就可以通过`https://域名:端口号`访问自己的项目了

![看完不赞，都是坏蛋](https://imgconvert.csdnimg.cn/aHR0cHM6Ly93d3cuNTJkb3V0dS5jbi9zdGF0aWMvdGVtcC9waWMvOWJkNjhkMTUwZjA3ODdjNTYwYTQzOWRhMzU5YTU4MGEucG5n?x-oss-process=image/format,png#pic_center)

> 今天的你多努力一点，明天的你就能少说一句求人的话！
>
> *我是小菜，一个和你一起学习的男人。* `💋`