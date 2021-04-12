---
title: "MySQL乐观锁电商库存并发问题应用"
date: 2020-11-19
tags: [Mysql]
categories: [Mysql锁]
draft: true
---
# MySQL乐观锁电商库存并发问题应用
## 下单涉及的一些操作

>1.下单
2.下单同时预占库存
3.支付
4.支付成功真正减扣库存
5.取消订单
>6.回退预占库存

## 方案选择：
- 商品加入购物车后，选择下单，这个时候去预占库存。用户选择去支付说明了，用户购买欲望强烈的。订单也有一个时效，例如半个小时。超过半个小时后，系统自动取消订单，回退预占库存。

## 可进行操作：
- 1.UI拦截，点击后按钮置灰，不能继续点击，防止用户，连续点击造成的重复下单。
- 2.下单前获取一个下单的唯一token，下单的时候需要这个token。后台系统校验这个 token是否有效，才继续进行下单操作。
- 3.进来的时候 先 get 库存数量是否充足，再执行 increment。以 increment > 0 为准。
- 4.因为检查库存 与 减少库存 不是原子性的，而redis.increment 是个原子操作，可以此为准。
redisService.increment(key, -req.getNum().longValue()) >= 0 说明库存充足，可以下单。
redisService.increment(key, -req.getNum().longValue()) < 0 的时候 不能下单，次数库存不足。并且需要 回加刚刚减去的库存数量，否则会导致刚才减扣的数量 一直买不出去。数据库与缓存的库存不一致。
- 5.都满足进行真正的扣减库存
- 5.1生成订单
- 5.2订单超过时效，订单取消等，订单取消，库存回滚。



# MySQL乐观锁电商库存并发问题应用
## 1.使用数据版本(Version)记录机制实现，这是乐观锁最常用的一种实现方式。
```sql
mysql> select * from t_goods;  
+----+--------+------+---------+  
| id | status | name | version |  
+----+--------+------+---------+  
|  1 |      1 | 道具 |       1 |  
|  2 |      2 | 装备 |       2 |  
+----+--------+------+---------+  
2 rows in set 

```
何谓数据版本？即为数据增加一个版本标识，一般是通过为数据库表增加一个数字类型的 “version” 字段来实现。当读取数据时，将version字段的值一同读出，数据每更新一次，对此version值加一。当提交更新的时候，判断数据库表对应记录的当前版本信息与第一次取出来的version值进行比对，如果数据库表当前版本号与第一次取出来的version值相等，则予以更新，否则认为是过期数据。


- 1.1.查询出商品信息：
```sql
select name,status,version from goods where id=#{id}
```
- 1.2.根据商品信息生成订单
- 1.3.修改商品status为2：
```sql
update goods set status=2,version=version+1
where id=#{id} and version=#{version};
```

## 2.使用条件限制实现乐观锁
```sql
mysql> select * from t_goods;  
+----+--------+------+---------+  
| id | status | name | quantity|  
+----+--------+------+---------+  
|  1 |      1 | 道具 |      10 |  
|  2 |      2 | 装备 |      10 |  
+----+--------+------+---------+  
rows in set 
```
> status表示产品状态：1-在售；2-暂停出售。quantity表示产品库存。

- 这个适用于只更新时做数据安全校验，适合库存模型，扣份额和回滚份额，性能更高。
```sql
update goods
set quantity = quantity- #{buyQuantity} 
where id = #{id} 
AND quantity - #{buyQuantity} >= 0 
AND status = 1
```

- 注意：乐观锁的更新操作，最好用主键或者唯一索引来更新，这样是行锁，否则更新时会锁表。


