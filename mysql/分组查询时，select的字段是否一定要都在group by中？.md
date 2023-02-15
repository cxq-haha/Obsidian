# 分组查询时，select的字段是否一定要都在group by中?


分组查询关键字group by通常和集合函数（MAX、MIN、COUNT、SUM、AVG）一起使用，它可以对一列或者多列结果集进行分组。例如要统计超市水果的种类，需要用count函数，要统计哪个水果价格最高，要用MAX（）函数。

一般情况下，我们在使用group by的时候，select中的列都要出现在group by中，比如select id,name,age from tuser group by id,name,age，那么我们是不是都要严格按照这种模式来写sql呢？下面我们来一起探索下。

数据准备

创建一张学生表

```js
CREATE TABLE `student1` (
  `id` int(11) NOT NULL COMMENT '学号',
  `name` varchar(60) NOT NULL COMMENT '姓名',
  `birth` date NOT NULL COMMENT '出生日期',
  `sex` varchar(1) DEFAULT NULL,
  `age` int(11) NOT NULL,
  `score` int(11) NOT NULL,
  PRIMARY KEY (`id`)
)
```


插入数据

```js
insert into student values(1,'Tom','1998-10-01','男',23,96),(2,'Jim','1997-07-04','男',24,95),(3,'Lily','1999-11-12','女',21,99),(4,'Lilei','1996-09-21','男',25,90),(5,'Lucy','1999-12-02','女',21,93),(6,'Jack','1988-04-27','男',32,89),(7,'Liam','1991-09-08',' 男',28,100);
```


数据展示

```js
mysql> select * from student;
+----+-------+------------+------+-----+-------+
| id | name  | birth      | sex  | age | score |
+----+-------+------------+------+-----+-------+
|  1 | Tom   | 1998-10-01 | 男   |  23 |    96 |
|  2 | Jim   | 1997-07-04 | 男   |  24 |    95 |
|  3 | Lily  | 1999-11-12 | 女   |  21 |    99 |
|  4 | Lilei | 1996-09-21 | 男   |  25 |    90 |
|  5 | Lucy  | 1999-12-02 | 女   |  21 |    93 |
|  6 | Jack  | 1988-04-27 | 男   |  32 |    89 |
|  7 | Liam  | 1991-09-08 | 男   |  28 |   100 |
+----+-------+------------+------+-----+-------+
7 rows in set (0.00 sec)
```


测试验证

1. select中的列都出现在group by中，通过下面的结果可以看出是可以正常执行的。

```js

mysql> select id,name,score from student where score >95  group by id,name,score;
+----+------+-------+
| id | name | score |
+----+------+-------+
|  1 | Tom  |    96 |
|  3 | Lily |    99 |
|  7 | Liam |   100 |
+----+------+-------+
3 rows in set (0.01 sec)
```


2. group by中只保留score或者name

```js
mysql> select id,name,score from student where score >95  group by score;
ERROR 1055 (42000): Expression #1 of 
SELECT list is not in GROUP BY clause 
and contains nonaggregated column 
'test.student.id' which is not functionally 
dependent on columns in GROUP BY clause; 
this is incompatible with sql_mode=only_full_group_by
```


3. group by中只保留id

```js
mysql> select id,name,score from student where score >95  group by id;
+----+------+-------+
| id | name | score |
+----+------+-------+
|  1 | Tom  |    96 |
|  3 | Lily |    99 |
|  7 | Liam |   100 |
+----+------+-------+
3 rows in set (0.00 sec)
```


通过这个实验可以看出group by中只保留id是可以正常执行的，为什么？id字段有什么特殊性呢？

通过表结构可以看出id字段是主键，查询官方文档，有针对主键列的解释。

**SELECT name, address, MAX(age) FROM t GROUP BY name;** The query is valid if name is a primary key of t or is a unique NOT NULL column. In such cases,[MySQL](https://cloud.tencent.com/product/cdb?from=10680) recognizes that the selected column is functionally dependent on a grouping column. Forexample, if name is a primary key, its value determines the value of address because each group has only one value of the primary key and thus only one row. As a result, there is no randomness in the choice of address value in a group and no need to reject the query.

The query is invalid if name is not a primary key of t or a unique NOT NULL column.

大致的意思是：如果name列是主键或者是唯一的非空列，name上面的查询是有效的。这种情况下，MySQL能够识别出select中的列依赖于group by中的列。比如说，如果name是主键，它的值就决定了address的值，因为每个组只有一个主键值，分组中的每一行都具有唯一性，因此也不需要拒绝这个查询。

4. 验证唯一非空索引

```js
alter table student add unique(name);
mysql> select id,name,score from student where score >95  group by name;
+----+------+-------+
| id | name | score |
+----+------+-------+
|  7 | Liam |   100 |
|  3 | Lily |    99 |
|  1 | Tom  |    96 |
+----+------+-------+
3 rows in set (0.00 sec)
```

对于有唯一性约束的字段，也可以不用在group by中把select中的字段全部列出来。不过针对主键或者唯一性字段进行分组查询意义并不是很大，因为他们的每一行都是唯一的。

ONLY_FULL_GROUP_BY

我们在上面提到select中的列都出现在group by中，其实在MySQL5.7.5之前是没有此类限制的，5.7.5版本在sql_mode中增加了ONLY_FULL_GROUP_BY参数，用来开启或者关闭针对group by的限制。下面我们在分别开启和关闭ONLY_FULL_GROUP_BY限制的情况下分别进行验证。

1. 我们先查询下sql_mode

```js
mysql> select @@sql_mode;
+-------------------------------------------------------------------------------------------------------------------------------------------+| @@sql_mode                                                                                                                                
|+-------------------------------------------------------------------------------------------------------------------------------------------+
| ONLY_FULL_GROUP_BY,STRICT_TRANS_TABLES,
NO_ZERO_IN_DATE,NO_ZERO_DATE,
ERROR_FOR_DIVISION_BY_ZERO,
NO_AUTO_CREATE_USER,
NO_ENGINE_SUBSTITUTION |
+-------------------------------------------------------------------------------------------------------------------------------------------++
1 row in set (0.00 sec)
```

2. sql_mode动态去除ONLY_FULL_GROUP_BY限制

```js
mysql> SET @@sql_mode = sys.list_drop(@@sql_mode, 'ONLY_FULL_GROUP_BY');
Query OK, 0 rows affected (0.05 sec)
```


再次执行分组查询

```js
mysql> select id,name,score from student where score >95  group by score;
+----+------+-------+
| id | name | score |
+----+------+-------+
|  1 | Tom  |    96 |
|  3 | Lily |    99 |
|  7 | Liam |   100 |
+----+------+-------+
3 rows in set (0.00 sec)
```

3. sql_mode动态增加ONLY_FULL_GROUP_BY限制SET

```javascript
SET @@sql_mode = sys.list_add(@@sql_mode, 'ONLY_FULL_GROUP_BY');
```

再次执行分组查询

```js
mysql> select id,name,score from student where score >95  group by score;
ERROR 1055 (42000): Expression #1 of 
SELECT list is not in GROUP BY clause 
and contains nonaggregated column 
'test.student.id' which is not functionally 
dependent on columns in GROUP BY clause; 
this is incompatible with sql_mode=only_full_group_by。
```