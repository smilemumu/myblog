---
title: "SQL优化"
date: 2020-11-24
tags: [SQL]
categories: [MySql]
draft: true
---
# SQL优化

## 1.SQL语句中过滤条件where和having的区别
1. where 是一个**约束声明**，使用 where 约束来自数据库的数据，where 是在结果返回之前起作用，where 中不能使用聚合函数。
2. Having 是一个**过滤声明**，是在查询返回结果集以后对查询结果进行的过滤操作，在 Having 中可以使用聚合函数。
3. 在查询过程中，where 子句执行优先级高于聚合语句。**聚合语句**(sum，min，max，avg，count)优先级高于 having 子句。

# 2.优化查询
- MySQL只有对以下操作符才使用索引：<，<=，=，>，>=，BETWEEN，IN，以及右模糊查询LIKE（'a%'）。
- 对于不等于(!= 和 <>)会放弃使用索引而进行全表扫描。
- 对于左模糊like ‘%...’无法直接使用索引，但可以利用reverse，变化成 右模糊like ‘…%’。
- 对于联合索引来说，如果存在范围查询，比如between、>、<等条件时，会造成后面的索引字段失效。
- 对查询进行优化，应尽量避免全表扫描，首先应考虑在 where 及 order by 涉及的列上建立索引。
- 在 where 子句中对字段进行 null 值判断，会导致引擎放弃使用索引而进行全表扫描，所以字段要有默认值
- 在 where 子句中对字段进行函数、算术运算或其他表达式运算，会导致引擎放弃使用索引而进行全表扫描。
- 如果字段类型是字符串，where 时一定用引号括起来，否则索引失效
- 将大的 delete，update 或者 insert 查询变成多个小查询
- 如果插入数据过多，考虑批量插入
- select语句务必指明字段名称，尽量避免使用"select * "。因为它会进行全表扫描。
- Inner join 、left join、right join，优先使用 Inner join。如果是 left join，左边表结果尽量小。Inner join 内连接，在两张表进行连接查询时，只保留两张表中完全匹配的结果集。left join 在两张表进行连接查询时，会返回左表所有的行，即使在右表中没有匹配的记录。right join 在两张表进行连接查询时，会返回右表所有的行，即使在左表中没有匹配的记录。都满足 SQL 需求的前提下，推荐优先使用 Inner join(内连接)，如果要使用 left join，左边表数据结果尽量小，如果有条件的尽量放到左边处理。
- 区分 in 和 exists、not in 和not exists
	- 如果是exists，那么以外层表为驱动表，先被访问；如果是IN，那么先执行子查询。所以**IN适合于外表大而内表小的情况；exists适合于外表小而内表大的情况。**

- 取出的结果集为A表不在B表中的数据？
	- 优：性能高
	```sql
	select colname … from tableA 
	left join tableB on a.id = b.id 
	where b.id is null
	```
	- 差：
	```sql
	select colname … from tableA 
	where a.id not in (select b.id from tableB)
	```
- 对于复合索引来说，要遵守最左前缀法则。
	- 当创建一个联合索引的时候，如(id,name,school)，相当于创建了(id)、(id,name)和(id,name,school)三个索引，这就是最左匹配原则。可以直接用id字段，也可以id、name这样的顺序，但是name、school都无法使用这个索引。（联合索引不满足最左原则，索引一般会失效，但是这个还跟 MySQL 优化器有关的。）
- 强制】不要使用count(列名)或count(常量)来替代count(*)，count(*)是SQL92定义的标准统计行数的语法，跟数据库无关，跟NULL和非NULL无关。	
> 说明：count(*)会统计值为NULL的行，而count(列名)不会统计此列为NULL值的行。
- 尽可能的使用 varchar/nvarchar 代替 char/nchar 。因为首先变长字段存储空间小，可以节省存储空间，其次对于查询来说，在一个相对较小的字段内搜索效率显然要高些。
- 使用EXPLAIN分析 SQL 执行情况。
- count(distinct col) 计算该列除NULL之外的不重复行数
- 【强制】当某一列的值全是NULL时，count(col)的返回结果为0，但sum(col)的返回结果为NULL，因此使用sum()时需注意NPE问题。
	- 正例：可以使用如下方式来避免sum的NPE问题：SELECT IF(ISNULL(SUM(g)),0,SUM(g)) FROM table;

## 3.大表优化
### 3.1单表优化
- 除非单表数据未来会一直不断上涨，否则不要一开始就考虑拆分，拆分会带来逻辑、部署、运维的各种复杂度。
### 3.2字段
- 尽量使用TINYINT、SMALLINT、MEDIUMINT作为整数类型而非INT，如果非负则加上UNSIGNED
- VARCHAR的长度只分配真正需要的空间
- 使用枚举或整数代替字符串类型
- 尽量使用TIMESTAMP而非DATETIME，
- 单表不要有太多字段，建议在20以内
- 避免使用NULL字段，很难查询优化且占用额外索引空间
- 用整型来存IP

### 3.3索引
- 索引并不是越多越好，要根据查询有针对性的创建，考虑在WHERE和ORDER BY命令上涉及的列建立索引，可根据EXPLAIN来查看是否用了索引还是全表扫描
- 应尽量避免在WHERE子句中对字段进行NULL值判断，否则将导致引擎放弃使用索引而进行全表扫描
- 值分布很稀少的字段不适合建索引，例如”性别”这种只有两三个值的字段

## 4.查询SQL
- 可通过开启慢查询日志来找出较慢的SQL
> 修改/etc/my.cnf
> 增加slow_query_log=1开启慢查询日志
> long_query_time=1,查询超过多长时间记录
> log_queries_not_using_indexes=1,记录没有索引的查询

## 5.MyISAM和InnoDB
总体来说：MyISAM适合select密集型的表，而InnoDB适合insert和update密集型的表。

## 6.系统调优参数


## 7.分表
- 什么是分表?
	- 分表是将一个大表按照一定的规则分解成多张具有独立存储空间的实体表，每个表都对应三个文件，MYD数据文件，.MYI索引文件，.frm表结构文件。这些表可以分布在同一块磁盘上，也可以在不同的机器上。app读写的时候根据事先定义好的规则得到对应的表名，然后去操作它。
	- 我们可以用merge存储引擎来实现分表
	- mysql集群虽然不是分表，但起到了和分表相同的作用。集群可分担数据库的操作次数，将任务分担到多台数据库上。集群可以读写分离，减少读写压力。从而提升数据库性能。

### 7.1垂直拆分
- 什么是垂直拆分？
	- 垂直切分是指数据表列的拆分，把一张列比较多的表拆分为多张表
	- 通常我们按以下原则进行垂直拆分:
	- 把不常用的字段单独放在一张表;
	- 把text，blob（binary large object，二进制大对象）等大字段拆分出来放在附表中;
	- 经常组合查询的列放在一张表中;
	- 垂直拆分更多时候就应该在数据表设计之初就执行的步骤，然后查询的时候用jion关键起来即可。

### 7.2水平拆分
- 什么是水平拆分？
	- 水平拆分是指数据表行的拆分，把一张的表的数据拆成多张表来存放。
- 水平拆分原则
	- 通常情况下，我们使用hash、取模等方式来进行表的拆分
	- 比如一张有400W的用户表users，为提高其查询效率我们把其分成4张表users1，users2，users3，users4
	- 通过用ID取模的方法把数据分散到四张表内Id%4= [0,1,2,3]
	- 然后查询,更新,删除也是通过取模的方法来查询	
	- 部分业务逻辑也可以通过地区，年份等字段来进行归档拆分;
	- 进行拆分后的表，这时我们就要约束用户查询行为。比如我们是按年来进行拆分的,这个时候在页面设计上就约束用户必须要先选择年,然后才能进行查询。
### 7.3 利用merge存储引擎来实现分表
1.根据原表创建子表

```sql
create table tb_member1(
	id bigint primary key,
	name varchar(20),
	sex tinyint  not null default 0
	) ENGINE=InnoDB DEFAULT=utf8;

create table tb_member2(
	id bigint primary key,
	name varchar(20),
	sex tinyint  not null default 0
	) ENGINE=InnoDB DEFAULT=utf8;

create table tb_member(
	id bigint primary key,
	name varchar(20),
	sex tinyint  not null default 0
	) ENGINE=MERGE UNION=(tb_member1,tb_member2) INSERT_METHOD=LAST DEFAULT=utf8;
```
2.将表数据分散到子表中
```sql
mysql> insert into tb_member1(id,name,sex) select id,name,sex from member where id%2=0;
mysql> insert into tb_member2(id,name,sex) select id,name,sex from member where id%2=1;
```
>注意：总表只是一个外壳，存取数据发生在一个一个的子表里面。
>注意：每个子表都有自已独立的相关表文件，而主表只是一个壳，并没有完整的相关表文件

## 8.分区
- 什么是分区？
	- 分区和分表相似，都是按照规则分解表。不同在于分表将大表分解为若干个独立的实体表，而分区是将数据分段划分在多个位置存放，分区后，表还是一张表，但数据散列到多个位置了。app读写的时候操作的还是表名字，db自动去组织分区的数据。
>mysql 5.6后根据show plugins;查看数据库是否支持分区，会发现 partition 为ACTIVE支持分区

### 8.1MySql分区类型
- 1.RANGE分区
	- 使用 VALUES LESS THAN操作符
```
CREATE TABLE employees (
    id INT NOT NULL,
    fname VARCHAR(30),
    lname VARCHAR(30),
    hired DATE NOT NULL DEFAULT '1970-01-01',
    separated DATE NOT NULL DEFAULT '9999-12-31',
    job_code INT NOT NULL,
    store_id INT NOT NULL
)  
 partition BY RANGE (store_id) (
    partition p0 VALUES LESS THAN (6),
    partition p1 VALUES LESS THAN (11),
    partition p2 VALUES LESS THAN (16),
    partition p3 VALUES LESS THAN (21),
	partition p4 VALUES LESS THAN maxvalue
);
--新增分区
alter table test2.user add partition (partition p4 values less than maxvalue);
```
- 2.LIST分区
	- 使用VALUE IN操作符
	- 类似于按RANGE分区，区别在于LIST分区是基于列值匹配一个离散值集合中的某个值来进行选择。
```sql
 PARTITION BY LIST(store_id)
    PARTITION pNorth VALUES IN (3,5,6,9,17),
    PARTITION pEast VALUES IN (1,2,10,11,19,20),
    PARTITION pWest VALUES IN (4,12,13,14,18),
    PARTITION pCentral VALUES IN (7,8,15,16)
)；
```
>可以使用查询“ALTER TABLE employees DROP PARTITION pWest；高效
>要点：如果试图插入列值不在分区值列表中的一行时，那么“INSERT”查询将失败并报错。例如，假定LIST分区的采用上面的方案，下面的插入将失败：
>INSERT INTO employees VALUES(224, 'Linus', 'Torvalds', '2002-05-01', '2004-10-12', 42, 21);
>这是因为“store_id”列值21不能在用于定义分区pNorth, pEast, pWest,或pCentral的值列表中找到。要重点注意的是，LIST分区没有类似如“VALUES LESS THAN MAXVALUE”这样的包含其他值在内的定义。将要匹配的任何值都必须在值列表中找到。

- 3.HASH分区
```sql
mysql> create table t_hash( a int(11), b datetime) partition by hash(year(b)) partitions 4;
```
- 4.key分区
- 5.columns分区
	- mysql-5.5开始支持COLUMNS分区，可视为RANGE和LIST分区的进化，COLUMNS分区可以直接使用非整形数据进行分区。 COLUMNS分区支持以下数据类型：
```	
所有整形，如INT SMALLINT TINYINT BIGINT。FLOAT和DECIMAL则不支持。
日期类型，如DATE和DATETIME。其余日期类型不支持。
字符串类型，如CHAR、VARCHAR、BINARY和VARBINARY。BLOB和TEXT类型不支持。
COLUMNS可以使用多个列进行分区。
```

## 9.mysql分表和分区有什么区别
### 9.1实现方式上
- （a）  mysql的分表是真正的分表，一张表分成很多表后，每一个小表都是完整的一张表，都对应三个文件，一个.MYD数据文件，.MYI索引文件，.frm表结构文件。
- （b）  分区不一样，一张大表进行分区后，他还是一张表，不会变成二张表，但是他存放数据的区块变多了

### 9.2数据处理上
- （a）分表后，数据都是存放在分表里，总表只是一个外壳，存取数据发生在一个一个的分表里面。

- （b）分区呢，不存在分表的概念，分区只不过把存放数据的文件分成了许多小块，分区后的表呢，还是一张表，数据处理还是由自己来完成。

### 9.3提高性能上
- （a）分表后，单表的并发能力提高了，磁盘I/O性能也提高了。并发能力为什么提高了呢，因为查寻一次所花的时间变短了，如果出现高并发的话，总表可以根据不同的查询，将并发压力分到不同的小表里面。

- （b）mysql提出了分区的概念，主要是想突破磁盘I/O瓶颈，想提高磁盘的读写能力，来增加mysql性能。
在这一点上，分区和分表的测重点不同，分表重点是存取数据时，如何提高mysql并发能力上；而分区呢，如何突破磁盘的读写能力，从而达到提高mysql性能的目的。

### 9.4实现的难易度上
- （a）分表的方法有很多，用merge来分表，是最简单的一种方式。这种方式跟分区难易度差不多，并且对程序代码来说可以做到透明的。如果是用其他分表方式就比分区麻烦了。

- （b）分区实现是比较简单的，建立分区表，根建平常的表没什么区别，并且对代码端来说是透明的。

## 10.mysql分表和分区有什么联系
1. 都能提高mysql的性能，在高并发状态下都有一个良好的表现。
2. 分表和分区不矛盾，可以相互配合的，对于那些大访问量，并且表数据比较多的表，我们可以采取分表和分区结合的方式，访问量不大，但是表数据很多的表，我们可以采取分区的方式等。
3. 分表技术是比较麻烦的，需要手动去创建子表，app服务端读写时候需要计算子表名。采用merge好一些，但也要创建子表和配置子表间的union关系。
4. 表分区相对于分表，操作方便，不需要创建子表。

refer to:<https://www.jianshu.com/p/f0c0d73cf6e6>
refer to:<https://blog.csdn.net/kangshuo2471781030/article/details/79316737>