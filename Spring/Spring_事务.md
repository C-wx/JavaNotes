大家好，我是小菜，一个渴望在互联网行业做到蔡不菜的小菜。可柔可刚，点赞则柔，白嫖则刚！

**死鬼~看完记得给我来个三连哦！**

![](https://user-gold-cdn.xitu.io/2020/4/11/17169c46045528af?w=240&h=224&f=jpeg&s=7529)

>本文主要介绍 `Spring 中的 事务`
>如有需要，可以参考
>如有帮助，不忘 **点赞** ❥
>
>创作不易，白嫖无义！

### 什么是事务

- 事务管理是企业级应用程序开发中必不可少的技术,  用来确保数据的完整性和一致性。

- 事务就是一系列的动作, 它们被当做一个单独的工作单元. 这些动作要么全部完成, 要么全部不起作用

- 事务的四个关键属性(ACID)

  - `原子性(atomicity)`

    事务是一个原子操作, 由一系列动作组成. 事务的原子性确保动作要么全部完成要么完全不起作用

  - `一致性(consistency)`

     一旦所有事务动作完成, 事务就被提交. 数据和资源就处于一种满足业务规则的一致性状态中

  - `隔离性(isolation)`

    可能有许多事务会同时处理相同的数据, 因此每个事物都应该与其他事务隔离开来, 防止数据损坏

  - `持久性(durability)`

    一旦事务完成, 无论发生什么系统错误, 它的结果都不应该受到影响. 通常情况下, 事务的结果被写到持久化存储器中

### Spring 中的事务管理器的不同实现：

- `Class DatasourceTransactionManager：` 在应用程序中只需要处理一个数据源, 而且通过 JDBC 存取
- `Class JtaTransactionManager：`在 JavaEE 应用服务器上用 JTA(Java Transaction API) 进行事务管理
- `Class HibernateTransactionManager：`用 Hibernate 框架存取数据库

### 事务的实现
#### 1）用 @Transactional 注解声明式地管理事务
- 在**Spring配置文件**中声明：

````xml
<!-- 配置事务管理器 -->
<bean id="transactionManager" class="org.springframework.jdbc.datasource.DataSourceTransactionManager">
    <property name="dataSource" ref="dataSource"></property>
</bean>
<!-- 启用事务注解 -->
<tx:annotation-driven transaction-manager="transactionManager"/>
````
- 在配置文件中声明了之后我们可以用`@Transactional`注解来标注事务方法

- 根据 Spring AOP 基于代理机制，只能标注`公有方法`

- 可以在`方法`或者`类`级别上添加 @Transactional 注解，当把这个注解应用到类上时, **这个类中的所有公共方法都会被定义成支持事务处理的**

#### 2)用事务通知声明式地管理事务
在**Spring配置文件**中声明：

````xml
	<!-- 1. 配置事务管理器 -->
	<bean id="transactionManager" class="org.springframework.jdbc.datasource.DataSourceTransactionManager">
		<property name="dataSource" ref="dataSource"></property>
	</bean>	
	<!-- 2. 配置事务属性 -->
	<tx:advice id="txAdvice" transaction-manager="transactionManager">
		<tx:attributes>
			<!-- 根据方法名指定事务的属性 -->
			<tx:method name="purchase" propagation="REQUIRES_NEW"/>
			<tx:method name="get*" read-only="true"/>
			<tx:method name="find*" read-only="true"/>
			<tx:method name="*"/>
		</tx:attributes>
	</tx:advice>
	<!-- 3. 配置事务切入点, 以及把事务切入点和事务属性关联起来 -->
	<aop:config>
		<aop:pointcut expression="execution(* cbuc.life.tx.xml.service.*.*(..))"
			id="txPointCut"/>
		<aop:advisor advice-ref="txAdvice" pointcut-ref="txPointCut"/>	
	</aop:config>
````
### 小菜与你小结
![ ](https://img-blog.csdnimg.cn/20190921213506513.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80MzI4NzIzOQ==,size_16,color_FFFFFF,t_70)
#### 一丶事务传播属性
- 当事务方法被另一个事务方法调用时，**必须指定事务应该如何传播**。 例如：方法可能继续在现有事务中运行, 也可能开启一个新事务， 并在自己的事务中运行。

- 事务的传播行为可以由**传播属性**指定，Spring 定义了 7  种类传播行为。

![ ](https://img-blog.csdnimg.cn/20190921213349873.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80MzI4NzIzOQ==,size_16,color_FFFFFF,t_70)

- 我们可以在注解 @Transactional 中使用 **propagation** 指定事务的传播行为，也可以在配置文件中指定

![ ](https://img-blog.csdnimg.cn/20190921213708605.png)
![ ](https://img-blog.csdnimg.cn/2019092121374153.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80MzI4NzIzOQ==,size_16,color_FFFFFF,t_70)

#### 二丶并发事务所导致的问题
- 当同一个应用程序或者不同应用程序中的多个事务在同一个数据集上并发执行时，可能会出现许多意外的问题

- 并发事务所导致的问题可以分为下面三种类型

  1）`脏读`

   对于两个事物 T1, T2，T1  读取了已经被 T2 更新但 还没有被提交的字段。之后， 若 T2 回滚，T1读取的内容就是临时且无效的

  2）`不可重复读`

  对于两个事物 T1, T2，T1  读取了一个字段， 然后 T2 更新了该字段。之后，T1再次读取同一个字段，值就不同了

  3）`幻读`

  对于两个事物 T1, T2，T1  从一个表中读取了一个字段，然后 T2 在该表中插入了一些新的行。之后，如果 T1 再次读取同一个表，就会多出几行

#### 三丶事务的隔离级别
- 从理论上来说，事务应该彼此完全隔离，以避免并发事务所导致的问题。然而，那样会对性能产生极大的影响，因为事务必须按顺序运行

- 在实际开发中， **为了提升性能, 事务会以较低的隔离级别运行**

- 事务的隔离级别可以通过**隔离事务**属性指定

![ ](https://img-blog.csdnimg.cn/20190921214032616.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80MzI4NzIzOQ==,size_16,color_FFFFFF,t_70)

- 事务的隔离级别要得到**底层数据库引擎**的支持，而不是应用程序或者框架的支持

- Oracle 支持的 *2* 种事务隔离级别：`READ_COMMITED`，`SERIALIZABLE`

- Mysql 支持 *4* 中事务隔离级别

- 我们可以在注解 @Transactional 中使用`isolation`指定事务隔离性，也可以在配置文件中指定

![ ](https://img-blog.csdnimg.cn/20190921214156803.png)
![ ](https://img-blog.csdnimg.cn/20190921214329865.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80MzI4NzIzOQ==,size_16,color_FFFFFF,t_70)

#### 四丶设置回滚事务属性
- 默认情况下只有**未检查异常（RuntimeException）**和**Error类型的异常**会导致事务回滚。而**受检查异常**则不会

- 事务的回滚规则可以通过 @Transactional 注解的 `rollbackFor` 和 `noRollbackFor` 属性来定义，这两个属性被声明为 Class[] 类型的，因此可以为这两个属性指定多个异常类

- 遇到`rollbackFor`时必须进行回滚 ；而`norollbackFor`则反之

- 可以通过 @Transactional 配置，也可以在配置文件中配置

![ ](https://img-blog.csdnimg.cn/20190921214727571.png)				![ ](https://img-blog.csdnimg.cn/20190921215022354.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80MzI4NzIzOQ==,size_16,color_FFFFFF,t_70)

#### 五丶设置超时和只读事务属性
- 超时事务属性：事务在强制回滚之前可以保持多久，这样可以防止长期运行的事务占用资源

- 只读事务属性：表示这个事务只读取数据但不更新数据，这样可以帮助数据库引擎优化事务

- 可以通过 @Transactional 配置，也可以在配置文件中配置

![ ](https://img-blog.csdnimg.cn/20190921215245746.png)
![ ](https://img-blog.csdnimg.cn/20190921215217785.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80MzI4NzIzOQ==,size_16,color_FFFFFF,t_70)

![看完不赞，都是坏蛋](https://imgconvert.csdnimg.cn/aHR0cHM6Ly93d3cuNTJkb3V0dS5jbi9zdGF0aWMvdGVtcC9waWMvOWJkNjhkMTUwZjA3ODdjNTYwYTQzOWRhMzU5YTU4MGEucG5n?x-oss-process=image/format,png#pic_center)

> 今天的你多努力一点，明天的你就能少说一句求人的话！
>
> *我是小菜，一个和你一起学习的男人。* `💋`