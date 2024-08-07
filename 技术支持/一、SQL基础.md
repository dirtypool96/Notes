# 一、SQL基础学习

## 1.1 基础语句

### 1.1.1 创建表 CREATE TABLE

```sql
CREATE TABLE Persons  
(  
	Id_P int,  
	LastName varchar(255),  
	FirstName varchar(255),  
	Address varchar(255),  
	City varchar(255)  
);
```

常用数据类型：

| **数据类型**                                         | **描述**                                                     |
| ---------------------------------------------------- | ------------------------------------------------------------ |
| integer(size),int(size),smallint(size),tinyint(size) | 仅容纳整数、在括号内规定数字的最大位数                       |
| decimal(size,d),numeric(size,d)                      | 容纳带有小数的数字、“size” 规定数字的最大位数、“d” 规定小数点右侧的最大位数 |
| char(size)                                           | 容纳固定长度的字符串（可容纳字母、数字以及特殊字符）、在括号中规定字符串的长度 |
| varchar(size)                                        | 容纳可变长度的字符串（可容纳字母、数字以及特殊的字符）、在括号中规定字符串的最大长度 |
| date(yyyymmdd)                                       | 容纳日期                                                     |

### 1.1.2 插入 INSERT INTO

```sql
INSERT INTO Persons VALUES (1, 'Gates', 'Bill', 'Xuanwumen 10', 'Beijing');

INSERT INTO Persons (LastName, Address) VALUES ('Wilson', 'Champs-Elysees');  
```

插入成功后数据如下：

![img](./res/fb1f47e98797417265f2cfa9f35b1fe1.png)

### 1.1.3 查询 SELECT

#### 1.1.3.1 普通查询

```sql
SELECT * FROM Persons;
SELECT LastName,FirstName FROM Persons; 
```

![img](./res/a3e9d10889006f0f2e568c6e67617482.png)

![img](./res/5d90d9ce257cd8421dfdd138ab68f209.png)

#### 1.1.3.2 DISTINCT – 去除重复值

```sql
SELECT DISTINCT 列名称 FROM 表名称;
```

#### 1.1.3.3 **WHERE - 条件过滤**

```sql
SELECT * FROM Persons WHERE City='Beijing';
```

下面的运算符可在 WHERE 子句中使用：

| **操作符** | **描述**     |
| ---------- | ------------ |
| =          | 等于         |
| <> / !=    | 不等于       |
| >          | 大于         |
| <          | 小于         |
| >=         | 大于等于     |
| <=         | 小于等于     |
| BETWEEN    | 在某个范围内 |
| LIKE       | 搜索某种模式 |

#### 1.1.3.4 AND & OR - 运算符

```sql
SELECT * FROM Persons WHERE (FirstName='Thomas' OR FirstName='William') AND LastName='Carter';
```

#### 1.1.3.5 ORDER BY – 排序

```sql
SELECT * FROM Persons ORDER BY LASTNAME DESC;
-- 默认升序排序，加DESC降序
-- 空值（NULL）默认排序在有值行之后
-- 可按首字母排序
-- 在第一列中有相同的值时，第二列是以升序排列的。如果第一列中有些值为 null 时，情况也是这样的
```

### 1.1.4 UPDATE - 更新数据

**更新某一行中的若干列：**

```sql
UPDATE Persons SET ID_P = 6,city= 'London' WHERE LastName = 'Wilson';  
```

![img](./res/7c8e608a9e7362f1a154f587d52cd11b.png)

### 1.1.5 DELETE – 删除数据

#### 1.1.5.1 DELETE - 删除表中的行

```sql
-- 删除某行：
DELETE FROM Persons WHERE LastName = 'Wilson';
-- 删除所有行，不删除表的情况下删除所有的行。这意味着表的结构、属性和索引都是完整的
-- 逻辑删除，有binlog记录，效率较慢
DELETE FROM Persons;
```

#### 1.1.5.2 TRUNCATE – 清除表数据

```sql
-- 物理删除，该删除不能回滚，delete可以
-- 存在外键约束，则删除失败
TRUNCATE TABLE Persons; 
```

#### 1.1.5.3 DROP TABLE – 删除表

```sql
-- DROP TABLE 语句用于删除表（表的结构、属性以及索引也会被删除）
drop table persons;  
```

## 1.2 高级言语

### 1.2.1 LIKE - 查找类似值

```sql
-- 选取居住在以 “N” 开头的城市里的人
SELECT * FROM Persons WHERE City LIKE 'n%';
-- 选取居住在以 “g” 结尾的城市里的人
SELECT * FROM Persons WHERE City LIKE '%g';
-- 选取居住在包含 “lon” 的城市里的人
SELECT * FROM Persons WHERE City LIKE '%lon%';
-- 选取居住在不包含 “lon” 的城市里的人
SELECT * FROM Persons WHERE City NOT LIKE '%lon%'; 
```

### 1.2.2 IN – 锁定多个值

```sql
SELECT * FROM Persons WHERE LastName IN ('Adams','Carter');
```

### 1.2.3 BETWEEN – 选取区间数据

```sql
-- 查询以字母顺序显示介于 “Adams”（包括）和 “Carter”（不包括）之间的人
SELECT * FROM Persons WHERE LastName BETWEEN 'Adams' AND 'Carter';  
-- 查询上述结果相反的结果，可以使用 NOT
SELECT * FROM Persons WHERE LastName NOT BETWEEN 'Adams' AND 'Carter';
```

### 1.2.4 AS – 别名

```sql
-- 列别名：
SELECT 列名称 as 别名 FROM 表名称;
-- AS 可以省略，但是列别名需要加上 " "
SELECT LastName "Family", FirstName "Name" FROM Persons;
-- 表别名：
SELECT p.LastName, p.FirstName  
FROM Persons p   
WHERE p.LastName='Adams' AND p.FirstName='John';
```

### 1.2.5 JOIN – 多表关联

> 数据库中的表可通过键将彼此联系起来。主键（Primary Key）是一个列，在这个列中的每一行的值都是唯一的

```sql
select 列名  
from 表A  
INNER|LEFT|RIGHT|FULL JOIN 表B  
ON 表A主键列 = 表B外键列
WHERE 表A/B.xx = 'xx';
-- WHERE 等同于 AND 表A/B.xx = 'xx'
-- ON是生成的条件，WHERE是过滤，先ON后WHERE
```

- JOIN: 如果表中有至少一个匹配，则返回行
- INNER JOIN: 内部连接，返回两表中匹配的行
- LEFT JOIN: 即使右表中没有匹配，也从左表返回所有的行，右边没有匹配返补null，看下面eg.
- RIGHT JOIN: 即使左表中没有匹配，也从右表返回所有的行，以上同理
- FULL JOIN: 只要其中一个表中存在匹配，就返回行

eg.![img](./res/650ac04315084ee9bcdcdd4888cf2fbc.png)

<img src="./res/6f9c02a8fac74be74e0e817b5ceceb64.png" alt="img" style="zoom:67%;" />

### 1.2.6 UNION – 合并结果集

> - UNION 操作符默认为选取不同的值，如果查询结果需要显示重复的值，请使用 UNION ALL
>
> - UNION 结果集中的列名总是等于 UNION 中第一个 SELECT 语句中的列名
>
> - UNION 内部的 SELECT 语句必须拥有相同数量的列。列也必须拥有相似的数据类型。同时，每条 SELECT 语句中的列的顺序必须相同。

```sql
SELECT 列名 FROM 表A  
UNION
SELECT 列名 FROM 表B;
```

### 1.2.7 NOT NULL – 非空

`NOT NULL` 约束强制列不接受 NULL 值

```sql
CREATE TABLE 表  
(  
列 int NOT NULL  
);
-- NOT NULL 也可以用于查询条件：
select * from persons where FirstName is not null;
```

## 1.3 常用函数

### 1.3.1 AVG – 平均值

```sql
-- AVG 函数返回数值列的平均值。NULL 值不包括在计算中。
select * from orders where orderno < (select avg(orderno) from orders);
```

### 1.3.2 COUNT – 汇总行数

```sql
-- 返回表中的记录数
SELECT COUNT(*) FROM 表名;  
-- 返回指定列的不同值的数目
SELECT COUNT(DISTINCT 列名) FROM 表名;  
-- 返回指定列的值的数目（NULL 不计入）
SELECT COUNT(列名) FROM 表名;  
```

### 1.3.3 MAX & MIN– 最大 / 最小值

```sql
-- MIN 和 MAX 也可用于文本列，以获得按字母顺序排列的最高或最低值。
-- MAX 函数返回一列中的最大值。NULL 值不包括在计算中
select max(列名) from 表名;
select min(列名) from 表名;
```

### 1.3.4 SUM – 求和

```sql
-- SUM 函数返回数值列的总数（总额）
SELECT SUM(列名) FROM 表名;  
```

### 1.3.5 GROUP BY – 分组

```sql
-- GROUP BY 语句用于结合合计函数，根据一个或多个列对结果集进行分组
SELECT 列名A, 统计函数(列名B)  
FROM 表名  
WHERE 查询条件  
GROUP BY 列名A; 

-- eg. 获取 Persons 表中住在北京的总人数，根据 LASTNAME 分组：
select lastname,count(city) from persons   
where city='Beijing'   
group by lastname; 
```

![img](./res/fb1f47e98797417265f2cfa9f35b1fe1-1719500050973-12.png)

### 1.3.6 HAVING – 句尾连接

> 在 SQL 中增加 HAVING 子句原因是，WHERE 关键字无法与合计函数一起使用。

```sql
-- 获取 Persons 表中住在北京的总人数大于1的 LASTNAME，根据 LASTNAME 分组：
select lastname,count(city) from persons   
where city='Beijing'   
group by lastname  
having count(city) > 1; 
```

### 1.3.7 杂项函数

```sql
-- 1. UCASE/UPPER – 大写
select upper(列名) from 表名;
select upper(lastname),firstname from persons;
-- 2. LCASE/LOWER – 小写
select lower(lastname),firstname from persons;
-- 3. LEN/LENGTH – 获取长度
select length(列名) from 表名;  
-- 4. ROUND – 数值取舍（四舍五入）
select round(列名,精度) from 表名;
-- 5. NOW/SYSDATE – 获取当前时间
select sysdate from 表名;
```

# 二、细节类数据库知识

## 2.1 数据库分类

###  2.1.1 关系型数据库

1. 典型：mysql、sql server、postgreSQL、Oracle
2. 优点：易维护、使用方便（通用sql语言）、容易理解
3. 缺点：不适合并发高的查询、效率低、多表关联性能差

### 2.1.2 非关系型数据库

**数据结构化存储**的集合。

- 优点：
  1. 可以根据需求去添加自己需要的字段，仅需要根据id取出相应的value就可以完成查询；
  2. 适用于SNS，数据结构巨大变动，这一点关系型数据库难以应付，需要新的结构化数据存储，不可能用一种数据结构化存储应付所有的新的需求。

- 不足：
  1. 只适合简单数据；
  2. 不适合持久存储海量数据。
- 分类：

1. **高性能并发读写：key-value数据库**

类似于java的map，每个键对应唯一的值，代表：**Redis**

2. **海量数据访问的面向文档数据库**

在海量的数据中可以快速的查询数据，主要是JSON，JSON文档也可以作为纯文本存储在键值存储或关系数据库系统中。代表：**MongoDB**

3. **搜索数据内容的搜索引擎**

专门用于搜索数据内容的 NoSQL 数据库管理系统，主要是用于对海量数据进行近实时的处理和分析处理，可用于机器学习和数据挖掘。代表：**Elasticsearch**

4. **面向可扩展性的分布式数据库**

具有很强的可拓展性，普通的关系型数据库都是以行为单位来存储数据的，擅长以行为单位的读入处理，比如特定条件数据的获取。因此，关系型数据库也被认为是面向行的数据库。相反，面向列的数据库是以列为单位来存储数据的，擅长以列为单位读入数据。

这类数据库想解决的问题就是传统数据库存在可扩展性上的缺陷，这类数据库可以适应数据量的增加以及数据结构的变化，将数据存储在记录中，能够容纳大量动态列。由于列名和记录键不是固定的，并且由于记录可能有数十亿列，因此可扩展性存储可以看作是二维键值存储。代表：**HBase**

### 2.1.3 关系型与非关系型数据库的比较

1. **成本：**Nosql数据库（非关系型数据库）**简单易部署，基本都是开源软件**，不需要像使用Oracle那样花费大量成本购买使用，相比关系型数据库价格便宜。

2. **查询速度：**Nosql数据库将**数据存储于缓存之中，而且不需要经过SQL层的解析**，关系型数据库将数据存储在硬盘中，自然查询速度远不及Nosql数据库。

3. **存储数据的格式：**Nosql的存储格式是**key，value形式、文档形式、图片形式等等**，所以可以存储基础类型以及对象或者是集合等各种格式，而数据库则只支持基础类型。

4. **扩展性：**关系型数据库有类似 join 这样的多表查询机制的限制导致扩展很艰难。Nosql基于键值对，数据之间没有耦合性，所以非常容易水平扩展。

5. **持久存储：**Nosql**不适用于持久存储**，海量数据的持久存储，还是需要关系型数据库。

6. **数据一致性：**非关系型数据库一般强调的是**数据最终一致性**，不像关系型数据库一样强调数据的强一致性，从非关系型数据库中读到的有可能还是处于一个中间态的数据。

7. **事务处理**：Nosql**不提供对事务的处理**。

## 2.2 Hadoop

[Hadoop大数据简介-CSDN博客](https://blog.csdn.net/u010520724/article/details/110432582)
