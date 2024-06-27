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

![img](https://img-blog.csdnimg.cn/img_convert/c70c35678f4b4c7129746721bc53110d.png)

### 1.1.3 查询 SELECT

#### 1.1.3.1 普通查询

```sql
SELECT * FROM Persons;
SELECT LastName,FirstName FROM Persons; 
```

![img](https://img-blog.csdnimg.cn/img_convert/a3e9d10889006f0f2e568c6e67617482.png)

![img](https://img-blog.csdnimg.cn/img_convert/5d90d9ce257cd8421dfdd138ab68f209.png)

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

![img](https://img-blog.csdnimg.cn/img_convert/7c8e608a9e7362f1a154f587d52cd11b.png)

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
- LEFT JOIN: 即使右表中没有匹配，也从左表返回所有的行
- RIGHT JOIN: 即使左表中没有匹配，也从右表返回所有的行
- FULL JOIN: 只要其中一个表中存在匹配，就返回行

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

![img](https://img-blog.csdnimg.cn/img_convert/fb1f47e98797417265f2cfa9f35b1fe1.png)

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



# 三、Linux基本命令

