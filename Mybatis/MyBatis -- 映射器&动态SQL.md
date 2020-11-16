大家好，我是小菜，一个渴望在互联网行业做到蔡不菜的小菜。可柔可刚，点赞则柔，白嫖则刚！
**死鬼~看完记得给我来个三连哦！**


![](https://user-gold-cdn.xitu.io/2020/4/11/17169c46045528af?w=240&h=224&f=jpeg&s=7529)


>本文主要介绍 `Mybatis中的映射器和动态 SQL`
>
>如有需要，可以参考
>
>如有帮助，不忘 **点赞** ❥
>
>
>微信公众号已开启，**小菜良记**，没关注的同学们记得关注哦！

*参考书籍*：【深入浅出MyBatis技术原理与实战】

上篇博文重点讲到了 MyBatis 中**配置文件标签**的介绍 

[Mybatis 配置](https://juejin.im/post/6864415407043051534)

没看过的同学，可以看看，没看过也不影响这篇博文的吸收哦！那么接下来就进入今天的主场吧，show time！

![](https://ftp.bmp.ovh/imgs/2020/08/13e9870702e285c5.gif)

## 开胃菜

`MyBatis` 是针对映射器构造的 SQL 构建的轻量级框架，并且通过配置生成对应的 `JavaBean` 给调用者，而这些配置主要便是映射器，在 MyBatis 中可以根据情况定义`动态 SQL` 来满足不同场景的需要，因此这就是它比其他框架更加灵活的原因。

映射器的主要元素：

| 元素名称     | 描述                          |
| ------------ | ----------------------------- |
| select       | 查询语句                      |
| insert       | 插入语句                      |
| update       | 更新语句                      |
| delete       | 删除语句                      |
| parameterMap | 定义入参映射关系              |
| resultMap    | 定义结果集映射关系            |
| sql          | 自定义SQL，可以在其他地方引用 |
| cache        | 给定命名空间的缓存配置        |
| cache-ref    | 其他命名空间缓存配置的引用    |

## 硬菜 之 映射器详解

### select 元素

`select` 元素是我们最常用的 SQL 语言。在执行 select 语言之前，我们需要定义参数，参数类型可以是基本数据类型，例如：int、float、String，也可以是复杂的参数类型，例如：javaBean、Map等。执行完 SQL，MyBatis 也支持参数映射，可以通过自动映射来把返回的结果集绑定到 JavaBean 中。

下面以 select 中常间的元素：

| 元素          | 描述                                                         |
| ------------- | ------------------------------------------------------------ |
| id            | 标识唯一，提供调用，并与接口的方法对应                       |
| parameterType | 参数类型，可以用类的全命名，也可以用别名，但别名要事先定义好，可以使用JavaBean、Map进行参数传递 |
| resultType    | 定义类的全路径，在自动匹配的情况下结果集可以通过JavaBean的规范映射，可以使用别名，也可定义为int，double、float等参数 |
| resultMap     | 它是映射集的引用，我们可以使用 resultMap 或者 resultType，其中 resultMap 给了我们更好的自定义规则 |
| flushCache    | 调用查询之后清空本地缓存和二级缓存                           |
| useCache      | 启动二级缓存                                                 |
| timeOut       | 超时参数，指定SQL超出规定时间会抛出异常                      |
| fetchSize     | 获取记录的总条数设定                                         |

**例子**：

*定义一个 student.xml*：

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE mapper PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN" "http://mybatis.org/dtd/mybatis-3-mapper.dtd" >
<mapper namespace="cbuc.ssm.mapper.StudentMapper">
    <select id="selectCountBySelective" resultType="int" parameterType="string">
        select count(*)
        from student
        where name like CONCAT('%',#{remark},'%')
    </select>
</mapper>
```

*然后定义一个studentMapper.class*：

```java
package cbuc.ssm.mapper;

public interface StudentMapper {
    int selectCountBySelective(String remark);
}
```

其中`mapper`的 namespace（命名空间）需要对应 StudentMapper类的全路径，`select`标签的**id**，需要与`StudentMapper`的*selectCountBySelective()* 方法对应，**parameterType** 定义参数类型，**resultType**定义返回值类型。

#### 自动映射

**自动映射** 也是 MyBatis 的一个特点，当`autoMapperingBehavior` 不设置为 NONE 的时候，MyBatis 会给我们提供自动映射的功能。前提就是 SQL 返回的列名，需要和 JavaBean 的属性一致，这样 MyBatis 就会自动帮我们回填这些字段值，当表中列名是以下划线命名的时候我们可以在配置文件中开启驼峰映射规则。

```java
<settings>
    <!-- 开启驼峰命名转换 -->
    <setting name="mapUnderscoreToCamelCase" value="true" />
</settings>
```

**例子**：

*Student.class*：

```java
public class Student {

    private Long id;

    private String name;

    private String remark;
	
    // 省略get/set
}
```

*StudentMapper.class*：

```java
public interface StudentMapper {
    Student selectByName(String name);
}
```

*Student.xml*：

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE mapper PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN" "http://mybatis.org/dtd/mybatis-3-mapper.dtd" >
<mapper namespace="cbuc.ssm.mapper.StudentMapper">
    <select id="selectByName" resultType="cbuc.ssm.entity.Student" parameterType="string">
        select id, name, remark
        from student
        where name like concat('%',#{name},'%')
    </select>
</mapper>
```

在上面例子中，我们是通过`name`来查找一条学生记录，其中表字段为：

|  字段  |    类型     |  描述  |
| :----: | :---------: | :----: |
|   id   |   INT(10)   | 主键ID |
|  name  | VARCHAR(8)  |  名称  |
| remark | VARCHAR(64) |  备注  |

可以看出表字段的名称是和实体属性对应的，如果多了一个表字段 create_time，而实体中属性的命名是createTime，那么只要在配置中开启驼峰映射，也是可以自动映射上的。除此之外，配置中 `settings`属性还可以配置 `autoMappingBehavior`来设置其他映射策略。

|      名称       |                       描述                       |
| :-------------: | :----------------------------------------------: |
|      NONE       |                   取消自动映射                   |
| PARTIAL（默认） | 只会进行自动映射，没有定义嵌套结果集映射的结果集 |
|      FULL       |    会自动映射任意复杂的结果集（无论是否嵌套）    |

#### 多参数传递

上面例子中我们是通过一个 String 类型的参数进行传递，但是很多时候我们传递的参数不止一个，这个时候怎么办呢

![](https://cbucbm.club/cc78d0c126e6c47785001f6e486fc236.jpg)

当然这个时候 MyBatis 也帮我们解决了后顾之忧

- **Map**

遇到多个参数的时候我们可以借助 Map 来进行参数的传递

*StudentMapper.class*：

```java
public interface StudentMapper {
    Student selectByCondition(Map conditionMap);
}
```

*Student.xml*：

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE mapper PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN" "http://mybatis.org/dtd/mybatis-3-mapper.dtd" >
<mapper namespace="cbuc.ssm.mapper.StudentMapper">
    <!--根据条件查询-->
    <select id="selectByCondition" resultType="cbuc.ssm.entity.Student" parameterType="map">
        select id, name, remark
        from student
        where name like CONCAT('%',#{name},'%') and remark like CONCAT('%',#{remark},'%')
    </select>
</mapper>
```

*执行查询*：

```java
Map<String, Object> conditionMap = new HashMap<>();
conditionMap.put("name", "菜");
conditionMap.put("remark", "关注小菜");
Student resStudent = studentMapper.selectByCondition(conditionMap);

/***	输出结果	***/
/*
Student{id=1, name='小菜', remark='关注小菜不迷路！'}
*/
```

在查询的xml文件中，我们定义的 `parameterType` 是 `map`，成功实现了多个参数进行传递，但是这个也有个不好的地方就是，map 对于我们来说就是个黑箱，我们不清楚里面存在哪些字段，可读性也相对比较低，因此我们不妨再看看下面几种方法！

- **JavaBean**

既然我们在返回结果的时候能够将结果集自动映射到 JavaBean 中，那么我们在传递参数的时候是否也可以通过 JavaBean 的方式进行传递呢？答案是可以的

*Student.class*：

```java
public class Student {

    private Long id;

    private String name;

    private String remark;
	
    // 省略get/set
}
```

*StudentMapper.class*：

```java
public interface StudentMapper {
    Student selectBySelective(Student student);
}
```

*Student.xml*：

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE mapper PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN" "http://mybatis.org/dtd/mybatis-3-mapper.dtd" >
<mapper namespace="cbuc.ssm.mapper.StudentMapper">
    <!--根据条件查询-->
    <select id="selectBySelective" resultType="cbuc.ssm.entity.Student" parameterType="cbuc.ssm.entity.Student">
        select id, name, remark
        from student
        where name like CONCAT('%',#{name},'%') and remark like CONCAT('%',#{remark},'%')
    </select>
</mapper>
```

*执行查询：*

```java
Student conditionStudent = new Student();
conditionStudent.setName("菜");
conditionStudent.setRemark("关注小菜");
Student resStudent = studentMapper.selectBySelective(conditionStudent);
System.out.println(resStudent);

/***	输出结果	***/
/*
Student{id=1, name='小菜', remark='关注小菜不迷路！'}
*/
```

在查询的xml文件中，我们定义的 `parameterType` 是 `Student`这个 JavaBean的全路径，然后我们就可以实现通过 JavaBean作为参数传递进行查询

- **@Param 注解**

最后一种传递参数的方式就是通过 @Param 标记入参的键方式来传递方式

*StudentMapper.class*：

```java
public interface StudentMapper {
    Student selectByParams(@Param("name") String name, @Param("remark") String remark);
}
```

*Student.xml*：

```java
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE mapper PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN" "http://mybatis.org/dtd/mybatis-3-mapper.dtd" >
<mapper namespace="cbuc.ssm.mapper.StudentMapper">
    <!--根据条件查询-->
    <select id="selectByParams" resultType="cbuc.ssm.entity.Student" parameterType="string">
        select id, name, remark
        from student
        where name like CONCAT('%',#{name},'%') and remark like CONCAT('%',#{remark},'%')
    </select>
</mapper>
```

当我们把参数传递到后台的时候，通过@Param 提供的名称 MyBatis 就会知道 #{name} 代表了 name 参数，可读性是加强了，但是如果传递的参数很多，那么这个方法是不是会很长，这就是使用注解的弊端之一，因此在传递多个参数的时候还是推荐使用 JavaBean 的方式。

#### resultMap 结果集映射

在上面我们看到的查询返回的定义都是使用 `resultType`来映射结果集，其实还有一个标签也能用来映射结果集，那就是使用 resultMap 来映射结果集，而且功能更加强大

**简单例子**：

*Student.xml*：

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE mapper PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN" "http://mybatis.org/dtd/mybatis-3-mapper.dtd" >
<mapper namespace="cbuc.ssm.mapper.StudentMapper">

    <!--自定义接收结果集-->
    <resultMap id="myResultMap" type="cbuc.ssm.entity.Student">
        <id column="id" property="id" javaType="long" jdbcType="BIGINT"/>
        <result column="name" property="name" javaType="string" jdbcType="VARCHAR"/>
        <result column="remark" property="remark" javaType="string" jdbcType="VARCHAR"/>
    </resultMap>

    <!--根据条件查询-->
    <select id="selectBySelective" resultMap="myResultMap" parameterType="cbuc.ssm.entity.Student">
        select id, name, remark
        from student
        where name like CONCAT('%',#{name},'%') and remark like CONCAT('%',#{remark},'%')
    </select>
</mapper>
```

在 select 标签上我们使用 `resultMap`来定义接收结果集，更强大的用法请往下看，这里先留个悬念！![](https://cbucbm.club/8f8699c72899595e8d3fa33e768aec5c.jpg)

### insert 元素

上面讲完了 `select` 元素，接下来就进入`insert` 元素环节

*insert 元素常用配置*：

|     属性名称     |                             描述                             |
| :--------------: | :----------------------------------------------------------: |
|        id        |            标识唯一，提供调用，并与接口的方法对应            |
|  parameterType   | 参数类型，可以用类的全命名，也可以用别名，但别名要事先定义好，可以使用JavaBean、Map进行参数传递 |
|    flushCache    |             执行 SQL 之后清空本地缓存和二级缓存              |
|     timeOut      |           超时参数，指定SQL超出规定时间会抛出异常            |
|   keyProperty    |     标识以哪个列作为属性的主键，不能 keyColumn 同时使用      |
| useGeneratedKeys | 让 MyBatis 使用 JDBC 的 getGenerateKeys 方法取出数据库生成的主键 |
|    keyColumn     | 标识第几列是主键，不能和 keyProperty 同时使用，只接受整形参数 |

有很多配置是和 select 相似的，学完前面的 select 相信对这些标签也不会太陌生

**简单例子**：

*StudentMapper.class*：

```java
public interface StudentMapper {
    int insertBySelective(Student student);
}
```

*Student.xml*：

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE mapper PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN" "http://mybatis.org/dtd/mybatis-3-mapper.dtd" >
<mapper namespace="cbuc.ssm.mapper.StudentMapper">
    <insert id="insertBySelective" parameterType="cbuc.ssm.entity.Student" keyProperty="id" useGeneratedKeys="true">
        insert into student
        (name, remark)
        values
        (#{name},#{remark})
    </insert>
</mapper>
```

*执行SQL*：

```java
Student insStudent = new Student();
insStudent.setName("帅菜");
insStudent.setRemark("帅菜有点甜");
studentMapper.insertBySelective(insStudent);
System.out.println(insStudent.getId());
/***	输出结果	***/
/*
		2
*/
```

从输出结果中我们可以得到这条记录的*ID为2*。

那么如果我们不想使用 MySql 的自动生成主键，而是用自己的生成策略那么该如何定义了，MyBatis 一样为我们准备好了！

*规则：如果数据库中没有数据的情况下，也就是 id 为空，那么插入数据的 id为1，如果有数据的情况下，插入数据的 ID 是最大 ID 的两倍*

**实现**：

**Student.xml**：

```xml
<insert id="insertBySelective" parameterType="cbuc.ssm.entity.Student" keyProperty="id" useGeneratedKeys="true">
    <selectKey keyProperty="id" resultType="long" order="BEFORE">
        select IF(ISNULL(MAX(id)), 1, MAX(id)*2) from student
    </selectKey>
    insert into student(id, name, remark) values (#{id}, #{name}, #{remark})
</insert>
```

*执行SQL*：

```java
Student insStudent = new Student();
insStudent.setName("帅菜");
insStudent.setRemark("帅菜有点甜");
studentMapper.insertBySelective(insStudent);
System.out.println(insStudent.getId());
/***	输出结果	***/
/*
		4
*/
```

因为此时数据库中已经有两天数据了，所以返回的 ID 为4，这也许便是 MyBatis 的性感之处吧，我们只需要简单定义配置，它就为我们做好了所有事。

![](https://cbucbm.club/05e9340d9b2e5ab0ceb3dfc126e1d2e9.gif)

### update 元素

`update`元素和`insert`元素一样，执行完 SQL，返回的结果便是数据库执行 SQL 影响的记录条数

**Student.xml**：

```xml
<update id="updateBySelective" parameterType="cbuc.ssm.entity.Student">
    update student set remark = #{remark} where name = #{name}
</update>
```

**StudentMapper.class**：

```java
public interface StudentMapper {
    int updateBySelective(Student student);
}
```

**执行更新**：

```java
Student updateStudent = new Student();
updateStudent.setRemark("小菜啊啊啊！");
updateStudent.setName("帅菜");
int res = studentMapper.updateBySelective(updateStudent);
System.out.println(res);
/***	输出结果	***/
/*
		1
*/
```

### delete 元素

`delete` 元素也和上面两个元素相似，返回的结果是数据库执行 SQL 影响的记录条数

**Student.xml**：

```xml
<delete id="deleteByPrimaryKey" parameterType="int">
    delete from student where id = #{id}
</delete>
```

**StudentMapper.class**：

```java
public interface StudentMapper {
    int deleteByPrimaryKey(int id);
}
```

**执行SQL**：

```java
int res = studentMapper.deleteByPrimaryKey(1);
System.out.println(res);
/***	输出结果	***/
/*
		1
*/
```

讲完了*增删改查*四大元素，接下来我们来点别的，换换口味
![](https://cbucbm.club/f659fc99081b351946bd4163c0a9fc46.jpg)

### sql 元素

开发中我们讲究的是 高内聚低耦合，那么 sql 元素存在的意义就是把相同的代码片段抽出来，达到可以复用的作用。

![](https://wx1.sbimg.cn/2020/08/18/3q7QJ.png)

我们从上图中不难发现，书写的 3 个 select 查询都用到了相同的字段（`id`,`name`,`remark`）。那么这部分冗余的代码我们就可以通过 `sql` 元素标签抽取出来，如下：

```xml
<!--定义了基本查询字段-->
<sql id="baseColumns">
    id, name, remark
</sql>
<!--通过 include 标签引用sql-->
<select id="selectByName" resultType="cbuc.ssm.entity.Student" parameterType="string">
    select <include refid="baseColumns"/>
    from student
    where name like concat('%',#{name},'%')
</select>
```

我们也可以给 sql 元素中传递值：

```xml
<sql id="baseColumns">
    #{tableName}.id, #{tableName}.name, #{tableName}.remark
</sql>
<!--通过 property 元素，将 t 这个值传递给 #{tableName}-->
<select id="selectByName" resultType="cbuc.ssm.entity.Student" parameterType="string">
    select
    <include refid="baseColumns">
        <property name="tableName" value="t"/>
    </include>
    from student t
    where name like concat('%',#{name},'%')
</select>
```

### cache 元素

缓存的出现是为了在读取的时候无需再从磁盘读入，具备快速读取和使用的特点，MyBatis 可以通过缓存将数据保存在内存中，如果缓存命中率高，那么可以极大地提高系统的性能。

#### 一级缓存和二级缓存

Mybatis 在没有配置缓存的情况下是默认开启*一级缓存*的（一级缓存只相对同一个 SqlSession 有效）。当参数和 SQL 完全一样的情况下，使用同一个 SqlSession 对象调用同一个 mapper 方法的时候，往往只会执行一次 SQL，因为在第一次查询后，MyBatis 会将结果放在缓存中，下次再查询的时候，没有使用`flushCache` 刷新缓存，并且缓存没有超时的情况下，SqlSession都只会取出当前缓存的数据，而不会再次发送SQL。当使用不同 SqlSession 对象执行mapper，就不会去缓存中的数据，因为每个 SqlSession 之间是相互隔离的。

SqlSessionFactory 的二级缓存是默认不开启的，二级缓存是作用在命名空间上的，如果开启我们需要在配置中声明，并且MyBatis要求返回的 JavaBean 必须要*可序列化*的，可以通过以下方法声明缓存：

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE mapper PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN" "http://mybatis.org/dtd/mybatis-3-mapper.dtd" >
<mapper namespace="cbuc.ssm.mapper.StudentMapper">
	<!--开启二级缓存-->
    <cache/>
</mapper>
```

如果我们只配置`<cache/>` 这个定义的话，那么默认配置为：

- 映射语句文件中所有的 SELECT 语句将会被缓存
- 映射语句文件中所有的 INSERT、UPDATE、DELETE 语句都会刷新缓存
- 缓存会使用默认的 LRU（Least Recently Used 最近最少使用）算法来回收
- 缓存会存储列表集合或对象（无论查询方法返回什么）的引用
- 缓存会被视为 read/write（可读/可写）的，可以安全地被调用者修改，不干扰其他调用者线程所做的潜在修改

添加配置后，我们要记得让 *JavaBean 实现 Serializable 接口，否则会抛出异常*

用过这个标签的小伙伴就会知道，cache 元素里面还存在其他属性，如下：

1. `eviction`：代表的是缓存回收策略
   - LRU：最近最少使用，移除最长时间不用的对象
   - FIFO：先进先出，按对象进入缓存的顺序来移除它们
   - SOFT：软引用，移除基于垃圾回收器状态和软引用规则的对象
   - WEAK：弱引用，更积极地移除基于垃圾收集器状态和弱引用规则的对象，采用的是 LRU，移除最长时间不用的对象
2. `flushInterval`：刷新间隔时间，单位为毫秒，如果不配置的话，那么只有当 SQL 被执行的时候才会去刷新缓存
3. `size`：引用数目，代表缓存最多可以存储多少个对象，*实际开发中不要设置过大，否则会导致内存溢出*
4. `readOnly`：只读，意味缓存数据只能读取而不能修改，可以加快缓存读取速度

#### 自定义缓存

MyBatis 的性感之处就是在乎它愿意包容我们，允许我们自定义

缓存我们也是可以自定义的，只需要实现 `org.apache.ibatis.cache.Cache`接口，然后在配置中声明，步骤如下：

- 定义`MyCache.class`：

![](https://cbucbm.club/a0550717)

- 在配置声明：

```xml
<cache type="cbuc.ssm.custom.MyCache>
```

这样子我们就可以使用我们自定义的缓存啦

### resultMap 元素

上面讲到 SELECT 元素的时候，我们留下了一个悬念，在这里就为大家介绍啦（可真坏坏！）

`resultMap` 是MyBatis 里面最复杂的元素，它的作用是定义映射规则，级联的更新，定制类型转化器等。听到这么多强大的功能，内心是否有点小激动，莫慌，待我细细道来

![](https://cbucbm.club/92c69eddae864d046d0b2ed8eb1d7f78.jpg)

#### resultMap元素的构成

![](https://cbucbm.club/code-snapshot%20%284%29.png)

- `constructor`

使用`constructor` 元素可以用来配置构造方法，一个 POJO 可能不存在无参构造方法，这个时候我们就可以使用 `constructor` 进行配置。在`Student`类中不存在无参构造函数，只要一个构造函数：

```java
public Student(Long id, String name, String remark) {
    this.id = id;
    this.name = name;
    this.remark = remark;
}
```

然后我们需要配置这个结果集：

```xml
<resultMap id="myResultMap" type="cbuc.ssm.entity.Student">
    <constructor>
        <idArg column="id" javaType="int"/>
        <arg column="name" javaType="string"/>
        <arg column="remark" javaType="string"/>
    </constructor>
</resultMap>
```

通过这样子配置之后，MyBatis 就知道需要使用哪个构造方法来构建 POJO了

- `id 和 result`

id 元素是用来标识哪个列是主键，允许多个主键，多个主键称为联合主键。

result 元素是配置 POJO 到 SQL 列名的映射关系

两个元素的共同属性如下：

|  元素名称   |                      说明                      |
| :---------: | :--------------------------------------------: |
|  property   | 映射到列结果的字段或属性，也就是 POJO 的属性名 |
|   column    |               对应数据表中的列名               |
|  javaType   |             POJO 属性的 Java 类型              |
|  jdbcType   |                数据表中列的类型                |
| typeHandler |                   类型处理器                   |

- 级联属性

`association`、`collection`都是属于比较常用到级联属性

- `association`：代表一对一关系，必须一个人有一张身份证是一对一个的关系
- `collection`：代表一对多关系，一个班级有多个学生

#### association

一对一关系，比如一个学生有一张学生卡，这个时候需要一个学生类和学生卡类

**student.class**：

```java
public class Student {

    private Long id;

    private String name;

    private String remark;

    private StudentCard studentCard;
    
    //省略get/set方法
}
```

**studentCard.class**：

```java
public class StudentCard {

    private Long id;

    private Date createTime;

    private String address;

    private Long studentId;
 
    //省略get/set方法
}
```

**studentCard.xml**：

![](https://cbucbm.club/c636f94d)

**student.xml**：

![](https://cbucbm.club/4ed34940)

**执行查询**：

```java
Student resStudent = studentMapper.selectByPrimaryKey(1);
System.out.println(resStudent);
/***	输出结果	***/
/*
Student{id=1, name='小菜', remark='关注小菜不迷路！', studentCard=StudentCard{id='1', createTime=Wed Jul 01 23:16:29 CST 2020, address='中国', studentId=1}}
*/
```

这样我们查到学生信息的同时也能够把学生证信息查到。上面的级联方式是通过 `select`去引入其它mapper 的查询结果，那么我们能不能直接用表连接的方式查询结果，然后自定义结果集呢，答案是可以的，方式如下：

**student.xml**：

![](https://cbucbm.club/72a7d8e0)

#### collection

一对多关系，比如一个学生有多张电话卡，这时候需要一个学生类和一个电话卡类

**student.class**：

```java
public class Student {
    private Long id;

    private String name;

    private String remark;

    private List<PhoneCard> phoneCardList;
    
    //省略get/set方法
}
```

**phoneCard.class**：

```java
public class StudentCard {
    private long id;

    private long studentId;

    private String phoneNum;
    
    //省略get/set方法
}
```

**phoneCard.xml**：

![](https://cbucbm.club/89b34f14)

**student.xml**：

![](https://cbucbm.club/76043b73)
**执行查询**：

```java
Student resStudent = studentMapper.selectByPrimaryKey(1);
System.out.println(resStudent);
/***	输出结果	***/
/*
Student{id=1, name='小菜', remark='关注小菜不迷路！', phoneCardList=[PhoneCard{id=1, studentId=1, phoneNum='13512138979'}, PhoneCard{id=2, studentId=1, phoneNum='13678657898'}]}
*/
```

在学生类中包含了一个类型为 `List<PhoneCard> phoneCardList`电话卡集合，使用 `collection` 级联就会使用`select` 中的查询方法然后自动将结果映射到 `phoneCardList` 中。

同样的也可以使用另一种级联方式：

![](https://cbucbm.club/d2847dd6)

*级联用起来确实挺方便的，能够方便快捷地获取数据，但是多层关联时（超过3层）尽量少用级联，因为不仅用处不大，而且户造成复杂度的增加，不利于他人的理解和维护。*

## 硬菜 之 动态SQL

我们在使用 JDBC 或者其他框架的时候需要根据实际条件去拼接 SQL ，这是件挺麻烦的事情。但是在我们使用 MyBatis 只有仿佛就不存在这些麻烦事。

MyBatis 提供对 SQL 语句动态的组装能力，而且它只有几个基本的元素，用起来也十分简单明了，大量的判断都可以在 MyBatis 的映射 XML 文件里面配置，以达到许多我们需要大量代码才能实现的功能，大大减少了我们编写代码的工作量。

*MyBatis 的动态 SQL 包含以下几种元素*：

|           名称            |                   作用                    |           备注            |
| :-----------------------: | :---------------------------------------: | :-----------------------: |
|            if             |                 判断语句                  |      单条件分支判断       |
| choose（when、otherwise） | 相当于 Java 中的 switch case default 语句 |      多条件分支判断       |
|     trim、where、set      |                 辅助元素                  | 用于处理一些 SQL 拼装问题 |
|          foreach          |                 循环语句                  | 在 in 语句等列举条件常用  |
|                           |              定义上下文变量               |  通过 OGNL 表达式自定义   |

### if 元素

看到 `if` 就莫名有一种亲切感，因为我们在平时开发中用到最多的应该就是`if` 了。

![](https://cbucbm.club/8d5713ca)

`if` 不仅在开发中用到比较多，在 MyBatis 中也是属于 *C位*的存在。它常常与`test`属性联合使用。在大部分情况下，`if`元素使用方法简单，下面是使用用例：

```xml
<!--根据条件查询-->
<select id="selectByCondition" resultType="cbuc.ssm.entity.Student" parameterType="map">
    select id, name, remark
    from student
    where 1 = 1
    <if test="name != null and name != ''">
        and name like CONCAT('%',#{name},'%') 
    </if>
    <if test="remark != null and remark != ''">
        and remark like CONCAT('%',#{remark},'%')
    </if>
</select>
```

就这？没错，`if`元素用起来就是这么简单，在`test`元素中添加判断的逻辑，成立则构造这个条件，这种场景在我们实际的开发中是十分常见的，通过 MyBatis 的条件语句我们可以节省许多拼接 SQL 的工作。

### choose 元素

有`choose`元素的地方就会有`when`，有时也会带上`otherwise`一起玩，相对于`if`元素非此即彼的关系，显然`choose`元素为我们选择了更多判断的机会，（*你看我还有三连的机会吗*）。`choose when otherwise`元素的用法跟 Java 中 `switch case default`相似

*有个场景如下*：

如果传入的`name`不为空，那我们就用`name`作为模糊查询条件，如果`name`为空而`remark`不为空，那么就用`remark`作用查询条件，如果两者都为空，就查询`name`不是空的记录。

*实现如下*：

```java
<!--根据条件查询-->
    <select id="selectByCondition" resultType="cbuc.ssm.entity.Student" parameterType="map">
        select id, name, remark
        from student
        where 1 = 1
        <choose>
            <when test="name != null and name != ''">
                and name like CONCAT('%',#{name},'%')
            </when>
            <when test="remark != null and remark != ''">
                and remark like CONCAT('%',#{remark},'%')
            </when>
            <otherwise>
                and name is not null
            </otherwise>
        </choose>
    </select>
```

一顿操作猛如虎，三下五除二的实现了功能，公司老大都忍不住给你加薪水了呢！

通过以上语句 MyBatis 就会根据参数的设置进行判断来动态组装 SQL，来满足不同的业务要求，语句清晰明了，功能一看便知。

### trim、where、set 元素

#### where

不知道细心的小伙伴有没有发现以上的 SQL 都带有 `where 1 = 1` 这样的语句，实际上像这种写法是十分不美观的，而且还影响 SQL  查询的性能，但是如果不加入这种写法，语句又不成立：

```sql
select id, name, remark from student where and name like CONCAT('%',#{name},'%') 
```

在你左右为难，写也不是不写也不是的情况下，你看到了小菜写的这篇文章，不禁感叹，*来的真**及时*。

在 MyBatis 中我们需要用到 `where` 查询的时候，它也给我们提供了一个 `where` 元素，用法如下：

```xml
<!--根据条件查询-->
<select id="selectByCondition" resultType="cbuc.ssm.entity.Student" parameterType="map">
    select id, name, remark
    from student
    <where>
        <if test="name != null and name != ''">
            and name like CONCAT('%',#{name},'%')
        </if>
        <if test="remark != null and remark != ''">
            and remark like CONCAT('%',#{remark},'%')
        </if>
    </where>
</select>
```

虽然每个判断中都带有 `and`，但是用了`where`元素，MyBatis 会自动帮我们拼接 SQL，把多余的`and` 去掉，达到我们预期的效果。

#### set

`update` 更新语句相信我们在实际开发中用的也不少了，当然 MyBatis 中也支持 `set`标签来帮助我们书写 SQL，用法如下：

```xml
<update id="updateBySelective" parameterType="cbuc.ssm.entity.Student">
    update student
    <set>
        <if test="name != null and name != ''">
            name = #{name},
        </if>
        <if test="remark != null and remark != ''">
            remark = #{remark},
        </if>
    </set>
    where name = #{id}
</update>
```

虽然每个判断后面都跟着*逗号*，但是没有关系，使用`set`标签，MyBatis 会帮我们自动拼接 SQL，把多余的逗号去掉。

#### trim

相对于前两种写法，`trim`的功能似乎更加强大，都能兼容前两种写法：

**where**：

```xml
<!--根据条件查询-->
<select id="selectByCondition" resultType="cbuc.ssm.entity.Student" parameterType="map">
    select id, name, remark
    from student
    <trim prefix="where" prefixOverrides="and">
        <if test="name != null and name != ''">
            and name like CONCAT('%',#{name},'%')
        </if>
        <if test="remark != null and remark != ''">
            and remark like CONCAT('%',#{remark},'%')
        </if>
    </trim>
</select>
```

**set**：

```xml
<update id="updateBySelective" parameterType="cbuc.ssm.entity.Student">
    update student
    <trim prefix="set" suffixOverrides=",">
        <if test="name != null and name != ''">
            name = #{name},
        </if>
        <if test="remark != null and remark != ''">
            remark = #{remark},
        </if>
    </trim>
</update>
```

`trim`元素意味着我们需要去掉一些特殊的字符串，`prefix` 代表的是语句的前缀，而`prefixOverrides`代表的是你需要去掉的字符串

### foreach 元素

`foreach`名如其意，就是循环语句，它的作用是遍历集合，它能够很好的支持数组和List、Set接口的集合，对此提供遍历的功能。

```xml
<select id="selectByIdList" resultType="cbuc.ssm.entity.Student">
    select name, remark from student
    where id in
    <foreach collection="idList" item="id" open="(" close=")" separator="," index="index">
        #{id}
    </foreach>
</select>
```

```java
List<Student> selectByIdList(@Param("idList") List<Long> idList);
```

- `collection`：配置的是传进来的参数名称，它可以是一个数组或者LIst、Set等集合
- `item`：配置的是循环中当前的元素
- `index`：配置的是当前元素在集合的位置下标
- `open 和 close`：配置的是以什么符号将这些元素包装起来
- `separator`：是各个元素的间隔符

对于大量数据的`in`语句需要我们特别注意，因为它会消耗大量的性能，还有一些数据库的 SQL 对执行的 SQL 长度也有限制，所以我们使用它的时候需要预估一下这个 collection 对象的长度。

#### bind 元素

`bind` 元素的作用是通过 OGNL 表达式去自定义一个上下文变量，这样更方便我们使用

以下是一个使用例子：

```xml
<!--根据条件查询-->
<select id="selectBySelective" resultMap="resultMap" parameterType="cbuc.ssm.entity.Student">
    <bind name="keyWord" value="'%' + name + '%'"/>
    select id, name, remark
    from student
    where name like #{keyWord}
</select>
```

这里的`name`就是传递进来的参数，与`%`拼接后赋值给了`keyWord`，然后在`select` 中可以使用这个变量进行模糊查询。

关于 MyBatis的使用我们就讲到这里啦，MyBatis 中还要挺多知识需要学习的，每天进步一点点，不仅要看，平时也要多加练习哦！

![看完不赞，都是坏蛋](https://imgconvert.csdnimg.cn/aHR0cHM6Ly93d3cuNTJkb3V0dS5jbi9zdGF0aWMvdGVtcC9waWMvOWJkNjhkMTUwZjA3ODdjNTYwYTQzOWRhMzU5YTU4MGEucG5n?x-oss-process=image/format,png#pic_center)


> 今天的你多努力一点，明天的你就能少说一句求人的话！
>
> *我是小菜，一个和你一起学习的男人。* `💋`
>
> 微信公众号已开启，搜 **小菜良记**，与小菜一同学习哦！