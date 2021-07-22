##### mysql优化的一般步骤
1. sql优化
开启慢查询日志 ->分析sql语句->分析是否需要加上索引->分析索引是否起效
2. 数据库表结构优化
三范式
    - 第一范式：确保表中每列的原子性，即不可拆分
    > 电话：应该拆分成 座机、手机
    - 第二范式：确保表中每列与主键相关，而不能只与主键的某部分相关（主要针对联合主键），主键列与非主键列遵循完全函数依赖关系，即完全依赖
    > 订单：订单信息和商品信息应该分开两张表
    - 第三范式：确保主键列之间没有传递函数依赖关系，即不存在传递依赖
    > 所有主键必须直接依赖于主键，而不能通过传递依赖
3. 系统配置
4. 优化计算机硬件

##### mysql事务的理解
ACID
原子性、稳定性、隔离性、可靠性


##### mysql触发器是什么
监视某种事件并触发某种操作（如商品的添加、订单的删除等等需要连贯进行的操作）
###### 触发四要素
- 监视地点 table
- 触发时间 after/before
- 监视事件 insert/update/delete
- 触发事件 insert/update/delete

```sql
-- 创建一个名为tg的触发器
-- 当向t1表插入数据前，就向a表中插入一条数据
mysql> create trigger tg before insert on t1 for each row # 固定写法
-> begin
-> insert int a values(4);
-> end//
```

##### 什么是组合索引？
将两个字段共同添加一条索引
根据要加索引的字段辨识度来
需要加索引的字段，要在where条件中
如果where条件中是OR关系，加索引不起作用
符合最左原则

创建索引
在执行CREATE TABLE语句时可以创建索引，也可以单独用CREATE INDEX或ALTER TABLE来为表增加索引。
```sql
-- 普通索引
ALTER TABLE table_name ADD INDEX index_name (column_list)
-- 唯一索引
ALTER TABLE table_name ADD UNIQUE (column_list)
-- 主键索引
ALTER TABLE table_name ADD PRIMARY KEY (column_list)

CREATE INDEX index_name ON table_name (column_list)
CREATE UNIQUE INDEX index_name ON table_name (column_list)
-- 不能用CREATE INDEX语句创建PRIMARY KEY索引。
```
索引不是越多越好的，可能会浪费资源。
索引占用资源，会影响插入性能。

##### mysql分区
- RANGE分区：基于属于一个给定连续区间的列值，把多行分配给分区
- LIST分区：类似于按RANGE分区，区别在于LIST分区是基于列值匹配一个离散值集合中的某个值来进行选取。
- HASH分区：基于用户定义的表达式的返回值来进行选择的分区，该表达式使用将要插入到表中的这些行的列值进行计算。

**如果表存在主键或者唯一索引时，分区列必须是唯一索引的一个组成部分。实战十有八九都是用RANGE分区。**
RANGE分区主要用于日期列的分区，比如交易表啊，销售表啊等。可以根据年月来存放数据。
```sql
CREATE TABLE `m_test_db`.`Order` (
  `id` INT NOT NULL AUTO_INCREMENT,
  `partition_key` INT NOT NULL,
  `amt` DECIMAL(5) NULL,
  PRIMARY KEY (`id`, `partition_key`)) 
  PARTITION BY RANGE(partition_key) PARTITIONS 5( 
      PARTITION part0 VALUES LESS THAN (201901),  
      PARTITION part1 VALUES LESS THAN (201902),  
      PARTITION part2 VALUES LESS THAN (201903),  
      PARTITION part3 VALUES LESS THAN (201904),  
      PARTITION part4 VALUES LESS THAN (201905)
    ) ;
-- 　　这时候我们先插入一些数据
INSERT INTO `m_test_db`.`Order` (`id`, `partition_key`, `amt`) VALUES ('1', '201901', '1000');
INSERT INTO `m_test_db`.`Order` (`id`, `partition_key`, `amt`) VALUES ('2', '201902', '800');
INSERT INTO `m_test_db`.`Order` (`id`, `partition_key`, `amt`) VALUES ('3', '201903', '1200');
```

分区也是一样，不是启动了分区数据库就会运行的更快，分区可能会给某些sql语句性能提高，**但是分区主要用于数据库高可用性的管理。** 数据库应用分为2类，一类是OLTP（在线事务处理），一类是OLAP（在线分析处理）。
##### MyISAM 和InnoDB的区别
||MyISAM|InnoDB|
|-|-|-|
|事务|×|√|
|表级锁|√|√|
|行级锁|×|√|
|外键|×|√|
|全文类型索引|√|×|
|表的总行数|√|×|

