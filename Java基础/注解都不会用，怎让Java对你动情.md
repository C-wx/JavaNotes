大家好，我是小菜，一个渴望在互联网行业做到蔡不菜的小菜。可柔可刚，点赞则柔，白嫖则刚！
**死鬼~看完记得给我来个三连哦！**


![](https://user-gold-cdn.xitu.io/2020/4/11/17169c46045528af?w=240&h=224&f=jpeg&s=7529)


>本文主要介绍 `Java 中注解的使用`
>
>如有需要，可以参考
>
>如有帮助，不忘 **点赞** ❥
>
>
>微信公众号已开启，**小菜良记**，没关注的同学们记得关注哦！

最近的文章系列相信大家也都发现了，都是一些比较基础的Java知识点。但是不知道在大家看了之后有没有觉得就是一些很基础的知识点，看样子挺简单的，其实还有挺多内容不会的。只有地基打牢了，开发才能跟更有效率，不要急于求成，不妨回头务实一番，别有收获。所以这期还是老样子，小菜和伙伴们一起来学习`注解`的使用。

### 初识

> **注解** 也称为 元数据。为我们在代码中添加信息提供了一种形式化的方法，使我们可以在稍后某个时刻非常方便地使用这些数据。

注解是在 Java 1.5 之后引入的，它可以提供用来完整地描述程序中所需的信息，可以由编译器来测试和验证的格式，存储有关程序的额外信息。

注解的使用很简单，只需要和 `@` 符号搭配。有些 Java 初学者常常会把 **注解** 和 **注释** 混淆，但是两者的作用却大同小异，都是用来描述信息，不同的是 **注解** 描述的信息是给应用程序看的，而 **注释** 描述的信息是给开发人员看的。

初学者对**注解**的印象可能不深，**注解**也许不起眼但是处处可见。

最常见到的便是 `@Override`，表示当前的方法定义将覆盖父类的方法，如果拼写错误，或者方法签名对不上被覆盖的方法，编译器就会发出错误提示。

既然都说到`@Override`注解了，那仔细回忆下，脑子里估计就浮现出`@SuppressWarnnings`注解。还记得我最早见到这个注解的时候，还是`Myeclipse`提示我使用的，我也不管三七二十一，就给标注上了。后来才知道这个注解是用来关闭编译器对类、方法、成员变量、变量初始化的警告。

上面两个都说完了，那不妨再来一个，这个可能是最不常见的。那就是`@Deprecated`

![](https://cbucbm.club/eacd3b1e)

不要因为这个类加了横线感到奇怪，那是因为`@Deprecated`的效果。它的具体功能就是用于标识方法或类，标识该类或方法已过时，建议不要使用。如果开发人员使用了注解为它的元素，那么编译器就会发出警告信息。

刚刚开局不久，我们就已经学到了三个注解的使用，虽然只是基本的，但接下来我们就通过这三个注解为导线通关打怪吧。

### 首关之程娲造注

注解一旦构造出来，就享有编译器的类型检查保护。让我们先看下一组代码热热身：

```java
public class TestService {

    @MyAnnotation
    public void runTset() {
        System.out.println("annotation test");
    }

}
```

<u>不要纳闷</u>，Java 中确实没有一个注解名为`MyAnnotation`，但这个怎么来的呢，就是我们自己造的。

那么关子卖完了，接下来就来揭秘注解的制造：

```java
@Target(ElementType.METHOD)
@Retention(RetentionPolicy.RUNTIME)
public @interface MyAnnotation {
}
```

这样子，一个简单的注解就新鲜出炉了。需要注意的是这可不是一个接口，需要留言`interface`前面还有一个`@`，这个标识要是漏掉了，那可以天差地别。

细心的小伙伴可能注意到了，定义的注解头上怎么还有注解。

![](https://cbucbm.club/a1dd7454)

这就是接下来要讲到，敲黑板，注意看！

#### 元注解来帮忙

在定义注解时，会需要一些元注解。上面出现了两个，分别是`@Target`和`@Retention`.

其中`@Target`用来定义你的注解将应用于什么地方（例如一个方法或一个域），`@Retention`用来定义该注解在哪一个级别可用，在源代码中（**SOURCE**），类文件中（**CLASS**）或者运行时（**RUNTIME**）。Java 提供了四种元注解，如下：

| 名称            | 用处                                                         |
| --------------- | ------------------------------------------------------------ |
| **@Target**     | 标识该注解可以用于什么地方。其中 `ElementType` 参数包括：<br />1. `CONSTARUCTOR`：构造器的声明<br />2. `FIELD`：域声明（包括enum实例）<br />3. `LOCAL_VARIABLE`：局部变量声明<br />4. `METHOD`：方法声明<br />5. `PACKAGE`：包声明<br />6. `TYPE`：类、接口（包括注解类型）或enum 声明 |
| **@Retention**  | 表示需要在什么级别保存该注解信息，其中`RetentionPolicy`参数包括：<br />1.`SOURCE`：注解将被编译器丢弃<br />2.`CLASS`：注解在 class 文件中可用，但会被 VM 丢弃<br />3. `RUNTIME`：VM 将在运行期也保留注解，因此可以通过反射机制读取注解的信息 |
| **@Documented** | 将此注解包含在 JavaDoc 中                                    |
| **@Inherited**  | 允许子类继承父类的注解                                       |

#### 注解也分类

我们在上面示例中创建了一个 `@MyAnnotation` 注解。看起来很简单，没什么内容，因此这种注解我们也称为 **标记注解**，注解也分几类：

- `标记注解`：注解内部没有属性。使用方式：**@注解名**

```java
//定义
@Target(ElementType.METHOD)
@Retention(RetentionPolicy.RUNTIME)
public @interface MyAnnotation {
}
```

- `单值注解`：注解内部只有一个属性。使用方式：**@注解名(key = value)**

```java
//定义
@Target(ElementType.METHOD)
@Retention(RetentionPolicy.RUNTIME)
public @interface SingleValueAnnotation {
    String name();
}
//使用
@SingleValueAnnotation(name = "test")
public void singleTest() {}
```

- `多值注解`：注解内部有过个属性。使用方式：**@注解名(key = value, key = value, ...)**

```java
//定义
@Target(ElementType.METHOD)
@Retention(RetentionPolicy.RUNTIME)
public @interface MultiValueAnnotation {
    String name();
    int num();
}
//使用
@MultiValueAnnotation(name = "test", num = 1)
public void multiTest() {}
```

#### 值也有默认

当我们使用的不是`标记注解`时，如果在使用注解的时候不给注解中的属性赋上值，那么编译器就会报错，提示我们需要赋值。

![](https://cbucbm.club/6e3c303d)

这样子是很不方便，有时候我们并没有使用到或值是固定的不想重复写，那么这个时候就需要借助**default**关键字来帮忙我们解决这种问题。

```java
@Target(ElementType.METHOD)
@Retention(RetentionPolicy.RUNTIME)
public @interface MultiValueAnnotation {
    
    String name();
    
    int num() default 0;
}
```

我们在属性上使用了 `default` 关键字来声明 `num` 属性的默认值为 0 ，这样子我们在使用上述那个注解的时候就可以不用手动给`num`赋值了。

### 次关之造器解注

注解具有让编译器进行编译检查的作用，但是如果没有用来读取注解的工具，那注解也不会比注释更有用，起码注释可以让开发人员更直观的看到此段代码的用处。

#### 重回反射

想要创建与使用 **注解处理器**，我们还需要借助反射机制来构造这类工具。以下是简单的例子：

```java
public class AnnotationHandle {

    public static void track(Class<?> c) {
        for (Method m : c.getDeclaredMethods()) {
            MultiValueAnnotation annotation = m.getAnnotation(MultiValueAnnotation.class);
            if (annotation != null) {
                System.out.println("name:" + annotation.name() +
                        "\n num:" + annotation.num());
            }
        }
    }

    public static void main(String[] args) {
        track(TestService.class);
    }
}

/*  OUTPUT:
  name:test
   num:0
*/
```

在上述例子中我们用到了两个反射的方法：`getDeclaredMethods()`和`getAnnotation()`。

其中`getDeclaredMethods()` 用来返回该类的所有方法，`getAnnotation()`用来获取指定类型的注解对象。如果方法上没有该注解则会返回 **null** 值。

#### 注解元素可用类型

上述`@MultiValueAnnotation`注解中我们定义了 `String`类型的 **name** 和 `int` 类型的 **num**，除此之外我们还可以使用其他类型如下：

- **基本类型**（**int、float、boolean等**）
- **String**
- **Class**
- **enum**
- **Annotation**
- **以上类型的数组**

如果使用了上面以外的其他类型，那么编译器就会报错。而且要注意的是，**也不能使用基本类型的包装类型**

#### 默认值的限制

上述例子中我们也看到了，我们可以在使用注解的时候给注解属性赋值，也可以在定义注解的时候给注解一个默认值，但是这两者都说明了一件事： **那就是，注解元素不能有不确定的值，要么具有默认值，要么在使用注解时提供元素的值**

基本元素不存在`null`值，因此对于非基本类型的元素，无论是在使用中声明，还是在定义时声明， **都不能将 null 值作为其值**。因此在实际开发中，我们往往会定义一些特殊值作为不存在的标识，例如 **负数** 或 **空字符串**

### 三关之运注帷幄

在前面两关中，我们学会了定义注解和创建注解处理器。接下来我们就要来更加深入掌握注解！

#### 注解也能嵌套

在修饰注解元素的时候我们看到可以使用`Annotation`来修饰，估计看到那的时候会觉得有点奇怪。在这里就来为你来揭秘。

先来看一组注解：

`@Constraints`

```java
@Target(ElementType.FIELD)
@Retention(RetentionPolicy.RUNTIME)
public @interface Constraints {
    boolean primaryKey() default false;
    boolean unique() default false;
}
```

`@SQLString`

```java
@Target(ElementType.FIELD)
@Retention(RetentionPolicy.RUNTIME)
public @interface SQLString {
    String name() default "";
    Constraints constraints() default @Constraints;
}
```

我们在`@SQLString`注解中使用`Constraints`注解元素，并将默认值设为`@Constraints`。这个时候``Constraints``中的值都是`@Constraints`注解中定义的默认值，如果我们要使用自定义的话，做法如下：

```java
@Target(ElementType.FIELD)
@Retention(RetentionPolicy.RUNTIME)
public @interface SQLString {
    String name() default "";
    Constraints constraints() default @Constraints(primaryKey = true);
}
```

这样子我们就可以使用自己定义的**value**

#### 注解不支持继承

我们不能使用`extends`来继承某个`@interface`，但是可以通过嵌套的方式来解决这一烦恼。

#### AOP与注解的搭配

**AOP** 在当今开发中我们并不陌生，那么 **AOP** 和 **注解** 能产生什么化学反应呢，请看以下代码：

`@ApiLog`：

```java
@Retention(RetentionPolicy.RUNTIME)
@Target({ElementType.METHOD, ElementType.TYPE})
public @interface ApiLog {
    /**
     * 接口名称
     */
    String name();
}
```

`使用`：

```java
@GetMapping(value = "/getConfig")
@ApiLog(name = "获取系统相关配置")
public Result getConfig() throws Exception {
    return sendOK(SystemService.getConfig(type));
}
```

`Aop使用`：

```java
@Aspect
@Component
public class SysLogAspect {
    @Autowired
    private LogService logService;
    
    @Pointcut("@annotation(cbuc.life.annotation.ApiLog)")
    public void logPointCut() {        
    }

    @Around("logPointCut()")
    public Object around(ProceedingJoinPoint point) throws Throwable {
        long beginTime = System.currentTimeMillis();
        //执行方法
        Object result = point.proceed();
        //执行时长(毫秒)
        long time = System.currentTimeMillis() - beginTime;
        //保存日志
        saveSysLog(point, time);
        return result;
    }

    private void saveSysLog(ProceedingJoinPoint joinPoint, long time) {
        MethodSignature signature = (MethodSignature) joinPoint.getSignature();
        Method method = signature.getMethod();

        LogEntity log = new LogEntity();
        ApiLog apiLog = method.getAnnotation(ApiLog.class);
        if(apiLog != null){
            //注解上的描述
            log.setMethodDescribe(syslog.value());
        }

        //请求的方法名
        String className = joinPoint.getTarget().getClass().getName();
        String methodName = signature.getName();
        log.setMethod(className + "." + methodName + "()");

        //请求的参数
        Object[] args = joinPoint.getArgs();
        String params = JSON.toJSONString(args[0]);
        log.setParams(params);

        //获取request
        HttpServletRequest request = ((ServletRequestAttributes) RequestContextHolder.getRequestAttributes()).getRequest();

        //设置IP地址
        log.setIp(ServletUtil.getIpAddress(request));

        //用户名
        String username = LoginInfo.getUsername();
        log.setUsername(username);

        //保存系统日志
        logService.save(log);
    }
}
```

通过以上代码，我们可以在期望记录日志的方法上增加`@ApiLog`注解，该方法的动作就会被记录进日志表，不管方法叫什么名字，类在什么位置，都可以轻松的解决，而且没有代码入侵！

到这里，我们已经成功地通过了三个关卡，相信你也充分掌握了注解的使用，这期注解咱们到此完结啦，切记：**不可因小而不学，不可因大而弃之。**

![看完不赞，都是坏蛋](https://imgconvert.csdnimg.cn/aHR0cHM6Ly93d3cuNTJkb3V0dS5jbi9zdGF0aWMvdGVtcC9waWMvOWJkNjhkMTUwZjA3ODdjNTYwYTQzOWRhMzU5YTU4MGEucG5n?x-oss-process=image/format,png#pic_center)
> 今天的你多努力一点，明天的你就能少说一句求人的话！
>
> *我是小菜，一个和你一起学习的男人。* `💋`
>
>
> 微信公众号已开启，**小菜良记**，没关注的同学们记得关注哦！