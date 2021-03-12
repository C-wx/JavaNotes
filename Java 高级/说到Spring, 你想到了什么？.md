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

之前有关注过小菜的小伙伴估计对小菜的文章有丝微的印象，小菜之前已经写过关于 **Spring** 的相关文章了：

- [伙计，是时候拉近你和【Spring】之间的距离了！](https://mp.weixin.qq.com/s/iXl2XRifIuTN41pwk2K8DA)

- [那些年你不能错过的之【Spring事务】](https://mp.weixin.qq.com/s/fqDU_66dd5_HT6iYNbXsVQ)

- [【Spring-AOP】不得不会的XML配置开发！](https://mp.weixin.qq.com/s/9lpz0Srx8q12O71fTXPycA)

- [【Spring-AOP】原来注解是这样实现的！](https://mp.weixin.qq.com/s/PW6A4XN5Ltsv4PoduFlQvQ)

- [【Spring-IOC】你需要掌握的注解版开发！](https://mp.weixin.qq.com/s/KdrBMXq8FudODDXD-uZqjg)

这些文章都看下来，我相信对 Spring 肯定有一定的了解了，那为什么还要再写这类文章了？

**"代码写的不好要重构，文章写的不好当然也要重构"**

有一说一，当然除了重构的想法，还有就是要对 **Spring** 温故而知新！

### 什么是IOC

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

#### 如何使用

##### XML方式

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

属性注入我们上面已经见识过了，那我们再来看下 **构造器注入** 是怎么一回事：

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

`XML` 的使用中还支持 **字面值** 的使用，你要问我什么是 **字面值**？那我们只能问你在 **Mybatis** 的xml配置文件中有没有见过`<![CDATA[<Value>]]` 这种写法，你要是还跟我说没有，那小菜就要考虑是否再出一期 **Mybatis** 的讲解了~ 话不多说，先看使用吧：

我们来创建一个名为 **Cbuc.~!<>** 的特殊用户，这么特殊的名称那肯定得用特殊的方式了

![](https://gitee.com/cbuc/picture/raw/master/20210312132507.png)

结果是成功创建也成功获取了~

什么是内部类，那就是我私生的孩子不想让别人发现！当然有时候也可以让别人发现，但是如果我在 **XML** 创建的过程中创建的类不想被人引用该怎么办，那当然也是有的办了：

![](https://gitee.com/cbuc/picture/raw/master/20210312133033.png)

简单发现三件事，第一件：对象成功创建也成功获取了(`废话`) 第二件：`<bean>` 的建立是在 `<property>` 属性里的(`这才符合内部类`)  第三件：`<bean>` 内部建立没有**ID** 这个属性(`都不想让别人引用，要 ID 有何用`)

如果你不想自己创建一个 **Address** 对象，而是想要别人的，然后自己再改一改也能用，这简单也说就是想白嫖，虽然说也行~

![](https://gitee.com/cbuc/picture/raw/master/20210312142158.png)



##### 注解方式

通过注解的方式创建**Bean**，我们需要用到两个注解`@Configuration`和 `@Bean`，用法如下：

创建一个`BeanConfig` 的配置类，然后通过 `AnnotationConfigApplicationContext` 获取该类

![](https://gitee.com/cbuc/picture/raw/master/20210312130944.png)

这种方法就很美滋滋，去取了 **XML**文件的配置，感觉方便了不少，嘴角也露出迷人的微笑。

既然都说到注解了，那我们











![](https://gitee.com/cbuc/picture/raw/master/20210310132840.png)







![](https://gitee.com/cbuc/picture/raw/master/20210311125442.png)



![](https://gitee.com/cbuc/picture/raw/master/typora/20210222211320.png)