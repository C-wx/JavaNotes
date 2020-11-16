大家好，我是小菜，一个渴望在互联网行业做到蔡不菜的小菜。可柔可刚，点赞则柔，白嫖则刚！
**死鬼~看完记得给我来个三连哦！**


![](https://user-gold-cdn.xitu.io/2020/4/11/17169c46045528af?w=240&h=224&f=jpeg&s=7529)


>本文主要介绍 `ElasticSearch的使用`
>
>如有需要，可以参考
>
>如有帮助，不忘 **点赞** ❥
>
>
>微信公众号已开启，**小菜良记**，没关注的同学们记得关注哦！

`ELK`是一个免费开源的日志分析架构技术栈总称，其中包含三大基础组件，分别是 `ElasticSearch`、`Logstash`、`Kibana`。`ELK`在实际开发中不仅仅使用于日志分析，它还可以支持其他任何数据搜索、分析和收集的场景，其中日志分析和收集更具有代表性。

既然 `ELK` 这么有用，那这篇我们就先来认识一下什么是 `ElasticSearch`吧！

## 简介

简单来说 `ElasticSearch` 就是一个搜索框架。对于搜索这个词我们并不陌生，当我们输入关键词后，返回含有该关键词的所有信息结果。

在我们平时用到最多的便是数据库搜索：

```sql
SELECT * FROM USE WHERE NAME LIKE %小菜%
```

但是用数据库做搜索存在着许多弊端，例如：

- **存储问题**：当数据量大的时候就必须进行分库分表。
- **性能问题**：当数据量过大时，使用`LIKE`会对上亿条数据进行逐行扫描，性能受到严重影响。
- **不能分词**：当我们搜索 **游戏本电脑** 的时候，只会返回完全和关键词一样的数据，如果搜索 **游戏电脑**，那么是不是就会没有数据返回。

因此基于以上问题，`ElasticSearch`出现了。它是使用 **Java** 开发的，基于 **Lucene**、分布式、通过 **Restful** 方式进行交互的近实时搜索平台框架。它的优点如下：

- 分布式的搜索引擎和数据分析引擎
- 全文检索，结构化检索和数据分析
- 对海量数据进行近实时的处理

### Lucene 介绍

**Lucene** 是一个功能强大的搜索库，如果我们直接基于 **Lucene** 开发，那么会非常复杂。而 `ElasticSearch` 是基于 **Lucene** 开发的，封装了许多 **Lucene** 底层功能，提供了简单易用的 **RestFul api**接口和许多语言的客户端。

### ElasticSearch核心概念

- **NRT（Near Realtime）** 近实时

1. 写入数据时，过 1 秒才会被搜索到，因为内部需要分词，引入索引
2. **es** 搜索和分析数据都是秒级内出结果

- **Cluster 集群**

包含一个或多个启动着**es**实例的机器群。通常一台机器起一个**es**实例。同一网络下，集名一样的多个**es**实例自动组成集群，自动均衡分片等行为。默认集群名为“**elasticsearch**”。

- **Node 节点**

每个**es**实例称为一个节点。节点名自动分配，也可以手动配置。

- **Index 索引**

包含一堆有相似结构的文档数据。索引创建规则：

1. 仅限小写字母

2. 不能包含**\、/、 *、?、"、<、>、|、#**以及空格符等特殊符号

3. 从**7.0**版本开始不再包含冒号

4. 不能以**-、_**或**+**开头

5. 不能超过255个字节（注意它是字节，因此多字节字符将计入255个限制）

- **Document 文档**

**es**中的最小数据单元。一个**document**就像数据库中的一条记录。通常以**json**格式显示。多个**document**存储于一个索引（**Index**）中。

- **Field 字段**

就像数据库中的列（**Columns**），定义每个**document**应该有的字段。

- **Type 类型**

每个索引里都可以有一个或多个**type**，**type**是**index**中的一个逻辑数据分类，一个**type**下的**document**，都有相同的**field**。

**注**：6.0之前的版本有type（类型）概念，type相当于关系数据库的表，ES官方将在ES9.0版本中彻底删除type。本教程typy都为_doc。

- **Shard 分片**

**index**数据过大时，将**index**里面的数据，分为多个**shard**，分布式的存储在各个服务器上面。可以支持海量数据和高并发，提升性能和吞吐量，充分利用多台机器的**cpu**。

- **replica 副本**

在分布式环境下，任何一台机器都会随时宕机，如果宕机，**index**一个分片都没有，那么导致此**index**不能搜索。所以，为了保证数据的安全，我们会将每个**index**的分片进行备份，存储在另外的机器上。保证少数机器宕机**es**集群仍可以搜索。

能正常提供查询和插入的分片我们叫做主分片（primary shard），其余的叫做备份的分片（replica shard）。

**与数据库同比**

| **关系型数据库**  | **非关系型数据库（Elasticsearch）** |
| ----------------- | ----------------------------------- |
| 数据库 `Database` | 索引 `Index`                        |
| 表 `Table`        | 索引 `Index`（原为 `Type`）         |
| 数据行 `Row`      | 文档 `Document`                     |
| 数据列 `Column`   | 字段 `Field`                        |
| 约束 `Schema`     | 映射 `Mapping`                      |

## 前期准备

演示环境： **Windows** 

### ElasticSearch 安装

我们进入 [ElasticSearch下载地址](https://www.elastic.co/cn/downloads/elasticsearch)  下载和解压 **ElasticSearch** ，目录结构如下：

- **bin**：脚本目录，包括：启动、停止等可执行的脚本
- **config**：配置文件目录
- **data**：索引目录
- **logs**：日志目录
- **modules**：模块目录，包括了 es 的功能模块
- **plugins**：插件目录，es 支持插件机制

准备好配置文件后，双击`elasticsearch.bat`启动，接着访问`http://localhost:9200`，出现以下界面即为成功：

![](https://cbucbm.club/c401c9e1)

### Kibana 安装

进入[下载地址](https://artifacts.elastic.co/downloads/kibana/kibana-6.0.0-windows-x86_64.zip) 下载和解压 **Kibana**，修改配置文件后，双击`kibana.bat`启动，接着访问`http://localhost:5601/`，出现以下界面即为成功：

![](https://cbucbm.club/eb43fb32)

进入 **Kibana** 控制台，输入 `GET /`查看`ElasticSearch`信息，出现以下界面即为成功：

![](https://cbucbm.club/f83333b0)

前期准备工作做好了，咱们现在开始进入`ElasticSearch`的正式环节！

![](https://cbucbm.club/bf808310)

## ES开讲

### 什么是 Index

**Index**就相当于数据库中的数据表，`ElasticSearch`会索引所有字段，经过处理后写入一个反向索引（Inverted Index）。查找数据的时候，直接查找该索引。所以，`ElasticSearch`数据管理的顶层单位就叫做 **Index（索引）**。**注：** 每个 **Index** 的名字必须是小写。

- 创建一个 **test** 索引：

语法：`PUT /${index}`

![](https://cbucbm.club/b250d5a7)

- 删除 `test` 索引：

语法：`DELETE /${index}`

![](https://cbucbm.club/2f550534)

其中删除支持其他语法：

```json
1. DELETE /test
2. DELETE /test1,test2
3. DELETE /test_*
4. DELETE /_all
```

### 什么是 Document

首先员工和部门对象如下：

```java
public class Employee{
    private String id;
    private String name;
    private String deptId;
}
public class Department{
    private String id；
    private String deptName；
    private String describe；
}
```

我们如果用关系型数据存数据的话，应该是一个员工表和一个部门表，查询员工的时候想要带上部门的信息就得使用关联查询。

但是在 **ES** 中，它是面向文档的，文档中存储的数据结构与对象一致。一个对象可以直接保存成一个文档，这就是`ES`中的 **Document**，**document** 是采用 **json** 数据格式表示，示例如下：

```json
{
    "id":"1",
    "name":"小菜",
    "department":{
        "id":"1",
        "deptName":"搬砖部",
        "describe":"努力搬好每一块砖"
    }
}
```

接下来我们在 **employee**索引中演示基本的增删改查操作：

**创建员工信息**：

![](https://cbucbm.club/04b817ce)

**获取员工信息**：

![](https://cbucbm.club/36cac30f)

**修改员工信息**：

以下是**替换操作**，要带上所有信息

![](https://cbucbm.club/9a36cc83)

**局部更新操作**：

![](https://cbucbm.club/36b6004c)

局部更新操作的语法为：`POST /{index}/_update/{id}`，其中要更新的信息需要放在`doc`里面

**删除员工信息**：

![](https://cbucbm.club/6da2bd0e)

#### 字段信息

```json
{
  "_index" : "employee",
  "_type" : "_doc",
  "_id" : "1",
  "_version" : 4,
  "_seq_no" : 7,
  "_primary_term" : 1,
  "found" : true,
  "_source" : {
    "id" : "1",
    "name" : "小菜",
    "department" : {
      "id" : "1",
      "deptName" : "搬砖部",
      "describe" : "努力搬好每一块砖"
    }
  }
}
```

当我们通过`GET /employee/_doc/1` 可以获取到一条文档信息如上，其中出现 **8** 个字段，接下来为你们解析一番重要字段：

- **_index**：文档所属的索引名称

- **_type**：类别。在 **es9** 只有会删除此字段，因此不用关注，默认都为 **_doc**

- **_id**：文档的唯一标志，类似于表中的主键ID，可以用来标识和定义一个文档。可以手动生成，也可以自动生成

  1. **手动生成**：

  ```java
  PUT /employee/_doc/1
  {
      "id":"1",
      "name":"小菜",
      "department":{
          "id":"1",
          "deptName":"搬砖部",
          "describe":"努力搬好每一块砖"
      }
  }
  ```

  2. 自动生成：

  **注：** 这里用到是 `POST`请求，将会为我们自动生成 **20** 个字符的 **ID**

  ```java
  POST /employee/_doc
  {
      "id":"3",
      "name":"小王",
      "department":{
          "id":"1",
          "deptName":"搬砖部",
          "describe":"努力搬好每一块砖"
      }
  }
  ```

  ![](https://cbucbm.club/44d99568)

  

- **_version**：版本号

这里的版本号是在 **全量替换** 、 **局部更新**和 **删除** 操作时，版本号都会加 **1**，上面 ID 为 1 的员工信息版本包为4，说明这条记录已经更新了 4 次。

- **_seq_no**：序列号

作用于 **version** 类似，当数据发生变更时，值就会加 **1**

- **_source**：插入数据时的所有字段和值

我们也可以不需要返回所有字段，需要用以下语句：

`GET /employee/_doc/1?_source_includes=id,name`

![](https://cbucbm.club/fe041a50)

当然不仅可以使用`_source_includes`  ，还可以使用`_source_excludes`，两个意思分别是**包括**和 **排除**。

### 乐观锁机制

在我们学习 **Java** 并发的时候，我们认识了 **CAS** 乐观锁机制，在 **ES** 中我们同样也可以使用乐观锁来控制。

**CAS**是基于版本号的，而在上述 **Document** 字段解析中，我们也看到了 **_seq_no** 这个字段，不由想象，我们是否也能根据 **_seq_no** 来做乐观锁控制解决并发问题呢，答案是可以的。

我们首先创建一条员工记录：

![](https://cbucbm.club/c156d439)

此时，版本号毋庸置疑是 **1**，**_seq_no**是 **10**

然后我们将这条员工信息删除：

![](https://cbucbm.club/f929cb78)

接着我们重新创建一条相同的员工信息：

![](https://cbucbm.club/41a13c99)

可以看到这个时候的版本号变成了 **3**，**_seq_no** 变成了 **14**。

这是因为 **ES** 内部采用了 **延迟删除策略**，这是因为如果删除一条数据里吗删除的话，所有分片和副本都要立马删除，这会对 **ES** 集群压力太大。

进行并发控制：

- 步骤1：我们先查出当前的 **_seq_no** 为 **17**

**语句：** `PUT /employee/_doc/5?if_seq_no=17&if_primary_term=1`

![](https://cbucbm.club/9528e63e)

可以看到更新后，**_version** 和 **_seq_no** 都加上了 **1**

如果 **_seq_no** 版本不匹配的情况下：

![](https://cbucbm.club/82cb76d4)

将会报出错误！

### 批量操作

#### 批量查询 (_mget)

上面我们用到的语句都是指定ID单个查询，如果我们想要查询当前索引下的所有数据，那么应当使用以下语句：

```java
GET /employee/_mget
{
  "docs" : [
      {
         "_id" : 1
      },
      {
         "_id" : 5
      }
   ]
}
```

![](https://cbucbm.club/7784f2cd)

如果我们要同时查询不同索引下的 ID 时，应当使用以下语句：

```java
GET /_mget
{
   "docs" : [
      {
         "_index" : "employee",
         "_id" : 1
      },
      {
         "_index" : "employee",
         "_id" : 5
      }
   ]
}
```

![](https://cbucbm.club/b3ffe606)

#### 批量增删改 （bulk）

语法：

```java
POST /_bulk
{"action": {"metadata"}}
{"data"}
```

示例：

![](https://cbucbm.club/d248b035)

**注：**

- **delete：** 删除一个文档，只需 1 个 **json** 串
- **create：** 创建一个文档，相当于  `PUT /index/type/id/_create`

- **index：** 普通的 **put** 操作，可以创建文档，也可以全量替换文档
- **update：** 更新一个文档，执行的是局部更新

**每个操作之间互不影响，操作失败的行会返回对应的失败信息**

**buld 操作请求一次不易过大， 否则一下子容易挤压到内存中，性能会下降**

### 与开发融合

`ElasticSearch` 是基于 Java 开发的，当然我们开发中要使用 `ElasticSearch` 也是非常方便的。

**首先便是引入依赖**

```java
    <dependency>
        <groupId>org.elasticsearch.client</groupId>
        <artifactId>elasticsearch-rest-high-level-client</artifactId>
        <version>7.9.0</version>
        <exclusions>
            <exclusion>
                <groupId>org.elasticsearch</groupId>
                <artifactId>elasticsearch</artifactId>
            </exclusion>
        </exclusions>
    </dependency>
    <dependency>
        <groupId>org.elasticsearch</groupId>
        <artifactId>elasticsearch</artifactId>
        <version>7.9.0</version>
    </dependency>
```

**获取员工信息示例**

![](https://cbucbm.club/5c572590)

```java
/** OUTPUT:
{"id":"1","name":"小菜啊","department":{"id":"1","deptName":"搬砖部","describe":"努力搬好每一块砖"}}
 */
```

如果你觉得只从 `getResponse` 中获取一个 `source` 不满足，没有关系！我们还可以从`getResponse` 获取以下信息：

![](https://cbucbm.club/2f58fd08)

我们在上面的查询文档中发现，如果需要从 `ES` 中获取信息，我们先要 **获取客户端连接**，然后再 **构建请求**，最后 **执行得到结果**。这一系列操作不知道你是否有点熟悉，我们以前用的 **JDBC**，也是需要这一系列的操作，后来出现了 **Hibernate** 和 **Mybatis**，大大简化了我们的代码量。无独有偶，`ES` 也可以完美结合 `Spring` 框架开发。

首先我们需要需要在 `application.yaml` 文件中定义好`elasticsearch` 的 `host`：

```yaml
server:
  port: 8081
spring:
  application:
    name: search-service
  elasticsearch:
    address: 127.0.0.1:9200 #多个节点用逗号分隔
```

然后我们需要在 `Spring` 中注册 `RestHighLevelClient`

![](https://cbucbm.club/0c9ca355)

#### 执行查询

![](https://cbucbm.club/b548b5ac)

我们在上述例子中认识到了`_source_includes`  和`_source_excludes`的用法，当然在 **Java** 中也是支持的：

![](https://cbucbm.club/99050956)

而且`ES`在 **Java** 中还支持异步查询：

![](https://cbucbm.club/c878c364)

#### 执行新增

![](https://cbucbm.club/254e6d7a)

在上面我们是用 **json** 传递我们需要新增的参数，当然也支持另外方式：

```java
//方式1: json
Map<String, String> insertInfo = new HashMap<>();
insertInfo.put("id", "8");
insertInfo.put("name", "老王");
request.source(JSON.toJSONString(insertInfo), XContentType.JSON);

//方式2: map
Map<String, String> insertInfo = new HashMap<>();
insertInfo.put("id", "8");
insertInfo.put("name", "老王");
request.source(insertInfo);

//方式3: XContentBuilder
XContentBuilder builder = XContentFactory.jsonBuilder();
builder.startObject();
{
    builder.field("id", "8");
    builder.field("name", "老王");
}
builder.endObject();
request.source(builder);

//方式4: 直接构建
request.source("id", "8", "name", "老王");
```

当然不止新增支持异步操作，更新同样也支持异步操作：

![](https://cbucbm.club/0d903f05)

#### 执行修改

![](https://cbucbm.club/94587bbc)

异步支持：

![](https://cbucbm.club/55b512c6)

#### 执行删除

![](https://cbucbm.club/45897408)

异步支持：

![](https://cbucbm.club/c352dc45)

#### 执行批量

![](https://cbucbm.club/839d5c34)

### Mapping 介绍

**什么是Mapping：** 自动或手动为 **index** 中的 **_doc** 建立的一种数据结构和相关配置

我们如果使用关系型数据库插入一条员工信息，首先需要建立一个员工表 **employee**，然后表里面有两个字段：`id`，`name`：

```sql
create table website(
    id varchar(8),
    name varchar(8)
);
```

我们往 **员工索引** 中插入数据：

```json
PUT /employee/_doc/1
{
    "id":"1",
    "name":"小菜"
}
```

感觉少了点什么，那就是我们不需要建立字段了，甚至不需要手动建立 **employee** 索引，只需要一句语句就可以解决！

这是因为`ES` 里面存在动态映射（**Dynamic Mapping**），会自动为我们建立 **index**，以及对应的 **mapping**， **mapping** 中包含了每个 **field** 对应的数据类型，以及如何分词等设置。

我们可以通过 `GET /{index}/_mapping` 查看自己索引的字段映射：

![](https://cbucbm.club/9850eea8)

#### 核心数据类型

![](https://cbucbm.club/bcfcc81d)

#### 动态推测类型

![](https://cbucbm.club/ac49162e)

|   **JSON datatype**   | **ElasticSearch datatype** |
| :-------------------: | :------------------------: |
| **true** or **false** |        **boolean**         |
|        **123**        |          **long**          |
|      **123.45**       |         **double**         |
|    **2019-01-01**     |          **date**          |
|      **"test"**       |      **text/keyword**      |

#### 自定义

我们在创建完索引后，可以手动创建映射：

**语法：** `PUT ${index}/_mapping`

```json
PUT department/_mapping
{
    "properties": {
        "id": {
            "type": "text"
        },
        "description": {
            "type": "text",
            "analyzer":"english",
            "search_analyzer":"english"
        }
    }
} 
```

其中可以通过**analyzer**属性指定分词器。上边指定了**analyzer**是指在索引和搜索都使用`english`，如果单独想定义搜索时使用的分词器则可以通过`search_analyzer`属性。

**注：** 日期类型不支持分词器

**【END】**

以上便是 **ElasticSearch** 的入门介绍啦！**ElasticSearch** 可以讲的内容还有很多哦，接下来也会抽时间整理一下 **ElasticSearch** 的深入内容哦，关注小菜不迷路。路漫漫，小菜与你一同求索！

![看完不赞，都是坏蛋](https://imgconvert.csdnimg.cn/aHR0cHM6Ly93d3cuNTJkb3V0dS5jbi9zdGF0aWMvdGVtcC9waWMvOWJkNjhkMTUwZjA3ODdjNTYwYTQzOWRhMzU5YTU4MGEucG5n?x-oss-process=image/format,png#pic_center)
> 今天的你多努力一点，明天的你就能少说一句求人的话！
>
> *我是小菜，一个和你一起学习的男人。* `💋`
>
>
> 微信公众号已开启，**小菜良记**，没关注的同学们记得关注哦！