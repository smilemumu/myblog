---
title: "SQL语法"
date: 2019-11-29
tags: [SQL]
categories: [SQL]
draft: true
---
# Mysql
- Mysql分页
```
select * from stu limit m, n; //m = (startPage-1)*pageSize,n = pageSize
```
第一个参数值m表示起始行，第二个参数表示取多少行（页面大小）
mysql从0开始计数行数
# Oracle
- Oralce分页
```
select * from (
select rownum rn,a.* from table_name a where rownum <= x
//结束行，x = startPage*pageSize
)
where rn >= y; //起始行，y = (startPage-1)*pageSize+1
```

- 创建索引
```
create index index_name(idx_****) on table table_name(column1_name,column2_name);
```
- oracle只查第一条数据
```sql
select * from (select * from <table> order by <key> desc) where rownum=1;
```
- oracle触发器
```sql
create or replace trigger HF_20_G_TRIGGER
  before insert or update on HF_20_G_INFO
  for each row
begin
  select sysdate into :new.update_time from dual;
end;
```
- ORALE一条SQL插入多条记录
```
INSERT ALL
INTO HF_ACCOUNT_MANAGER_INFO(ID, NAME) VALUES ('1700601010','范磊')
INTO HF_ACCOUNT_MANAGER_INFO(ID, NAME) VALUES ('1700601011','吴凯')
SELECT 1 FROM DUAL;
```
  
- SQL多线程查询
```
例如存在sql:select * from table_name
4线程查询sql:select /*+ parallel(4)*/ * from table_name
```

- 4表联查
```
select * from a 
left inner join b on a.a=b.b 
right inner join c on a.a=c.c 
inner join d on a.a=d.d 
where conditions
```

- 查询相同条目并记录重复次数(大于2)
```
select a,count(a) from tab group by a having count(*) > 2
```

- 根据已有表创建表
```
create table ntab like otab;(使用旧表B创建新表A)  (MySQL)
```
>此种方式在将表B复制到A时候，会将表B完整的字段结构和索引复制到表A中来。但不会复制数据。

- 增加一个列
```
alter table 表名 ADD 字段名称 字段类型(字段长短-选填)  
NOT NULL[是否为null] default 0 comment '字段备注';
```

- 添加主键/删除主键
```
alter table tab add primary key(col);//添加主键
alter table tab drop primary key(col);//删除主键 
```

- 创建索引/删除索引索引是不可以更改的，想更改必须删除重新建。
```
//创建索引
create [unique] index idxname on tab(col...);
//删除索引
drop index idxname;
drop index idxname on table;
```

- 其他系统数据查询
```
select * from dba_blockers;查询锁
select * from dba_waiters;查询被阻塞的会话
select column_name from tab_old intersect select column_name from tab_new;显示两个表内的相同数据
select @@version; 或 select version();查看数据库版本
select database();查看当前数据库
select user();查看当前用户
show tables;查看所有表
show columns from table;查看表中的列的基本信息
desc table; 或 describe table; 表名后面加字段名，查看该字段的基本信息
select CHARACTER_LENGTH(col) from table;查询该字段值的长度
```
- 数据长度
	- 对于字符类型，MySQL 具有 CHAR 和 VARCHAR，最大长度允许为65535字节(CHAR 最多可以为255字节，VARCHAR 为65535字节)。

- Oracle模糊查询like拼接
```SQL
select * from where column like '%'||'xxx';
```

- 修改列名
```
alter table 表名 rename column 现列名 to 新列名;
```

- 删除列
```
alter table A drop column 列名称；
```

- 修改表名
```
alter table 表名 rename to 新表名
```

- 新增字段
```
alter table 表名 add (字段名 字段类型 默认值 是否为空);
例如：
alter table sf_users add (userName varchar2(30) default '空' not null);
```

- 查看表结构
```
select * from user_tab_columns where table_name = '表名';
```
- 查看表注释

```
select * from user_col_comments where table_name = '表名';
```