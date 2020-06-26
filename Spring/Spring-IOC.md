大家好，我是小菜，一个渴望在互联网行业做到蔡不菜的小菜。可柔可刚，点赞则柔，白嫖则刚！
**死鬼~看完记得给我来个三连哦！**

![](https://user-gold-cdn.xitu.io/2020/4/11/17169c46045528af?w=240&h=224&f=jpeg&s=7529)

> 本文主要介绍 `Spring 中的核心之一 IOC`
> 如有需要，可以参考
> 如有帮助，不忘 **点赞** ❥

### 什么是Spring：

- Spring 是一个开源框架
- Spring 为简化企业级应用开发而生. 使用 Spring 可以使简单的 JavaBean 实现以前只有 EJB 才能实现的功能
- Spring 是一个 IOC(DI) 和 AOP 容器框架.

### 具体描述：

- 轻量级：Spring 是非侵入性的 - 基于 Spring 开发的应用中的对象可以不依赖于 Spring 的 API
- 依赖注入(DI --- dependency injection、IOC)
- 面向切面编程(AOP --- aspect oriented programming)
- 容器: Spring 是一个容器, 因为它包含并且管理应用对象的生命周期
- 框架: Spring 实现了使用简单的组件配置组合成一个复杂的应用. 在 Spring 中可以使用 XML 和 Java 注解组合这些对象
- 一站式：在 IOC 和 AOP 的基础上可以整合各种企业应用的开源框架和优秀的第三方类库 （实际上 Spring 自身也提供了展现层的 SpringMVC 和 持久层的 Spring JDBC）

### Spring 核心模块：

![在这里插入图片描述](https://img-blog.csdnimg.cn/20190919095047273.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80MzI4NzIzOQ==,size_16,color_FFFFFF,t_70)
#### IOC 和 DI

- IOC(Inversion of Control)：其思想是反转资源获取的方向. 传统的资源查找方式要求组件向容器发起请求查找资源. 作为回应, 容器适时的返回资源. 而应用了 IOC 之后, 则是容器主动地将资源推送给它所管理的组件, 组件所要做的仅是选择一种合适的方式来接受资源. 这种行为也被称为查找的被动形式。

- DI(Dependency Injection) — IOC 的另一种表述方式：即组件以一些预先定义好的方式(例如: setter 方法)接受来自如容器的资源注入. 相对于 IOC 而言，这种表述更直接。

***

###  搭建 Spring 开发环境：

#### 1）所需Jar包

![在这里插入图片描述](https://img-blog.csdnimg.cn/20190919095233667.png)
#### 2）Spring配置文件
一个典型的 Spring 项目需要创建一个或多个 Bean 配置文件, 这些配置文件用于在 Spring IOC 容器里配置 Bean. Bean 的配置文件可以放在 classpath 下, 也可以放在其它目录下.

![在这里插入图片描述](https://img-blog.csdnimg.cn/20190919100001595.png)
####  3）例子

 - ##### 创建一个类：
````java
public class Person {

	private String name;
	
	public Person() {
		System.out.println("Person's constructor...");
	}

	public Person(String name) {
		this.name = name;
	}

	public String getName() {
		return name;
	}

	public void setName(String name) {
		this.name = name;
	}
}

````
- ##### 传统方式获取这个类：
	````java
	Person person = new Person();
	person.setName("Cbuc");
	person.hello();
	````
- ##### Spring 管理：
	+ 在beans.xml中注入bean：
	````java
	/** 
	* class： 通过全类名的方式配置Bean
	*    id：Bean的名称； 
	* 			- 在 IOC 容器中必须是惟一的；
	* 			- 若 id 没有指定，Spring 自动将权限定性类名作为 Bean 的名字
	* 			- id 可以指定多个名字，名字之间可用逗号、分号、或空格分隔 
	*/
	/**
	* 依赖注入的方式
	* 		1）属性注入
	* 		2）构造器注入
	* 		3）工厂方法注入（很少使用，不推荐）
	*/
	<!-- 通过 setter 方法注入属性值 -->
	<bean id="person1" class="cbuc.life.test_01.Person">
		<!-- 为属性赋值 -->
		<!-- 通过属性注入: 通过 setter 方法注入属性值 -->
		<property name="name" value="Cbuc"></property>
	</bean>
	
	<!-- 通过构造器注入属性值 -->
	<bean id="person2" class="cbuc.life.test_01.Person">
		<!-- 要求: 在 Bean 中必须有对应的构造器.  -->
		<constructor-arg value="Cbuc"></constructor-arg>
	</bean>
	````
	+ 注入完成，获取Bean：
	````java
	/**
	*	在 Spring IOC 容器读取 Bean 配置创建 Bean 实例之前, 必须对它进行实例化. 只有在容器实例化后, 才可以从 IOC 容器里获取 Bean 实例并使用.
	*	Spring 提供了两种类型的 IOC 容器实现. 
	*			1）BeanFactory: IOC 容器的基本实现.
	*			2）ApplicationContext: 提供了更多的高级特性. 是 BeanFactory 的子接口.
	*	BeanFactory 是 Spring 框架的基础设施，面向 Spring 本身；ApplicationContext 面向使用 Spring 框架的开发者，几乎所有的应用场合都直接使用 ApplicationContext 而非底层的 BeanFactory
	*/
	
	//1.  Spring 装配 IOC
	ApplicationContext ctx = new ClassPathXmlApplicationContext("beans.xml");
	//2. 通过 IOC 获取 bean "person1"  通过指定ID的方式
	Person person = (Person) ctx.getBean("Person2");

	//通过指定类型的方式，弊端：如果IOC中配置了多个Person类，那么将会不知道为你注入那个Bean
	//Person perosn1 = ctx.getBean(Person.class);
	````
****
### 小菜与你小结

#### 1） 构造方法注入引入的问题

*场景1：***当一个类中有两个构造方法**

new Person("Cbuc",22,"男")：我想使用的是第二个构造方法给age赋值,但是却进入到了第一个构造方法中,为salary赋值.

![](https://img-blog.csdnimg.cn/20190919110435251.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80MzI4NzIzOQ==,size_16,color_FFFFFF,t_70)
![](https://img-blog.csdnimg.cn/20190919111103372.png)

***解决:***	按类型匹配入参

![](https://img-blog.csdnimg.cn/20190919111008336.png)
成功为age赋值:
![](https://img-blog.csdnimg.cn/20190919111018723.png)

*场景2：***当一个类中有个构造方法**

 bean注入属性的时候没有按照构造方法的顺序注入

![](https://img-blog.csdnimg.cn/20190919111259961.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80MzI4NzIzOQ==,size_16,color_FFFFFF,t_70)
![](https://img-blog.csdnimg.cn/20190919112847503.png)
那么获得Bean的时候成功报错
![](https://img-blog.csdnimg.cn/20190919113015720.png)

***解决:***	按索引匹配入参

![](https://img-blog.csdnimg.cn/2019091911312495.png)
#### 2）字面值
-  可用字符串表示的值，可以通过 `<value>` 元素标签或 `value 属性`进行注入。

- 基本数据类型及其封装类、String 等类型都可以采取`字面值注入`的方式

- 若字面值中包含特殊字符，可以使用` <![CDATA[]]>` 把字面值包裹起来。

````java
<bean id="person4" class="cbuc.life.test_01.Person">
	<property name="name">
		<!-- 若字面值中包含特殊字符, 则可以使用 DCDATA 来进行赋值 -->
		<value><![CDATA[<Cbuc>]]></value>
	</property>
	<property name="age" value="22"></property>
	<property name="gender" value="1"></property>
</bean>
````
![](https://img-blog.csdnimg.cn/20190919154037686.png)
#### 3）引用其它 Bean
*场景：* **Person 中需要一个 Car 类**

![](https://img-blog.csdnimg.cn/20190919154251926.png)
![](https://img-blog.csdnimg.cn/20190919154537828.png)

***解决：***

首先在IOC中注入Car：

````java
<bean id="Mycar" class="cbuc.life.test_01.Car">
	<property name="maxSpeed" value="220"></property>
	<property name="price" value="200000"></property>
	<property name="brand" value="WEY"></property>
</bean>
````
*方法一：*通过 **ref** 在person中引用：

![](https://img-blog.csdnimg.cn/20190919155314980.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80MzI4NzIzOQ==,size_16,color_FFFFFF,t_70)
![在这里插入图片描述](https://img-blog.csdnimg.cn/20190919155355103.png)

*方法二：* **内部Bean**（注意内部Bean只能当前Bean使用，外部不能引用！）

![](https://img-blog.csdnimg.cn/20190919155626243.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80MzI4NzIzOQ==,size_16,color_FFFFFF,t_70)
![在这里插入图片描述](https://img-blog.csdnimg.cn/20190919155701696.png)

#### 4）设置级联属性

![](https://img-blog.csdnimg.cn/20190919155918640.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80MzI4NzIzOQ==,size_16,color_FFFFFF,t_70)
![在这里插入图片描述](https://img-blog.csdnimg.cn/20190919160203260.png)

#### 5）设置集合属性

- `List`

  ![](https://img-blog.csdnimg.cn/20190919161128495.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80MzI4NzIzOQ==,size_16,color_FFFFFF,t_70)
  ![](https://img-blog.csdnimg.cn/20190919161139162.png)
- `Map`

  ![](https://img-blog.csdnimg.cn/20190919161740291.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80MzI4NzIzOQ==,size_16,color_FFFFFF,t_70)
  ![](https://img-blog.csdnimg.cn/20190919161823479.png)
- `Set与List用法一致`
- `属性集合`

  ![](https://img-blog.csdnimg.cn/20190919162343691.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80MzI4NzIzOQ==,size_16,color_FFFFFF,t_70)
  ![在这里插入图片描述](https://img-blog.csdnimg.cn/20190919162323491.png)
- `数组`

  ![](https://img-blog.csdnimg.cn/20190919162724525.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80MzI4NzIzOQ==,size_16,color_FFFFFF,t_70)
  ![](https://img-blog.csdnimg.cn/20190919162708155.png)

#### 6） p 命名空间的使用

![](https://img-blog.csdnimg.cn/20190919163912253.png)
![](https://img-blog.csdnimg.cn/201909191639185.png)
![](https://img-blog.csdnimg.cn/20190919163956219.png)

#### 7）自动装配

- ` byType`(根据类型自动装配):
	
	若 IOC 容器中有多个与目标 Bean 类型一致的 Bean. 在这种情况下, Spring 将无法判定哪个 Bean 最合适该属性, 所以不能执行自动装配.
- `byName`(根据名称自动装配):
	

必须将目标 Bean 的名称和属性名设置的完全相同.
	![](https://img-blog.csdnimg.cn/20190919164949320.png)
-  `Constructor`(通过构造器自动装配) ： 不推荐使用

#### 8）Bean中的继承（**parent**）

 - 子 Bean 从父 Bean 中继承配置, 包括 Bean 的属性配置
	
	![](https://img-blog.csdnimg.cn/20190920214348600.png)
 - 子 Bean 也可以覆盖从父 Bean 继承过来的配置	![](https://img-blog.csdnimg.cn/20190920214413358.png)
 - 父 Bean 可以作为`配置模板`, 也可以作为 Bean 实例. 若只想把父 Bean 作为模板, 可以设置 <bean>的`abstract` 属性为 true, 这样 Spring 将不会实例化这个 Bean
	
	![](https://img-blog.csdnimg.cn/20190920214445165.png)
 - 并不是 <bean> 元素里的所有属性都会被继承. 比如: `autowire`,` abstract` 等.
 - 可以忽略父 Bean 的 class 属性, 让子 Bean 指定自己的类, 而共享相同的属性配置. 但此时 abstract 必须设为 true
#### 9）依赖 Bean 配置
 - Spring 允许用户通过 `depends-on` 属性设定 Bean 前置依赖的Bean，前置依赖的 Bean 会在本 Bean 实例化之前创建好
	
	![](https://img-blog.csdnimg.cn/20190920214843623.png)
 -  如果前置依赖于多个 Bean，则可以通过逗号，空格或的方式配置 Bean 的名称
#### 10）使用外部属性文件
 - Spring 提供了一个 `PropertyPlaceholderConfigurer` 的 `BeanFactory` 后置处理器, 这个处理器允许用户将 Bean 配置的部分内容外移到属性文件中. 可以在 Bean 配置文件里使用形式为 `${var}` 的变量, PropertyPlaceholderConfigurer 从属性文件里加载属性, 并使用这些属性来替换变量.
	
	![](https://img-blog.csdnimg.cn/20190920215120618.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80MzI4NzIzOQ==,size_16,color_FFFFFF,t_70)
#### 11）Spring表达式语言（**SpEL**）

> - Spring 表达式语言（简称SpEL）：是一个支持运行时查询和操作对象图的强大的表达式语言。
> - 语法类似于 EL：SpEL 使用 #{…} 作为定界符，所有在大框号中的字符都将被认为是 SpEL
> - SpEL 为 bean 的属性进行动态赋值提供了便利

*通过 SpEL 可以实现：*

+  通过 bean 的 id 对 bean 进行引用
+  调用方法以及引用对象中的属性
+  计算表达式的值
+ 正则表达式的匹配

  ![](https://img-blog.csdnimg.cn/201909202204337.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80MzI4NzIzOQ==,size_16,color_FFFFFF,t_70)

- **字面量的表示：**

  + 整数：`<property name="count" value="#{5}"/>`

  + 小数：`<property name="frequency" value="#{89.7}"/>`

  + 科学计数法：`<property name="capacity" value="#{1e4}"/>`

  + String可以使用单引号或者双引号作为字符串的定界符号：

    `<property name="name" value="#{'Chuck'}"/>` 

    `<property name='name' value='#{"Chuck"}'/>`

  + Boolean：`<property name="enabled" value="#{false}"/>`

#### 12）Bean 的生命周期

1. 在Person类中自定义 Init 和 destroy 方法

![](https://img-blog.csdnimg.cn/20190920224042844.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80MzI4NzIzOQ==,size_16,color_FFFFFF,t_70)

2. 在注入Bean的时候声明两个方法

![](https://img-blog.csdnimg.cn/20190920223906610.png)

#### 13）在 classpath 中扫描组件
- 组件扫描(`component scanning`):  Spring 能够从 classpath 下自动扫描, 侦测和实例化具有特定注解的组件.

- **特定组件包括:**
	
	+  @Component: 基本注解, 标识了一个受 Spring 管理的组件
	+  @Respository: 标识持久层组件
	+  @Service: 标识服务层(业务层)组件
	+  @Controller: 标识表现层组件
	
- 对于扫描到的组件, **Spring 有默认的命名策略:**

  - 使用非限定类名, 第一个字母小写.

  - 在注解中通过 value 属性值标识组件的名称

- 当在组件类上使用了特定的注解之后, 还需要在 **Spring 的配置文件**中声明 `<context:component-scan>`
	
	+  base-package 属性指定一个需要扫描的基类包，Spring 容器将会扫描这个基类包里及其子包中的所有类.
	+  当需要扫描多个包时, 可以使用逗号分隔.
	+ 如果仅希望扫描特定的类而非基包下的所有类，可使用 **resource-pattern** 属性过滤特定的类，示例：

	  ![在这里插入图片描述](https://img-blog.csdnimg.cn/20190920225330498.png)
	  ![在这里插入图片描述](https://img-blog.csdnimg.cn/20190920225406275.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80MzI4NzIzOQ==,size_16,color_FFFFFF,t_70)



![看完不赞，都是坏蛋](https://imgconvert.csdnimg.cn/aHR0cHM6Ly93d3cuNTJkb3V0dS5jbi9zdGF0aWMvdGVtcC9waWMvOWJkNjhkMTUwZjA3ODdjNTYwYTQzOWRhMzU5YTU4MGEucG5n?x-oss-process=image/format,png#pic_center)

> 今天的你多努力一点，明天的你就能少说一句求人的话！
> `很久很久之前，有个传说，据说：`
> **看完不赞，都是坏蛋**