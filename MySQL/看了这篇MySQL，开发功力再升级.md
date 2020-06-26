> 本文主要介绍Mysql开发中所需的知识和面试中所必知的
> **本文较长，分为上下篇（可收藏，勿吃尘）**
> 如有需要，可以参考
> 如有帮助，不忘 **点赞** ❥

### 一、查询截取分析
#### 1）慢查询日志
>- MySQL 的慢查询日志是MySQL提供的一种日志记录，它用来记录在MySQL中响应时间超过阀值的语句，具体指运行时间超过long_query_time值的SQL，则会被记录到慢查询日志中。
>- 具体指运行时间超过long_query_time值的SQL，则会被记录到慢查询日志中。long_query_time的默认值为10，意思是运行10秒以上的语句。
>- 我们可以查看哪些SQL超出了我们的最大忍耐时间值，比如一条SQL执行超过5秒钟，我们就算慢SQL，希望能收集超过5秒的sql，可以结合之前explain进行全面分析。

***开始使用：***
默认情况下，MySQL数据库没有开启慢查询日志，需要我们手动来设置这个参数。
通过**show variables like '%slow_query_log'** 查看是否开启了慢查询日志
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200321234046255.png)
***设置方法：***
```sql
# 以下方式只对当前数据库有效，MySQL重启后失效
set global slow_query_log = 1;
set global long_query_time = 1.0;
# 主要重新连接或者新开一个会话才能看到修改值
set session long_query_time = 1.0;
```
永久生效就得修改 **my.cnf**
```sql
slow_query_log = 1

#指定生成位置，如果没有指定默认生成 host_name-slow.log
slow_query_log_file=/var/lib/mysql/cbuc_slow.log
```
开启后如果long_query_time没有指定，默认为10秒，那么假如运行时间正好等于long_query_tie的情况，并不会被记录下来，也就是说在mysql源码里面的判断是`大于long_query_time,而非大于等于`
**实验：**
```sql
# 手动制造一条慢SQL
select sleep(9)
```
跟踪日志文件 ： tail -50f cbuc_slow.log
![](https://img-blog.csdnimg.cn/20200322103554288.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80MzI4NzIzOQ==,size_16,color_FFFFFF,t_70)
**查询当前系统中有多少条慢查询：**
```sql
show global status like '%Slow_queries%'
```
**【配置小结】**
在 ***my.ini***或者***my.cnf***配置文件下的配置
```properties
show_query_log = 1;
show_query_log_file = /var/lib/mysql/cbuc_slow.log
long_query_time = 3;
log_output = FILE
```
`日志分析工具mysqldumpslow`
**查看mysqldumpslow的帮助信息:**
1. s：是表示按照何种方式排序；
2. c：访问次数
3. l：锁定时间
4. r：返回记录
5. t：查询行数
6. al：平均锁定时间  
7. ar：平均返回记录数
8. at：平均查询时间
9. t：即为返回前面多少条的数据
10. g：后边搭配一个正则匹配模式，大小写不敏感

`【使用参考】`
**1、** 得到返回记录集最多的10个SQL
```properties
mysqldumpslow -s -t 10 /var/lib/mysql/cbuc_slow.log
```
**2、** 得到访问次数最多的10个SQL
```properties
mysqldumpslow -s -c -t 10 /var/lib/mysql/cbuc_slow.log
```
**3、** 得到按照时间排序的前10条里面含有左连接的查询语句
```properties
mysqldumpslow -s -t -t 10 -g "left join"  /var/lib/mysql/cbuc_slow.log
```
**4、** 另外建议在使用这些命令是结合 | 和 more使用, 否则有可能出现爆屏的情况
```properties
mysqldumpslow -s r -t 10 /var/lib/mysql/cbuc_slow.log | more
```
#### 2）Show Profile
>- 是mysql提供可以用来分析当前会话中语句执行的资源消耗情况，可以用于SQL的调优的测量
>- 默认情况下，参数处于关闭状态,并保存最近15次的运行结果

*【分析步骤】*
- **查看是否支持**
```sql
# 默认是关闭，使用前需要开启
show variables like 'profiling';
```
![](https://img-blog.csdnimg.cn/2020032211143964.png)
- **开启**
```sql
set profiling = 1;
```
![](https://img-blog.csdnimg.cn/20200322111530334.png)
- **测试**
```sql
# 运行两个SQL查看
select * from tbl_emp a left join tbl_dept b on a.deptId = b.id
select * from tbl_emp a right join tbl_dept b on a.deptId = b.id
```
*查看结果 ：*
![](https://img-blog.csdnimg.cn/20200322111847904.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80MzI4NzIzOQ==,size_16,color_FFFFFF,t_70)
![](https://img-blog.csdnimg.cn/20200322112217909.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80MzI4NzIzOQ==,size_16,color_FFFFFF,t_70)
`参数说明：`
- ALL：显示所有的开销信息
- BLOCK IO ：显示块 IO 相关开销
- CONTEXT SWITCHES ：上下文切换相关开销
- CPU ：显示CPU相关开销信息
- IPC ：显示发送和接收相关开销信息
- MEMORY ：显示内存相关开销信息
- PAGE FAULTS ：显示页面错误相关开销信息
- SOURCE ：显示和Source_function,Source_file,Source_line 相关的开销信息
- SWAPS ：显示交换次数相关开销的信息
#### 3）全局查询日志
-  *配置启用*
在 mysql 的`my.cnf`或`my.ini`中设置
```properties
# 开启
general_log = 1

# 记录日志文件的路径
general_log_file = /path/logfile

# 输出格式
log_output = FILE
```
-  *编码启用*
命令：`set global general_log = 1;`
全局日志可以存放在日志文件文件中，也可以存放在MySQL系统表中。存放在日志中性能会更好一些，存储到表中：
`set global log_output = 'TABLE' `
此后，你所编写的sql 语句，将会记录到mysql 库里的 general_log 表，可以用下面的命令查看
`select * from mysql.general_log`
### 二、Mysql锁机制
#### 1）概述
> 锁是计算机协调多个进程或线程并发访问某一资源的机制。
> 在数据库中，除传统的计算资源（如CPU、RAM、I/O等）的争用以外，数据也是一种供许多用户共享的资源。如何保证数据并发访问的一致性，有效性是所有数据库必须解决的一个问题，锁冲突也是影响数据库并发访问性能的一个重要因素。从这个角度来说，锁对数据库而言显的尤其重要，也更加复杂。

 `【案例理解】`
一件商品这个时候只有一件库存，但是同时用A、B两个人要下单，那么是A下单成功还是B下单成功。
这种时候就要使用到事务，我们要先从库存表中取出物品数量，然后生成订单，付款成功后生成付款信息，再更新商品数量。这个流程中，我们需要使用到锁对有限的资源进行保护，解决隔离和并发问题。
 `【锁的分类】`
- **从数据操作的类型划分 （读/写锁）**
  1. **读锁（共享锁）：** 针对同一份数据，多个读操作可以同时进行而不会互相影响。
  2. **写锁（排它锁）：** 当前写操作没有完成前，它会阻断其他写锁和读锁。
- **从数据操作的颗粒度划分**
  1. 表锁
  2. 行锁
#### 2）三级锁
 `【表锁】`
**特点：（偏读）**
>偏向MyISAM存储引擎，开销小，加锁快；无死锁；锁定粒度大；发生锁冲突的概率高，并发度最低。
- 手动加锁：
```sql
lock table <table_name1> <read/write>,<table_name2> <read/write>
```
- 查看表上加过的锁：
```sql
show open tables;
```
![](https://ae01.alicdn.com/kf/H2c583d2bd7d74116ac8784a0b48f8ea2i.png)
- 释放表锁
```sql
unlock tables;
```
**`读锁说明：`**
新建两个session会话，**session1** 和**session2**
此时在session1中对mylock表进行**read** 锁定，情况如下：
1. session1可以**查询**该表的信息，session2也可以**查询**该表的记录
2. session1中不能**查询**其他没有锁定的表，session2可以**查询和更新**其它没有锁定的表
3. session1**插入或更新**`锁定的表`都会提示错误，session2**插入或更新**`锁定的表`会一直等待。
4. 当session1释放锁后，session2之前**插入或更新**执行完成。

**`写锁说明：`**
同样新建两个session会话，**session1** 和**session2**
此时在session1中对mylock表进行**write** 锁定，情况如下：

1. session1 对锁定表的**查询+更新+插入**操作都可以执行，session2 对锁定表的**查询** 被阻塞，需要等待锁的释放。但是如果session2之前有数据缓存，则可以读出缓存数据，一旦数据发生改变，缓存将失效，操作将被阻塞。

**`【小结】`：**
MyISAM在执行查询语句的前，会自动给涉及的所有表加读锁，在执行增删改操作前，会自动给涉及的表加写锁。

| 锁类型 | 他人可读 | 他人可写 |
| ------ | -------- | -------- |
| 读锁   | 是       | 否       |
| 写锁   | 否       | 否       |
**1、** 对MyISAM表的读操作（加读锁），不会阻塞其他进程对同一表的读请求，但会阻塞对同一表的写请求，只有当读锁释放后，才会执行其他进程的写操作。
**2、** 对MyISAM表的写操作（加写锁），会阻塞其他线程对同一表的读和写操作，只用当写锁释放后，才会执行其他进程的读写操作。
**总结：读锁会阻塞写，但是不会阻塞读。而写锁则会把读和写都阻塞**
 `【行锁】`
**特点：（偏读）**
>偏向InnoDB存储引擎，开销大，加锁慢；会出现死锁；锁定粒度最小，发生锁冲突的概率最低，并发度也最高。
>InnoDB与MyISAM的最大不同有两点：
>- 支持事务（TRANSACTION）
>- 采用了行级锁

**事务复习：**
事务是由一组SQL语句组成的逻辑处理单元，事务具有以下4个属性，通常简称为事务的ACID属性。
- `原子性（Atomicity）：` 事务是一个原子操作的单元，其对数据的修改，要么全部执行，要么全都不执行。
- `一致性（Consistent）：`在事务开始和完成时候，数据都必须保持一致状态。这意味着所有相关的数据规则都必须应用于事务的修改，以保持数据的完整性；事务结束时，所有内部的数据结构（如B树索引或双向链表）也都必须是正确的。
- `隔离性（Isolation）：`数据库系统提供一定的隔离机制，保证事务在不受外部并发操作影响的“独立”环境执行。这意味着事务处理过程中的中间状态对外部是不可见的，反之亦然。
- `持久性（Durable）：`事务完成之后，它对于数据的修改是永久性的，即使出现系统故障也能够保持。

**并发事务处理带来的问题：**
- `更新丢失（Lost Update）`
当两个或多个事务选择同一行，然后基于最初选定的值更新该行是，由于每个事务都不知道其他事务的存在，就会发生丢失更新的问题 -- 最后的更新覆盖了由其他事务所做的更新。
- `脏读（Dirty Reads）`
事务A读取到了事务B**已修改但尚未提交**的数据，还在这个数据基础上做了操作。此时，如果B事务回滚，A读取的数据无效，不符合一致性要求。
- `不可重复读（Non-Repeatable Reads）`
一个事务范围内两个相同的查询却返回了不同数据。
- `幻读（Phantom Reads）`
一个事务按相同的查询重新读取以前检索过的数据，却发现其他事务插入了满足其查询条件的新数据，这种现象就称为“幻读”。也就是说事务A读取到了事务B提交的新增数据，不符合隔离性。

**事务隔离级别：**
```sql
# 查看事务的隔离级别
show variable like 'tx_isolate'
```
| 隔离级别                     | 读数据一致性                             | 脏读 | 不可重复读 | 幻读 |
| ---------------------------- | ---------------------------------------- | ---- | ---------- | ---- |
| 未提交读（Read uncommitted） | 最低级别，只能保证不读取物理上损坏的数据 | 是   | 是         | 是   |
| 已提交读（Read committed）   | 语句级                                   | 否   | 是         | 是   |
| 可重复读（Repeatable read）  | 事务级                                   | 否   | 否         | 是   |
| 可序列化（Serializable）     | 最高级别，事务级                         | 否   | 否         | 否   |
数据库的事务隔离越严格，并发副作用越小，但付出的代价也就越大，因为事务隔离实质上就是使事务在一定程度上“串行化”进行，这显然与“并发”是矛盾的。同时，不同的应用对读一致性和事务隔离程度的要求也是不同的，比如许多应用对“不可重复读”和“幻读”并不敏感，可能更关心数据并发访问的能力。
`无索引时行锁会升级为表锁`
**通过select加锁：**
**读锁（共享锁）：**
加上读锁后，其他事务可以并发读取数据，但任何事务都不能对数据进行修改（获取数据上的排它锁），直到已释放所有共享锁。
如果事务T对数据A加上共享锁后，则其他事务只能对A再加共享锁，不能加排他锁。获准共享锁的事务只能读数据，不能修改数据。
```sql
# 通过这段加锁，Mysql会对查询结果中的每行都加共享锁
select ... <lock in share mode>
```
**写锁（排他锁）：**
加上排它锁后，其他事务不能再对A加任何类型的锁。已获取到排它锁的事务既能读数据，又能修改数据。
```sql
# 通过这段加锁，mysql会对查询结果中的每行都加排他锁
select ... for update;
```
**间隙锁：**
当我们用范围条件而不是相等条件检索数据，并请求共享或排他锁时，InnoDB会给符合条件的已有数据记录的索引项加锁；对于键值在条件范围内但并不存在的记录，叫做“间隙（GAP）”
InnoDB也会对这个“间隙”加锁，这种锁机制就是所谓的间隙锁（GAP Lock）
`危害：`
因为Query执行过程中通过范围查找的话，他会锁定整个范围内所有的索引键值，即使这个键值并不存在，间隙锁有一个比较致命的弱点，就是当锁定一个范围键值之后，即使某些不存在的键值也会被无辜的锁定，而造成在锁定的时候无法插入锁定键值范围内的任何数据。在某些场景下这可能会对性能造成很大的危害
`优化建议：`
1. 尽可能让给所有数据检索都通过索引来完成，避免无索引行锁升级为表锁。
2. 尽可能较少检索条件，避免间隙锁。
3. 尽量控制事务大小，减少锁定资源量和时间长度。
4. 锁住某行后，尽量不要去调别的行或表，赶紧处理被锁住的行然后释放掉锁。
5. 涉及相同表的事务，对于调用表的顺序尽量保持一致。
6. 在业务环境允许的情况下，尽可能低级别事务隔离。
 `【页锁】`
开销和加锁时间介于表锁和行锁之间，会出现死锁；锁定粒度介于表锁和行锁之间，并发度一般。

### 三、主从复制 
#### 1）复制的基本原理
> slave 会从 master 读取binlog来进行数据同步

 `【三个步骤】`
- master将改变记录到二进制日志（binary log）。这些记录过程叫做二进制日志时间，binary log events
- slave将master的binary log events拷贝到它的中继日志中（relay log）
- slave重做中继日志中的事件，将改变应用到自己的数据库中，mysql复制是异步的且串行化的。
![在这里插入图片描述](https://img-blog.csdnimg.cn/2020032218092156.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80MzI4NzIzOQ==,size_16,color_FFFFFF,t_70)
#### 2）复制的基本原则
- 每个slave 只有一个master
- 每个slave只能有一个唯一的服务器ID
- 每个master可以有多个slave

**复制的最大问题：** 延迟

#### 3）主从常见配置
**mysql 版本一致且后台以服务运行，主从配置都在[mysqld]结点下，都是小写**
 `【主机修改my.ini配置文件】`
-  **[必须]** 主服务器唯一ID
```properties
server-id = 1
```
-  **[必须]** 启用二进制文件
```properties
log-bin = 自己本地的路径/data/mysqlbin
log-bin = D:/devSoft/MySQLServer5.5/data/mysqlbin
```
-  **[可选]** 启用错误日志
```properties
log-err = 自己本地的路径/data/mysqlerr
log-err = D:/devSoft/MySQLServer5.5/data/mysqlerr
```
-  **[可选]** 根目录
```properties
basedir = "自己本地路径"
basedir = D:/devSoft/MySQLServer5.5/
```
-  **[可选]** 临时目录
```properties
tmpdir = "自己本地路径"
tmpdir =  D:/devSoft/MySQLServer5.5/
```
-  **[可选]** 数据目录
```properties
datadir = "自己本地路径"
datadir =  D:/devSoft/MySQLServer5.5/data/
```
-  **read-only = 0**
主机读写都可以
-  **[可选]** 设置不要复制的数据库
```properties
binlog-ignore-db = mysql
```
-  **[可选]** 设置需要复制的数据库
```properties
binlog-do-db = 需要复制的数据库的名字
```
 `【从机修改my.ini配置文件】`
-  **[必须]** 从服务器唯一ID
-  **[可选]** 启用二进制文件

 **`【修改后，主从机都需要重启后台mysql服务】`**
**` 【主从机都需要关闭防火墙】`**
**` 【在windows主机上建立账户并授权slave】`**】
- 步骤1：
```sql
GRANT REPLICATION SLAVE ON *.* TO 'zhangsan'@'从机的数据库IP'INDETIFIED BY '123456'
```
- 步骤2：
```sql
flush privileges;
```
- 查看master状态
```sql
show master status;
# 记录File和Position 的值
```
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200322194401897.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80MzI4NzIzOQ==,size_16,color_FFFFFF,t_70)
- 执行完以上步骤便不要再操作，防止主服务器状态值发生改变

**` 【在Linux从机上配置需要复制的主机】`**】
- 步骤1
```sql
change master to master_host = '主机IP'，master_user='zhangsan',master_password = '123456',master_log_file='file名字',master_log_pos=position数字
```
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200322194859848.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80MzI4NzIzOQ**,size_16,color_FFFFFF,t_70)
- 步骤2：
启动从服务器复制功能
```sql
start slave;
```
- 步骤3：
```sql
show slave status
#下面两个参数都是Yes，便说明主从配置成功
Slave_IO_Running：Yes
Slave_SQL_running:Yes
```
**` 【主机新建库，新建表，insert记录，从机便会复制】`**
**` 【停止从服务复制功能】`**
```sql
stop slave;
```
![看完不赞，都是坏蛋](https://imgconvert.csdnimg.cn/aHR0cHM6Ly93d3cuNTJkb3V0dS5jbi9zdGF0aWMvdGVtcC9waWMvOWJkNjhkMTUwZjA3ODdjNTYwYTQzOWRhMzU5YTU4MGEucG5n?x-oss-process=image/format,png#pic_center)

> 本文较长，能看到这里的都是好样的，成长之路学无止境
> 今天的你多努力一点，明天的你就能少说一句求人的话！
> `很久很久之前，有个传说，据说：`
> **看完不赞，都是坏蛋**

