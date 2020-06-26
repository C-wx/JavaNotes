大家好，我是小菜，一个渴望在互联网行业做到蔡不菜的小菜。可柔可刚，点赞则柔，白嫖则刚！

**死鬼~看完记得给我来个三连哦！**

![](https://user-gold-cdn.xitu.io/2020/4/11/17169c46045528af?w=240&h=224&f=jpeg&s=7529)

>本文主要介绍 `Spring 中AOP的注解版开发`
>如有需要，可以参考
>如有帮助，不忘 **点赞** ❥
>
>创作不易，白嫖无义！

### AOP【动态代理】:

*是指在程序运行期间动态的将某段代码切入到指定方法指定位置进行运行的编程方式*

#### 1）导入aop模块
````xml
<dependency>
	<groupId>org.springframework</groupId>
	<artifactId>spring-aspects</artifactId>
	<version>4.3.12.RELEASE</version>
</dependency>
````
#### 2）定义一个业务逻辑类（MathCalculator）
````java
public class MathCalculator {
	public int div(int i,int j){
		return i/j;	
	}
}
````
#### 3）定义一个日志切面类（LogAspects）
![](https://ae01.alicdn.com/kf/H32f287dcac5a4ce186d7a44c1640ac3cA.jpg)

#### 4）定义配置类（MainConfigOfAOP）
![](https://ae01.alicdn.com/kf/Hae65e43cac4b4e9f93280465cf017e8eE.jpg)

### 核心步骤：
#### 1）将业务逻辑组件和切面类都加入到容器中（`@Bean注入`）；告诉Spring哪个是切面类（`@Aspect`）
#### 2）在切面类上的每一个通知方法上标注通知注解，告诉Spring何时何地运行（`切入点表达式`）
#### 3）开启基于注解的aop模式：`@EnableAspectJAutoProxy`
### 执行效果：
- #### 正常执行：

  *前置通知--> 目标方法--> 后置通知--> 返回通知*

- #### 出现异常：

  *前置通知--> 目标方法--> 后置通知--> 异常通知*

![看完不赞，都是坏蛋](https://imgconvert.csdnimg.cn/aHR0cHM6Ly93d3cuNTJkb3V0dS5jbi9zdGF0aWMvdGVtcC9waWMvOWJkNjhkMTUwZjA3ODdjNTYwYTQzOWRhMzU5YTU4MGEucG5n?x-oss-process=image/format,png#pic_center)

> 今天的你多努力一点，明天的你就能少说一句求人的话！
>
> *我是小菜，一个和你一起学习的男人。* `💋`

