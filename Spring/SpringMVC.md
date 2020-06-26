大家好，我是小菜，一个渴望在互联网行业做到蔡不菜的小菜。可柔可刚，点赞则柔，白嫖则刚！

**死鬼~看完记得给我来个三连哦！**

![](https://user-gold-cdn.xitu.io/2020/4/11/17169c46045528af?w=240&h=224&f=jpeg&s=7529)

>本文主要介绍 `SprinMVC`
>如有需要，可以参考
>如有帮助，不忘 **点赞** ❥
>
>创作不易，白嫖无义！

### 一丶SpringMVC概述

- Spring 为展现层提供的基于 MVC 设计理念的优秀的Web 框架，是目前最主流的 MVC 框架之一
- Spring3.0 后全面超越 Struts2，成为最优秀的 MVC 框架
- Spring MVC 通过一套 MVC 注解，让 POJO 成为处理请  求的控制器，而无须实现任何接口。
- 支持 REST 风格的 URL 请求
- 采用了松散耦合可插拔组件结构，比其他 MVC 框架更具扩展性和灵活性

### 二丶SpringMVC简单使用
#### 1）在 web.xml 中配置 DispatcherServlet：

````xml
<!-- 配置 DispatcherServlet -->
	<servlet>
		<servlet-name>dispatcherServlet</servlet-name>
		<servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class>
		<!-- 配置 DispatcherServlet 的一个初始化参数: 配置 SpringMVC 配置文件的位置和名称 -->
		<!-- 
			实际上也可以不通过 contextConfigLocation 来配置 SpringMVC 的配置文件, 而使用默认的.
			默认的配置文件为: /WEB-INF/<servlet-name>-servlet.xml
		-->
		<init-param>
			<param-name>contextConfigLocation</param-name>
			<param-value>classpath:springmvc.xml</param-value>
		</init-param>
		<load-on-startup>1</load-on-startup>
	</servlet>
	<servlet-mapping>
		<servlet-name>dispatcherServlet</servlet-name>
		<url-pattern>/</url-pattern>
	</servlet-mapping>
````
####  2）加入 Spring MVC 的配置文件

````xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
	xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
	xmlns:context="http://www.springframework.org/schema/context"
	xmlns:mvc="http://www.springframework.org/schema/mvc"
	xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd
		http://www.springframework.org/schema/context http://www.springframework.org/schema/context/spring-context-4.0.xsd
		http://www.springframework.org/schema/mvc http://www.springframework.org/schema/mvc/spring-mvc-4.0.xsd">

	<!-- 配置自定扫描的包 -->
	<context:component-scan base-package="cbuc.life.springmvc"></context:component-scan>
	
	<!-- 配置视图解析器: 如何把 handler 方法返回值解析为实际的物理视图 -->
	<bean class="org.springframework.web.servlet.view.InternalResourceViewResolver">
		<property name="prefix" value="/WEB-INF/views/"></property>
		<property name="suffix" value=".jsp"></property>
	</bean>
	
</beans>
````
#### 3）编写处理请求的处理器，并使用**@Controller** 注解标识为处理器

````java
@Controller
public class HelloWorldController {
	/**
	 * 1. 使用 @RequestMapping 注解来映射请求的 URL
	 * 2. 返回值会通过视图解析器解析为实际的物理视图, 对于 InternalResourceViewResolver 视图解析器, 会做如下的解析:
	 * 通过 prefix + returnVal + 后缀 这样的方式得到实际的物理视图, 然会做转发操作
	 * /WEB-INF/views/success.jsp
	 */
	@RequestMapping("/helloworld")
	public String hello(){
		System.out.println("hello world");
		return "success";
	}
}
````
#### 4） 编写视图 JSP

在/WEB-INF/views/目录下创建一个succes.jsp

````jsp
<%@ page language="java" contentType="text/html; charset=UTF-8"
    pageEncoding="UTF-8"%>
<%@ taglib prefix="fmt" uri="http://java.sun.com/jsp/jstl/fmt" %>
    
<!DOCTYPE html PUBLIC "-//W3C//DTD HTML 4.01 Transitional//EN" "http://www.w3.org/TR/html4/loose.dtd">
<html>
<head>
<meta http-equiv="Content-Type" content="text/html; charset=UTF-8">
<title>Insert title here</title>
</head>
<body>
	<h1>成功跳转页面</h1>
</body>
</html>
````
#### 5）将项目运行起来访问 ：localhost:8080/hellowoorld

![](https://img-blog.csdnimg.cn/20190927211910474.png)
### 三丶使用 @RequestMapping 映射请求

- Spring MVC 使用 `@RequestMapping` 注解为控制器指定可以处理哪些 URL 请求
- 在控制器的`类`定义及`方法`定义处都可标注
  - *类定义*：提供初步的请求映射信息。相对于 WEB 应用的根目录
  - *方法*：提供进一步的细分映射信息。相对于类定义处的 URL。若类定义处未标注 @RequestMapping，则方法处标记的 URL 相对于` WEB 应用的根目录`
- `DispatcherServlet` 截获请求后，就通过控制器上`@RequestMapping` 提供的映射信息确定请求所对应的处理 方法。

#### 1）标准请求头

![](https://img-blog.csdnimg.cn/20190927212628134.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80MzI4NzIzOQ==,size_16,color_FFFFFF,t_70)
#### 2）@RequestMapping

@RequestMapping 的**value**、**method**、**params** 及 **heads**  分别表示***请求 URL***、***请求方法***、***请求参数***及***请求头的映射条件***，他们之间是*与*的关系，联合使用多个条件可让请求映射更加精确化。

````java
/**
	 * 可以使用 params 和 headers 来更加精确的映射请求. params 和 headers 支持简单的表达式.
	 * 
	 * @return
	 */
	@RequestMapping(value = "testParamsAndHeaders",
					params = { "username","age!=10" },
					headers = { "Accept-Language=en-US,zh;q=0.8" },
					method = RequestMethod.POST)
	public String test() {
		System.out.println("test...");
		return "success";
	}
````
#### 3）支持Ant 风格

- `?` ：匹配文件名中的一个字符

**/user/createUser?**

匹配  /user/createUser*a* 或者 user/createUser*b* 等 URL

- `*`：匹配文件名中的任意字符

**/user/*/createUser: **

匹配 /user/*aaa*/createUser 或者 /user/*bbb*/createUser 等 URL

- `**`：** 匹配多层路径

**/user/\**/createUser**

匹配  /user/createUser 或者 /user/*aaa/bbb*/createUser 等 URL

### 四丶@PathVariable

*映射 URL 绑定的占位符*

- 带占位符的 URL 是 Spring3.0 新增的功能，该功能在  SpringMVC 向 REST 目标挺进发展过程中具有里程碑的意义
- 通过`@PathVariable`可以将 URL 中占位符参数绑定到控制器处理方法的入参中：URL 中的 `{xxx}` 占位符可以通过`@PathVariable("xxx")` 绑定到操作方法的入参中。

````java
/**
 * @PathVariable 可以来映射 URL 中的占位符到目标方法的参数中.
 */
@RequestMapping("/testPathVariable/{id}")
public String test(@PathVariable("id") Integer id) {
	System.out.println("id: " + id);
	return "success";
}
````
### 五丶REST风格
> `REST`：即 Representational State Transfer。（资源）表现层状态转化。是目前最流行的一种互联网软件架构。它结构清晰、符合标准、易于理解、扩展方便，  所以正得到越来越多网站的采用

*示例*：

- /order/1	HTTP `GET` ：***得到*** id = 1 的 order 记录

- /order/1	HTTP `DELETE`：***删除*** id = 1的 order 记录

- /order/1	HTTP `PUT`：***更新*** id = 1的 order 记录

- /order	HTTP `POST`：***新增*** 一条order记录

### 六丶@RequestParam 绑定请求参数值

- 在处理方法入参处使用 @RequestParam 可以把请求参数传递给请求方法
  - `value`：参数名
  - `required`：是否必须；默认为 true，表示请求参数中必须包含对应的参数，若不存在，将抛出异常

````java
/**
 * @RequestParam 来映射请求参数. value 值即请求参数的参数名 required 该参数是否必须. 默认为 true
 *               defaultValue 请求参数的默认值
 */
@RequestMapping(value = "/testRequestParam")
public String testRequestParam(
		@RequestParam(value = "username") String username,
		@RequestParam(value = "age", required = false, defaultValue = "0") int age) {
	System.out.println("testRequestParam, username: " + username + ", age: " + age);
	return "success";
}
````
### 七丶@RequestHeader 绑定请求报头的属性值
````java
/**
 *   映射请求头信息 用法同 @RequestParam
 */
@RequestMapping("/testRequestHeader")
public String testRequestHeader(
		@RequestHeader(value = "Accept-Language") String al) {
	System.out.println("testRequestHeader, Accept-Language: " + al);
	return "success";
}
````
### 八丶@CookieValue 绑定请求中的 Cookie 值
````java
/**
 * @CookieValue: 映射一个 Cookie 值. 属性同 @RequestParam
 */
@RequestMapping("/testCookieValue")
public String testCookieValue(@CookieValue("JSESSIONID") String sessionId) {
	System.out.println("testCookieValue: sessionId: " + sessionId);
	return "success";
}
````
### 九丶POJO 对象绑定请求参数值
````java
/**
 * Spring MVC 会按请求参数名和 POJO 属性名进行自动匹配， 自动为该对象填充属性值。支持级联属性。
 * 如：dept.deptId、dept.address.tel 等
 */
@RequestMapping("/testPojo")
public String testPojo(User user) {
	System.out.println("testPojo: " + user);
	return "success";
}
````
### 十丶MVC 中Handler 方法可以接受的ServletAPI 类型的参数
- *HttpServletRequest*

- *HttpServletResponse*

- *HttpSession*

- *Writer*

- java.security.Principal

- Locale

- InputStream

- OutputStream

- Reader

### 十一丶处理模型数据
1）`ModelAndView`

处理方法返回值类型为 ModelAndView时，方法体可通过该对象添加模型数据，ModelAndView中既包含**视图信息**，也包含**模型数据信息**。

2）`Map 及 Model`

 入参为  **org.springframework.ui.Model**、**org.springframework.ui.ModelMap** 或 **java.uti.Map** 时，处理方法返回时，Map  中的数据会自动添加到模型中。

3）`@SessionAttributes:`

将模型中的某个属性暂存到**HttpSession**中，以便多个请求之间可以共享这个属性（从**session**域中获取）

- 若希望在多个请求之间共用某个模型属性数据，则可以在 控制器*类*上标注一个 `@SessionAttributes`，Spring MVC 将在模型中对应的属性暂存到 `HttpSession` 中。

- `@SessionAttributes `除了可以通过属性名指定需要放到会话中的属性外，还可以通过模型属性的对象类型指定哪些模型属性需要放到会话中

  1）*@SessionAttributes(types=User.class)*： 会将隐含模型中所有类型为 **User.class** 的属性添加到会话中

  2）*@SessionAttributes(value={“user1”, “user2”})*：会将隐含模型中对象名为**user1**，**user2** 的属性添加到会话中

  3）*@SessionAttributes(types={User.class, Dept.class})*：会将隐含模型中所有类型为 **User.class**，**Dept.class** 的属性添加到会话中

  4）*@SessionAttributes(value={“user1”, “user2”},  types={Dept.class})*：会将隐含模型中对象名为**user1**，**user2** 的属性和所有类型为 **Dept.class** 的属性添加到会话中

4）`@ModelAttribute`

 方法入参标注该注解后, 入参的对象就会放到数据模型中

### 十二丶@ModelAttribute

- 在方法定义上使用 `@ModelAttribute` 注解：Spring MVC在调用目标处理方法前，会先逐个调用在方法级上标注了`@ModelAttribute` 的方法。
- 在方法的入参前使用 `@ModelAttribute` 注解：
  - 可以从隐含对象中获取隐含的模型数据中获取对象，再将请求参数绑定到对象中，再传入入参
  - 将方法入参对象添加到模型中

##### *示例*：

![](https://ae01.alicdn.com/kf/Hd251238206aa4c5e97a7180a065ac60cc.jpg)

### 十三丶视图和视图解析器

- 请求处理方法执行完成后，最终返回一个 `ModelAndView`  对象。对于那些返回 **String**，**View** 或 **ModeMap** 等类型的处理方法，Spring MVC 也会在内部将它们装配成一个  ModelAndView 对象，它包含了*逻辑名*和*模型对象的视图*。
- Spring MVC 借助视图解析器（`ViewResolver`）得到最终的视图对象（`View`），最终的视图可以是 `JSP `，也可能是  `Excel`、`JFreeChart`等各种表现形式的视图。
- 对于最终究竟采取何种视图对象对模型数据进行渲染，处理器并不关心，处理器工作重点聚焦在生产模型数据的工 作上，从而实现 MVC 的充分解耦。

#### 1）视图 

我们只需要实现View这个接口就可以自定义视图

##### *示例*：
````java
@Component
public class HelloView implements View{
	@Override
	public String getContentType() {
		return "text/html";
	}
	@Override
	public void render(Map<String, ?> model, HttpServletRequest request,
			HttpServletResponse response) throws Exception {
		response.getWriter().print("hello view, time: " + new Date());
	}
}
````
````java
@RequestMapping("/testView")
	public String testView(){
		System.out.println("testView");
		return "helloView";	//这里返回的就是我们自定义的视图
	}
````

![](https://img-blog.csdnimg.cn/2019092722493042.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80MzI4NzIzOQ==,size_16,color_FFFFFF,t_70)

#### 2）视图解析器

- SpringMVC 为逻辑视图名的解析提供了不同的策略，可以在 Spring WEB 上下文中配置***一种***或***多种***解析策略，并指定他们之间的***先后顺序***。每一种映射策略对应一个具体的视图解析器实现类。
- 视图解析器的作用比较单一，***将逻辑视图解析为一个具体的视图对象***。
- 所有的视图解析器都必须实现 **ViewResolver** 接口。
- 程序员可以选择一种视图解析器或混用多种视图解析器。
- 每个视图解析器都实现了**Ordered接口**并开放出一个 order 属性，可  以通过**order 属性指定解析器的优先顺序**，order	**越小优先级越高**。
- SpringMVC 会按视图解析器顺序的优先顺序对逻辑视图名进行解析，直到解析成功并返回视图对象，否则将抛出 `ServletException` 异常

*SpringMVC.xml中的配置*：

````xml
<!-- 配置视图解析器: 如何把 handler 方法返回值解析为实际的物理视图 -->
<bean class="org.springframework.web.servlet.view.InternalResourceViewResolver">
	<property name="prefix" value="/WEB-INF/views/"></property>
	<property name="suffix" value=".jsp"></property>
</bean>
	
<!-- 配置视图  BeanNameViewResolver 解析器: 使用视图的名字来解析视图 -->
<!-- 通过 order 属性来定义视图解析器的优先级, order 值越小优先级越高 -->
<bean class="org.springframework.web.servlet.view.BeanNameViewResolver">
	<property name="order" value="100"></property>
</bean>
````
![](https://img-blog.csdnimg.cn/20190927225514697.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80MzI4NzIzOQ==,size_16,color_FFFFFF,t_70)

![看完不赞，都是坏蛋](https://imgconvert.csdnimg.cn/aHR0cHM6Ly93d3cuNTJkb3V0dS5jbi9zdGF0aWMvdGVtcC9waWMvOWJkNjhkMTUwZjA3ODdjNTYwYTQzOWRhMzU5YTU4MGEucG5n?x-oss-process=image/format,png#pic_center)

> 今天的你多努力一点，明天的你就能少说一句求人的话！
>
> *我是小菜，一个和你一起学习的男人。* `💋`

