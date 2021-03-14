大家好，我是小菜，一个渴望在互联网行业做到蔡不菜的小菜。可柔可刚，点赞则柔，白嫖则刚！
**死鬼~看完记得给我来个三连哦！**


![](https://gitee.com/cbuc/picture/raw/master/typora/20210221200133.jpeg)


>本文主要介绍 `Spring的全面知识点`
>
>如有需要，可以参考
>
>如有帮助，不忘 **点赞** ❥
>
>
>微信公众号已开启，**小菜良记**，没关注的同学们记得关注哦！

说到`Spring` 你能想到什么？ 有经历过面试的小伙伴相信都会在面试的时候被问到 "谈谈你对 Spring 的理解"。

对 Spring 的理解？那不就是个框架嘛，用来简化代码，提高开发效率的。话糙理不糙，事实却是如此。但是想归想，该回答还是得回答。那么 Spring 到底是什么？我们脑瓜子一闪而过！**IOC、AOP** 顺口而出，如果你没想到这两个基本的概念，那么估计就得面试等通知了~

好了，既然 **IOC、AOP** 都已经顺口而出了，面试官估计想听的也就这些，你如果扯出什么源码之类的，面试官估计也该冒出冷汗了。

![](https://gitee.com/cbuc/picture/raw/master/typora/20210221201422.png)

哈哈，说归说，闹归闹，当然具体得看你面试的等级了！当然你有底气将源码讲好，那便是你的能耐了~

可以说不懂Spring ，基本上就可以不用去面试 **Java 开发**了。那么面试官问 **Spring** 会从哪些方面来问你呢？小菜大概整理如下：

![](https://gitee.com/cbuc/picture/raw/master/typora/20210221204531.png)

看了这张图，总感觉每个点都知道，但是让你详细针对某个点进行深讲，又不知从而说起？

Spring 可以说是我们开发中的核心，我们从 Spring 可以扩展出了 **SpringMVC**、**SpringBoot**、**SpringCloud**

![](https://gitee.com/cbuc/picture/raw/master/typora/20210221205230.png)

一篇文章想搞定 **Spring** 的所有知识点，那有点天方夜谭了，饭要一口一口吃，技术要一点一点学，学好 **Spring** 那就从 **IOC** 开始！

### IOC 大法

听到`IOC`这个词，如果脑瓜子里没有想到 **"控制反转"**，那就说明你真的不了解 **IOC**。**控制反转** 不是一门技术，而是一种设计思想。我们在以往的编程中如果需要一个类，一般是通过 **new** 的方式来创建一个类对象，而 **IOC** 的思想便是我们在 **Spring** 中只需要定义这个类，然后 **Spring** 就会自动的帮我们管理这些类，我们需要的时候只需要从这个容器获取，而不用通过 **new** 的方式。

![](https://gitee.com/cbuc/picture/raw/master/20210309124444.png)

就好像这个，如果我们想要购买苹果或菠萝，我们得单独去购买，但是自从水果店的出现后我们就不用单独购买，统一从水果店获取即可

如果以上例子不能很好的理解这个概念，我们可以从代码中分析：

![](https://gitee.com/cbuc/picture/raw/master/20210309124520.png)

看完以上的图后，我们再来理解一下这一段话：**IOC** 的思想就是反转资源获取的方向，传统的资源查找方式要求组件向容器发起请求查找资源，作为回应，容器适时地返回资源。而应用了IOC之后，则是容器主动地将资源推送给他所管理的组件，组件所要做的仅是选择一种合适的方式来接收资源，这种行为也被称为查找的被动形式。

既然我们谈到了 **IOC** 的概念，那就顺便讲下什么是 **DI**。

**DI** 是 **IOC** 另一种表述的方式：即组件以一些预先定义好的方式(setter, construct...)接收来自容器的资源注入，相对于 **IOC** 来说，这种表述更加直接。

看完以上，我们明白几个概念：

1. 谁依赖谁？ `应用程序依赖于 IOC 容器`

2. 为什么需要依赖？`应用程序需要IOC容器提供对象所需要的外部资源`

3. 谁注入谁？`IOC容器向应用程序注入外部资源`

4. 注入了什么？`注入应用程序所需要的外部资源(对象，资源，常量数据等)`

#### ㈠ 如何使用

##### ① XML方式

让我们来看下案例：

现有对象`User`：

![](https://gitee.com/cbuc/picture/raw/master/20210309132657.png)



我们传统获取对象的方式是这样的：

```java
User user = new User();
user.setName("小菜");
```

这种方式也挺简单，直接通过 `new` 的方式来创建一个对象，传统也直接。那么在 `Spring` 里面我们是怎么样来获取对象的呢？

我们先创建一个`xml`用来声明 `bean`，那文件名就叫做 `bean.xml` 吧，内容如下：

![](https://gitee.com/cbuc/picture/raw/master/20210310125114.png)

通过`<bean>`标签来声明一个对象，用`<property>` 来声明对象的属性，获取的方式也比较简单：

```java
ApplicationContext ac = new ClassPathXmlApplicationContext("bean.xml");
User user = ac.getBean("user1", User.class);
System.out.println(user);
```

通过 `ApplicationContext` 来获取到这个对象，而且这种方法处处皆可用，获取的的对象便是我们已经定制好的`User`对象。

通过`<property>`注解声明属性的方式是基于**属性注入**，在**IOC**依赖注入有三种方式：

- 属性注入
- 构造器注入
- 工厂方法注入（较少使用）

属性注入我们上面已经见识过了，那我们再来看下 **构造器注入** 是怎么一回事

###### 1. 构造器注入

![](https://gitee.com/cbuc/picture/raw/master/20210310130056.png)

通过这种方式的注入，我们一样能获取到名称为 **小菜** 的 **User对象**。

使用构造器注入的方式，虽然也能很好的提供 Bean 对象，但是如果使用不恰当，那也是会踩坑的！小菜本着负责的态度，当然要为小伙伴们指明道路~

![](https://gitee.com/cbuc/picture/raw/master/20210311130144.png)

我们现在看下 `<constructor-org>` 标签里面有哪些属性：

![image-20210311125706584](https://gitee.com/cbuc/picture/raw/master/20210311125706.png)

`value`  我们已经使用过了，就是用来表示注入的值

`name` 就是用来表明对象属性的名称

`type` 用来表明对象属性的类型

`index` 用来表明改属性在构造函数中的位置，默认从 **0** 开始计数

![](https://gitee.com/cbuc/picture/raw/master/20210311132043.png)

这不就是在说`<constructor-org>` 的用法吗，哪里出现坑了，我看是小菜在坑人才是~莫急莫急， 坑这就来了！
咱们来看一下新的`User` 对象：

![image-20210311132600788](https://gitee.com/cbuc/picture/raw/master/20210311132600.png)

对象当然没什么问题，该有的构造函数也有。我们来试一下构造一个 **名为小菜，年龄23** 的用户对象吧，一顿操作之下：

```xml
<bean id="user3" class="cbuc.life.ioc.User">
    <constructor-arg value="小菜"/>
    <constructor-arg value="23"/>
</bean>
```

你看上去没啥问题，我看上去也没啥问题，但问题它就来了：

```java
ApplicationContext ac = new ClassPathXmlApplicationContext("bean.xml");
User user = ac.getBean("user3", User.class);
System.out.println(user);
/** OUTPUT:
 * User(name=小菜, salary=23.0)
 */
```

这尼玛傻眼了，使用的人看到没为年龄赋值，还以为是创建一个 **名为小菜，工资23**的用户对象。解决的方法上面也说了，具体怎么用呢？

```xml
<bean id="user4" class="cbuc.life.ioc.User">
    <constructor-arg value="小菜"/>
    <constructor-arg value="23" type="int"/>
</bean>
```

这样就可以避免复制错误的尴尬。如果你跟我说那 `salary` 也是 **int** 类型的怎么办，那就可以借助 `name` 了

```xml
<bean id="user4" class="cbuc.life.ioc.User">
    <constructor-arg value="小菜"/>
    <constructor-arg value="23" type="int" name="age"/>
</bean>
```

那如果一个构造函数中既有 `age` 又有 `salary` ，我们又可以这样做：

```java
public User(String name, int age, int salary) {
    this.name = name;
    this.age = age;
    this.salary = salary;
}
```

这种情况我们还可以用到 `index` 这个属性：

```xml
<bean id="user5" class="cbuc.life.ioc.User">
    <constructor-arg value="小菜"/>
    <constructor-arg value="23" index="1"/>
    <constructor-arg value="2000" index="2"/>
</bean>
```

这样子我们就可以获取到想要的 **User** 对象了：

```java
User(name=小菜, age=23, salary=2000)
```

唉，真麻烦，用这种方式创建出来的对象还不如我 `new` 出来的香。顿时对 Spring 产生了质疑的态度

![](https://gitee.com/cbuc/picture/raw/master/20210312125452.png)

等等等等，贴心的小菜又来了，简单给你说下另外一种创建的方法，那就是借助 **注解** 的方式。真是个渣男，用到时候美滋滋，创建的时候就抱怨这抱怨那的~ 已经给你提前剧透还有注解的方式了，那就让小菜把剩下的 **XML** 讲完吧，毕竟小菜绝不始乱终弃，拒做渣男~

###### 2. 字面值

`XML` 的使用中还支持 **字面值** 的使用，你要问我什么是 **字面值**？那我们只能问你在 **Mybatis** 的xml配置文件中有没有见过`<![CDATA[<Value>]]` 这种写法，你要是还跟我说没有，那小菜就要考虑是否再出一期 **Mybatis** 的讲解了~ 话不多说，先看使用吧：

我们来创建一个名为 **Cbuc.~!<>** 的特殊用户，这么特殊的名称那肯定得用特殊的方式了

![](https://gitee.com/cbuc/picture/raw/master/20210312132507.png)

结果是成功创建也成功获取了~

###### 3. 内部Bean

什么是内部类，那就是我私生的孩子不想让别人发现！当然有时候也可以让别人发现~ 但是如果我在 **XML** 创建的过程中创建的类不想被人引用该怎么办，那当然也是有的办了：

![](https://gitee.com/cbuc/picture/raw/master/20210312133033.png)

简单发现三件事，第一件：对象成功创建也成功获取了(`废话`) 第二件：`<bean>` 的建立是在 `<property>` 属性里的(`这才符合内部类`)  第三件：`<bean>` 内部建立没有**ID** 这个属性(`都不想让别人引用，要 ID 有何用`)

如果你不想自己创建一个 **Address** 对象，而是想要别人的，然后自己再改一改也能用，这简单也说就是想白嫖，虽然说也行~

![](https://gitee.com/cbuc/picture/raw/master/20210312142158.png)

> 用技术可以白嫖，学技术不可白嫖
>
> 关注VX：小菜良记
>
> 每篇都是初恋的味道~

###### 4. 集合属性赋值

除了上面我们可以给基本类型和**String** 赋值之外，还可以给 `List/Map` 这些赋值，用法如下：

我们在 **User** 类中添加了几个新属性：

```java
private List<String> phoneList;

private List<Address> addressList;

private Map<String, String> animalMap;
```

![image-20210313170116952](https://gitee.com/cbuc/picture/raw/master/typora/20210313170117.png)

如果你觉得包一层`<property>` 有写碍事，那么也不要紧，尽量满足你的要求！`p 命名空间` 它来了~

![](https://gitee.com/cbuc/picture/raw/master/typora/20210313173206.png)

 尽管对集合属性不好复制也可以借助 `<util:map />` 或 `<util:list />` 来帮助。真是 **Spring 一点一点变好，而你一点一点变渣**，现在是不是脑子还想着怎么使用**注解** 的方式来开发~

![](https://gitee.com/cbuc/picture/raw/master/typora/20210313173551.png)

你以为结束了，却只是过半！通过 **XML** 我们甚至可以看到继承关系，惊不惊喜意不意外~

###### 5. Bean 的继承

![](https://gitee.com/cbuc/picture/raw/master/typora/20210313174000.png)

我们直接通过 `parent` 属性来指明父类对象，然后再给自己其他属性赋值。说到这里，我们就再提一嘴，有一个 `abstract` 属性，看单词就知道抽象的意思，但是却起到 **Java**中 **接口**的作用，用处便是添加这个属性并设为 **true** ，那么它的工作就是专门 **“生孩子”** ，也就是变成了一个 **配置模板**，只能作为其他 **Bean** 的模板，而不能被 **Spring** 实例化。

![](https://gitee.com/cbuc/picture/raw/master/typora/20210313174349.png)

配置了这个属性，我们要再想通过 Spring容器获取，就会抛出以下错误：

![](https://gitee.com/cbuc/picture/raw/master/typora/20210313174512.png)

###### 6. 生命周期

如果我们有点属性 **Bean** 的生命周期，那么应该对 `init` 和 `destory` 两个方法有些熟悉，那就是在 **Bean** 创建和销毁的时候执行的方法，我们同样可以在 **XML** 配置 **Bean** 的时候指出。

在 **User** 对象中我们有个 **initMethod()** 和 **destroyMethod()** 方法，然后在构建 **Bean** 的时候指明：

![](https://gitee.com/cbuc/picture/raw/master/typora/20210313175901.png)

然后从控制台中可以看到初始化方法执行了：

![](https://gitee.com/cbuc/picture/raw/master/typora/20210313175950.png)

讲到这里**XML** 的配置方式也讲的差不多了，那就如你所愿，看看注解的方式吧~

##### ② 注解方式

通过注解的方式创建**Bean**，我们需要用到两个注解`@Configuration`和 `@Bean`，用法如下：

创建一个`BeanConfig` 的配置类，然后通过 `AnnotationConfigApplicationContext` 获取该类

![](https://gitee.com/cbuc/picture/raw/master/20210312130944.png)

这种方法就很美滋滋，去取了 **XML**文件的配置，感觉方便了不少，嘴角也露出迷人的微笑。

注解的方式简单而强大， 让人不胜向往！接下来欢迎你来到注解新天地，导游小菜~

![](https://gitee.com/cbuc/picture/raw/master/typora/20210314223726.gif)

我们在上面见识到了 `@Configuration` 和 `@Bean` 两个注解，这两个注解的使用帮我们很好的构建和获取bean 的过程。那我们就从认识这两个注解开始！

`@Configuration & @Bean`

这个注解用在类上，作用于xml配置文件一致，向Spring表明这是一个配置类。配合 **@Bean** 可以初始化自定义Bean

那我们有时候会不会只想要一个默认的 Bean，不需要定制化，当然用上面的方式我们也能很好的实现：

```java
@Configuration
public class BeanConfig {
    @Bean
    public User getDefaultUser() {
        return new User();
    }
}
```

但是如果一两个 Bean，我们这样子做还可以接受，当 Bean 的数量多起来估计又会抱怨了~

解决方法又来了，那就是 **包扫描**，何为 **包扫描**？那就是我们扫描指定包下的实体类，然后将其注册到Spring容器中，这个方法实在是秒啊~那如何实现呢？借助`@ComponentScan`注解：

![](https://gitee.com/cbuc/picture/raw/master/typora/20210314225553.png)

该有的注解`@ComponentScan`有了，但是好像还有个陌生的注解 `@Component` 。其实这里不止可以用 `@Component` 还可以用 **@Controller、@Repository、@Service ... ** 这些注解，这些注解的共同点就是把标记了这些注解的类纳入Spring容器中进行管理。但是单单标记这些注解还不够，还需要我们上面说的 `@ComponentScan` 这个注解的配合，它告诉Spring 哪个packages下用注解标识的类会被spring自动扫描并且装入bean容器。













































#### ㈡ IOC 容器

上面如何使用是讲的差不多了，你学到了多少呢~ 那你在学的过程中有没有一个问号一直在。那就是为啥能通过 `ApplicatinContext` 来获取到 Spring 中构建的 **Bean** 呢？。

```java
ApplicationContext ac = new ClassPathXmlApplicationContext("bean.xml");
User user = ac.getBean("user4", User.class);
```

> **IOC** 容器其实就是一个大工厂，它用来管理我们所有的对象以及依赖关系

原理简单来说就是三步：

- Ⅰ：通过 Java 反射技术获取类的所有信息

- Ⅱ：再通过配置文件(`XML`) 或 注解(`Annoation`) 来描述类与类之间的关系
- Ⅲ：结合上面两步获取到的信息就可以构建出对应的对象之间的依赖关系

![](https://gitee.com/cbuc/picture/raw/master/typora/20210313181704.png)

在 Spring IOC 容器读取 **Bean** 的配置创建 **Bean** 之前，必须对它进行实例化，只有在容器实例化后，才可以从 **IOC** 容器里面获取到 Bean 实例并使用。

那么 Spring 中提供了两种类型的 IOC 容器实现：

- **BeanFactory** ： IOC 容器的基本实现
- **ApplicationContext**：提供了更多的高级特性，是 **BeanFactory** 的子接口

**BeanFactory** 是 Spring 框架的基础设施，面向 Spring 本身，**ApplicationContext** 是面向 Spring 框架的开发者，几乎所有的应用场景都直接用 **ApplicationContext** 而非底层的 **BeanFactory**。

![](https://gitee.com/cbuc/picture/raw/master/typora/20210313181134.png)

通过继承图可以看到 **ApplicationContext** 又有两个子类 **ClassPathXmlApplicationContext** 和 **AnnoationConfigApplicationContext** ，当然实际远远不止这些，我们上面用 **XML** 的方式是用到 **ClassPathXmlApplicationContext** ，而用注解的方式是用到 **AnnoationConfigApplicationContext** 。

那么为什么使用 `ApplicationContext` 而不是 `BeanFactory` ，我们就先来看看两者的区别：

- `ApplicationContext` 能够利用反射技术自动识别出配置文件中定义的 **BEANPostProcessor、InstantiationAwareBeanPostProcessor、BeanFactoryPostProcessor** ，并自动将它们注册到应用上下文中，而 `BeanFactory` 需要在代码中通过手动调用 **addBeanFatoryPostProcessor()**方法进行注册。
- `ApplicationContext` 在初始化应用上下文的时候就实例化了所有但实例的Bean，而 `BeanFactory` 在初始化容器的时候并为实例化 Bean，直到第一次访问某个Bean时才实例化目标 Bean

那么我们可以继续看下**Bean**的初始化过程：

![](https://gitee.com/cbuc/picture/raw/master/typora/20210313210706.png)

#### ㈢备战面试

以上那些内容相信已经为你储备了不少知识干粮了，对于开发，对于面试，相信你也已经摩拳擦掌了！既然已经准备好了，那我们就进入面试环节！

![](https://gitee.com/cbuc/picture/raw/master/typora/20210313211417.gif)

> 控制反转(IOC)有什么用?

- 管理对象的创建和依赖关系的维护
- 解耦，由容器器维护具体的对象
- 托管了类的产生过程

> IOC的优点是什么？

IOC或依赖注入把应用的代码降到最低，它使应用更加容易测试，单元测试不再需要单例和 JNDI 查找机制。以最小的代价和最小的侵入性使松散耦合得以实现。IOC 容器支持加载服务时以 **懒汉式** 或 **饿汉式** 加载。

> IOC容器装配Bean有几种方式？4 种！

- XML 配置
- 注解配置
- JavaConfig
- 基于Groovy DSL配置（少见）

> 依赖注入有几种方式？3 种！

- 属性注入(通过 setter() 方法)
- 构造函数注入
- 工厂方法注入

> Bean有几种作用域？5 种！

- **singleton**：bean在每个SpringIOC容器中都只有一个实例 （默认作用域）
- **protorype**：一个bean的定义可以有多个实例
- **request**：：每次 http 请求都会创建一个 bean。该作用域仅在基于web的Spring ApplicationContext 情形下有效
- **session**：在一个 http session 中，一个bean定义对应一个示例。该作用域仅在基于web的SpringApplicationContext 情形下有效
- **global-session**：在一个全局的 http session 中，一个bean定义对应一个示例。该作用域仅在基于web的SpringApplicationContext情形下有效

> Spring中有几种自动装配方式？5 种

- **no**：默认的方式是不进行自动装配的，通过手工设置ref属性来进行装配bean。

- **byName**：通过bean的名称进行自动装配，如果一个bean的 property 与另一bean 的name 相同，就进行自动装配。

- **byType**：通过参数的数据类型进行自动装配。

- **constructor**：利用构造函数进行装配，并且构造函数的参数通过byType进行装配。

- **autodetect**：自动探测，如果有构造方法，通过 construct的方式自动装配，否则使用 byType的方式自动装配。

>  @Autowired 注解有什么作用

- 默认是按照类型装配注入的，默认情况下它要求依赖对象必须存在（可以设置它required属性为false）。

- 提供了更细粒度的控制，包括在何处以及如何完成自动装配。可以修饰setter方法、构造器、属性等。

> @Autowired和@Resource之间的区别

1. @Autowired可用于：构造函数、成员变量、Setter方法
2. @Autowired默认是按照类型装配注入的，默认情况下它要求依赖对象必须存在（可以设置它required属性为false）
3. @Resource默认是按照名称来装配注入的，只有当找不到与名称匹配的bean才会按照类型来装配注入

> Spring中 Bean 的生命周期

1. Spring 容器从 XML 文件中读取 Bean 的定义，并实例化 Bean
2. Spring根据 Bean 的定义填充所有属性
3. 如果 Bean 实现了 **BeanNameAware** 接口，Spring将传递 Bean 的ID到 `setBeanName ()`方法中
4. 如果 Bean 实现了 **BeanFactoryAware** 接口，Spring 将传递 beanFactory 到 `setBeanFactory()` 方法中
5. 如果有和 Bean 相关联的 **BeanPostProcessors**，Spring会在 `postProcesserBeforeInitialization()` 方法内调用他们
6. 如果 Bean 实现了**InitializingBean**，会调用它的 `afterPropertySet()` 方法，如果 Bean 声明了初始化方法，那么会调用此方法
7. 如果有**BeanPostProcessors** 和 Bean 关联，那么会调用这些 Bean 的 `postProcesserAfterInitialization()` 方法
8. 如果 Bean实现了 **DisposableBean** ，它将调用`destroy()`方法

> ApplicationContext 和 BeanFactory 的区别

- **BeanFactory**可以称为 `低级容器`。它比较简单，可以理解为就是一个 HashMap，key 是 BeanName，Value 就是 Bean 实例。通常只提供注册(put) 和 获取(get)  这两个功能。
- **ApplicationContext** 可以称为 `高级容器`。它过继承了多个接口，因此多了更多的功能。例如资源的获取，支持多种消息（例如 JSP tag 的支持），对 BeanFactory 多了工具级别的支持等待，它 代表着整个大容器的所有功能。该接口定义了一个 `refresh()` 方法，用于刷新整个容器，即重新加载/刷新所有的 bean。

> ApplicationContext 的通常实现是什么?

- **FileSystemXmlApplicationContext**：此容器从一个 XML 文件中加载 bean 的定义，我们需要提供Bean配置文件的全路径名给它的构造函数

- **ClassPathXmlApplicationContext**：此容器也从一个XML文件中加载beans的定义，这里，你需要正确设置classpath因为这个容器将在classpath里找bean配置
- **WebXmlApplicationContext**：此容器加载一个XML文件，此文件定义了一个WEB应用的所有bean







byName 和 byType 

