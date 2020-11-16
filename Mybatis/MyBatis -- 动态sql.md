大家好，我是小菜，一个渴望在互联网行业做到蔡不菜的小菜。可柔可刚，点赞则柔，白嫖则刚！
**死鬼~看完记得给我来个三连哦！**


![](https://user-gold-cdn.xitu.io/2020/4/11/17169c46045528af?w=240&h=224&f=jpeg&s=7529)


>本文主要介绍 `Mybatis`
>
>如有需要，可以参考
>
>如有帮助，不忘 **点赞** ❥
>
>
>微信公众号已开启，**小菜良记**，没关注的同学们记得关注哦！

*参考书籍*：【深入浅出MyBatis技术原理与实战】

[MyBatis -- 映射器]()

[ MyBatis -- 配置]()

前面两篇文章分别介绍了 MyBatis 中的配置和映射器，还没看的小伙伴可以去看下顺便来个三连哦！

![](https://cbucbm.club/43c1b0dd)

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

