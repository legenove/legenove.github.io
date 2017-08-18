---
title: 数据库SQL基础－Postgresql版
date: 2017-08-18
categories:
  - 技术
tags:
  - 数据库
  - postgresql
---

# 数据库SQL基础-Postgresql版

## 一、创建&删除
### 1、数据库
* 创建
`CREATE DATABASE 数据库名;`

* 删除
`DROP DATABASE 数据库名;`

### 2、表
* 创建
```sql
create table 表名 
(
    列名称1 数据类型,
    列名称2 数据类型,
);
```

*例子:*

```sql
CREATE TABLE account 
(
    id serial PRIMARY KEY ,
    name VARCHAR NOT NULL ,
    gender CHAR NOT NULL DEFAULT '0', -- 0 表示未知性别  1 表示女性 2 表示男性
    mobile CHAR(11),
    is_return BOOLEAN NOT NULL DEFAULT False, 
    date_created TIMESTAMP(0) NOT NULL DEFAULT CURRENT_TIMESTAMP
);
```

* 删除
`DROP TABLE 表名;`


### 3、常用数据类型:

#### 1. 数值类型

名字 | 描述|范围 	
----|------|----
smallint | 小范围整数  | -32768 到 +32767
integer | 常用的整数  | -2147483648 到 +2147483647
bigint | 大范围的整数	 | -9223372036854775808 到 9223372036854775807
decimal/numeric | 用户定义精度，可以精确地表示小数  | 无限制
real | 精度可变，不能精确地表示小数 | 精度是6个十进制位
double precision | 精度可变，不能精确地表示小数 | 精度是15个十进制位
serial | 小范围的自增整数 | 大范围的自增整数
bigserial | 大范围的自增整数 | 1 到 9223372036854775807



1.整数类型

类型smallint、integer和bigint存储各种范围的全部是数字的数，也就是没有小数部分的数字。试图存储超出范围以外的数值将导致一个错误。常用的类型是integer，因为它提供了在范围、存储空间和性能之间的最佳平衡。一般只有在磁盘空间紧张的时候才使用smallint。而只有在integer的范围不够的时候才使用bigint，因为前者(integer)绝对快得多。

2.任意精度数值
类型numeric可以存储最多1000位精度的数字并且准确地进行计算。因此非常适合用于货币金额和其它要求计算准确的数量。不过，numeric类型上的算术运算比整数类型或者浮点数类型要慢很多。

3.浮点数类型
数据类型real和double是不准确的、牺牲精度的数字类型。不准确意味着一些数值不能准确地转换成内部格式并且是以近似的形式存储的，因此存储后再把数据打印出来可能显示一些缺失。

4.自增整数
serial和bigserial类型不是真正的类型，只是为在表中设置唯一标识做的概念上的便利。


#### 2. 字符类型

名字 | 描述
----|----
varchar(n) | 变长，有长度限制
char(n) | 定长,不足补空白
text | 变长，无长度限制

SQL 定义了两种基本的字符类型，varchar(n)和char(n)，这里的n是一个正整数。两种类型都可以存储最多n个字符长的字串，试图存储更长的字串到这些类型的字段里会产生一个错误，除非超出长度的字符都是空白，这种情况下该字串将被截断为最大长度。如果没有长度声明，char等于char(1)，而varchar则可以接受任何长度的字串。

#### 3. 日期/时间类型

名字 | 描述 
----|----
timestamp[无时区] | 包括日期和时间
timestamp[含时区] | 日期和时间，带时区
interval | 时间间隔
date | 只用于日期
time[无时区] | 只用于一日内时间	

#### 4. 布尔类型

boolean数据类型。boolean只能有两个状态之一：真(True)或 假(False)。

*例子:*

```sql
CREATE TABLE date_type 
(
    -- 数字类型
    id serial PRIMARY key,
    col_int INTEGER DEFAULT 0,
    col_float DECIMAL DEFAULT 0.0,
    -- 字符类型
    col_char CHAR(10),
    col_varchar VARCHAR(10),
    col_text text, 
    -- 日期类型
    col_date DATE DEFAULT CURRENT_DATE ,
    col_time TIME DEFAULT CURRENT_TIME ,
    col_timestamp TIMESTAMP(0) DEFAULT CURRENT_TIMESTAMP ,
    col_timestamp_with_timezone TIMESTAMP(0) WITH TIME zone DEFAULT CURRENT_TIMESTAMP ,
    -- bool类型
    col_bool BOOLEAN DEFAULT FALSE 
);
```

### 4、表的修改

1. 增加字段（一列）
     `ALTER TABLE 表名 ADD COLUMN 列名 数据类型;`

2. 删除字段（一列）
     `ALTER TABLE 表名 DROP COLUMN 列名;`

3. 更改字段（列）的数据类型
    `ALTER TABLE 表名 ALTER COLUMN  列名  TYPE 数据类型;`

4. 给列添加|删除default
    `ALTER TABLE 表名  ALTER COLUMN 列名 SET DEFAULT 7.77`

5. 表的重命名
    `ALTER TABLE 表名 RENAME TO 新表名;`

6. 更改字段名
    `ALTER TABLE 表名 RENAME 列名 TO  新列名;`

7. 更改约束
    `ALTER TABLE 表名 ADD CONSTRAINT 约束名 UNIQUE (列名(可以多个));`
    `ALTER TABLE 表名 ALTER COLUMN 列名 SET NOT NULL`

*例子:*

```sql
-- 增加
ALTER TABLE account add COLUMN test CHAR(11) ;

ALTER TABLE account add COLUMN test1 INTEGER ;

-- 删除
ALTER TABLE account DROP COLUMN test;

-- 重命名
ALTER TABLE account rename COLUMN test1 TO department ;

-- 修改字段类型
ALTER TABLE account ALTER COLUMN department type VARCHAR;

-- 给字段增加非空约束
ALTER TABLE account_score add CONSTRAINT account_score_account_id UNIQUE (account_id);
```

### 5、表的约束

1. NOT NULL 约束（非空约束）
    默认情况下，列可以保存NULL值。如果不希望一列具有NULL值，那么需要在此列指定NULL约束定义。 NOT NULL约束总是写成一列约束。
    NULL并不相同，因为没有数据，而是它代表着未知的数据。

2. UNIQUE 约束 （唯一约束）
    唯一约束防止两个记录在一个特定的列具有相同的值。可以为一个，也可以为多个

3. PRIMARY KEY 约束（主键约束）
    数据库表中的PRIMARY KEY约束唯一标识每条记录。可以有多个UNIQUE的列，但在一个表中只有一个主键。在设计数据库表的主键是重要的。主键是唯一的ID。

4. FOREIGN KEY 约束（外键约束）
    外键约束指定一列（或一组列）中的值的值必须符合另一个表中出现的一些行。我们说这是维持两个相关的表之间的引用完整性。它们被称为外键，因为约束是外部的，也就是说表的外部。有时也被称为外键引用的关键。

5. 其他约束
    CHECK 约束（检查约束）
    EXCLUSION 约束（扩展约束）
    删除约束

*例子:*

```sql
-- 主键约束，唯一约束
CREATE TABLE study_group 
(
    id serial PRIMARY KEY ,
    name CHAR(30),
    summary text,
    leader_id INTEGER UNIQUE 
);

-- 多个字段组合成唯一约束
CREATE TABLE follow 
(
    follower_id INTEGER NOT NULL , 
    following_id INTEGER NOT NULL , 
    date_created TIMESTAMP(0) NOT NULL DEFAULT CURRENT_TIMESTAMP ,
    CONSTRAINT uk_follow_follower_id_following_id UNIQUE (follower_id,following_id)
);

-- 创建外键约束
CREATE TABLE account_score (
    account_id INTEGER NOT NULL ,
    score serial ,
    FOREIGN KEY (account_id) REFERENCES account(id)
);

-- 增加外键约束
ALTER TABLE account add  FOREIGN KEY (group_id) REFERENCES study_group;

```

## 二、数据的增删改

### 1、插入数据

按照列的顺序插入：
`INSERT INTO 表名称 VALUES (值1, 值2,....)`
我们也可以指定所要插入数据的列：
`INSERT INTO table_name (列1, 列2,...) VALUES (值1, 值2,....)`

* 注意

1. 自增唯一的字段，尽量不要给赋值
2. 默认值如果不需要赋值的话，不需要给赋值
3. 如果赋值，需要注意字段类型和格式

*例子:*

```sql
-- 按顺序插入
INSERT INTO follow VALUES (1,3);

-- 指定字段插入一组数据
INSERT INTO account (name, mobile) VALUES ('haha', '12345677890');

-- 指定字段插入多组数据
INSERT INTO account (name, mobile) VALUES ('haha1', '12345677890'),
('haha2', '123234325');
```

### 2、修改数据

修改全部值（***_!!!慎用_***）
`UPDATE 表名称 SET 列名称 = 新值`

修改某个条件下的值
`UPDATE 表名称 SET 列名称 = 新值 WHERE 列名称 = 某值`

*例子:*

```sql
UPDATE account SET name = '1345' WHERE id = 1
```

### 3、删除数据

删除全部的行（***_!!!慎用_***）
`DELETE FROM 表名称`

删除某一个条件下的行
`DELETE FROM 表名称 WHERE 列名称 = 值`

*例子:*

```sql
DELETE FROM account WHERE id = 2;
```


## 三、数据的查询
语法:
1. 全部查询
	`SELECT * FROM 表名`

2. 单列查询
	`SELECT 列名 FROM 表名`

3. 多列查询
	`SELECT 列名1, 列名2 FROM 表名`

4. 带条件查询
	`SELECT * FROM 表名 WHERE 列1=值1 AND 列2>值2 OR 列3 NOT IN (值1, 值2) AND  列4 LIKE 'abc%' ;`

5. 分组排序汇总查询
	以列1分组，并汇总、排序
	`SELECT 列1, SUM(列2) FROM 表名 WHERE 列3 ＝ 值 GROUP BY 列1 HAVING SUM(列2) > 10 ORDER BY SUM(列2) DESC;`

6. 限制条目查询
	获取第20条开始的10条数据
	`SELECT * FROM 表名 ORDER BY 列1 LIMIT 10 OFFSET 20;`

### 1、唯一值查询
语法
`SELECT DISTINCT 列名称 FROM 表名称`

### 2、数据汇总函数
语法
`SELECT function(列) FROM 表`

常用function

函数 | 描述 
----|----
COUNT(column) | 返回某列的行数（不包括 NULL 值）
COUNT(*) | 返回被选行数
AVG(column) | 返回某列的平均值
MAX(column) | 返回某列的最高值
MIN(column) | 返回某列的最低值
SUM(column) | 返回某列的总和

### 3、条件语句where

* 连接词: and or in
* 模糊查找: like
* 操作符: in, between
* 运算符号: >, <, =, <>, >=, <=

*例子:*

```sql
SELECT * FROM Persons WHERE FirstName='Thomas' AND LastName='Carter'
```

### 4、分组

关键字: group by
GROUP BY 语句用于结合合计函数，根据一个或多个列对结果集进行分组。

*例子:*

```sql
SELECT study_group.name, count(account.id) FROM account 
RIGHT JOIN study_group ON study_group.id = account.group_id
GROUP BY study_group.id;
```

### 5、排序语句

关键字: order by 
倒叙排列: desc

ORDER BY 语句用于根据指定的列对结果集进行排序。
ORDER BY 语句默认按照升序对记录进行排序。
如果您希望按照降序对记录进行排序，可以使用 DESC 关键字。

*例子:*


```sql
-- 按照id 倒叙排列
select * from account order by id desc;
```

### 6、限制条目

关键字: limit, offset

limit是限制条目
offset是偏移量

*例子:*

```sql
-- 按照id正序排列 从第11个位置开始 限制10个条目
selece * from account order by id limit 10 offset 10
```

### 7、多表查询

#### 传统连接
连接就是将多个表中的数据连接到一起的查询，即连接操作可以在一个Select语句中完成从多个表中查找和处理数据，使用连接时可以使用名字相同的不同表的列，也可以不同，但要求连接的列不需可连接，即数据类型相同。

`SELECT * FROM 表名1 T1,表名2 T2 WHERE T1.column=T2.column;`

*例子:*

```sql
SELECT follower.name, following.name, follow.date_created 
FROM follow, account AS follower,account AS following  
WHERE follower.id =follow.follower_id AND following.id=follow.following_id;
```

#### 连表查询

1. Union 联合查询
    UNION 操作符用于合并两个或多个 SELECT 语句的结果集。
 
2. INNER JOIN（内连接）
    (INNER) JOIN（内连接），也成为自然连接，根据两个或多个表中的列之间的关系，从这些表中查询数据。

3. 外连接
    与内连接相比，即使没有匹配行，也会返回一个表的全集。
    1）LEFT OUTER JOIN，简称LEFT JOIN，左外连接（左连接）
    2）RIGHT JOIN(right outer join)右外连接(右连接)

4. CROSS JOIN（交叉连接）
    交叉连接。交叉连接返回左表中的所有行，左表中的每一行与右表中的所有行组合。


*例子:*

```sql
-- UNION
SELECT id,name FROM account
UNION
SELECT id,name FROM study_group;

-- 内链接
SELECT account.id, account.name, account.group_id, study_group.name FROM account INNER JOIN study_group ON account.group_id = study_group.id;

-- 左外链接
SELECT account.id, account.name, account.group_id, study_group.name FROM account LEFT JOIN study_group ON account.group_id = study_group.id;

-- 右外连接
SELECT account.id, account.name, account.group_id, study_group.name FROM account RIGHT JOIN study_group ON account.group_id = study_group.id;

-- 交叉连接
SELECT account.id, account.name, study_group.name FROM account CROSS JOIN study_group;
```

#### 子查询

当一个查询是另一个查询的条件时，称之为子查询。

*例子:*

```sql
SELECT * FROM account WHERE id IN (SELECT follow.follower_id FROM follow);
```

