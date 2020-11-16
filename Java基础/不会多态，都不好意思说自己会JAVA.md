大家好，我是小菜，一个渴望在互联网行业做到蔡不菜的小菜。可柔可刚，点赞则柔，白嫖则刚！
**死鬼~看完记得给我来个三连哦！**


![](https://user-gold-cdn.xitu.io/2020/4/11/17169c46045528af?w=240&h=224&f=jpeg&s=7529)


>本文主要介绍 `Java中多态的用法`
>
>如有需要，可以参考
>
>如有帮助，不忘 **点赞** ❥
>
>
>微信公众号已开启，**小菜良记**，没关注的小伙伴记得关注哦！

今天是周五，跟往常一样踩点来到了公司。坐到自己的工位上打开电脑，`"又是搬砖的一天"`。想归想，还是`"熟练"`的打开了 Idea，看了下今天的需求，便敲起了代码。咦，这些代码是谁写的，怎么出现在我的代码里面，而且还是待提交状态，我记得我没写过呀，饶有兴趣的看了看：

![](https://cbucbm.club/d69568ac)

这不是多态吗，谁在我电脑写的测试，不禁一阵奇怪。

`"你看看这会输出什么结果？"`

一阵声音从身后传来，因为在思考输出结果，也没在意声音的来源，继续看了看代码，便得出结论：

```xml
/*
    polygon() before cal()
    square.cal(), border = 2
    polygon() after cal()
    square.square(), border = 4
*/
```

心里想：就这？起码也是名 Java 开发工程师好吗，虽然平时搬搬砖，一些基本功还是有的。不禁有点得意了~

`"这就是你的答案吗？看来你也不咋的"`

声音又突然响起，这次我不淡定了，尼玛！这答案我也是在心里想的好吗，谁能看得到啊，而且说得话让人那么想施展一套阿威十八式。`"你是谁啊？"`带着丝微疑惑和愤怒转过了头。怎么没人？容不得我疑惑半分，`"小菜，醒醒，你怎么上班时间就睡着了"`

上班时间，睡着了？我睁开了眼，看了下周围环境，原来是梦啊，舒了一口气。望眼就看到部门主管站在我面前，上班时间睡觉，你是身体不舒服还是咋样？昨天写了一堆 bug 没改，今天又提交什么乱七八糟的东西上去，我看你这个月的绩效是不想要的，而且基于你的表现，我也要开始为部门考虑考虑了。

`"我不是，我没有，我也不知道怎么就睡着了，你听我解释啊！"` 这句话还没来得及说出口，`心里的花我要带你回家，在那深夜酒吧哪管它是真是假，请你尽情摇摆忘记钟意的他，你是最迷人噶，你知道吗`，闹铃响了起来，我一下子立起身子，后背微湿，额顶微汗，看了下手机，周六，8点30分，原来那是梦啊！

奇怪，怎么会做那么奇怪的梦，也太吓人了。然后就想到了梦中的那部分代码，难道我的结果是错的吗？凭着记忆，在电脑上重新敲了出来，运行结果如下：

```xml
/*
    polygon() before cal()
    square.cal(), border = 0
    polygon() after cal()
    square.square(), border = 4
*/
```

`square.cal(), border`的结果居然是 0，而不是2。难道我现在连多态都不会了吗？电脑手机前的你，不知道是否得出了正确答案了呢！不管有没有，接下来就跟小菜一起来复习一下多态吧！

有些小伙伴疑惑的点可能不止`square.cal(), border`的结果是 0，也有为什么不是 ` square.square(), border = 4` 先输出的疑惑。那么我们就带着疑惑，整起！

## 多态

**在面向对象的程序设计语言中，多态是继数据抽象和继承之后的第三种基本特征。**

多态不但能够改善代码的组织结构和可读性，还能够创建可扩展的程序。多态的作用就是消除类型之间的`耦合关系`。

### 1. 向上转型

根据`里氏代换原则`：任何基类可以出现的地方，子类一定可以出现。

对象既可以作为它自己本身的类型使用，也可以作为它的基类型使用。而这种吧对某个对象的引用视为对其基类型的引用的做法被称作为 - `向上转型`。因为父类在子类的上方，子类要引用父类，因此称为 `向上转型`。

```java
public class Animal {
    void eat() {
        System.out.println("Animal eat()");
    }
}

class Monkey extends Animal {

    void eat() {
        System.out.println(" Monkey eat()");
    }
}

class test {

    public static void start(Animal animal) {
        animal.eat();
    }

    public static void main(String[] args) {
        Monkey monkey = new Monkey();
        start(monkey);
    }
}

/* OUTPUT:
Monkey eat()
*/
```

上述 `test` 类中的 `start()` 方法接收一个 `Animal`  的引用，自然也可以接收从 `Animal` 的导出类。调用`eat()` 方法的时候，自然而然的使用到 `Monkey` 中定义的`eat()`方法，而不需要做任何的类型转换。因为从 `Monkey` 向上转型到 `Animal` 只能减少接口，而不会比`Animal` 的接口更少。

打个不是特别恰当的比方：*你父亲的财产会继承给你，而你的财产还是你的，总的来说，你的财产不会比你父亲的少。*

![](https://cbucbm.club/cd29a2b8)

#### 忘记对象类型

在 `test.start()`方法中，定义传入的是 `Animal` 的引用，但是却传入`Monkey`，这看起来似乎忘记了`Monkey` 的对象类型，那么为什么不直接把`test`类中的方法定义为`void start(Monkey monkey)`，这样看上去难道不会更直观吗。

直观也许是它的优点，但是就会带来其他问题：`Animal`不止只有一个`Monkey`的导出类，这个时候来了个`pig` ，那么是不是就要再定义个方法为`void start(Monkey monkey)`，重载用得挺溜嘛小伙子，但是未免太麻烦了。懒惰才是开发人员的天性。

因此这样就有了`多态`的产生

### 2.显露优势

**方法调用**中分为 `静态绑定`和`动态绑定`。何为绑定：将一个方法调用同一个方法主体关联起来被称作绑定。

- `静态绑定`：又称为**前期绑定**。是在程序执行前进行把绑定。我们平时听到"静态"的时候，不难免想到`static`关键字，被`static`关键字修饰后的变量成为静态变量，这种变量就是在程序执行前初始化的。`前期绑定`是面向过程语言中默认的绑定方式，例如 C 语言只有一种方法调用，那就是前期绑定。

***引出思考：***

```java
public static void start(Animal animal) {
    animal.eat();
}
```

在`start()`方法中传入的是`Animal` 的对象引用，如果有多个`Animal`的导出类，那么执行`eat()`方法的时候如何知道调用哪个方法。如果通过`前期绑定`那么是无法实现的。因此就有了`后期绑定`。

- `动态绑定`：又称为`后期绑定`。是在程序运行时根据对象类型进行绑定的，因此又可以称为`运行时绑定`。而 Java 就是根据它自己的后期绑定机制，以便在运行时能够判断对象的类型，从而调用正确的方法。

***小结：***

Java 中除了 `static` 和 `final` 修饰的方法之外，都是属于后期绑定

#### 合理即正确

显然通过`动态绑定`来实现`多态`是合理的。这样子我们在开发接口的时候只需要传入 基类 的引用，从而这些代码对所有 基类 的 导出类 都可以正确的运行。

![](https://cbucbm.club/74c55dbf)

其中`Monkey`、`Pig`、`Dog`皆是`Animal`的导出类

`Animal animal = new Monkey()` 看上去不正确的赋值，但是上通过继承，`Monkey`就是一种`Animal`，如果我们调用`animal.eat()`方法，不了解多态的小伙伴常常会误以为调用的是`Animal`的`eat()`方法，但是最终却是调用了`Monkey`自己的`eat()`方法。

`Animal`作为基类，它的作用就是为导出类建立公用接口。所有从`Animal`继承出去的导出类都可以有自己独特的实现行为。

#### 可扩展性

有了多态机制，我们可以根据自己的需求对系统添加任意多的新类型，而不需要重载`void start(Animal animal)`方法。

在一个设计良好的OOP程序中，大多数或者所有方法都会遵循`start()`方法的模型，只与基类接口同行，这样的程序就是具有**可扩展性**的，我们可以通过从通用的基类继承出新的数据类型，从而添加一些功能，那些操纵基类接口的方法就不需要任何改动就可以应用于新类。

#### 失灵了？

我们先来复习一下权限修饰符：

|  作用域   | 当前类 | 用一个package | 子孙类 | 其他package |
| :-------: | :----: | :-----------: | :----: | :---------: |
|  public   |   √    |       √       |   √    |      √      |
| protected |   √    |       √       |   √    |      ×      |
|  default  |   √    |       √       |   ×    |      ×      |
|  private  |   √    |       ×       |   ×    |      ×      |

- public：所有类可见
- protected：本类、本包和子类都可见
- default：本类和本包可见
- private：本类可见

**私有方法带来的失灵**：

复习完我们再来看一组代码：

```java
public class PrivateScope {

    private void f() {
        System.out.println("PrivateScope f()");
    }

    public static void main(String[] args) {
        PrivateScope p = new PrivateOverride();
        p.f();
    }
}

class PrivateOverride extends PrivateScope {

    private void f() {
        System.out.println("PrivateOverride f()");
    }
}
/* OUTPUT
 PrivateScope f()
*/
```

是否感到有点奇怪，为什么这个时候调用的`f()`是基类中定义的，而不像上面所述的那样，通过`动态绑定`，从而调用导出类`PrivateOverride`中定义的`f()`。不知道心细的你是否发现，基类中`f()`方法的修饰是**private**。没错，这就是问题所在，`PrivateOverride`中定义的`f()`方法是一个全新的方法，因为`private`的缘故，对子类不可见，自然也不能被重载。

*结论*：

只有非 `private` 修饰的方法才可以被覆盖

我们通过 Idea 写代码的时候，重写的方法头上可以标注`@Override`注解，如果不是重写的方法，标注`@Override`注解就会报错：

![](https://cbucbm.club/e903853b)

这样也可以很好的提示我们非重写方法，而是全新的方法。

**域带来的失灵**：

当小伙伴看到这里，就会开始认为所有事物（除`private`修饰）都可以多态地发生。然而现实却不是这样子的，**只有普通的方法调用才可以是多态的**。这边是多态的误区所在。

让我们再看看下面这组代码：

```java
class Super {
    public int field = 0;

    public int getField() {
        return field;
    }
}

class Son extends Super {
    public int field = 1;

    public int getField() {
        return field;
    }

    public int getSuperField() {
        return super.field;
    }
}

class FieldTest {
    public static void main(String[] args) {
        Super sup = new Son();
        System.out.println("sup.field:" + sup.field + " sup.getField():" + sup.getField());

        Son son = new Son();
        System.out.println("son.field:" + son.field + " son.getField:" + son.getField() + " son.getSupField:" + son.getSuperField());
    }
}
/* OUTPUT
sup.field:0 sup.getField():1
son.field:1 son.getField:1 son.getSupField:0
*/
```

从上面代码中我们看到`sup.field`输出的值不是 `Son` 对象中所定义的，而是`Super`本身定义的。这与我们认识的多态有点冲突。

![](https://cbucbm.club/5b08e46e)

其实不然，当`Super`对象转型为`Son`引用时，任何域访问操作都将由编译器解析，因此不是多态的。在本例中，为`Super.field`和`Son.field`分配了不同的存储空间，而`Son`类是从`Super`类导出的，因此，`Son`实际上是包含两个称为`field`的域：**它自己的+`Super`的**。

虽然这种问题看上去很令人头痛，但是我们开发规范中，通常会将所有的域都设置为 private，这样就不能直接访问它们，只能通过调用方法来访问。

**static 带来的失灵**：

看到这里，小伙伴们应该对多态有个大致的了解，但是不要掉以轻心哦，还有一种情况也是会出现失灵的，**那就是如果某个方法是静态的，那么它的行为就不具有多态性。**

老规矩，我们看下这组代码：

```java
class StaticSuper {

    public static void staticTest() {
        System.out.println("StaticSuper staticTest()");
    }

}

class StaticSon extends StaticSuper{

    public static void staticTest() {
        System.out.println("StaticSon staticTest()");
    }

}

class StaticTest {
    public static void main(String[] args) {
        StaticSuper sup = new StaticSon();
        sup.staticTest();
    }
}
/* OUTPUT
StaticSuper staticTest()
*/
```

**静态方法是与类相关联，而非与对象相关联**

### 3.构造器与多态

首先我们需要明白的是构造器不具有多态性，因为构造器实际上是`static`方法，只不过该`static`的声明是隐式的。

我们先回到开头的那段神秘代码：

![](https://cbucbm.club/d69568ac)

其中输出结果是：

```xml
/*
    polygon() before cal()
    square.cal(), border = 0
    polygon() after cal()
    square.square(), border = 4
*/
```

我们可以看到先输出的是基类`polygon`中构造器的方法。

这是因为基类的构造器总是在导出类的构造过程中被调用，而且是按照继承层次逐渐向上链接，以使每个基类的构造器都能得到调用。

![](https://cbucbm.club/d576b337)

因为构造器有一项特殊的任务：检查对象是否能正确的被构造。导出类只能访问它自己的成员，不能访问基类的成员（基类成员通常是private类型）。只有基类的构造器才具有权限来对自己的元素进行初始化。因此，必须令所有构造器都得到调用，否则就不可能正确构造完整对象。

步骤如下：

- 调用基类构造器，这个步骤会不断的递归下去，首先是构造这种层次结构的根，然后是下一层导出类，...，直到最底层的导出类
- 按声明顺序调用成员的初始化方法
- 调用导出类构造其的主体

打个不是特别恰当的比方：*你的出现是否先要有你父亲，你父亲的出现是否先要有你的爷爷，这就是逐渐向上链接的方式*

#### 构造器内部的多态行为

有没有想过如果在一个构造器的内调用正在构造的对象的某个动态绑定方法，那么会发生什么情况呢？
动态绑定的调用是在运行时才决定的，因为对象无法知道它是属于方法所在的那个类还是那个类的导出类。如果要调用构造器内部的一个动态绑定方法，就要用到那个方法的被覆盖后的定义。然而因为被覆盖的方法在对象被完全构造之前就会被调用，这可能就会导致一些难于发现的隐藏错误。

*问题引索*：

一个动态绑定的方法调用会向外深入到继承层次结构内部，它可以调动导出类里的方法，如果我们是在构造器内部这样做，那么就可能会调用某个方法，而这个方法做操纵的成员可能还未进行初始化，这肯定就会招致灾难的。

敏感的小伙伴是不是想到了开头的那段代码：

![](https://cbucbm.club/d69568ac)

输出结果是：

```xml
/*
    polygon() before cal()
    square.cal(), border = 0
    polygon() after cal()
    square.square(), border = 4
*/
```

我们在进行`square`对象初始化的时候，会先进行`polygon`对象的初始化，在`polygon`构造器中有个`cal()`方法，这个时候就采用了动态绑定机制，调用了`square`的`cal()`，但这个时候`border`这个变量尚未进行初始化，int 类型的默认值为 0，因此就有了`square.cal(), border = 0`的输出。看到这里，小伙伴们是不是有种拨开云雾见青天的感觉！

这组代码初始化的实际过程为：

- 在其他任何事物发生之前，将分配给对象的存储空间初始化成二进制的零
- 调用基类构造器时，会调用被覆盖后的`cal()`方法，由于步骤1的缘故，因此 border 的值为 0
- 按照声明的顺序调用成员的初始化方法
- 调用导出类的构造器主体

呼~终于复习完多态了，幸好是梦，没人发现我的菜。不知道电脑手机前的你，是否跟小菜一样呢，如果是的话赶紧跟小菜一起复习，不让别人发现自己还不会多态哦！

`不知道下次又会做什么样的梦~`

![看完不赞，都是坏蛋](https://imgconvert.csdnimg.cn/aHR0cHM6Ly93d3cuNTJkb3V0dS5jbi9zdGF0aWMvdGVtcC9waWMvOWJkNjhkMTUwZjA3ODdjNTYwYTQzOWRhMzU5YTU4MGEucG5n?x-oss-process=image/format,png#pic_center)
> 今天的你多努力一点，明天的你就能少说一句求人的话！
>
> *我是小菜，一个和你一起学习的男人。* `💋`
>
>
> 微信公众号已开启，**小菜良记**，没关注的小伙伴记得关注哦！