大家好，我是小菜，一个渴望在互联网行业做到蔡不菜的小菜。可柔可刚，点赞则柔，白嫖则刚！
**死鬼~看完记得给我来个三连哦！**

![](https://user-gold-cdn.xitu.io/2020/4/11/17169c46045528af?w=240&h=224&f=jpeg&s=7529)

> 本文主要介绍 `SprinBoot`
> 如有需要，可以参考
> 如有帮助，不忘 **点赞** ❥

### 一、大致了解

>`官方`：Spring Boot makes it easy to create stand-alone, production-grade Spring based Applications that you can "just run".
>We take an opinionated view of the Spring platform and third-party libraries so you can get started with minimum fuss. Most Spring Boot applications need minimal Spring configuration.
>`官翻`：通过Spring Boot，可以轻松地创建独立的，基于生产级别的基于Spring的应用程序，您可以“运行”它们。
>我们对Spring平台和第三方库持固执己见的观点，因此您可以以最小的麻烦开始使用。大多数Spring Boot应用程序需要最少的Spring配置。

#### 1）背景
- J2EE笨重的开发
- 繁多的配置
- 低下的开发效率
- 复杂的部署流程
- 第三方技术集成难度大

#### 2）优点

-  快速创建`独立运行`的Spring项目以及与主流框架集成
-  使用`嵌入式`的Servlet容器，应用无需打成`WAR`包
-  `starters`自动依赖与版本控制
-  大量的`自动配置`，简化开发，也可修改默认值
-  `无需配置XML`，无代码生成，开箱即用
-  准生产环境的运行时`应用监控`

### 二、深入了解

#### 1) 配置文件

`SpringBoot使用一个全局的配置文件，配置文件名是固定的`

- ##### application.properties
- ##### application.yml

*YAML*

> YAML（YAML Ain't Markup Language） 标记语言：
> -  以前的配置文件：大多都使用的是  **xxxx.xml**文件；
> - YAML：**以数据为中心**，比json、xml等更适合做配置文件；

```yaml
//YAML 配置文件
server:
  port: 8081
  
//XML 配置文件
<server>
	<port>8081</port>
</server>
```

##### 1、基本写法
`k:(空格)v`： 表示一对键值对（空格必须有）；
以**空格**的缩进来控制层级关系；只要是左对齐的一列数据，都是同一个层级的

```yaml
server:
    port: 8081
    path: /hello
```
注：*属性和值是大小写敏感*
##### 2、值的写法
 `字面量：普通的值（数字，字符串，布尔）`
- k: v：字面直接来写；
			字符串默认不用加上单引号或者双引号；
	+  ""：双引号；不会转义字符串里面的特殊字符；特殊字符会作为本身想表示的意思
		 name:   "zhangsan \n lisi"：输出；zhangsan 换行  lisi
	+ ''：单引号；会转义特殊字符，特殊字符最终只是一个普通的字符串数据
	    name:   ‘zhangsan \n lisi’：输出；zhangsan \n  lisi

`对象、Map（属性和值）（键值对）`
- k: v：在下一行来写对象的属性和值的关系；注意缩进
	+ 对象还是k: v的方式
	```yaml
	friends:
			lastName: zhangsan
			age: 20
	```
	+ 行内写法：
	 ```yaml
		friends: {lastName: zhangsan,age: 18}
	 ```
`数组（List、Set）`
用 ==-== 值表示数组中的一个元素
```yaml
pets:
 - cat
 - dog
 - pig
```
行内写法
```yaml
pets: [cat,dog,pig]
```
##### 3、配置文件值注入
配置文件：
```yaml
person:
    lastName: Cbuc
    age: 22
    birth: 11/13
    maps: {k1: v1,k2: v2}
    lists:
      - apple
      - pear
    dog:
      name: 旺财
      age: 12
```
javaBean：
```java
/**
 * 将配置文件中配置的每一个属性的值，映射到这个组件中
 * @ConfigurationProperties：告诉SpringBoot将本类中的所有属性和配置文件中相关的配置进行绑定；
 *      prefix = "person"：配置文件中哪个下面的所有属性进行一一映射
 *
 * 只有这个组件是容器中的组件，才能容器提供的@ConfigurationProperties功能；
 *
 */
@Component
@ConfigurationProperties(prefix = "person")
public class Person {

    private String lastName;
    private Integer age;
    private Date birth;

    private Map<String,Object> maps;
    private List<Fruit> lists;
    private Dog dog;
}
```
我们可以导入配置文件处理器，以后编写配置就有提示了：
```xml
<!--导入配置文件处理器，配置文件进行绑定就会有提示-->
<dependency>
	<groupId>org.springframework.boot</groupId>
	<artifactId>spring-boot-configuration-processor</artifactId>
	<optional>true</optional>
</dependency>
```
**`@Value获取值和@ConfigurationProperties获取值比较`**
|                      | @ConfigurationProperties | @Value     |
| -------------------- | ------------------------ | ---------- |
| 功能                 | 批量注入配置文件中的属性 | 一个个指定 |
| 松散绑定（松散语法） | 支持                     | 不支持     |
| SpEL                 | 不支持                   | 支持       |
| JSR303数据校验       | 支持                     | 不支持     |
| 复杂类型封装         | 支持                     | 不支持     |
==配置文件yml还是properties他们都能获取到值==
1. 如果说，我们只是在某个业务逻辑中需要获取一下配置文件中的某项值，使用@Value；
2. 如果说，我们专门编写了一个javaBean来和配置文件进行映射，我们就直接使用@ConfigurationProperties；

##### 4、配置文件注入值数据校验
```java
@Component
@ConfigurationProperties(prefix = "person")
@Validated
public class Person {
   //lastName必须是邮箱格式
    @Email
    private String lastName;
    private Integer age;
    private Date birth;
    
    private Map<String,Object> maps;
    private List<Fruit> lists;
    private Dog dog;
}
```
##### 5、加载配置
+ ` @PropertySource:`加载指定的配置文件
```java
@PropertySource(value = {"classpath:person.properties"})
@Component
public class Person {
    private String lastName;
    private Integer age;
```
+ ` @ImportResource:`导入Spring的配置文件，让配置文件里面的内容生效

SpringBoot里面没有Spring的配置文件，我们自己编写的配置文件，不能自动识别

想让Spring的配置文件生效，加载进来；@**ImportResource**标注在一个配置类(主配置类)上
```java
//导入Spring的配置文件让其生效
@ImportResource(locations = {"classpath:beans.xml"})
```
+ ` @Bean:`加载指定的配置文件
```xml
<bean id="helloService" class="cbuc.life.service.HelloService"></bean>
```
==SpringBoot通常不用xml的方式导入组件，推荐注解的方式==

1. 配置类`@Configuration` => Spring配置文件
2. 使用`@Bean`给容器中添加组件
```java
/**
 * @Configuration：指明当前类是一个配置类；就是来替代之前的Spring配置文件
 *
 * 在配置文件中用<bean><bean/>标签添加组件
 *
 */
@Configuration
public class MyAppConfig {
    //将方法的返回值添加到容器中；容器中这个组件默认的id就是方法名
    @Bean
    public HelloService helloService02(){
        System.out.println("配置类@Bean给容器中添加组件了...");
        return new HelloService();
    }
}
```
##### 6、Profile多环境配置
- [Spring Boot中 Profile 多环境配置](https://blog.csdn.net/weixin_43287239/article/details/99674796) 
#### 3) 日志框架
`市面上的框架`
>JUL（java.util.logging），JCL（Apache  Commons Logging），Log4j，Log4j2，Logback、SLF4j、jboss-logging等

`SpringBoot中`
>==SpringBoot：底层是Spring框架，Spring框架默认是用JCL==
>在框架内部使用JCL，spring-boot-starter-logging采用了  slf4j+logback的形式
>Spring Boot也能自动适配（jul、log4j2、logback） 并简化配置
>SpringBoot选用==SLF4j==和==logback==

`如何实现`
==左边选一个门面（抽象层）、右边来选一个实现==
| 日志门面  （日志的抽象层）                                   | 日志实现                                             |
| ------------------------------------------------------------ | ---------------------------------------------------- |
| ~~JCL（Jakarta  Commons Logging）~~    SLF4j（Simple  Logging Facade for Java）    **~~jboss-logging~~** | Log4j  JUL（java.util.logging）  Log4j2  **Logback** |
##### 1、SLF4j使用
给系统里面导入slf4j的jar和  logback的实现jar
```java
import org.slf4j.Logger;
import org.slf4j.LoggerFactory;

public class HelloWorld {
  public static void main(String[] args) {
    Logger logger = LoggerFactory.getLogger(HelloWorld.class);
    logger.info("Hello World");
  }
}
```
![在这里插入图片描述](https://img-blog.csdnimg.cn/20191002200333542.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80MzI4NzIzOQ==,size_16,color_FFFFFF,t_70)
每一个日志的实现框架都有自己的配置文件。使用slf4j以后，==配置文件还是做成日志实现框架自己本身的配置文件==
`存在问题：`
![在这里插入图片描述](https://img-blog.csdnimg.cn/20191002200617435.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80MzI4NzIzOQ==,size_16,color_FFFFFF,t_70)
`解决步骤：`
1. 将系统中其他日志框架先排除出去
2. 用中间包来替换原有的日志框架
3. 我们导入slf4j 或其他的日志实现
##### 2、SpringBoot日志关系
```xml
<dependency>
	<groupId>org.springframework.boot</groupId>
	<artifactId>spring-boot-starter</artifactId>
</dependency>
```
SpringBoot的日志功能：

```xml
<dependency>
	<groupId>org.springframework.boot</groupId>
	<artifactId>spring-boot-starter-logging</artifactId>
</dependency>
```
`底层依赖关系：`
![在这里插入图片描述](https://img-blog.csdnimg.cn/20191002200848958.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80MzI4NzIzOQ==,size_16,color_FFFFFF,t_70)
`总结：`
>SpringBoot能自动适配所有的日志，而且底层使用slf4j+logback的方式记录日志，引入其他框架的时候，只需要把这个框架依赖的日志框架排除掉即可
1. SpringBoot 底层也是使用 ==slf4j + logback==的方式进行日志记录
2. SpringBoot 也把其他的日志都替换成了==slf4j==
3. 需要中间替换包
```java
@SuppressWarnings("rawtypes")
public abstract class LogFactory {
static String UNSUPPORTED_OPERATION_IN_JCL_OVER_SLF4J = "http://www.slf4j.org/codes.html#unsupported_operation_in_jcl_over_slf4j";
static LogFactory logFactory = new SLF4JLogFactory();
}
```
![在这里插入图片描述](https://img-blog.csdnimg.cn/20191002201056234.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80MzI4NzIzOQ==,size_16,color_FFFFFF,t_70)4.  如果我们要引入其他框架，一定要把这个框架的默认日志依赖==移除==掉
Spring框架自身用到是用的是commons-logging
```xml
<dependency>
	<groupId>org.springframework</groupId>
	<artifactId>spring-core</artifactId>
	<exclusions>
		<exclusion>
			<groupId>commons-logging</groupId>
			<artifactId>commons-logging</artifactId>
		</exclusion>
	</exclusions>
</dependency>
```
##### 3、日志的使用
**`默认配置：`**
SpringBoot默认配置好了的日志
```java
	//日志记录器
	Logger logger = LoggerFactory.getLogger(getClass());
	@Test
	public void contextLoads() {
		//System.out.println();
		//日志的级别；
		//由低到高   trace<debug<info<warn<error
		//可以调整输出的日志级别；日志就只会在这个级别以以后的高级别生效
		logger.trace("这是trace日志...");
		logger.debug("这是debug日志...");
		//SpringBoot默认给我们使用的是info级别的，没有指定级别的就用SpringBoot默认规定的级别；root级别
		logger.info("这是info日志...");
		logger.warn("这是warn日志...");
		logger.error("这是error日志...");
	}
```
SpringBoot修改日志的默认配置
>   日志输出格式：
>       		==%d==	表示日期时间，
>       		==%thread==	表示线程名，
>       		==%-5level==	级别从左显示5个字符宽度
>       		==%logger{50}==	 表示logger名字最长50个字符，否则按照句点分割。 
>       		==%msg==	日志消息，
>       		==%n==	是换行符
>          `%d{yyyy-MM-dd HH:mm:ss.SSS} [%thread] %-5level %logger{50} - %msg%n`
```properties
#给所在的包下设计日志级别
logging.level.cbuc.life=trace

# 不指定路径在当前项目下生成springboot.log日志
#logging.path=

# 可以指定完整的路径；
#logging.file=D:/springboot.log

# 在当前磁盘的根路径下创建spring文件夹和里面的log文件夹；使用 spring.log 作为默认文件
logging.path=/spring/log

#  在控制台输出的日志的格式
logging.pattern.console=%d{yyyy-MM-dd} [%thread] %-5level %logger{50} - %msg%n

# 指定文件中日志输出的格式
logging.pattern.file=%d{yyyy-MM-dd} === [%thread] === %-5level === %logger{50} ==== %msg%n
```
| logging.file | logging.path | Example  | Description                        |
| ------------ | ------------ | -------- | ---------------------------------- |
| (none)       | (none)       |          | 只在控制台输出                     |
| 指定文件名   | (none)       | my.log   | 输出日志到my.log文件               |
| (none)       | 指定目录     | /var/log | 输出到指定目录的 spring.log 文件中 |
**`指定配置：`**
==给类路径下放上每个日志框架自己的配置文件即可，SpringBoot就不会使用它默认配置的了==
| Logging System          | Customization                                                |
| ----------------------- | ------------------------------------------------------------ |
| Logback                 | `logback-spring.xml`, `logback-spring.groovy`, `logback.xml` or `logback.groovy` |
| Log4j2                  | `log4j2-spring.xml` or `log4j2.xml`                          |
| JDK (Java Util Logging) | `logging.properties`                                         |
==logback.xml：== 直接会被日志框架识别
==logback-spring.xml：== 日志框架就不直接加载日志的配置项，由SpringBoot解析日志配置，可以使用SpringBoot的高级Profile功能
```xml
<springProfile name="staging">
	<!--可以指定某段配置只在某个环境下生效-->
    <!-- configuration to be enabled when the "staging" profile is active -->
</springProfile>
```
如：
```xml
	<appender name="stdout" class="ch.qos.logback.core.ConsoleAppender">
        <!--
        日志输出格式：
			%d	表示日期时间，
			%thread	表示线程名，
			%-5level：	级别从左显示5个字符宽度
			%logger{50}   表示logger名字最长50个字符，否则按照句点分割。 
			%msg：	日志消息，
			%n	是换行符
        -->
        <layout class="ch.qos.logback.classic.PatternLayout">
            <springProfile name="dev">
                <pattern>%d{yyyy-MM-dd HH:mm:ss.SSS} ----> [%thread] ---> %-5level %logger{50} - %msg%n</pattern>
            </springProfile>
            <springProfile name="!dev">
                <pattern>%d{yyyy-MM-dd HH:mm:ss.SSS} ==== [%thread] ==== %-5level %logger{50} - %msg%n</pattern>
            </springProfile>
        </layout>
    </appender>
```
==如果使用logback.xml作为日志配置文件，还要使用profile功能，会有以下错误：==`no applicable action for [springProfile]`
##### 4、切换日志框架
可以按照slf4j的日志适配图，进行相关的切换；

slf4j+log4j的方式；
```xml
<dependency>
  <groupId>org.springframework.boot</groupId>
  <artifactId>spring-boot-starter-web</artifactId>
  <exclusions>
    <exclusion>
      <artifactId>logback-classic</artifactId>
      <groupId>ch.qos.logback</groupId>
    </exclusion>
    <exclusion>
      <artifactId>log4j-over-slf4j</artifactId>
      <groupId>org.slf4j</groupId>
    </exclusion>
  </exclusions>
</dependency>
<dependency>
  <groupId>org.slf4j</groupId>
  <artifactId>slf4j-log4j12</artifactId>
</dependency>
```
切换为log4j2：
```xml
 <dependency>
     <groupId>org.springframework.boot</groupId>
     <artifactId>spring-boot-starter-web</artifactId>
     <exclusions>
         <exclusion>
             <artifactId>spring-boot-starter-logging</artifactId>
             <groupId>org.springframework.boot</groupId>
         </exclusion>
     </exclusions>
 </dependency>
<dependency>
  <groupId>org.springframework.boot</groupId>
  <artifactId>spring-boot-starter-log4j2</artifactId>
</dependency>
```
#### 4) Web开发
 `简易步骤：`
 1. 创建SpringBoot应用，选中我们需要的模块
 2. SpringBoot已经默认将这些场景配置好了，只需要在配置文件中指定少量的配置就可以运行起来
 3. 编写业务代码

`自动配置原理`
1. ==xxxAutoConfiguration：== 帮我们给容器中自动配置组件
2. ==xxxProperties：==配置类来封装配置文件的内容
##### 1、SpringBoot对静态资源的映射规则
```java
@ConfigurationProperties(prefix = "spring.resources", ignoreUnknownFields = false)
public class ResourceProperties implements ResourceLoaderAware {
  //可以设置和静态资源有关的参数，缓存时间等
}
```
`1）所有 /webjars/** ，都去 classpath:/META-INF/resources/webjars/ 中寻找资源`
	==webjars：以jar包的方式引入静态资源==
![在这里插入图片描述](https://img-blog.csdnimg.cn/20191004201008932.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80MzI4NzIzOQ==,size_16,color_FFFFFF,t_70)
例如：引入jquery-webjar
```xml
<!--在访问的时候只需要写webjars下面资源的名称即可-->
<dependency>
	<groupId>org.webjars</groupId>
	<artifactId>jquery</artifactId>
	<version>3.3.1</version>
</dependency>
```
引入后我们可以通过 ：localhost:8080/webjars/jquery/3.3.1/jquery.js 访问到静态资源
`2）"/**" 访问当前项目的任何资源，都去（静态资源的文件夹）找映射`
```xml
"classpath:/META-INF/resources/", 
"classpath:/resources/",
"classpath:/static/", 
"classpath:/public/" 
"/"：当前项目的根路径
```
`3）欢迎页； 静态资源文件夹下的所有index.html页面；被"/**"映射`
访问：localhost:8080/  便会去静态资源文件夹下找index页面
`4）所有的 **/favicon.ico  都是在静态资源文件下找`
##### 2、模板引擎
>市面上常见的模板引擎：JSP、Velocity、Freemarker、Thymeleaf
>![在这里插入图片描述](https://img-blog.csdnimg.cn/20191004201530166.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80MzI4NzIzOQ==,size_16,color_FFFFFF,t_70)
>
>==具体使用可以参考博文：==
- [Thymeleaf的使用](https://blog.csdn.net/weixin_43287239/article/details/102092145)
- [FreeMarker的使用](https://blog.csdn.net/weixin_43287239/article/details/100081185)
##### 3、SpringMVC自动配置
>SpringBoot自动配置好了SpringMVC，以下是SpringBoot对SpringMVC的默认配置：==WebMvcAutoConfiguration==
>
>+  自动配置了==ViewResolver==（视图解析器：根据方法的返回值得到视图对象（View），视图对象决定如何渲染 ==（转发/重定向）==
+ ==ContentNegotiatingViewResolver==组合所有的视图解析器

`自定义配置：`我们可以自己给容器中添加一个视图解析器，自动的将以下组合进来
+ webjars：静态资源文件夹路径
+ Static index.html suppor：静态首页访问
+  favicon.ico ：个性化图标
+ Converter：类型转换器
+ Formatter：格式化器
```java
@Bean
@ConditionalOnProperty(prefix = "spring.mvc", name = "date-format")//在文件中配置日期格式化的规则
public Formatter<Date> dateFormatter() {
	return new DateFormatter(this.mvcProperties.getDateFormat());//日期格式化组件
}
```
==在配置文件中自定义规则：==
```xml
spring.mvc.date-format=yyyy-MM-dd
```
+ HttpMessageConverter：SpringMVC用来转换Http请求和响应的
+ Automatic registration of MessageCodesResolver (see below）：.定义错误代码生成规则
##### 4、扩展SpringMVC
`传统写法：`
```xml
<mvc:view-controller path="/hello" view-name="success"/>
<mvc:interceptors>
    <mvc:interceptor>
        <mvc:mapping path="/hello"/>
    </mvc:interceptor>
</mvc:interceptors>
```
`SpringBoot写法：`
+ 编写一个配置类（==@Configuration==）：既保留了所有的自动配置，也能用我们扩展的配置
+ 是WebMvcConfigurerAdapter类型
```java
//使用WebMvcConfigurerAdapter可以来扩展SpringMVC的功能
@Configuration
public class MyMvcConfig extends WebMvcConfigurerAdapter {
    @Override
    public void addViewControllers(ViewControllerRegistry registry) { 
        //浏览器发送 /hello 请求直接来到 success
        registry.addViewController("/hello").setViewName("success");
    }
}
```
`原理：`
+ WebMvcAutoConfiguration是SpringMVC的自动配置类
+ 在做其他自动配置时会导入：==@Import(EnableWebMvcConfiguration.class)==
```java
    @Configuration
	public static class EnableWebMvcConfiguration extends DelegatingWebMvcConfiguration {
      private final WebMvcConfigurerComposite configurers = new WebMvcConfigurerComposite();

	 //从容器中获取所有的WebMvcConfigurer
      @Autowired(required = false)
      public void setConfigurers(List<WebMvcConfigurer> configurers) {
          if (!CollectionUtils.isEmpty(configurers)) {
              //一个参考实现；将所有的WebMvcConfigurer相关配置都来一起调用； 
              this.configurers.addWebMvcConfigurers(configurers);
              }
          }
	}
```
---
```java
@Override
public void addViewControllers(ViewControllerRegistry registry) {
     for (WebMvcConfigurer delegate : this.delegates) {
        delegate.addViewControllers(registry);
      }
}
```
+ 容器中所有的WebMvcConfigurer都会一起起作用，我们的配置类也会被调用；

==效果：SpringMVC的自动配置和我们的扩展配置都会起作用==
##### 5、全面接管SpringMVC
>==我们需要在配置类中添加@EnableWebMvc即可==
>- SpringBoot对SpringMVC的自动配置不需要了，所有都是我们自己配置
>- 所有的SpringMVC的自动配置都失效了

```java
//使用WebMvcConfigurerAdapter可以来扩展SpringMVC的功能
@EnableWebMvc
@Configuration
public class MyMvcConfig extends WebMvcConfigurerAdapter {
    @Override
    public void addViewControllers(ViewControllerRegistry registry) { 
        //浏览器发送 /hello 请求直接来到 success
        registry.addViewController("/hello").setViewName("success");
    }
}
```
`问题：`==为什么添加@EnableWebMvc自动配置就失效了==
`原理：`
+ @EnableWebMvc的核心
```java
@Import(DelegatingWebMvcConfiguration.class)
public @interface EnableWebMvc {}
```
+ DelegatingWebMvcConfiguration类
```java
@Configuration
public class DelegatingWebMvcConfiguration extends WebMvcConfigurationSupport {}
```
+ WebMvcAutoConfiguration 类
```java
@Configuration
@ConditionalOnWebApplication
@ConditionalOnClass({ Servlet.class, DispatcherServlet.class, WebMvcConfigurerAdapter.class })
//容器中没有这个组件的时候，这个自动配置类才生效
@ConditionalOnMissingBean(WebMvcConfigurationSupport.class)
@AutoConfigureOrder(Ordered.HIGHEST_PRECEDENCE + 10)
@AutoConfigureAfter({ DispatcherServletAutoConfiguration.class,
		ValidationAutoConfiguration.class })
public class WebMvcAutoConfiguration {
```
+ @EnableWebMvc将WebMvcConfigurationSupport组件导入进来
+ 导入的WebMvcConfigurationSupport只是SpringMVC最基本的功能（==所以有时候避免添加@EnableWebMvc==）
##### 6、修改SpringBoot的默认配置
+ SpringBoot在自动配置很多组件的时候，先看容器中有没有用户自己配置的（@Bean、@Component）如果有就用用户配置的，如果没有，才自动配置；如果有些组件可以有多个（ViewResolver）将用户配置的和自己默认的组合起来。
+ 在SpringBoot中会有非常多的==xxxConfigurer==帮助我们进行扩展配置
+ 在SpringBoot中会有很多的==xxxCustomizer==帮助我们进行定制配置
##### 7、拦截器的使用
- [Spring boot 中使用拦截器](https://blog.csdn.net/weixin_43287239/article/details/98960135)
##### 8、错误处理机制
使用过SpringBoot的人对下图都不陌生：
![在这里插入图片描述](https://img-blog.csdnimg.cn/20191004212939871.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80MzI4NzIzOQ==,size_16,color_FFFFFF,t_70)
==浏览器发送请求的请求头：==
![在这里插入图片描述](https://img-blog.csdnimg.cn/20191004213037993.png)
==如果是其他客户端，默认响应一个json数据（PostMan）==
![在这里插入图片描述](https://img-blog.csdnimg.cn/20191004213122190.png)
![在这里插入图片描述](https://img-blog.csdnimg.cn/20191004213154533.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80MzI4NzIzOQ==,size_16,color_FFFFFF,t_70)
`实现：`
参照ErrorMvcAutoConfiguration；错误处理的自动配置
**给容器添加以下组件**
- ==DefaultErrorAttributes==
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200317102051834.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80MzI4NzIzOQ==,size_16,color_FFFFFF,t_70)
- ==BasicErrorController：处理默认/error请求==
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200317101957235.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80MzI4NzIzOQ==,size_16,color_FFFFFF,t_70)
- ==ErrorPageCustomizer==
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200317102231939.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80MzI4NzIzOQ==,size_16,color_FFFFFF,t_70)
- ==DefaultErrorViewResolver==
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200317102612911.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80MzI4NzIzOQ==,size_16,color_FFFFFF,t_70)
`步骤：`
> 系统出现4xx或者5xx之类的错误：ErrorPageCustomizer就会生效（定制错误的响应规则）；就会来到/error请求；就会被**BasicErrorController**处理
- 响应页面：去哪个页面是由**DefaultErrorViewResolver**解析得到的
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200317102900772.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80MzI4NzIzOQ==,size_16,color_FFFFFF,t_70)
##### 9、定制错误响应
- ==定制错误的页面==
	+ 有模板引擎的情况下：error/状态码
	>- 将错误页面命名为  错误状态码.html 放在模板引擎文件夹里面的 error文件夹下，发生此状态码的错误就会来到对应的页面
	>-  我们可以使用4xx和5xx作为错误页面的文件名来匹配这种类型的所有错误，精确优先（优先寻找精确的状态码.html）

	`页面能获取的信息`
	```properties
	timestamp：时间戳
	status：状态码
	error：错误提示
	exception：异常对象
	message：异常消息
	errors：JSR303数据校验的错误都在这里
	```
	+ 没有模板引擎（模板引擎找不到这个错误页面），静态资源文件夹下找
	+ 以上都没有错误页面，就是默认来到SpringBoot默认的错误提示页面
- ==定制错误的json数据==
	+ 自定义异常处理&返回定制json数据
	```java
	@ControllerAdvice
	public class MyExceptionHandler {
	    @ResponseBody
	    @ExceptionHandler(UserNotExistException.class)
	    public Map<String,Object> handleException(Exception e){
	        Map<String,Object> map = new HashMap<>();
	        map.put("code","user.notexist");
	        map.put("message",e.getMessage());
	        return map;
	    }
	}
	```
	+ 转发到/error进行自适应响应效果处理
	```java
	 @ExceptionHandler(UserNotExistException.class)
	    public String handleException(Exception e, HttpServletRequest request){
	        Map<String,Object> map = new HashMap<>();
	        //传入我们自己的错误状态码  4xx 5xx，否则就不会进入定制错误页面的解析流程
	        /**
	         * Integer statusCode = (Integer) request
	         .getAttribute("javax.servlet.error.status_code");
	         */
	        request.setAttribute("javax.servlet.error.status_code",500);
	        map.put("code","user.notexist");
	        map.put("message",e.getMessage());
	        //转发到/error
	        return "forward:/error";
	    }
	```
- ==将定制数据携带出去==
>出现错误以后，会来到/error请求，会被BasicErrorController处理，响应出去可以获取的数据是由getErrorAttributes得到的（是AbstractErrorController（ErrorController）规定的方法）
- 编写一个ErrorController的实现类【或者是编写AbstractErrorController的子类】，放在容器中
- 页面上能用的数据，或者是json返回能用的数据都是通过errorAttributes.getErrorAttributes得到，	容器中==DefaultErrorAttributes.getErrorAttributes()== 默认进行数据处理的
`自定义ErrorAttributes`
	```java
	//给容器中加入我们自己定义的ErrorAttributes
	@Component
	public class MyErrorAttributes extends DefaultErrorAttributes {
	
	    @Override
	    public Map<String, Object> getErrorAttributes(RequestAttributes requestAttributes, boolean includeStackTrace) {
	        Map<String, Object> map = super.getErrorAttributes(requestAttributes, includeStackTrace);
	        map.put("name","cbuc");
	        return map;
	    }
	}
	```

 + 最终的效果：响应是自适应的，可以通过定制ErrorAttributes改变需要返回的内容