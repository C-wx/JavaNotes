大家好，我是小菜，一个渴望在互联网行业做到蔡不菜的小菜。可柔可刚，点赞则柔，白嫖则刚！
**死鬼~看完记得给我来个三连哦！**

![](https://user-gold-cdn.xitu.io/2020/4/11/17169c46045528af?w=240&h=224&f=jpeg&s=7529)

> 本文主要介绍 `SprinBoot`
> 如有需要，可以参考
> 如有帮助，不忘 **点赞** ❥

### 一、 配置嵌入式Servlet容器

![](https://img-blog.csdnimg.cn/20191004214947995.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80MzI4NzIzOQ==,size_16,color_FFFFFF,t_70)
#### 1）定制和修改Servlet容器的相关配置

*法1：修改和server有关的配置*

 ```properties
server.tomcat.uri-encoding=UTF-8
//通用的Servlet容器设置
server.xxx
//Tomcat的设置
server.tomcat.xxx
 ```
*法2：编写一个EmbeddedServletContainerCustomizer：嵌入式的Servlet容器的定制器；来修改Servlet容器的配置*
![](https://img-blog.csdnimg.cn/20200317105759936.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80MzI4NzIzOQ==,size_16,color_FFFFFF,t_70)

#### 2）注册Servlet三大组件

- `Servlet`
- `Filter`
- `Listener`

*由于 SpringBoot 默认是以jar包的方式启动嵌入式的Servlet容器来启动SpringBoot的web应用，所以没有`web.xml`文件*

*注册三大组件用以下方式*：

`ServletRegistrationBean`：
![](https://img-blog.csdnimg.cn/20200317105946418.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80MzI4NzIzOQ==,size_16,color_FFFFFF,t_70)
`FilterRegistrationBean`：
![ ](https://img-blog.csdnimg.cn/20200317110058769.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80MzI4NzIzOQ==,size_16,color_FFFFFF,t_70)
`ServletListenerRegistrationBean`：
![ ](https://img-blog.csdnimg.cn/20200317110237493.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80MzI4NzIzOQ==,size_16,color_FFFFFF,t_70)

#### 3)替换为其他嵌入式Servlet容器

*默认支持以下容器*

- `Tomcat`
```xml
<!-- 引入web模块默认就是使用嵌入式的Tomcat作为Servlet容器 -->
<dependency>
   <groupId>org.springframework.boot</groupId>
   <artifactId>spring-boot-starter-web</artifactId>
</dependency>
```
- `Jetty`
```xml
<!-- 先排除内置默认容器 -->
<dependency>
   <groupId>org.springframework.boot</groupId>
   <artifactId>spring-boot-starter-web</artifactId>
   <exclusions>
      <exclusion>
         <artifactId>spring-boot-starter-tomcat</artifactId>
         <groupId>org.springframework.boot</groupId>
      </exclusion>
   </exclusions>
</dependency>

<!--引入其他的Servlet容器-->
<dependency>
   <artifactId>spring-boot-starter-jetty</artifactId>
   <groupId>org.springframework.boot</groupId>
</dependency>
```
- `Undertow`
```xml
<!-- 先排除内置默认容器 -->
<dependency>
   <groupId>org.springframework.boot</groupId>
   <artifactId>spring-boot-starter-web</artifactId>
   <exclusions>
      <exclusion>
         <artifactId>spring-boot-starter-tomcat</artifactId>
         <groupId>org.springframework.boot</groupId>
      </exclusion>
   </exclusions>
</dependency>

<!--引入其他的Servlet容器-->
<dependency>
   <artifactId>spring-boot-starter-undertow</artifactId>
   <groupId>org.springframework.boot</groupId>
</dependency>
```
#### 4）嵌入式Servlet容器自动配置原理

`EmbeddedServletContainerAutoConfiguration`：
![](https://img-blog.csdnimg.cn/20200317111205750.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80MzI4NzIzOQ==,size_16,color_FFFFFF,t_70)

- `EmbeddedServletContainerFactory`（嵌入式Servlet容器工厂）
	
	```java
	public interface EmbeddedServletContainerFactory {
	   //获取嵌入式的Servlet容器
	   EmbeddedServletContainer getEmbeddedServletContainer(
	         ServletContextInitializer... initializers);
		}
	}
	```
	
	​	![ ](https://img-blog.csdnimg.cn/20191004221259908.png)
	
- `EmbeddedServletContainer`：（嵌入式的Servlet容器）
  ![ ](https://img-blog.csdnimg.cn/20191004221343698.png)
- 以`TomcatEmbeddedServletContainerFactory`为例
  ![ ](https://img-blog.csdnimg.cn/20200317111722491.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80MzI4NzIzOQ==,size_16,color_FFFFFF,t_70)

- 嵌入式容器的配置修改怎么生效
	- *方法1*：`ServerProperties `
	- *方法2*： `EmbeddedServletContainerCustomizer`（定制器帮我们修改了Servlet容器的配置）

*修改原理*：

容器中导入了`EmbeddedServletContainerCustomizerBeanPostProcessor`

`ServerProperties`：也是定制器
![ ](https://img-blog.csdnimg.cn/20200317112003841.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80MzI4NzIzOQ==,size_16,color_FFFFFF,t_70)

- SpringBoot根据导入的依赖情况，给容器中添加相应的`EmbeddedServletContainerFactory【TomcatEmbeddedServletContainerFactory】`
- 容器中某个组件要创建对象就会惊动后置处理器：`EmbeddedServletContainerCustomizerBeanPostProcessor`（只要是嵌入式的Servlet容器工厂，后置处理器就工作）
- 后置处理器，从容器中获取所有的`EmbeddedServletContainerCustomizer`，调用定制器的定制方法
#### 5）使用外置的Servlet容器

嵌入式Servlet容器：应用打成可执行的`jar`
*优点*： 简单、便携
*缺点*：默认不支持JSP、优化定制比较复杂

*步骤*：

1. 创建一个war项目
2. 将嵌入式的Tomcat指定为provided
	```xml
	<dependency>
	   <groupId>org.springframework.boot</groupId>
	   <artifactId>spring-boot-starter-tomcat</artifactId>
	   <scope>provided</scope>
	</dependency>
	```

 3. 编写一个`SpringBootServletInitializer`的子类，并调用`configure()`方法
	```java
	public class ServletInitializer extends SpringBootServletInitializer {
	   @Override
	   protected SpringApplicationBuilder configure(SpringApplicationBuilder application) {
	       //传入SpringBoot应用的主程序
	      return application.sources(SpringBoot04WebJspApplication.class);
	   }
	}
	```
3. 启动服务器就可以使用

*原理*：

`jar包`：执行SpringBoot主类的main方法，启动 `Ioc`容器，创建嵌入式的Servlet容器

`war包`：启动服务器，服务器启动SpringBoot应用`SpringBootServletInitializer`，启动 `Ioc`容器

### 二、数据访问

对于数据访问层，无论是`SQL`还是`NOSQL`，`Spring Boot`默认采用整合`Spring Data`的方式进行统一处理，添加大量自动配置，屏蔽了很多设置。引入各种`xxxTemplate`，`xxxRepository`来简化我们对数据访问层的操作。对我们来说只需要进行简单的设置即可。

![](https://img-blog.csdnimg.cn/20191005223514558.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80MzI4NzIzOQ==,size_16,color_FFFFFF,t_70)
#### 1）整合基本JDBC与数据源

- 引入starter  ：` spring-boot-starter-jdbc`
```xml
<dependency>
	<groupId>org.springframework.boot</groupId>
	<artifactId>spring-boot-starter-jdbc</artifactId>
</dependency>
<dependency>
	<groupId>mysql</groupId>
	<artifactId>mysql-connector-java</artifactId>
	<scope>runtime</scope>
</dependency>
```
- 配置`application.yml`
```yaml
spring:
  datasource:
    username: root
    password: 123456
    url: jdbc:mysql://192.168.15.22:3306/test
    driver-class-name: com.mysql.jdbc.Driver
```
*结论*：

- 默认使用`org.apache.tomcat.jdbc.pool.DataSource`作为数据源
- 数据源的相关配置都在`DataSourceProperties`里面

*自动配置原理*：

1. 参考`DataSourceConfiguration`，根据配置创建数据源，默认使用Tomcat连接池。可以使用`spring.datasource.type`指定自定义的数据源类型

2. SpringBoot默认可以支持：
	+ `org.apache.tomcat.jdbc.pool.DataSource`
	+ `HikariDataSource`
	+ `BasicDataSource`
	
3. 自定义数据源类型
	```java
	@ConditionalOnMissingBean(DataSource.class)
	@ConditionalOnProperty(name = "spring.datasource.type")
	static class Generic {
	   @Bean
	   public DataSource dataSource(DataSourceProperties properties) {
	       //使用DataSourceBuilder创建数据源，利用反射创建响应type的数据源，并且绑定相关属性
	      return properties.initializeDataSourceBuilder().build();
	   }
	}
	```
	
4. `DataSourceInitializer`：ApplicationListener
	
	*作用*：
	
1. `runSchemaScripts()`：运行建表语句
	2. `runDataScripts()`：运行插入数据的sql语句
	
	*默认只需要将文件命名为*：
	
	`schema-*.sql`、`data-*.sql`
   
5. 操作数据库：自动配置了JdbcTemplate操作数据库

#### 2）整合Druid数据源

- 引入druid数据源
```xml
<dependency>
	<groupId>com.alibaba</groupId>
	<artifactId>druid</artifactId>
	<version>1.1.8</version>
</dependency>
```
 - 配置文件

![](https://ae01.alicdn.com/kf/H2f7ed5e8322c420187599eee814d42233.jpg)

- 配置数据源

![](https://ae01.alicdn.com/kf/H53739e6d25814195acf8bb09f995666f8.jpg)

#### 3）整合MyBatis

- 引入依赖：
```xml
<dependency>
	<groupId>org.mybatis.spring.boot</groupId>
	<artifactId>mybatis-spring-boot-starter</artifactId>
	<version>1.3.1</version>
</dependency>
```
*步骤*：
	1）配置数据源相关属性
	2）给数据库建表
	3）创建JavaBean
	4）注解使用
![](https://img-blog.csdnimg.cn/20200317125427309.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80MzI4NzIzOQ==,size_16,color_FFFFFF,t_70)
*自定义MyBatis的配置规则*：

+ 在容器中添加一个ConfigurationCustomizer
  ![ ](https://img-blog.csdnimg.cn/20200317125500211.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80MzI4NzIzOQ==,size_16,color_FFFFFF,t_70)

+ 在启动类中添加MapperScan注解批量扫描所有的Mapper接口
![ ](https://img-blog.csdnimg.cn/20200317125624218.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80MzI4NzIzOQ==,size_16,color_FFFFFF,t_70)
	
	

5）配置文件使用

```yaml
mybatis:
  #指定全局配置文件的位置
  config-location: classpath:mybatis/mybatis-config.xml
  #指定sql映射文件的位置
  mapper-locations: classpath:mybatis/mapper/*.xml 
```
#### 4）整合SpringData JPA

`SpringData简介`

>Spring Data是一个用于简化数据库访问，并支持云服务的开源框架。其主要目标是使得对数据的访问变得方便快捷。
>它可以极大的简化JPA的写法，可以在几乎不用写实现的情况下，实现对数据的访问和操作。除了CRUD外，还包括如分页、排序等一些常用的功能。

![ ](https://img-blog.csdnimg.cn/20191005230408950.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80MzI4NzIzOQ==,size_16,color_FFFFFF,t_70)
`SpringData整合`
+ 编写一个实体类（bean）和数据表进行映射，并且配置好映射关系
![ ](https://img-blog.csdnimg.cn/20200317130104547.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80MzI4NzIzOQ==,size_16,color_FFFFFF,t_70)
+ 编写一个Dao接口来操作实体类对应的数据表（Repository）
![ ](https://img-blog.csdnimg.cn/20200317130222844.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80MzI4NzIzOQ==,size_16,color_FFFFFF,t_70)
+ 配置JpaProperties

```yaml
spring:  
 jpa:
    hibernate:
      #更新或者创建数据表结构
      ddl-auto: update
      #控制台显示SQL
   	  show-sql: true
```
#### 5）事件监听机制

*以下文件是配置在`META-INF/spring.factories`*

- `ApplicationContextInitializer`
![](https://img-blog.csdnimg.cn/20200317131206780.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80MzI4NzIzOQ==,size_16,color_FFFFFF,t_70)
- `SpringApplicationRunListener`
![ ](https://img-blog.csdnimg.cn/20200317131254943.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80MzI4NzIzOQ==,size_16,color_FFFFFF,t_70)

*以上两个需要配置在（`META-INF/spring.factories`）*

```properties
org.springframework.context.ApplicationContextInitializer=\
com.atguigu.springboot.listener.HelloApplicationContextInitializer

org.springframework.boot.SpringApplicationRunListener=\
com.atguigu.springboot.listener.HelloSpringApplicationRunListener
```
`以下两个只需要放在ioc容器中`
- `ApplicationRunner`
![ ](https://img-blog.csdnimg.cn/202003171315100.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80MzI4NzIzOQ==,size_16,color_FFFFFF,t_70)
- `CommandLineRunner`
![ ](https://img-blog.csdnimg.cn/20200317131537773.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80MzI4NzIzOQ==,size_16,color_FFFFFF,t_70)



![看完不赞，都是坏蛋](https://imgconvert.csdnimg.cn/aHR0cHM6Ly93d3cuNTJkb3V0dS5jbi9zdGF0aWMvdGVtcC9waWMvOWJkNjhkMTUwZjA3ODdjNTYwYTQzOWRhMzU5YTU4MGEucG5n?x-oss-process=image/format,png#pic_center)

> 今天的你多努力一点，明天的你就能少说一句求人的话！
>
> *我是小菜，一个和你一起学习的男人。* `💋`