大家好，我是小菜，一个渴望在互联网行业做到蔡不菜的小菜。可柔可刚，点赞则柔，白嫖则刚！
**死鬼~看完记得给我来个三连哦！**

![](https://user-gold-cdn.xitu.io/2020/4/11/17169c46045528af?w=240&h=224&f=jpeg&s=7529)

>本文主要介绍 `Spring 中IOC的注解开发`
>如有需要，可以参考
>如有帮助，不忘 **点赞** ❥
>
>创作不易，白嫖无义！

### 前戏概要：
#### XML 配置注入类：

- 创建一个需要注入的类

- 创建一个Spring 配置文件

- 然后通过`<bean></bean>` 的方式注册

#### 基于注解注入：
- 编写一个Person类：

````java
public class Person {
	private String name;
	private Integer age;
	//省略 get / set 方法
}
````
- 编写一个配置类：

````java
//配置类==配置文件
@Configuration  //告诉Spring这是一个配置类
public class MainConfig {
	//给容器中注册一个Bean;类型为返回值的类型，id默认是用方法名作为id
	//也可以通过@Bean(value)的方式指定ID
	@Bean("person")
	public Person person01(){
		return new Person("Cbuc", 22);
	}
}
````
- 编写一个测试类：

````java
public void test01(){
    //可以注意到之前基于xml的时候是 new ClassPathXmlApplicationContext() ，现在基于注解是 new AnnotationConfigApplicationContext()
    AnnotationConfigApplicationContext applicationContext = new AnnotationConfigApplicationContext(MainConfig.class);
    Person person = (Person) applicationContext.getBean("person");
    System.out.println(person);
}
````
`成功获取到注入的类：`

![](https://img-blog.csdnimg.cn/20190922103026422.png)

### 步骤详解

#### 1）包扫描
*XML扫描方式*：可以配置配置一些过滤条件

````xml
<context:component-scan base-package="cbuc.lie" use-default-filters="false"></context:component-scan>
````
*注解扫描方式*：（`@ComponentScan`）

````java
/**
 * @ComponentScan  value:指定要扫描的包
 * excludeFilters = Filter[] ：指定扫描的时候按照什么规则排除那些组件
 * includeFilters = Filter[] ：指定扫描的时候只需要包含哪些组件
 * FilterType.ANNOTATION：按照注解
 * FilterType.ASSIGNABLE_TYPE：按照给定的类型；
 * FilterType.ASPECTJ：使用ASPECTJ表达式
 * FilterType.REGEX：使用正则指定
 * FilterType.CUSTOM：使用自定义规则
 */

//配置类==配置文件
@ Configuration     //告诉Spring这是一个配置类
@ ComponentScans(	//componentScan 的组合
		value = {
				@ ComponentScan(value="cbuc.life.bean",includeFilters = {
						@Filter(type=FilterType.ANNOTATION,classes={Component.class})
					/*	@Filter(type=FilterType.ASSIGNABLE_TYPE,classes={BookService.class}),
						@Filter(type=FilterType.CUSTOM,classes={MyTypeFilter.class}) */
						},
						useDefaultFilters = false)
				}
)
public class MainConfig {
	//给容器中注册一个Bean;类型为返回值的类型，id默认是用方法名作为id
	@Bean("person")
	public Person person01(){
		return new Person("Cbuc", 22);
	}
}
````
*- FilterType.CUSTOM的使用*

1. 定义一个TypeFilter的实现类MyTypeFilter

````java
public class MyTypeFilter implements TypeFilter {
	/**
	 * metadataReader：读取到的当前正在扫描的类的信息
	 * metadataReaderFactory:可以获取到其他任何类信息的
	 */
	public boolean match(MetadataReader metadataReader, MetadataReaderFactory metadataReaderFactory)
			throws IOException {
		//获取当前类注解的信息
		AnnotationMetadata annotationMetadata = metadataReader.getAnnotationMetadata();
		//获取当前正在扫描的类的类信息
		ClassMetadata classMetadata = metadataReader.getClassMetadata();
		//获取当前类资源（类的路径）
		Resource resource = metadataReader.getResource();
		
		String className = classMetadata.getClassName();
		System.out.println("--->"+className);
		if(className.contains("er")){	//放行类名中含有“er”的类
			return true;
		}
		return false;
	}
}
````
2. 通过`@Filter(type=FilterType.CUSTOM,classes={MyTypeFilter.class} `在classes中引入即可

#### 2）作用域
通过`@Scope`：调整作用域（默认是单实例的）

- **singleton**：单实例的（默认值），IOC容器启动会调用方法创建对象放到 IOC 容器中。 以后每次获取就是直接从容器					***map.get()***中拿，

- **request**：同一次请求创建一个实例

- **session**：同一个session创建一个实例

![ ](https://img-blog.csdnimg.cn/20190922125413232.png)

#### 3）懒加载
*单实例bean：*默认在容器启动的时候创建对象；

*懒加载：*容器启动不创建对象。第一次使用(获取)Bean创建对象，并初始化；

![](https://img-blog.csdnimg.cn/20190922125749200.png)
#### 4） 按条件注入`@Conditional({Condition}) `
1. 编写 Condition 的实现类 **WindowsCondition**

````java
//判断当前环境是否是windows系统
public class WindowsCondition implements Condition {
	public boolean matches(ConditionContext context, AnnotatedTypeMetadata metadata) {
		Environment environment = context.getEnvironment();
		String property = environment.getProperty("os.name");
		if(property.contains("Windows")){
			return true;
		}
		return false;
	}
}
````
2. 根据条件注入Bean

![ ](https://img-blog.csdnimg.cn/20190922132028917.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80MzI4NzIzOQ==,size_16,color_FFFFFF,t_70)

3. 我们除了可以把 @Conditional 注解放在方法上 , 还可以放在类上(优先级比方法上高)

![ ](https://img-blog.csdnimg.cn/2019092213312531.png)

#### 5） 给容器中导入一个组件`@Import` 
*给容器中注册组件:*

1. 包扫描+组件标注注解（@Controller/@Service/@Repository/@Component）

2. 在配置类在通过@Bean 注册

3.  @Import (快速给容器中导入一个组件)

   - 1）@Import(要导入到容器中的组件)；容器中就会自动注册这个组件，id默认是全类名

     ![](https://img-blog.csdnimg.cn/20190922145445942.png)

   - 2）ImportSelector:返回需要导入的组件的全类名数组

     **编写ImportSelector的实现类**

     ````java
     public class MyImportSelector implements ImportSelector {
     	//返回值，就是到导入到容器中的组件全类名
     	//AnnotationMetadata:当前标注@Import注解的类的所有注解信息
     	public String[] selectImports(AnnotationMetadata importingClassMetadata) {
     		// TODO Auto-generated method stub
     		//importingClassMetadata
     		//方法不要返回null值
     		return new String[]{"cbuc.life.bean.Blue","cbuc.life.bean.Yellow"};
     	}
     }
     ````

     **在@Import中声明**

     ![](https://img-blog.csdnimg.cn/20190922145747271.png)

   - 3）ImportBeanDefinitionRegistrar:手动注册bean到容器中

     **编写ImportBeanDefinitionRegistrar的实现类**

     ```java
     public class MyImportBeanDefinitionRegistrar implements ImportBeanDefinitionRegistrar {
     	/**
     	 * AnnotationMetadata：当前类的注解信息
     	 * BeanDefinitionRegistry:BeanDefinition注册类；
     	 * 		把所有需要添加到容器中的bean；调用
     	 * 		BeanDefinitionRegistry.registerBeanDefinition手工注册进来
     	 */
     	public void registerBeanDefinitions(AnnotationMetadata importingClassMetadata, BeanDefinitionRegistry registry) {
     		boolean definition = registry.containsBeanDefinition("Red");
     		boolean definition2 = registry.containsBeanDefinition("Blue");
     		if(definition && definition2){	//如果 IOC 容器中含有 Red 和 Blue 再注册 RainBow 类
     			//指定Bean定义信息；（Bean的类型，Bean。。。）
     			RootBeanDefinition beanDefinition = new RootBeanDefinition(RainBow.class);
     			//注册一个Bean，指定bean名
     			registry.registerBeanDefinition("rainBow", beanDefinition);
     		}
     	}
     }
     ```

     **在@Import中声明**

     ![](https://img-blog.csdnimg.cn/20190922150339772.png)

#### 6）FactoryBean（工厂Bean）
1）默认获取到的是工厂bean调用getObject创建的对象

2）要获取工厂Bean本身，我们需要给id前面加一个&colorFactoryBean

1. 编写FactoryBean的实现类:

````java
//创建一个Spring定义的FactoryBean
public class ColorFactoryBean implements FactoryBean<Color> {
	//返回一个Color对象，这个对象会添加到容器中
	public Color getObject() throws Exception {
		System.out.println("ColorFactoryBean...getObject...");
		return new Color();
	}
	public Class<?> getObjectType() {
		return Color.class;
	}
	//是单例？
	//true：这个bean是单实例，在容器中保存一份
	//false：多实例，每次获取都会创建一个新的bean；
	public boolean isSingleton() {
		return false;
	}
}
````
2. 在配置类中注册：

````java
	@Bean
	public ColorFactoryBean colorFactoryBean(){
		return new ColorFactoryBean();
	}
````
3. 注意点：

此时虽然注入的是colorFactoryBean，但是获取到的是Color
![ ](https://img-blog.csdnimg.cn/2019092217071279.png)
如果想要获取colorFactoryBean，就在id前面加一个&
![ ](https://img-blog.csdnimg.cn/20190922170952861.png)

#### 7）生命周期
**bean的生命周期：**

bean创建---初始化----销毁的过程

**容器管理bean的生命周期；我们可以自定义初始化和销毁方法；容器在bean进行到当前生命周期的时候来调用我们自定义的初始化和销毁方法**

1）指定初始化和销毁方法；

* 	通过@Bean指定`init-method`和`destroy-method`

*我们创建一个Car类，里面自定义初始化和销毁方法：*

```java
public class Car {
	public Car(){
		System.out.println("car constructor...");
	}
	public void init(){
		System.out.println("car ... init...");
	}
	public void detory(){
		System.out.println("car ... detory...");
	}
}
```

*注册到 IOC容器中：*

```java
//在 Bean 注解里面指定初始化和销毁方法
@Bean(initMethod="init",destroyMethod="detory")
public Car car(){
	return new Car();
}
```

2）通过让Bean实现

*  `InitializingBean`（定义初始化逻辑）
 * 	`DisposableBean`（定义销毁逻辑）

*我们创建一个Cat类，实现InitializingBean 和 DisposableBean 两个接口：*

```java
@Component		//在配置类中开启包扫描，自动注册到 IOC 容器中
public class Cat implements InitializingBean,DisposableBean {
	public Cat(){
		System.out.println("cat constructor...");
	}
	public void afterPropertiesSet() throws Exception {
		//初始化方法
		System.out.println("cat...afterPropertiesSet...");
	}
	public void destroy() throws Exception {
		//销毁方法
		System.out.println("cat...destroy...");
	}
}
```

3）使用JSR250

* 	`@PostConstruct`：在bean创建完成并且属性赋值完成，来执行初始化方法
 * 	`@PreDestroy`：在容器销毁bean之前通知我们进行清理工作

*首先创建一个Dog类：*

```java
@Component		//在配置类中开启包扫描，自动注册到 IOC 容器中
public class Dog {
	public Dog(){
		System.out.println("dog constructor...");
	}
	//对象创建并赋值之后调用
	@PostConstruct
	public void init(){
		System.out.println("Dog....@PostConstruct...");
	}
	//容器移除对象之前
	@PreDestroy
	public void detory(){
		System.out.println("Dog....@PreDestroy...");
	}
}
```

4）**BeanPostProcessor**【interface】：bean的后置处理器

* 	在bean初始化前后进行一些处理工作；
 * 	`postProcessBeforeInitialization`：在初始化之前工作
 * 	`postProcessAfterInitialization`：在初始化之后工作

*我们创建一个 MyBeanPostProcessor 类实现 BeanPostProcessor 接口：*

````java
@Component
public class MyBeanPostProcessor implements BeanPostProcessor {
	//初始化前进行的操作
	public Object postProcessBeforeInitialization(Object bean, String beanName) throws BeansException {
		System.out.println("postProcessBeforeInitialization..."+beanName+"=>"+bean);
		return bean;
	}
	//初始化后进行的操作
	public Object postProcessAfterInitialization(Object bean, String beanName) throws BeansException {
		System.out.println("postProcessAfterInitialization..."+beanName+"=>"+bean);
		return bean;
	}
}
````
#### 8）参数赋值
*使用@Value赋值：*

1. 基本数值

2. 可以写SpEL：**#{}**

3. 可以写**${}** ：取出配置文件【properties】中的值（在运行环境变量里面的值）

![](https://img-blog.csdnimg.cn/20190922194911114.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80MzI4NzIzOQ==,size_16,color_FFFFFF,t_70)
![ ](https://img-blog.csdnimg.cn/20190922194942512.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80MzI4NzIzOQ==,size_16,color_FFFFFF,t_70)
![](https://img-blog.csdnimg.cn/20190922195014483.png)

#### 9）自动装配
> Spring利用依赖注入（DI），完成对IOC容器中中各个组件的依赖关系赋值

**一、@Autowired：自动注入**

![](https://img-blog.csdnimg.cn/20190922200321204.png)

1. 默认优先按照类型去容器中找对应的组件：

`applicationContext.getBean(BookDao.class)` 找到就赋值

2. 如果找到多个相同类型的组件，再将属性的名称作为组件的id去容器中查找：

   `applicationContext.getBean("bookDao")`

3. 使用 `@Qualifier` 指定需要装配的组件的id，而不是使用属性名

![ ](https://img-blog.csdnimg.cn/20190922200816644.png)

4. 自动装配默认一定要将属性赋值好，没有就会报错

   可以使用`@Autowired(required=false)`允许空装配

![ ](https://img-blog.csdnimg.cn/20190922200830368.png)

5. 使用 `@Primary`：让Spring进行自动装配的时候，默认使用首选

​         也可以继续使用 `@Qualifier` 指定需要装配的bean的名字，优先级比较高

![ ](https://img-blog.csdnimg.cn/20190922201002275.png)

**二、Spring还支持使用 @Resource(JSR250) 和 @Inject(JSR330) **

1. `@Resource`：
   - 可以和 **@Autowired** 一样实现自动装配功能；默认是按照组件名称进行装配的；
   - 不能支持 **@Primary** 功能且不能支持 **@Autowired（reqiured=false）**
     ![ ](https://img-blog.csdnimg.cn/20190922201226418.png)

2. @Inject:
   - 需要导入 **javax.inject** 的包，和 **Autowired **的功能一样。没有 **required=false** 的功能；
     ![ ](https://img-blog.csdnimg.cn/20190922201350798.png)

**三、@Autowired 标记位置 : 构造器，参数，方法，属性**

*构造器*：如果组件只有一个有参构造器，这个有参构造器的 `@Autowired` 可以省略，参数位置的组件还是可以自动从容器中获取

![ ](https://img-blog.csdnimg.cn/2019092220573138.png)

参数（@Autowired 可以省略）

![ ](https://img-blog.csdnimg.cn/20190922205818971.png)

*方法* ：@Bean+方法参数；参数从容器中获取;默认不写@Autowired效果是一样的；都能自动装配

1. 

![ ](https://img-blog.csdnimg.cn/20190922205753204.png)
2. 

![ ](https://img-blog.csdnimg.cn/20190922210224601.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80MzI4NzIzOQ==,size_16,color_FFFFFF,t_70)

*属性：*

![ ](https://img-blog.csdnimg.cn/20190922205705986.png)
#### 10）@Profile 指定环境
**Spring为我们提供的可以根据当前环境，动态的激活和切换一系列组件的功能（开发环境、测试环境、生产环境...）**

- `@Profile`：指定组件在哪个环境的情况下才能被注册到容器中，不指定，任何环境下都能注册这个组件
  1. 加了环境标识的bean，只有这个环境被激活的时候才能注册到容器中。默认是default环境
  2. 写在配置类上，只有是指定的环境的时候，整个配置类里面的所有配置才能开始生效
  3. 没有标注环境标识的bean,在任何环境下都是加载的
     ![](https://img-blog.csdnimg.cn/20190922214248241.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80MzI4NzIzOQ==,size_16,color_FFFFFF,t_70)

- `切换环境`

   1）使用命令行动态参数: 在虚拟机参数位置加载 -Dspring.profiles.active=test

  ![](https://img-blog.csdnimg.cn/20190922214539612.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80MzI4NzIzOQ==,size_16,color_FFFFFF,t_70)

  2）代码的方式激活某种环境

````java
//1、创建一个applicationContext
AnnotationConfigApplicationContext applicationContext = new AnnotationConfigApplicationContext();
//2、设置需要激活的环境
applicationContext.getEnvironment().setActiveProfiles("dev");
//3、注册主配置类
applicationContext.register(MainConfigOfProfile.class);
//4、启动刷新容器
applicationContext.refresh();
````

![看完不赞，都是坏蛋](https://imgconvert.csdnimg.cn/aHR0cHM6Ly93d3cuNTJkb3V0dS5jbi9zdGF0aWMvdGVtcC9waWMvOWJkNjhkMTUwZjA3ODdjNTYwYTQzOWRhMzU5YTU4MGEucG5n?x-oss-process=image/format,png#pic_center)

> 今天的你多努力一点，明天的你就能少说一句求人的话！
>
> *我是小菜，一个和你一起学习的男人。* `💋`

