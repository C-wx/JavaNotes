大家好，我是小菜，一个渴望在互联网行业做到蔡不菜的小菜。可柔可刚，点赞则柔，白嫖则刚！
**死鬼~看完记得给我来个三连哦！**

![](https://user-gold-cdn.xitu.io/2020/4/11/17169c46045528af?w=240&h=224&f=jpeg&s=7529)

> 本文主要介绍 `Mysql开发中所需的知识和面试中所必知的`
> 如有需要，可以参考
> 如有帮助，不忘 **点赞** ❥

### 一、MySQL架构
#### 1）MySQL简介
- MySQL是一个关系型数据库管理系统，由瑞典MYSQL AB公司开发，目前属于`Oracle`公司。
- MySQL 是一种关系型数据库管理系统，将数据保存在不同的表中，而不是将所有数据放在一个大仓库中，这样就增加了速度并提高了灵活性。
- Mysql是开源的，是可以定制的，采用了`GPL`协议，你可以修改源码来开发自己的MySQL系统。
- MySQL支持大型的数据库。可以处理拥有上千万条记录的大型数据库。MySQL可以允许于多个系统上，并且支持多种语言。这些编程语言包括C、C++、Python、Java、Perl、PHP、Eiffel、Ruby和Tcl等。
- MySQL支持大型数据库，支持5000条记录的数据仓库，32位系统表文件最大可支持4GB，64位系统支持最大的表文件为8TB。

#### 2）MySQL配置文件
- `binlog(二进制日志)`

用于主从复制及备份恢复：binlog中存放了所有操作记录，可用于恢复。相当于Redis中的AOF，my.ini中binlog配置（默认是关闭的）

*开启*：

```properties
[mysqld]
log-bin = mysql-bin
binlog-format = row
```
- `Error log(错误日志)`

默认是关闭的，通常用于记录数据库服务端启动、重启、主从复制时，记录错误，将日志详情保留在文件中，方便DBA、运维开发人员阅读。

*开启*：

```properties
[mysqld]
log-error=/data/mysql_error.log
```
- `慢查询日志log`

默认是关闭的。记录查询的sql语句，如果开启会减低mysql的整体性能，因为记录日志也是需要消耗系统资源的。

*开启*：

```properties
[mysqld]
slow_query_log = ON
slow_query_log_file = /usr/local/mysql/data/slow.log     //linux
long_query_time = 1
```
#### 3）数据文件

*windows*：
`..\mysql-8.0.19-winx64\data`目录下存储数据库文件
*linux*：
默认路径`/var/lib/mysql`（可在配置文件中更改`/usr/share/mysql/`下的`my-huge.cnf`）每个目录代表一个同名的库。
*Myisam存放方式*：

- `frm文件（framework）`：存放表结构
- `myd文件（data）`：存放表数据
- `myi文件（index）`：存放表索引

*innodb存放方式*：

- `ibdata1`：Innodb引擎将所有表的数据都存放在这里面/usr/share/mysql/ibdata1而frm文件存放在库同名的包下
- `frm文件`：存放表结构
#### 4）配置方式

+ windows：`my.ini` 配置文件
+ linux：`my.cnf` 配置文件

#### 5）MySQL的用户与权限管理
`MysSQL用户管理`

+	*创建用户*
```sql
create user cbuc identified by '123456'
```
+ *关于 user 表*
```sql
select host,user,select_priv,insert_priv,drop_priv from mysql.user;
```
![ ](https://img-blog.csdnimg.cn/20200320102608434.png)
`host：` 表示连接类型
`user：`表示用户名
`select_priv，insert_priv，drop_priv等：`该用户所拥有的权限
+ *设置密码*
修改当前用户的密码：
	
	```sql
	set password = password('123456')
	```
	修改某个用户的密码
	```sql
	update mysql.user set password = password('123456') where user = 'cbuc'
	```
+ *修改用户*
修改用户名：
	
	```sql
	update mysql.user set user = 'cbuc' where user='c1';
	flush privileges; #所有通过user表修改后必须用该命令才能生效
	```
+ *删除用户*
	
	```sql
	drop user cbuc;
	# 不要通过delete from user t1 where t1.user='cbuc'进行删除，系统会有残留信息保留
	```

`权限管理`

+	*授予权限*：
```sql
grant 权限1，权限2,...,权限n on 数据库名.表名 to 用户名@用户地址 identified by '密码'
#如果发现没有该用户，则会直接创建一个用户
grant select,insert,delete,update on db_crm.* to cbuc@localhost;
#给cbuc用户赋予对表增删改查的权限
```
+	*收回权限*：
```sql
revoke 权限1，权限2,...,权限n on 数据库名.表名 from 用户名@用户地址
revoke all privileges on mysql.* from cbuc@localhost
#如果已赋全库的表，就回收全库全表的所有权限
```
+	*收回权限*：
```sql
#查看当前用户权限
show grants;
#查看某用户的全局权限
select * from mysql.user;
#查看某用户的某库的权限
select * from mysql.db;
#查看某用户的某个表的权限
select * from mysql.tables_priv;
```

#### 6）MySQL其他配置
`大小写问题`

*windows系统默认是大小写不敏感，但是linux系统是大小写敏感的。*

- `0（默认）`： 大小写敏感

- `1`： 大小写不敏感。创建的表，数据库都是以小写形式存放在磁盘中，对于sql语句都是转换为小写对表的DB进行查找。

- `2`： 创建的表和DB依据语句上格式存放，凡是查找都是转换为小写进行

```sql
SHOW VARIABLES LIKE '%lower_case_table_names%';
```
![ ](https://img-blog.csdnimg.cn/20200320105050311.png)
*设置*：

```sql
set lower_case_table_names = 1;
#此变量是只读权限，需要在配置文件中修改
```
*在`my.ini` / `my.cnf`中添加*

```properties
[mysqld]
lower_case_table_names = 1 
```

*重启服务器（重启前要将原来的数据库和表转换为小写，否则更改后将找不到数据库名）*

- `sql_mode`

sql_mode 是个很容易被忽视的变量，默认值是空值，在这种设置下是可以允许一些非法操作的， 比如允许一些非法数据的插入。在生产环境必须将这个值设置为严格模式，所以开发、测试环境的数据库也必须要设置，这样在开发测试阶段就可以发现问题。

*常用设置*：

```properties
[mysqld]
sql-mode="STRICT_TRANS_TABLES,NO_ZERO_IN_DATE,NO_ZERO_DATE,ERROR_FOR_DIVISION_BY_ZERO,NO_ENGINE_SUBSTITUTION"
```
#### 7）MySQL存储引擎
*查看引擎*：

```sql
show engines;
```
![ ](https://img-blog.csdnimg.cn/20200320111505746.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80MzI4NzIzOQ==,size_16,color_FFFFFF,t_70)
可以看出默认的存储引擎是`InooDB`

*各引擎简介*：

+ `InnoDB存储引擎`：
InnoDB是MySQL默认的*事务型引擎*，它被设计用来处理大量的短期（short-lived）事务。除非有非常特别的原因需要使用其他的存储引擎，否则应该优先考虑InnoDB引擎。具有*行级锁*，*外键*，*事务*等优势，适合*高并发情况*。
+ `MyISAM存储引擎`：
MyISAM提供了大量的特性，包括全文索引、压缩、空间函数（GIS）等，但MyISAM*不支持事务和行级锁（MyISAM改表时会将整个表全锁住）*，缺陷：*崩溃后无法安全恢复*。
+ `Archive引擎`：
rchive存储引擎*只支持 insert 和 select*操作，在MySQL5.1之前不支持索引。Archive表适合日志和数据采集类引用。*适合低访问量大数据等情况*。
+ `Blackhole引擎`：
Blackhole引擎没事实现任何存储机制，它会丢弃所有插入的数据，不任何保存。但服务器会记录Blackhole表的日志，所以可以用于复制数据到备库，或者简单地记录到日志。但这种应用方式会碰到很多问题，因此并不推荐。
+ `CSV引擎`：
CSV引擎可以将普通的CSV文件作为MySQL的表来处理，但不支持索引。可以作为一种数据交换的机制，非常有用。存储的数据直接可以在操作系统里，用文本编辑器，或者excel读取。
+ `Memory引擎`：
如果需要快速地访问数据，并且这些数据不会被修改，重启后丢失也没有关系的话，那么使用Memory表是非常有用的。Memory表至少比MyISAM表要快一个数量级。
+ `Federated引擎`：
Federated引擎是访问其他MySQL服务器的一个代理，尽管该引擎看起来提供了一种很好的跨服务器的灵活性，但也经常带来问题，因此默认是禁用的。

*MyISAM和InnoDB比较*：

| 外键           | 不支持                                                   | 支持                                                         |
| -------------- | -------------------------------------------------------- | ------------------------------------------------------------ |
| 索引           | B Tree                                                   | B+ Tree                                                      |
| 事务           | 不支持                                                   | 支持                                                         |
| 行表锁         | 表锁，即使操作一条记录也会锁住整个表，不适合高并发的操作 | 行锁，操作时只锁某一行，不对其他行有影响，`适合高并发操作`   |
| 缓存           | 只缓存索引，不缓存真实数据                               | 不仅缓存索引还要缓存真实数据，对内存要求比较高，而且内存大小对性能有决定性的影响 |
| 表空间         | 小                                                       | 大                                                           |
| 关注点         | 性能                                                     | 事务                                                         |
| 默认安装       | 是                                                       | 是                                                           |
| 用户表默认使用 | 否                                                       | 是                                                           |
| 自带系统表使用 | 是                                                       | 否                                                           |
*InnoDB主键为聚簇索引，基于聚簇索引的增删改查效率非常高*
`聚簇索引`： 实际存储的循序结构与数据存储的物理机构是一致的
`非聚簇索引`： 记录的物理顺序与逻辑顺序没有必然的联系，与数据的存储物理结构没有关系

### 二、索引优化分析
#### 1）性能下降/SQL执行时间长
- *查询数据过多*
	能拆则拆，条件过滤尽量少
- *过多JOIN*
	JOIN原理：用A表的每一条数据扫描B表的所有数据，所以尽量先过滤再关联
- *没有利用到索引*
	索引针对`列`建索引，但并不可能每一列都建索引
	索引并非越多越好。当数据更新了，索引会进行调整，也会很消耗性能。
	并且MySQL并不会把所有索引都用上，只会根据其算法挑一个索引用。所以建的准很重要
- *服务器调优及各个参数设置（缓冲、线程数）*
#### 2）JOIN查询
- `SQL执行顺序`
	*人工读取顺序*：
	
	```sql
	SELECT (DISTINCT)
		< select_list >
	FROM
		< left_table > < join_type >
	JOIN < right_table > ON < join_condition >
	WHERE
		< where_condition >
	GROUP BY 
		< group_by_list >
	HAVING
		< having_condition>
	ORDER BY
		< order_by_condition >
	LIMIT < limit_number >
	```
	*引擎执行顺序*：
	
	```sql
		FROM < left_table >
		ON < join_condition >
		< join_type > JOIN < right_table >
		WHERE < where_condition >
		GROUP BY < group_by_list >
		HAVING < having_condition >
		SELECT 
		(DISTINCT) < select_list >
		ORDER BY < order_by_condition >
		LIMIT < limit_number >
	```
	
	*总结*：![ ](https://img-blog.csdnimg.cn/20200320144359874.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80MzI4NzIzOQ==,size_16,color_FFFFFF,t_70)
- `共有/独有`
	有两个表，员工表Employee和部门表Dept，员工表里面有Dept字段与部门表的主键ID相对应。
	*共有*：满足employee.deptId = dept.id 的数据 称为两表共有
	*独有*：employee.deptId <> dept.id 的数据 称为员工表独有
- `七种JOIN`
	有两个表，t1 表是员工表 emp，t1 表是部门表 dept

	1、*t1 表和 t2 表共有 (`inner join`)*
	![ ](https://img-blog.csdnimg.cn/20200320151901712.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80MzI4NzIzOQ==,size_16,color_FFFFFF,t_70)
	
	```sql
	select * from emp t1 inner join dept t2 on t1.deptId = t2.id
	```
	2、*t1 表和 t2 表共有 + t1 表独有 (`left join`)*
	![ ](https://img-blog.csdnimg.cn/20200320151928917.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80MzI4NzIzOQ==,size_16,color_FFFFFF,t_70)
	
	```sql
	select * from emp t1 left join dept t2 on t1.deptId = t2.id
	```
	3、*t1 表和 t2 表共有 + t2表独有（`right join`）*
	![ ](https://img-blog.csdnimg.cn/20200320151949999.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80MzI4NzIzOQ==,size_16,color_FFFFFF,t_70)
	
	```sql
	select * from emp t1 right join dept t2 on t1.deptId = t2.id
	```
	4、*t1 表的独有（`left join...where t2.id is null`）*
	![ ](https://img-blog.csdnimg.cn/20200320152019392.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80MzI4NzIzOQ==,size_16,color_FFFFFF,t_70)
	
	```sql
	select * from emp t1 left join dept t2 on t1.deptId = t2.id where t2.id is null
	```
	5、*t2 表的独有（`right join...where t1.id is null`）*
	![ ](https://img-blog.csdnimg.cn/20200320152103413.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80MzI4NzIzOQ==,size_16,color_FFFFFF,t_70)
	
	```sql
	select * from emp t1 right join dept t2 on t1.deptId = t2.id where t1.id is null
	```
	6、*t1 表和 t2 表全有（`union`）*
	![ ](https://img-blog.csdnimg.cn/2020032015262913.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80MzI4NzIzOQ==,size_16,color_FFFFFF,t_70)
	MySQL中不支持FULL JOIN
	`UNION`： 可去除重复数据
	`UNION ALL`： 不去除重复数据
	
	```sql
	select * from emp t1 left join dept t2 on t1.deptId = t2.id
	union
	select * from emp t1 right join dept t2 on t1.deptid = t2.id
	```
	7、*t1 表的独有 + t2 表的独有（`union`）*
	![ ](https://img-blog.csdnimg.cn/20200320152617512.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80MzI4NzIzOQ==,size_16,color_FFFFFF,t_70)
	
	```sql
	select * from emp t1 left join dept t2 on t1.deptId = t2.id where t2.id is null
	UNION
	select * from emp t1 right join dept t2 on t1.deptId = t2.id 
	where t1.deptId is null 
	```
#### 3）索引简介
> MySQL官方对索引的定义为：索引（Index）是帮助MySQL高效获取数据的数据结构。可以得到索引的本质：==索引是数据结构。== 目的在于提高查询效率，可以类比字典。

+ 简单理解为 *“排好序的快速查找数据结构”*  ：
在数据之外，*数据库系统还维护着满足特定查找算法的数据结构*，这些数据结构以某种方式引用（指向）数据。
![ ](https://img-blog.csdnimg.cn/20200320154156277.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80MzI4NzIzOQ==,size_16,color_FFFFFF,t_70)
左边是数据表，一共有两列七条数据，最左边是数据记录的物理地址，为了加快Col2 的查找，可以维护一个右边所示的二叉查找树，每个节点分别包含索引值和一个指向对应数据记录物理地址的指针，这样就可以运用二叉查找在一定的复杂度内获取到相应数据，从而快速的检索出符合条件的记录。
`二叉树`： 二叉树很可能会发生两边不平衡的情况。
`B-Tree`： 会自动根据两边的情况自动调节，使两端无限趋近于平衡状态，可以使性能最稳定。但是`插入/修改`操作过多时，B-TREE会不断调整平衡，消耗性能。
+ 一般来说索引本身也很大，不可能全部存储在内存中，因此索引往往以索引文件的形式存储在*磁盘*上。
+ 我们平常所说的索引，如果没有特别指明，都是指`B树` （多路搜索树，并不一定是二叉的）结构组织的索引。其中聚集索引，次要索引，覆盖索引，复合索引，前缀索引，唯一索引默认都是使用B+树这种类型的索引之外，还有哈希索引（hash index）等。

*索引优势*：

+ 类似图书馆建立书目索引，提高数据检索的效率，降低数据库的IO成本。
+ 通过索引列对数据进行排序，降低数据排序的成本，降低了CPU的消耗。

*索引劣势*：

+ 实际上索引也是一张表，该表保存了主键与索引字段，并指向实体表的记录，所以索引列也是要占用空间的。
+ 虽然索引大大提高了查询速度，同时却会==降低更新表的速度==，如对表进行INSERT、UPDATE、和DELETE，因为更新表时，MySQL不仅要保存数据，还要保存一下索引文件每次更新添加了索引列的字段，都会调整因为更新所带来的键值变化后的索引信息。
+ **索引只是提高效率的一个因素，如果你的MySQL有大量的表，就需要花时间研究建立最优秀的索引，或优化查询语句**

*索引结构*

+ `BTree索引`：
**真实数据存在于叶子节点**，即3、5、9、10、13、15、28、29、36、60、75、79、90、99.
![ ](https://img-blog.csdnimg.cn/20200320165739681.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80MzI4NzIzOQ==,size_16,color_FFFFFF,t_70)
【查找过程】
如果要查找数据29，那么首先会把磁盘块1有磁盘加载到内存，此时发生一次IO，在内存中用二分查找确定29在17和35之间，锁定磁盘块1的P2指针，内存时间因为非常短（相比磁盘的IO）可以忽略不计，通过磁盘块1的的P2指针的磁盘的磁盘地址把磁盘块3由磁盘加载到内存，发生第二次IO，29在26和30之间，锁定磁盘块3的P2指针，通过指针加载磁盘块8到内存，发生第三次IO，同时内存中做二分查找到29，结束查询，总计三次IO
+ `B+Tree索引`：
**非叶子节点不存储真实的数据，只存储指引搜索方向的数据项**，如5、10并不真实存在于数据表中。
![ ](https://img-blog.csdnimg.cn/20200320173639608.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80MzI4NzIzOQ==,size_16,color_FFFFFF,t_70)
B+Tree 第二级的数据并不能直接取出来，只作索引使用。在内存有限的情况下，查询效率高于BTree
BTree第二级可以直接取出来，树形结构比较重，在内存无限大到时候有优势。

*【B+Tree 和 BTree 的区别】*

1. 内存有限的情况下，B+Tree永远比BTree好，无限内存则反之
2.  B树的关键字和记录是放在一起的，叶子节点可以看做外部节点，不包含任何信息；B+树叶子节点中国你只有关键字和指向下一个节点的索引，记录只放在叶子节点中。（一次查询可能进行两次I/O操作）
3.  在B树中，越靠近根节点的记录查找时间越快，只要找到关键字即可确定记录存在；而B+树每个记录的查找时间基本是一样的，都需要从根节点走到叶子节点，而且在叶子节点中还要在比较关键字。从这个角度看B树的性能好像会比B+树好，而在实际应用中却是B+树的性能要好些。因为B+树的非叶子节点不存放实际的数据，这样每个节点可容纳的元素个数比B数多，树高比B树小，这样带来的好处是减少磁盘访问次数。尽管B+树找到一个记录所需的比较次数比B树多但是一次磁盘访问时间相当于成百上千次内存比较时间，因此实际中B+树的性能可能还会好写，而且B+树的叶子节点使用指针连接在一起，方便顺序遍历（例如查看一个目录下的所有文件，一个表中的所有记录等）
4.  B+树的磁盘读写代价更低，相对来说IO读写次数也就降低了。
5.  B+树的查询效率更加稳定。由于非终结点并不是指向文件内容的节点，而只是叶子节点中关键字的索引。所以任何关键字的查找必须走一条从根节点到叶子节点的路。所以关键字查询的路径长度相同，导致每一个数据的查询效率相当。

*聚簇索引*
![ ](https://img-blog.csdnimg.cn/20200320210314462.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80MzI4NzIzOQ==,size_16,color_FFFFFF,t_70)
`好处：`
按照聚簇索引排序顺序，查询显示一定范围数据的时候，由于数据都是紧密相连，数据库不用从多个数据块中提取数据，所以节省了大量的IO操作。
`限制：`

- 对于MySQL数据库目前只有InnoDB数据引擎支持聚簇索引，而MyISAM并不支持聚簇索引。
- 由于数据物理存储排序方式只能有一种，所以每个MySQL的表只能有一个聚簇索引。一般情况下就是该表的**主键**。

*full-text全文索引*

>全文索引（也称全文检索）是目前搜索引擎使用的一种关键技术。它能够利用`分词技术`等多种算法智能分析出文本文字中关键词的频率和重要性，然后按照一定的算法规则智能地筛选出我们想要的搜索结果。

+ `查询：`
```sql
# 传统 LIKE 查询
select * from blink t1 where t1.content like '%菜%'
# 全文检索
select * from blink t1 where MATCH(title,content) AGAINST('菜') 
```
+  `限制：` MySQL5.6.4之前只用MyISAM 支持，5.6.4以后 InnoDB才支持，但是官方版不支持中文分词，需要第三方分词插件。

*Hash索引*

+ Hash 索引只有Memory，NDB两种引擎支持，Memory引擎默认支持。
+ Hash索引，如果多个Hash值相同，出现哈希碰撞，那么索引以链表的方式存储。
+ NoSql采用此索引结构。

*RTree索引*
R-Tree在MySQL很少使用，仅支持geometry 数据结构，支持该类型的存储引擎只有MyISAM、bdb、InnoDB、ndb、archive几种。相对于B-Tree，R-Tree的优势在于`查找`。

#### 4）索引分类

+ *主键索引*
设定为主键后数据库会自动建立索引，InnoDB采用聚簇索引
`语法：`
```sql
# 随表一起创建
CREATE TABLE emp (
	# 使用AUTO_INCREMENT关键字的列必须要有索引
	ID int(10) UNSIGNED AUTO_INCREMENT
	, NAME varchar(8)
	, PRIMARY KEY(ID)
 )
 # 单独建主键索引
 ALTER TABLE emp add PRIMARY KEY emp(id);
 # 删除主键索引
 ALTER TABLE emp drop PRIMARY KEY;
 # 修改主键索引前必须删除(drop)原索引，再新建(add)索引
```
+ *单值索引*
即一个索引只包含单个列，一个表可以有多个单列索引。
`语法：`
```sql
# 随表一起创建
CREATE TABLE emp (
	# 使用AUTO_INCREMENT关键字的列必须要有索引
	ID int(10) UNSIGNED AUTO_INCREMENT
	, EMP_NO varchar(8)
	, NAME varchar(8)
	, KEY(EMP_NO)
 )
 # 单独建单列索引
 create index idx_emp_no on emp(EMP_NO)
 # 删除单列索引
 drop index idx_emp_no
```
+ *唯一索引*
索引列的值必须唯一，但允许有空值。
```sql
# 随表一起创建
CREATE TABLE emp (
	# 使用AUTO_INCREMENT关键字的列必须要有索引
	ID int(10) UNSIGNED AUTO_INCREMENT
	, EMP_NO varchar(8)
	, NAME varchar(8)
	, UNIQUE(EMP_NO)
 )
#建立唯一索引是必须保证所有的值是唯一的(除了null)，若有重复数据，会报错

 # 单独建唯一索引
 create unique index idx_emp_no on emp(EMP_NO)
 # 删除主键索引
 drop index idx_emp_no on emp
```
+ *复合索引*
在数据库操作期间，复合索引比单值索引所需要的开销更小（对于相同的多个列建索引），当表的行数远大于索引列的数目时可以使用复合索引。
```sql
# 随表一起创建
CREATE TABLE emp (
	# 使用AUTO_INCREMENT关键字的列必须要有索引
	ID int(10) UNSIGNED AUTO_INCREMENT
	, EMP_NO varchar(8)
	, NAME varchar(8)
	, key(EMP_NO，NAME)
 )
#建立唯一索引是必须保证所有的值是唯一的(除了null)，若有重复数据，会报错

 # 单独建唯一索引
 create index idx_no_name on emp(EMP_NO,NAME)
 # 删除主键索引
 drop index idx_no_name on emp
```
**`【基本语法】`**

```sql
# 创建
alter < table_name > add [unique] index <index_name> on <column_name>
# 删除
drop index <index_name> on <table_name>
#查看
show index from <table_name>
#使用ALTER命令
#方式1：该语句添加一个主键，这意味着索引值必须是唯一的，且不能为null
alter table <table_name> add primary key <column_name>
#方式2：该语句添加一个唯一索引，值必须是唯一的(null外，null可能会出现很多次)
alter table <table_name> add unique key <column_name>
#方式3：该语句添加普通索引，索引值可以出现很多次
alter table <table_name> add index <index_name>(column_name)
#方式4：该语句指定了索引为FULLTEXT，用户全文索引
alter table <table_name> add FULLTEXT <index_name>(column_name)
```

*哪些情况需要建立索引*

+ 主键自动建立唯一索引
+ 频繁作为`查询条件`的字段应该创建索引（where后面的语句）
+ 查询中与其他表关联的字段，外键关系建立索引
+ 单键/组合索引的选择问题（在高并发下倾向创建组合索引）
+ `查询中排序的字段`，排序字段若通过索引去访问将大大提高排序速度
+ `查询中统计或者分组字段`

*哪些情况不需要建立索引*

+ 表记录太少
+ 经常增删改的表（因为更新表时，MySQL不仅要保存数据，还要保存一下索引文件）
+ where 条件里用不到的字段不创建索引
+ 数据重复且分布平均的表字段，因此应该只为最经常查询和最经常排序的数据列建立索引。注意，如果某个数据列包含许多重复的内容，为它建立索引就没有太大的实际效果。

#### 4）性能分析
*MySQL常见瓶颈*

+ `CPU`
SQL中对大量数据进行比较、关联、排序、分组（最大的压力在于**比较**）
+ `IO`
实例内存满足不了缓存数据或排序等需要，导致产生大量物理IP。查询执行效率低，扫描过多数据行。
+ `锁`
不适宜的锁的设置，导致线程阻塞，性能下降。死锁，线程之间交叉调用资源，导致死锁，程序卡主。

*服务器硬件的性能瓶颈*：
`top`，`free`，`iostat`和`vmstat`来查看系统的性能状态

**`Explain的使用`**

使用*Explain*关键字可以模拟优化器执行SQL查询语句，从而知道MySQL是如何处理你的SQL语句的。分析你的查询语句或是表结构的性能瓶颈

*可以查看的内容*：

1. 表的读取顺序
2. 哪些索引可以使用
3. 哪些索引被实际使用
4. 表之间的引用
5. 每张表有多少行被优化器查询

*怎么用*：
`explain + SQL语句`
包含的信息：
![ ](https://img-blog.csdnimg.cn/20200320234315412.png)

*各字段解释*

`1.【 id】`
	select查询的序列号，包含一组数字，表示查询中执行select字句或操作表的顺序。**三种情况：**
- **id相同，执行顺序由上至下**
	![ ](https://img-blog.csdnimg.cn/20200321155430110.png)

+ **id不同，如果是子查询，id的序号会递增，id值越大优先级越高，越被先执行**
	![ ](https://img-blog.csdnimg.cn/20200321155841307.png)
+ **id相同和不同同时存在**
![ ](https://img-blog.csdnimg.cn/20200321160609390.png)
*id如果相同，可以认为是一组，从上往下顺序执行；在所有组中，id值越大，优先级越高，越先执行。*

`2.【select_type】`
	![ ](https://img-blog.csdnimg.cn/20200321160949800.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80MzI4NzIzOQ==,size_16,color_FFFFFF,t_70)
- **SIMPLE**
		简单的select查询，查询中不包含子查询或者UNION 
		![ ](https://img-blog.csdnimg.cn/2020032116193777.png)
- **PRIMARY**
	查询中若包含任何复杂的子部分，最外层查询则被标记为Primary
	![ ](https://img-blog.csdnimg.cn/2020032116211212.png)
- **DERIVED**
		在FROM列表中包含的子查询被标记为DERIVED（衍生）MySQL会递归执行这些子查询，把结果放在临时表里。
		![ ](https://img-blog.csdnimg.cn/2020032116223744.png)
- **SUBQUERY**
		在SELECT或WHERE列表中包含了子查询
		![ ](https://img-blog.csdnimg.cn/20200321162504880.png)
- **DEPENDENT SUBQUERY**
		在SELECT或WHERE列表中包含了子查询，子查询基于外层
	![ ](https://img-blog.csdnimg.cn/20200321162619290.png)
		`【dependent subquery 和 subquery 的区别】`
		依赖子查询：子查询结果为多值
		子查询：查询结果为单值
- **UNCACHEABLE SUBQUERY**
		无法被缓存的子查询
		![ ](https://img-blog.csdnimg.cn/2020032116290516.png)
		@@表示查的是环境参数，没办法缓存
- **UNION**
		若第二个SELECT出现在UNION之后，则被标记为UNION；
	若UNION 包含在FROM字句的子查询中，外层SELECT将被标记为：DERIVED
	![ ](https://img-blog.csdnimg.cn/20200321163905156.png)
- **UNION RESULT**
		从UNION表获取结果的SELECT
	![ ](https://img-blog.csdnimg.cn/20200321164007797.png)
	

`3.【table】`
显示这一行的数据是关于哪张表的
`4.【type】`
	type显示的是访问类型，是较为重要的一个指标，结果值从最好到最坏的依次排序：
	`system` > `const` > `eq_ef` > **`ref`** > **`range(尽量保证)`** > `index` > `ALL`
	*一般来说，得保证查询至少达到range级别，最好能达到ref*

- **system**
		表只有一行记录（等于系统表），这是const类型的特列，平时不会出现，这个也可以忽略不计
- **const**
		表示通过索引一次就找到了，const	用于比较primary key或者 unique索引。因为只匹配一行数据，所以很快将主键置于where列表中，MySQL就能将该查询转换为一个常量
- **eq_ref**
		唯一性索引扫描，对于每个索引键，表中只有一条记录与之匹配。常见于主键或唯一索引扫描
- **ref**
		非唯一性索引扫描，返回匹配某个单独值的所有行，本质上也是一种索引访问，它返回所有匹配某个单独值的行，然而，他可能会找到多个符合条件的行，所以他应该属于查找和扫描的混合体
- **range**
		只**检索给定范围的行**，使用一个索引来选择行。key列显示使用了哪个索引，一般就是在你的where语句中出现了**between**、**<**、**>**、**in**等查询。这种范围扫描索引比全表扫描要好，因为它只需要开始于索引的某一个点，而结束于另一点，不用扫描全部索引。
- **index**
		Full Index Scan，index与ALL区别为**index类型只遍历索引树**。这通常比ALL快，因为索引文件通常比数据文件小。（*也就是说虽然all和index都是读全表的*），但index是从索引中读取的，而all是从硬盘中读的。
- **all**
		Full Table Scan，将遍历全表以找到匹配的行
		![ ](https://img-blog.csdnimg.cn/20200321172703940.png)
		![ ](https://img-blog.csdnimg.cn/2020032117272499.png)
		

`5.【possible_keys】`
	显示可能应用到这张表中的索引，一个或多个。查询涉及到的字段上若存在的索引，则该索引将被列出，*但不一定被查询实际使用*
`6.【key】`
	实际使用的索引，如果为NULL，则没有使用索引。查询中若使用了覆盖索引，则该索引和查询的select字段重叠。
`7.【key_len】`
	表示索引中使用的字节数，可通过该列计算查询中使用的索引的长度。key_len字段能够帮你检查是否充分利用上了索引。
	![ ](https://img-blog.csdnimg.cn/20200321173550881.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80MzI4NzIzOQ==,size_16,color_FFFFFF,t_70)
	*计算方式*：
	![ ](https://img-blog.csdnimg.cn/20200321173900744.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80MzI4NzIzOQ==,size_16,color_FFFFFF,t_70)
	*动态类型包括：varchar，detail text（）截取字符串*
	本张的表结构如下：
	![ ](https://img-blog.csdnimg.cn/20200321173935448.png)
	第一组计算为：
	`key_len=deptno(int)+null+ename(varchar(20)*3+动态=4+1+20*3+2=67`
	第二组计算为：
	`key_len=deptno(int)+null=4+1=5`
	
`8.【ref】`
	显示索引的哪一列被使用了，如果可能的话，是一个常数。哪些列或者常量被用于查找索引列上的值
	![ ](https://img-blog.csdnimg.cn/20200321174526992.png)
`9.【row】`
	rows列显示MySQL认为它执行查询时必须检查的行数（越少越好）
	![ ](https://img-blog.csdnimg.cn/20200321174718941.png)
`10.【Extra】`
	包含不适合在其他列中显示但十分重要的额外信息。

- **Using filesort**
	说明mysql会对数据使用一个外部的索引排序，而不是按照表内的索引顺序进行读取。MySQL中无法利用索引完成的排序操作称为“文件排序”。
- **Using temporary**
	使用了临时表保存中间结果，MySQL在对查询结果排序时使用临时表。常见于排序order by 和分组查询 group by。
- **`Using index`**
	表示相应的select操作中使用了覆盖索引（Covering Index），避免了表的数据行，效率不错。如果同时出现**using where**，表明索引被用来执行索引键值的查找；如果没有同时出现**using where**，表明索引只是用来读取数据而非利用索引执行查找。
		`覆盖索引：`
		一个索引包含了（或覆盖了）【select子句】与查询条件【where 子句】中所有需要的字段就叫做覆盖索引。
		==注意：== 只取出需要的列，不可select *，不可将所有字段一起做索引
- **`Using where`**
		表明使用了 where 过滤
- **Using join buffer**
		使用了连接缓存
#### 5）查询优化
**`索引的使用`**

1. *全值匹配我最爱*
	staffs 表建立索引 idx_staffs_nameAgePos,以name，age，pos的顺序建立，全值匹配标识按顺序匹配。
	![ ](https://img-blog.csdnimg.cn/2020032118155577.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80MzI4NzIzOQ==,size_16,color_FFFFFF,t_70)
2. *最佳左前缀原则*
	如果索引了多列，要遵守最左前缀原则，值得是查询从索引的最左前列开始，并且*不跳过索引中的列*
	**and 忽略左右关系**，即使没有按顺序，由于优化器的存在，会自动优化
3. *不在索引列上做任何操作（计算、函数、（自动或手动）类型转换），会导致索引失效而转向全表扫描。*
	![ ](https://img-blog.csdnimg.cn/20200321182239290.png)
4. *存储引擎不能使用索引中范围条件右边的列*
	范围若有索引则能使用到索引，范围条件右边的索引会失效（范围条件右边与范围条件使用的同一个组合索引，右边的才会失效，若是不同索引则不会失效）
	![ ](https://img-blog.csdnimg.cn/20200321182540949.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80MzI4NzIzOQ==,size_16,color_FFFFFF,t_70)
5. *尽量使用覆盖索引（只访问索引的查询（索引列和查询列一致）），减少*`（select *）`
![ ](https://img-blog.csdnimg.cn/20200321182832424.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80MzI4NzIzOQ==,size_16,color_FFFFFF,t_70)
6. *MySQL在使用不等于（!= 或 <>）的时候无法使用索引，会导致全表扫描。*
where age != 10 and name = 'xxx' 这种情况下，mysql会自动优化将 name = 'xxx' 放在 age != 10 之前，name依然能使用索引，只是age的索引失效
![ ](https://img-blog.csdnimg.cn/20200321183121615.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80MzI4NzIzOQ==,size_16,color_FFFFFF,t_70)
7. *is not null 也无法使用索引，但是 is null 是可以使用索引*
![ ](https://img-blog.csdnimg.cn/20200321183409797.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80MzI4NzIzOQ==,size_16,color_FFFFFF,t_70)
8. *like 以通配符开头（'%xxx'）索引失效变成全表扫描*
`like '%xxx'`：type 类型会变成all
`like 'xxx%'`：type 类型为range，算是范围，可以使用索引
![ ](https://img-blog.csdnimg.cn/20200321195655768.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80MzI4NzIzOQ==,size_16,color_FFFFFF,t_70)
9. *字符串不加单引号索引失效*
底层进行类型转换时索引失效，使用了函数造成了索引失效
![ ](https://img-blog.csdnimg.cn/20200321195814135.png)
10. *少用or，用它连接时索引会失效*
 ![ ](https://img-blog.csdnimg.cn/20200321195857924.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80MzI4NzIzOQ==,size_16,color_FFFFFF,t_70)
 `【例子小节】`
 此时复合索引index（a,b,c）

| where 语句                                              | 索引是否被使用                                              |
| ------------------------------------------------------- | ----------------------------------------------------------- |
| where a = 3                                             | 是，使用到 ==a==                                            |
| where a = 3 and b = 4                                   | 是，使用到 ==a==，==b==                                     |
| where a = 3 and b = 4 and c = 5                         | 是，使用到 ==a==，==b==，==c==                              |
| where b = 4 或者 where c = 5 或者 where b = 4 and c = 5 | 否，`带头大哥`==a== 不在                                    |
| where a = 3 and c = 5                                   | 是，使用到==a==，==c== 没有使用，因为`中间兄弟`==b== 给没了 |
| where a = 3 and b > 4 and c = 5                         | 是，使用到==a==，==b==，因为 `>` 后面不使用索引             |
| where a = 3 and b like 'xxx%' and c = 5                 | 是，使用到 ==a==，==b==，==c==                              |
| where a = 3 and b like '%xxx' and c = 5                 | 是，使用到 ==a==，使用`like'%xxx' `及之后索引会失效         |
| where a = 3 and b like ''x%xxx% and c =5                | 是，使用到 ==a==，==b==，==c==                              |
 `【使用建议】`
1. 对于单键索引，尽量选择针对当前query过滤性更好的索引
 2. 在选择组合索引的时候，当前query中过滤性最好的字段在索引字段顺序中，位置越靠前越好。（避免索引过滤性好的索引失效）
 3. 在选择组合索引的时候，尽量选择可以能够包含当前query中where字句中更多字段的索引
 4. 尽可能通过分析统计信息和调整query的写法来达到选择合适索引的目的

**`关联查询优化`**

1、保证被驱动表的join字段已经被索引（join后的表为驱动表）
2、left join时，选择小表作为驱动表，大表作为被驱动表（left join一定是左边是驱动表，右边是被驱动表）
3、inner join时，MySQL会自己帮你把小结果集选为驱动表。因为**驱动表无论如何都会被全表扫描**，所以扫描次数越少越好。
4、子查询尽量不要放在被驱动表，有可能使用不到索引。

```sql
# 未加索引，type为ALL
explain select * from class left join book on class.card = book.card
# 添加索引优化，第二行的type变成了ref
alter table book add index idx_card_B(card);
# 这是由左连接特效决定的，left join条件用于确定如何从右表搜索行，左边一定都有
# 继续优化，删除旧索引，新建新索引
drop index idx_card_B on book;
alter table class add index idx_card_A(card)
```
**`子查询优化`**

1、 有索引的情况下用 inner join 是最好的，其次是 in，exists最糟糕
2、 无索引的情况下用小表驱动大表，因为 join 方式需要 distinct ，没有索引distinct 消耗性能比较大，所以 exists 性能最佳，其次 in 其次，join性能最差
3、 无索引的情况下大表驱动小表，in和exists的性能应该是接近的，都比较糟糕，exists稍微好一点，但是超不过5%

**`order by关键字优化`**

尽量使用Index方式排序，避免使用FileSort 方式排序。MySQL中支持两种方式的排序，`FileSort`和`Index`，其中index效率高，它指Mysql扫描索引本身完成排序，FileSort方式效率比较低。满足三种情况会使用Index排序。

- Order By语句使用索引最左前列
- 使用where子句与order by子句条件列组合满足索引最左前列
- where子句中如果出现索引的范围查询（即explain中出现range）会导致order by索引失效。

`例子：talA 表中有索引 （age,birth,name）`
![ ](https://img-blog.csdnimg.cn/20200321220318936.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80MzI4NzIzOQ==,size_16,color_FFFFFF,t_70)
![ ](https://img-blog.csdnimg.cn/20200321220450759.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80MzI4NzIzOQ==,size_16,color_FFFFFF,t_70)

**`分页查询的优化--limit`**

```sql
explain select sql_no_chache  * from emp order by deptno limit 10000,40
```
![ ](https://img-blog.csdnimg.cn/20200321222420502.png)
```sql
#加上索引
create index emp on emp(deptno)
#重新查看执行 
```
![ ](https://img-blog.csdnimg.cn/20200321222559336.png)
```sql
# 通过以上结果可以看出加上索引并不能改变
# 进一步优化：先利用覆盖索引把要取的数据行的主键取到，然后再用这个主键列与数据表做关联（查询数据量小了后）
explain select sql_no_cache * from emp e inner join (select id from emp e order by deptno limit 10000,40)a on a.id = e.id 
```
![ ](https://img-blog.csdnimg.cn/20200321224143655.png)

**`GROUP BY 关键字优化`**

1、group by实质上是先排序后进行分组，遵照索引建的最佳左前缀
2、当无法使用索引列，增大max_length_for_sort_data参数的设置+增大sort_buffer_size参数的设置
3、where高于having，能写在where限定的条件就不要去having限定了。

**`去重优化`**

尽量不要使用distinct关键字去重
`例子：`

```sql
# 使用distinct关键字去重消耗性能
select distinct BOOK_NAME from book where id in(1,2,5,4,8)
# 使用 group by能够利用到索引
select BOOK_NAME from book where id in(1,2,5,4,8) group by BOOK_NAME
```

![ ](https://imgconvert.csdnimg.cn/aHR0cHM6Ly93d3cuNTJkb3V0dS5jbi9zdGF0aWMvdGVtcC9waWMvOWJkNjhkMTUwZjA3ODdjNTYwYTQzOWRhMzU5YTU4MGEucG5n?x-oss-process=image/format,png#pic_center)

> 本文较长，能看到这里的都是好样的，成长之路学无止境
> 今天的你多努力一点，明天的你就能少说一句求人的话！

