---
title: 基于postgresql和postgis纪录地理位置，计算距离
date: 2017-08-08
categories:
  - 技术
tags:
  - 数据库
  - postgresql
---

## 基于postgresql和postgis纪录地理位置，计算距离

> PostGIS是PostgreSQL关系数据库的空间数据库扩展。增加了对地理类型的支持，允许在SQL中运行位置查询。
> postgis安装这里就不在赘述，[官网](http://postgis.net/install/)有很详细的说明，这里主要是讲postgis地理对象的应用。

### 创建postgis插件

在数据库里输入
```
CREATE EXTENSION postgis;
CREATE EXTENSION postgis_topology;
```

查看结果：
```
test=# \dx
                                         List of installed extensions
       Name       | Version |   Schema   |                             Description
------------------+---------+------------+---------------------------------------------------------------------
 plpgsql          | 1.0     | pg_catalog | PL/pgSQL procedural language
 postgis          | 2.2.2   | public     | PostGIS geometry, geography, and raster spatial types and functions
 postgis_topology | 2.2.2   | topology   | PostGIS topology spatial types and functions
(3 rows)
```

### 在数据中插入geography类型

在表中添加geography类型字段:
`alter table story add where_is GEOGRAPHY;`

查看表结构

```
test=# \d test
                                   Table "public.test"
    Column     |           Type           |                   Modifiers
---------------+--------------------------+-----------------------------------------------
 id            | character varying(32)    | not null
 where_is      | geography                |
 latitude      | double precision         |
 longitude     | double precision         |
```
> 其中 latitude, longitude 分别存入经纬度字段，where_is是geography类型，会根据经纬度，生成point数据

### 插入数据

`INSERT INTO test VALUES ('123456',NULL, 39.123124, 116.324124);`

然后更新where_is的值

`UPDATE test SET where_is = ST_POINT(latitude, longitude) where id = '123456';`

查询结果为：

```
test=# SELECT * FROM test;

    id    |                      where_is                      | latitude  | longitude
----------+----------------------------------------------------+-----------+------------
  123456  | 0101000020E610000025ADF886C28F43405E13D21A83D64F40 | 39.123124 | 116.324124
```

> where_is是我们不可读的WKB(Well Known Binary)这是在postgis中存储的二进制编码.
> 可以通过ST_AsText()来解读为WKT(Well Known Text)，成为我们可读的信息;

例如:
```
test=# select ST_AsText('010100000025ADF886C28F434051F69672BE145D40');

          st_astext
-----------------------------
 POINT(39.123124 116.324124)
(1 row)
```

### 计算距离

使用ST_DISTANCE()方法来计算距离，单位是米

```
test=# select ST_DISTANCE(test.where_is, ST_POINT(39, 113)) from test where id = '8036056820168814';

NOTICE:  Coordinate values were coerced into range [-180 -90, 180 90] for GEOGRAPHY
   st_distance
------------------
 370675.586395211
(1 row)
```



