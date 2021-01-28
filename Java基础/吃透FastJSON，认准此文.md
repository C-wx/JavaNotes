大家好，我是小菜，一个渴望在互联网行业做到蔡不菜的小菜。可柔可刚，点赞则柔，白嫖则刚！
**死鬼~看完记得给我来个三连哦！**


![](https://user-gold-cdn.xitu.io/2020/4/11/17169c46045528af?w=240&h=224&f=jpeg&s=7529)


>本文主要介绍 `FastJSON 的使用`
>
>如有需要，可以参考
>
>如有帮助，不忘 **点赞** ❥
>
>
>微信公众号已开启，**小菜良记**，没关注的同学们记得关注哦！

####  JSON 介绍

> JSON(JavaScript Object Notation) 是一种轻量级的数据交换格式。它使得人们很容易的进行阅读和编写。同时也方便了机器进行解析和生成。它采用一种 **"键 : 值"** 对的文本格式来存储和表示数据，在系统交换数据过程中常常被使用，是一种理想的数据交换语言。

**"XML 的时代已经过去，现在是 JSON 的时代"** 。相信现在这个观点很多人已经默默认同，那么我们是否有认真思考过为什么现在 **JSON** 能够顶替 **XML** 的地位。我们来简单看下两种的表示方式：

```xml
<?xml version="1.0" encoding="gb2312"?>
<class>
    <stu id="001">
        <name>杨过</name> 
        <sex>男</sex>
        <age>20</age>
    </stu>  
    <stu id="002">
        <name>小龙女</name>    
        <sex>女</sex>
        <age>21</age>
    </stu>
</class>
```

```json
[
    {
        "id": "001",
        "name": "杨过",
        "sex": "男",
        "age": "20"
    },
    {
        "id": "002",
        "name": "小龙女",
        "sex": "女",
        "age": "21"
    }
]
```

两种方式都是用来描述简单的班级信息，信息不多，但是明显可以看出 **JSON** 比 **XML** 更加简洁。具体区别可为以下几点：

- **可读性：** JSON 和 XML 的可读性可谓不相上下，一边是简易的语法，一边是规范的标签形式，很难分出胜负
- **可扩展性：** XML 天生有很好的扩展性，JSON 当然也有，因此 XML 能扩展的，JSON 也可以扩展
- **编码难度：** XML 有丰富的编码工具，比如 DOM4J，JDom 等，JSON 也提供许多工具。但是在没有工具的情况下，因为 XML 有很多结构上的字符，编程难度相对较高。
- **解码难度：** XML 的解析需要考虑到子节点父节点，难度较大，而 JSON 的解析难度几乎为 0，看上去就能理解数据结构

#### JSON 认知

##### JSON 具有以下形式

-  ##### `JSON 对象`

![源网侵删](http://www.json.org.cn/misc/object.gif)

```json
{
    "id": "002",
    "name": "小龙女",
    "sex": "女",
    "age": "21"
}
```

这就是一个简单的JSON 对象，我们观察可以得出 JSON 的一些语法：

1. 数据在花括号中 `[]`
2. 数据以 `键 : 值` 对的形式出现（其中键多以字符串的形式出现，值可为字符串，数值，以及 JSON 对象）
3. 每两个 `键 : 值` 对以逗号分隔 `,` ， 最后一个键值对需省略 `,`

我们按照上面的 **3** 点特征，便可很简单的构建出一个 JSON 对象

- ##### `JSON 数组`

![源网侵删](http://www.json.org.cn/misc/array.gif)

```json
["value1","value2","value3"]
```

或

```json
[
    {
        "id": "001",
        "name": "杨过",
        "sex": "男",
        "age": "20"
    },
    {
        "id": "002",
        "name": "小龙女",
        "sex": "女",
        "age": "21"
    }
]
```

数组的表示方式也很简单：

1. 头尾由 `[]` 包裹
2. 数据主键以 `,` 隔开

- ##### `JSON 字符串`

![源网侵删](http://www.json.org.cn/misc/string.gif)

```json
'{"id": "001", "name": "杨过", "sex": "男", "age": "20"}'
```

JSON 字符串与 Java 的字符串非常相似。

1. 它必须以 `""` 或者 `''` 包裹数据，支持字符串的各种操作
2. 里面的数据格式可以为 **json对象**，也可以是 **json数组**亦或者是两个基本形式的组合变形

以上便是 **JSON** 的基本形式，**JSON** 可以使用于各种语言，每个语言皆有自己各自的 **JSON** 实现方式。下面我们主要来熟悉一下 **Java** 语言中的 **FastJSON** 的使用。

#### FastJSON 

>  **FastJSON** 是由阿里巴巴工程师基于 JAVA 开发的一款 JSON 解析器和生成器，可用于将 Java 对象转换为其 JSON 表示形式，它还可以用于将 JSON 字符串转换为等效的 Java 对象。**FastJSON** 可以处理任意 Java 对象，包括没有源代码的预先存在的对象

FastJSON 使用十分方便，我们只需要在 Maven 工程的 **pom** 文件中引入以下依赖即可：

```xml
<dependency>
    <groupId>com.alibaba</groupId>
    <artifactId>fastjson</artifactId>
    <version>1.2.73</version>
</dependency>
```

FastJSON API 的入口类是 `com.alibaba.fastjson.JSON`，常用的序列化操作都可以在`JSON`类上的静态方法直接完成。

![](https://cbuc.top/1611323054215.png)

##### `API`

我们已经在项目中引入 **FastJSON** 的依赖，和已经存在了一个用户类：

```java
@Data
public class User {
    private int id;

    private String name;

    private String sex;
    
    private int age;
}
```

###### `toJSONString(Object o)`

这个方法平时最常见了，将**JavaBean**序列化成 JSON 文本

![](https://cbuc.top/1611399303500.png)

我们通过传入一个对象，便可以将对象转成 JSON 字符串，这里我们传入的不仅仅是 **JavaBean** 还可以是一个 **Map 对象**

![](https://cbuc.top/1611399535458.png)

传入一个 **Map 对象** 我们同样可以获取到一个 JSON 字符串。**List 对象**也很适用：

![](https://cbuc.top/1611400624958.png)

结果是一个标准的 **JSONArray** 的字符串

如果说 `toJSONString(Object o)` 的输出结果只有单调的一行让你看起来有点吃力，那么我们可以使用 `toJSONString(Object o, boolean prettyFormat)` 来让输出结果看起来舒服点：

![](https://cbuc.top/1611400823965.png)

通过 `JSON` 自带的格式化，让输出结果看起来更加清晰，真是贴心~

有小伙伴估计说到这两种我平时都用腻歪了，哪里有的着在你这，小菜一想，言之有理。那就介绍一下 `toJSONString` 的扩展用法。

`JSON.toJSONString(Object object, SerializerFeature... features) `

我们可以看到这个方法里面有个参数 ` SerializerFeature...`，可能感到有点陌生，确实，我也很陌生，我们先来看下什么是 **SerializerFeature**，通过源码我们可以发现 **SerializerFeature** 原来是个枚举类：

![](https://gitee.com/cbuc/picture/raw/master/20210123200621.png)

看到这张图我们不要晕，里面虽然有很多实例，但是大部分都被 `@deprecated` 注释了，说明这些已经废弃了。那有哪些是我们平时经常用到的呢：

|                    对象                    |                             描述                             |
| :----------------------------------------: | :----------------------------------------------------------: |
|    `SerializerFeature.UseSingleQuotes`     |           使用单引号而不是双引号，默认为**false**            |
|      `SerializerFeature.PrettyFormat`      |               结果是否格式化，默认为**false**                |
| `SerializerFeature.WriteDateUseDateFormat` | 如果时间是data、时间戳类型，按照这种格式初始化时间 **"yyyy-MM-dd HH:mm"** |
|   `SerializerFeature.WriteMapNullValue`    |           是否输出值为null的字段，默认为**false**            |
|     `SerializerFeature.WriteClassName`     |            序列化时写入类型信息，默认为**false**             |

使用案例：

- `SerializerFeature.UseSingleQuotes`

使用单引号而不是双引号,默认为**false**

![](https://gitee.com/cbuc/picture/raw/master/20210123201944.png)

- `SerializerFeature.PrettyFormat`

结果是否格式化,默认为**false**

![](https://gitee.com/cbuc/picture/raw/master/20210123202530.png)

- `SerializerFeature.WriteDateUseDateFormat`

如果时间是data、时间戳类型，按照这种格式初始化时间 **"yyyy-MM-dd HH:mm"**

![](https://gitee.com/cbuc/picture/raw/master/20210123203331.png)

通过这种方式我们将日期输出成了固定的格式：**yyyy-MM-dd HH:mm**，有时候我们不想得到这种格式那该怎么办，办法总会有的：

![](https://gitee.com/cbuc/picture/raw/master/20210123203442.png)

这个方式支持自定义时间格式，但是用到方法是 `toJSONStringWithDateFormat()`，这里需要注意下，不要到时候用错了方法，还说小菜 **渣男** ~

- `SerializerFeature.WriteMapNullValue`

是否输出值为null的字段，默认为**false**。

这个用什么用处了，我们应该很清楚开发规范中鼓励用**JavaBean**传递参数，尽量减少通过 **Map** 传递参数，因为 **Map** 相当于一个黑盒，对于使用者来说根本不知道里面存在哪些字段，而对于创建者来说估计也会忘记里面存在哪些字段，为了解决这个痛，**JSON** 也推出了解决方法：

![](https://gitee.com/cbuc/picture/raw/master/20210123210048.png)

通过普通方式的 `toJSONString()` 方法，空值仿佛被 **吃掉** 了，这可是一个灾难！

- `SerializerFeature.WriteClassName`

序列化时写入类型信息，默认为**false**。这个方法可以在反序列化的时候用到，用法如下：

![image-20210123210452475](C:/Users/Cbuc/AppData/Roaming/Typora/typora-user-images/image-20210123210452475.png)

通过这样我们可以看到我们序列化的对象是什么类型的。

上面这些便是 **toJSONString** 的扩展用法，小伙伴们有没有慢慢的收获

> vx 搜：小菜良记
>
> 更多干货值得关注，每篇都是初恋的味道，钉~

- ###### **`parseObject(String text)`**

上面说到的是 **序列化**，那么对应的便是 **反序列化**

反序列化就是把JSON格式的字符串转化为Java Bean对象。

![](https://gitee.com/cbuc/picture/raw/master/20210123213130.png)

用法十分简单，可以将一个标准的 **JSON 字符串** 转为一个 **JSONObject 对象**，由于 **JSONObject 类** 实现了 **Map 接口**，因此我们可以通过 `get()` 来获取到值。

我们已经知道了 **Map** 的致命不足，所以我们更希望能得到一个 **JavaBean** 对象。

![](https://gitee.com/cbuc/picture/raw/master/20210123213530.png)

当然也是可以的！我们通过传入我们想要转换的对象类型，就可以得到我们想要的 **JavaBean**

除了 **基本反序列化** 之外，还有一种 **泛型反序列化** 可供使用

![](https://gitee.com/cbuc/picture/raw/master/20210123222412.png)

通过 **泛型** ，我们就可以不用传入一个 **Class 对象**，而直接获取到我们的 **JavaBean**

**FastJSON** 序列化还有一个用处那便是进行 **深克隆**。有看过我前面文章的小伙伴们相信现在对软件设计模式都有一定的了解了，其中`原型模式`涉及到的 **深克隆** 和 **浅克隆**。

浅克隆的实现方式十分简单，我们只需要实现 **Cloneable** 接口，然后重写 `clone()` 方法 :

![](https://gitee.com/cbuc/picture/raw/master/20210124115544.png)

结果中我们看到的**好人卡** 都是属于`小王`的，这就是 **浅克隆** 的弊端的了。我们想要实现 **深克隆** 有许多种方式：

- 手动为引用属性赋值
- 借助 **FastJSON**
- 使用 **java 流**的序列化对象

方法有许多，我们重点看下 `FastJSON` 的实现方式：
![](https://gitee.com/cbuc/picture/raw/master/20210124115820.png)

通过 `FastJSON` 的反序列化，我们得到的两个对象实际上是不同的，这也很方便的实现了 **深克隆**。

> 更多设计模式的了解，请移位：
>
> [2021还不多学几种创建型模式，创建个对象！](https://mp.weixin.qq.com/s/UPS1CMn6Q0ng3WoUa7-LJQ)
>
> [图文并茂走进《结构型模式》，原来这么简单！](https://mp.weixin.qq.com/s/xnX2s5Ub38m2O2fOPJDdkw)
>
> [敲黑板了！《行为型模式》来袭](https://mp.weixin.qq.com/s/gx6MyvogT2_1M9XDSowV4A)

###### **`parseArray(String text)`**

这是一个将 **JSON字符串** 转为 **JSONArray** 的方法

![](https://gitee.com/cbuc/picture/raw/master/20210123232533.png)

同样我们也可以通过使用 **泛型序列化** 来实现同样的功能：

![](https://gitee.com/cbuc/picture/raw/master/20210124212155.png)

这种方式有个坑就是，我们使用 `parseArray()` 这个方法的时候第二个参数需要传入我们要反序列化的对象类型，不知道你有没有为传入一个数组感到奇怪，有没有为数组里面有什么放了两个一样的`type`感到奇怪？没错！这就是这个方法的坑，我们 **List** 里面有多少个对象，	`Type[]` 这个数组里面的个数要与之匹配，不然会抛出以下错误：

![](https://gitee.com/cbuc/picture/raw/master/20210124212441.png)

但是如果一个 **List** 中存在多个不同类型的对象时，我们可以使用这个方法：

![](https://gitee.com/cbuc/picture/raw/master/20210124214259.png)

###### **`toJSONBytes(Object o)`**

将**JSON**对象转换成**Byte**(字节)数组

我们平时在进行网络通讯的时候，需要将对象转为字节然后进行传输。我们使用字符串的时候，不由然的可以想到字符串中有个很便捷的 **API** 可以将字符串转为字节数组

```java
String str = "小菜";
byte[] bytes = str.getBytes();
```

但是我们如果要将一个 **JavaBean** 对象转为字节数组的时候，我们得借助 **ByteArrayOutputStream** 流的帮助：

![](https://gitee.com/cbuc/picture/raw/master/20210124215144.png)

这种方式也可以很好的将 **JavaBean** 对象转为字节数组，但是代码不免有点多了！而 **FastJSON** 中也提供了很方便的 **API** 以供使用：

![](https://gitee.com/cbuc/picture/raw/master/20210124215409.png)

而我们要将字节数组转为对象，**FastJSON** 也同样支持：

![](https://gitee.com/cbuc/picture/raw/master/20210124220605.png)

从`parseObject()`这个方法中我们又看到了一个奇怪的参数 `Feature`，我们点击进入源码可以发现这其实也是一个枚举类：

![](https://gitee.com/cbuc/picture/raw/master/20210124220131.png)

看的同样云里雾里的，这么多对象实例，以下我们对比较常用的做出了注释：

|               对象               |                             描述                             |
| :------------------------------: | :----------------------------------------------------------: |
|    `AllowUnQuotedFieldNames`     |            决定parser是否将允许使用非双引号属性名            |
|       `AllowSingleQuotes`        |       决定parser是否允许单引号来包住属性名称和字符串值       |
|        `InternFieldNames`        | 决定JSON对象属性名称是否可以被String#intern 规范化表示，如果允许，则JSON所有的属性名将会 intern() ；如果不设置，则不会规范化，默认下，该属性是开放的。 |
|     `AllowISO8601DateFormat`     | 设置为true则遇到字符串符合ISO8601格式的日期时，会直接转换成日期类 |
|      `AllowArbitraryCommas`      |      允许多重逗号,如果设为true,则遇到多个逗号会直接跳过      |
|         `UseBigDecimal`          |    设置为true则用BigDecimal类来装载数字，否则用的是double    |
|         `IgnoreNotMatch`         |                          忽略不匹配                          |
| `DisableCircularReferenceDetect` |                       禁用循环引用检测                       |
|     `InitStringFieldAsEmpty`     |               对于没有值得字符串属性设置为空串               |
|       `SupportArrayToBean`       |                        支持数组to对象                        |
|          `OrderedField`          |                      属性保持原来的顺序                      |
|    `DisableSpecialKeyDetect`     |                       禁用特殊字符检查                       |
|         `UseObjectArray`         |                         使用对象数组                         |

###### **`writeJSONString(OutputStream os, Object o)`**

这个方法是将对象写入输出流中的：

![](https://gitee.com/cbuc/picture/raw/master/20210124221514.png)

![](https://gitee.com/cbuc/picture/raw/master/20210124221550.png)

传入的参数还可以是一个 `Writer` 对象：

![](https://gitee.com/cbuc/picture/raw/master/20210124221803.png)

以上便是我们平时常用的 **API**，除此之外，在 **FastJSON** 中还有一个注解 `@JSONField` 我们也要学会灵活运用

##### `@JSONField`

###### 命名重塑

![](https://gitee.com/cbuc/picture/raw/master/20210126210154.png)

**注：** 若属性是 `私有`的，必须要有 `set()` 方法，否则无法反序列化！

`@JSONField` 用法简单，可以配置在 **getter()** 、**setter()** 或者 **属性字段** 上

![](https://gitee.com/cbuc/picture/raw/master/20210126211012.png)

测试结果：

![](https://gitee.com/cbuc/picture/raw/master/20210126211231.png)

这个方法的最大好处便是用来对接奇奇怪怪的文档，为什么说奇奇怪怪呢，有时候我们需要调用第三方的接口，但是这个接口返回的值可能是不符合命名规范的，那我们这边就需要定义一个实体类去接收它（Map虽然也行，但是也不规范）。

这个时候我们定义的实体类的属性名就得按照返回的字段名来命名，这对强迫症程序猿来说是致命打击，这个时候 `@JSONField` 的用处就来了，我们简单看个例子。有个车牌信息实体的返回字段是这样的：

```json
{"PLATEID" : 01, "PLATE": '闽A6666D', "IMAGEURL":'http://...'}
```

我们可以看到返回的字段名全都不满足小驼峰规则，我们定义的实体类可不能这样，借助 `@JSONField` 的写法如下：

![](https://gitee.com/cbuc/picture/raw/master/20210126213232.png)

测试下是否能够成功接收结果：

![](https://gitee.com/cbuc/picture/raw/master/20210126220717.png)

可以看到我们已经成功接收到结果了，而且实体类的命名也符合我们的规范，一举两得。

![](https://gitee.com/cbuc/picture/raw/master/20210126220610.jpeg)

###### DataFormat

我们也可以使用该注解来将我们的日期格式化成我们想要的样子

![](https://gitee.com/cbuc/picture/raw/master/20210126224624.png)

###### 控制序列化

在序列化或反序列化的时候我们可以指定字段不序列化，这个有点像 **Java 流**中的 `transient` 修饰。**FastJSON** 中也可以实现相似的功能：

![](https://gitee.com/cbuc/picture/raw/master/20210126225115.png)

但是反序列化有个缺点就是，虽然值是空的，但是属性名还在~

###### ordinal

我们可以使用**ordinal**来指定字段的顺序

![](https://gitee.com/cbuc/picture/raw/master/20210126225557.png)

通过接收结果可以看到 **属性字段** 按照我们规定的顺序所排列，用处可以在于我们返回字段给前端过多的时候，将有用的字段优先排列到前面，可以更好的取值，而不用一层一层的查找需要的字段。

###### 定制序列化

万物皆可定制，序列化也不例外~ 我们可以使用**serializeUsing**制定属性的序列化类

![](https://gitee.com/cbuc/picture/raw/master/20210126235334.png)

通过这种方式我们针对 **age** 这个属性进行了处理，展示加上了单位.

