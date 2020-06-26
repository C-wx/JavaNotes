大家好，我是小菜，一个渴望在互联网行业做到蔡不菜的小菜。可柔可刚，点赞则柔，白嫖则刚！
**死鬼~看完记得给我来个三连哦！**

![](https://user-gold-cdn.xitu.io/2020/4/11/17169c46045528af?w=240&h=224&f=jpeg&s=7529)

>本文主要介绍 `Spring 中 AOP的XML配置开发`
>如有需要，可以参考
>如有帮助，不忘 **点赞** ❥
>
>创作不易，白嫖无义！

### 概念了解:

- AOP(Aspect-Oriented Programming, 面向切面编程): 是一种新的方法论, 是对传统 OOP(Object-Oriented Programming, 面向对象编程) 的补充。

- AOP 的主要编程对象是切面(aspect), 而切面模块化横切关注点。

- 应用 AOP 编程时, 仍然需要定义公共功能, 但可以明确的定义这个功能在哪里, 以什么方式应用, 并且不必修改受影响的类. 这样一来横切关注点就被模块化到特殊的对象(切面)里。

  #### AOP 的好处:

  - 每个事物逻辑位于一个位置, **代码不分散, 便于维护和升级**

  - 业务模块更**简洁**, 只包含核心业务代码.

    ![](https://img-blog.csdnimg.cn/20190921140626964.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80MzI4NzIzOQ==,size_16,color_FFFFFF,t_70)

### AOP 中的术语：
- **切面(Aspect)**：横切关注点（跨越应用程序多个模块的功能)被模块化的特殊对象）

- **通知(Advice)**：切面必须要完成的工作

- **目标(Target)**： 被通知的对象

- **代理(Proxy)**：向目标对象应用通知之后创建的对象

- **连接点（Joinpoint）**：程序执行的某个特定位置：如类某个方法调用前、调用后、方法抛出异常后等。连接点由两个信息确定：

  - 方法表示的程序执行点

  - 相对点表示的方位。

    *例如 ：*ArithmethicCalculator#add() 方法执行前的连接点

    ***执行点***为 ArithmethicCalculator#add()； ***方位***为该方法执行前的位置

- **切点（pointcut）**：每个类都拥有多个连接点：例如 ArithmethicCalculator 的所有方法实际上都是连接点，即连接点是**程序类中客观存在的事务**。AOP 通过切点定位到特定的连接点。

  *例如：* 连接点相当于数据库中的记录，切点相当于查询条件。

  ***切点和连接点不是一对一的关系，一个切点匹配多个连接点***

  切点通过 org.springframework.aop.Pointcut 接口进行描述，它使用类和方法作为连接点的查询条件。

### 在 Spring 中启用 AspectJ 注解支持：
1. AspectJ 类库：
   - *com.springsource.net.sf.cglib-2.2.0.jar*
   - *com.springsource.org.aopalliance-1.0.0.jar*
   - *com.springsource.org.aspectj.weaver-1.6.8.RELEASE.jar*
   - *spring-aspects-4.0.0.RELEASE.jar*

2. 在 Spring 的配置文件中加入 `aop` 的命名空间。 

3. 基于`注解`的方式来使用 AOP
   - 在配置文件中配置自动扫描的包: `<context:component-scan base-package="cbuc.spring.aop" />`
   - 加入使 AspjectJ 注解起作用的配置: `<aop:aspectj-autoproxy></aop:aspectj-autoproxy>`
   - 为匹配的类自动生成动态代理对象. 

4. 编写切面类
   - 一个一般的 Java 类
   - 在其中添加要额外实现的功能. 

5. 配置切面
   - 切面必须是 IOC 中的 bean: 实际添加了 `@Component` 注解
   - 声明是一个切面: 添加 `@Aspect`
   - 声明通知：即额外加入功能对应的方法. 

### 实际操作一番：
*目录：*

![在这里插入图片描述](https://img-blog.csdnimg.cn/20190921142453444.png)
![在这里插入图片描述](https://img-blog.csdnimg.cn/20190921142503552.png)

**1）ArithmeticCalculator：**

````java
public interface ArithmeticCalculator {
	int add(int i, int j);
	int div(int i, int j);
}
````
**2）ArithmeticCalculatorImpl：**

````java
@Component("arithmeticCalculator")		//注册到 IOC容器中
public class ArithmeticCalculatorImpl implements ArithmeticCalculator {
	@Override
	public int add(int i, int j) {
		int result = i + j;
		return result;
	}
	@Override
	public int sub(int i, int j) {
		int result = i - j;
		return result;
	}
	@Override
	public int mul(int i, int j) {
		int result = i * j;
		return result;
	}
	@Override
	public int div(int i, int j) {
		int result = i / j;
		return result;
	}
}
````
**3）LoggingAspect：**

````java
//通过添加 @Aspect 注解声明一个 bean 是一个切面!
@Aspect
@Component
public class LoggingAspect {
	/**
	*	AspectJ 支持 5 种类型的通知注解：
	*	@Before: 前置通知, 在方法执行之前执行
	*	@After: 后置通知, 在方法执行之后执行 
	*	@AfterReturning: 返回通知, 在方法返回结果之后执行
	*	@AfterThrowing: 异常通知, 在方法抛出异常之后
	*	@Aruond: 环绕通知, 围绕着方法执行
	*/
	@Before("execution(public int cbuc.life.aop.ArithmeticCalculator.*(int, int))")
	public void beforeMethod(JoinPoint joinPoint){
		String methodName = joinPoint.getSignature().getName();
		Object [] args = joinPoint.getArgs();

		System.out.println("The method " + methodName + " begins with " + Arrays.asList(args));
	}
	@After("execution(* cbuc.life.aop.*.*(..))")
	public void afterMethod(JoinPoint joinPoint){
		String methodName = joinPoint.getSignature().getName();
		System.out.println("The method " + methodName + " ends");
	}
}
````
**4）XML中配置支持：**

````xml
	<!-- 自动扫描的包 -->
	<context:component-scan base-package="cbuc.life.aop"></context:component-scan>
	<!-- 使 AspectJ 的注解起作用 -->
	<aop:aspectj-autoproxy></aop:aspectj-autoproxy>
````
**5）启动类：**

````java
public static void main(String[] args) {
		ApplicationContext ctx = new ClassPathXmlApplicationContext("applicationContext-aop.xml");
		ArithmeticCalculator arithmeticCalculator = (ArithmeticCalculator) ctx.getBean("arithmeticCalculator");
		
		int result = arithmeticCalculator.add(1, 2);
		System.out.println("result:" + result);
		
		result = arithmeticCalculator.div(100, 10);
		System.out.println("result:" + result);
	}
````
**6）运行：**

![](https://img-blog.csdnimg.cn/2019092114371179.png)

`可以看到我们在切面中设置了前置通知和后置通知，就会在调用方法之前和之后执行我们想要的方法`

###  五种通知注解 ：
####  1）前置通知（`@Before`）
- 在**方法执行之前执行**的通知

- 使用 @Before 注解, 并将切入点表达式的值作为注解值.

![](https://img-blog.csdnimg.cn/20190921144936306.png)

####  2）后置通知（`@After`）
- 在**连接点完成之后执行**的, 即连接点返回结果或者抛出异常的时候, 下面的后置通知记录了方法的终止. 

- 一个切面可以包括一个或者多个通知.

![](https://img-blog.csdnimg.cn/20190921144944846.png)

####  3）返回通知（`@AfterReturning`）
- 无论连接点是正常返回还是抛出异常, 后置通知都会执行. 如果只想在连接点返回的时候记录日志, 应使用返回通知代替后置通知.

- 发生异常不会执行返回通知

- 在返回通知中, 只要将 `returning` 属性添加到 **@AfterReturning** 注解中, 就可以访问连接点的返回值. 该属性的值即为用来传入返回值的参数名称. 

- 必须在通知方法的签名中添加一个`同名参数`. 在运行时, Spring AOP 会通过这个参数传递返回值.

- `原始的切点表达式需要出现在 pointcut 属性中`

![](https://img-blog.csdnimg.cn/20190921145510192.png)

![](https://img-blog.csdnimg.cn/20190921150145350.png)

####  4）异常通知（`@AfterThrowing`）
- 只在连接点抛出异常时才执行异常通知

- 将 `throwing` 属性添加到 @AfterThrowing 注解中, 也可以访问连接点抛出的异常。**Throwable 是所有错误和异常类的超类. 所以在异常通知方法可以捕获到任何错误和异常**

- 如果只对某种特殊的异常类型感兴趣, 可以将参数声明为其他异常的参数类型. 然后通知就只在抛出这个类型及其子类的异常时才被执行.

![在这里插入图片描述](https://img-blog.csdnimg.cn/20190921150809280.png)

####  5）环绕通知（`@Around`）
- 环绕通知是所有通知类型中**功能最为强大**的, 能够全面地控制连接点. 甚至可以控制是否执行连接点.

- 对于环绕通知来说, 连接点的参数类型必须是 `ProceedingJoinPoint` . 它是 JoinPoint 的子接口, 允许控制何时执行, 是否执行连接点.

- 在环绕通知中需要明确调用 **ProceedingJoinPoint 的 proceed()** 方法来执行被代理的方法. 如果忘记这样做就会导致通知被执行了, 但目标方法没有被执行.

*注意*： 环绕通知的方法需要返回目标方法执行之后的结果, 即调用 joinPoint.proceed() 的返回值, 否则会出现空指针异常

![](https://img-blog.csdnimg.cn/20190921152407757.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80MzI4NzIzOQ==,size_16,color_FFFFFF,t_70)

### 基于 XML 的配置声明切面
- 除了使用 AspectJ 注解声明切面, Spring 也支持在 **Bean 配置文件中声明切面**。 这种声明是通过 **aop schema** 中的 XML 元素完成的.

- 正常情况下，**基于注解的声明要优先于基于 XML 的声明**。 ***通过 AspectJ 注解, 切面可以与 AspectJ 兼容, 而基于 XML 的配置则是 Spring 专有的***。由于 AspectJ 得到越来越多的 AOP 框架支持, 所以以注解风格编写的切面将会有更多重用的机会.

#### 目录
![](https://img-blog.csdnimg.cn/20190921155725293.png)
![](https://img-blog.csdnimg.cn/20190921155741875.png)

#### 1）声明切面
````java
public class LoggingAspect {	
	public void beforeMethod(JoinPoint joinPoint){
		String methodName = joinPoint.getSignature().getName();
		System.out.println("The method " + methodName + " begins with " + Arrays.asList(joinPoint.getArgs()));
	}
	
	public void afterMethod(JoinPoint joinPoint){
		String methodName = joinPoint.getSignature().getName();
		System.out.println("The method " + methodName + " ends");
	}
	
	public void afterReturning(JoinPoint joinPoint, Object result){
		String methodName = joinPoint.getSignature().getName();
		System.out.println("The method " + methodName + " ends with " + result);
	}
	
	public void afterThrowing(JoinPoint joinPoint, Exception e){
		String methodName = joinPoint.getSignature().getName();
		System.out.println("The method " + methodName + " occurs excetion:" + e);
	}
}
````
#### 2）XML配置

- 注册Bean

````xml
	<!-- 配置 bean -->
<bean id="arithmeticCalculator" 
	class="cbuc.life.xml.ArithmeticCalculatorImpl"></bean>

<!-- 配置切面的 bean. -->
<bean id="loggingAspect"
	class="cbuc.life.xml.LoggingAspect"></bean>
````
- 配置 AOP

````xml
<aop:config>
		<!-- 配置切点表达式 -->
		<aop:pointcut expression="execution(* cbuc.life.xml.ArithmeticCalculator.*(int, int))"
			id="pointcut"/>
		<!-- 配置切面及通知 -->
		<aop:aspect ref="loggingAspect" order="2">
			<aop:before method="beforeMethod" pointcut-ref="pointcut"/>
			<aop:after method="afterMethod" pointcut-ref="pointcut"/>
			<aop:after-throwing method="afterThrowing" pointcut-ref="pointcut" throwing="e"/>
			<aop:after-returning method="afterReturning" pointcut-ref="pointcut" returning="result"/>
			<!--  
			<aop:around method="aroundMethod" pointcut-ref="pointcut"/>
			-->
		</aop:aspect>	
	</aop:config>
````
### 小菜与你小结：

#### 1）根据方法的签名来匹配各种方法:

- **execution * com.atguigu.spring.ArithmeticCalculator.*(..)**

  匹配 ArithmeticCalculator 中声明的所有方法,第一个 * 代表任意修饰符及任意返回值. 第二个 * 代表任意方法. .. 匹配任意数量的参数. 若目标类与接口与该切面在同一个包中, 可以省略包名.

- **execution public * ArithmeticCalculator.*(..)**

  匹配 ArithmeticCalculator 接口的所有公有方法.

- **execution public double ArithmeticCalculator.*(..)**

  匹配 ArithmeticCalculator 中返回 double 类型数值的方法

- **execution public double ArithmeticCalculator.*(double, ..)**

  匹配第一个参数为 double 类型的方法, .. 匹配任意数量任意类型的参数

- **execution public double ArithmeticCalculator.*(double, double)**

  匹配参数类型为 double, double 类型的方法.

#### 2）合并切入点表达式

在 AspectJ 中, 切入点表达式可以通过操作符 `&&`, `||`, `!` 结合起来. 

![、](https://img-blog.csdnimg.cn/20190921144744279.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80MzI4NzIzOQ==,size_16,color_FFFFFF,t_70)

####  3）让通知访问当前连接点的细节

可以在通知方法中声明一个类型为 **JoinPoint** 的参数. 然后就能访问链接细节. 如方法名称和参数值. 

![](https://img-blog.csdnimg.cn/2019092114473044.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80MzI4NzIzOQ==,size_16,color_FFFFFF,t_70)

#### 4）指定切面的优先级

- 在同一个连接点上应用不止一个切面时, 除非明确指定, 否则它们的优先级是**不确定**的.

- 切面的优先级可以通过实现 **Ordered 接口**或利用 **@Order** 注解指定.

- 实现 Ordered 接口, getOrder() 方法的**返回值越小, 优先级越高**.

- 若使用 @Order 注解, **序号出现在注解中**

![](https://img-blog.csdnimg.cn/20190921152523918.png)

#### 5）重用切入点定义

*引出问题*：在编写 AspectJ 切面时, 可以直接在通知注解中书写切入点表达式. 但同一个切点表达式可能会在多个通知中**重复**出现.

*解决*：

- 可以通过 **@Pointcut** 注解将一个切入点声明成简单的方法. 切入点的方法体通常是空的, 因为将切入点定义与应用程序逻辑混在一起是不合理的. 

- 切入点方法的访问控制符同时也控制着这个切入点的可见性. 如果切入点要在多个切面中**共用, 最好将它们集中在一个公共的类中**。在这种情况下, 它们必须被声明为 public。 在引入这个切入点时，必须将类名也包括在内。 如果类没有与这个切面放在同一个包中，还必须包含包名。

- 其他通知可以通过方法名称引入该切入点。

![](https://img-blog.csdnimg.cn/2019092115282266.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80MzI4NzIzOQ==,size_16,color_FFFFFF,t_70)

![看完不赞，都是坏蛋](https://imgconvert.csdnimg.cn/aHR0cHM6Ly93d3cuNTJkb3V0dS5jbi9zdGF0aWMvdGVtcC9waWMvOWJkNjhkMTUwZjA3ODdjNTYwYTQzOWRhMzU5YTU4MGEucG5n?x-oss-process=image/format,png#pic_center)

> 今天的你多努力一点，明天的你就能少说一句求人的话！
> `很久很久之前，有个传说，据说：`
> **看完不赞，都是坏蛋**