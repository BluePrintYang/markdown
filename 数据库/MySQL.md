## MySQL

行：表中的一个记录

主键（primary key）：一列（或一组列），其值能唯一区分表中每个行

### 使用MySQL

展示所有数据库

```sql
show databases;
```

选择数据库

```sql
use crashcourse;
```

获取一个数据库内的表的列表

```sql
show tables;
```

显示表列，需要给出表名

```sql
show columns from customers;
-- 下面语句等价
describe customers;
```

其他show语句

```sql
show status;	-- 显示广泛的服务器状态信息
show create database;	-- 显示创建特定数据库
show create table;	-- 显示创建特定表
show grants;	-- 显示授予用户的安全权限
show errors;	-- 显示服务器错误
show warnings;	--显示服务器警告
```

进一步了解show

```sql
help show;
```

### 检索数据

#### select语句

检索单个列

```sql
select prod_name from products;
```

检索多个列

```sql
select prod_id, prod_name, prod_name from products;
```

检索所有列

```sql
select * from products;
```

检索不同的行（数据不重复）

```sql
select distinct vend_id from products;	-- distinct放在列名前面
```

限制结果 

##### LIMIT子句

```sql
select prod_name from products limit 5;	-- 只返回前5行数据，不够5行则返回所有数据
select prod_name from products limit 5,5;	-- 返回从行5开始的5行，第一个数为开始位置，第二个数为行数
```

==第一行为行0==

使用完全限定的表名

```sql
select products.prod_name from products;
select products.prod_name from crashcourse.products;
```

### 检索排序数据

#### 排序数据

##### ORDER BY子句

```sql
select prod_name from products order by prod_name;
```

按多个列排序

> 首先按prod_price排序，prod_price相同才按prod_name

```sql
select prod_id, prod_price, prod_name from products order by prod_price, prod_name;
```

指定排序方向

> 默认升序(ASC)，使用DESC关键字降序

```sql
select prod_id, prod_price, prod_name from products order by prod_price DESC;
```

先按prod_price降序，再按prod_name升序

```sql
select prod_id, prod_price, prod_name from products order by prod_price DESC, prod_name;
```

### 过滤数据

#### 使用WHERE子句

```sql
select prod_price, prod_name from products where prod_price = 2.50;
```

#### where子句操作符

| 操作符  | 说明             |
| ------- | ---------------- |
| =       | 等于             |
| <>      | 不等于           |
| !=      | 不等于           |
| <       | 小于             |
| <=      | 小于等于         |
| >       | 大于             |
| >=      | 大于等于         |
| BETWEEN | 在指定两个值之间 |

#### 范围值检查

```sql
select prod_price, prod_name from products where prod_price between 5 and 10;
```

#### 空值检查

```sql
select prod_name from products where prod_price is null;
```

### 数据过滤

#### 组合where子句

> 允许给出多个where子句，子句之间用AND或OR联结

##### AND操作符

```sql
select prod_id, prod_price, prod_name from products where vend_id = 1003 AND prod_price <= 10;
```

##### OR操作符

```sql
select prod_price, prod_name from products where vend_id = 1002 OR vend_id = 1003;
```

##### 计算次序

对于下面的语句，优先处理AND操作符（AND操作符在计算次序中优先级更高）

```sql
select prod_price, prod_name from products where vend_id = 1002 OR vend_id = 1003 AND prod_price >= 10;
```

如果需要先计算OR联结的语句，需要加括号

```sql
select prod_price, prod_name from products where (vend_id = 1002 OR vend_id = 1003) AND prod_price >= 10;
```

##### IN操作符

> in用来指定条件范围

```sql
select prod_price, prod_name from products where vend_id in(1002,1003) order by prod_name;
```

##### NOT操作符

> 否定后面的条件

```sql
select prod_price, prod_name from products where vend_id not in(1002,1003) order by prod_name;
```

### 用通配符过滤

#### LIKE操作符

##### %通配符

> 表示任何字符出现任何次数

```sql
select prod_id, prod_name from products where prod_name like 'jet%';
```

##### _通配符

> 匹配单个任意字符

```sql
select prod_id, prod_name from products where prod_name like '_ ton anvil';
```

#### 使用通配符技巧

- 不要过度使用通配符。优先使用其他能达到相同目的的操作符。
- 除非绝对有必要，否则不要把通配符放在搜索模式的开始处。这样搜索起来是最慢的
- 仔细注意通配符的位置

### 使用正则表达式进行搜索

#### 基本字符匹配

> 检索文本包含1000的所有行

```sql
select prod_name from products where prod_name regexp '1000' order by prod_name;
```

与使用like关键词的语句非常像，只不过把like改为了regexp

==但是like匹配整个列值，一般需要搭配通配符使用==

.000中 . 表示匹配任意一个字符

```sql
select prod_name from products where prod_name regexp '.000' order by prod_name;
```

正则表达式匹配不区分大小写，如果需要区分，则在regexp后面添加binary关键字

#### 进行OR匹配

> 搜索两个串之一，使用|，可以给出两个以上的OR条件

```sql
select prod_name from products where prod_name regexp '1000|2000' order by prod_name;
```

#### 匹配几个字符之一

> 使用一组用[和]括起来的字符

```sql
select prod_name from products where prod_name regexp '[123] Ton' order by prod_name;
```

[^123]则匹配除这些字符以外的任意字符

#### 匹配范围

**[0123456789]可简写为[0-9]**

#### 匹配特殊字符

```sql
-- 加\\为前导 \\-表示查找- ， \\.表示查找.
```

| 元字符 | 说明     |
| ------ | -------- |
| \\\f   | 换页     |
| \\\n   | 换行     |
| \\\r   | 回车     |
| \\\t   | 制表     |
| \\\v   | 纵向制表 |

==为了匹配\本身，需要使用\\\\\\==

#### 匹配字符类

| 类         | 说明                                                         |
| ---------- | ------------------------------------------------------------ |
| [:alnum:]  | 任意字母和数字（同[a-zA-Z0-9]）                              |
| [:alpha:]  | 任意字符（同[a-zA-Z]）                                       |
| [:blank:]  | 空格和制表（同[\\\t]）                                       |
| [:cntrl:]  | ASCII控制字符（ASCII0到31和127）                             |
| [:digit:]  | 任意数字（同[0-9]）                                          |
| [:graph:]  | 与[:print:]相同，但不包含空格                                |
| [:lower:]  | 任意小写字母（同[a-z]）                                      |
| [:print:]  | 任意可打印字符                                               |
| [:punct:]  | 既不在[:alnum:]又不在[:cntrl:]中的任意字符                   |
| [:space:]  | 包括空格在内的任意空白字符（同[\\\\\f\\\\n\\\\r\\\\t\\\\v]） |
| [:upper:]  | 任意大写字母（同[A-Z]）                                      |
| [:xdigit:] | 任意十六进制数字（同[a-fA-F0-9]）                            |

#### 匹配多个实例

| 元字符 | 说明                         |
| ------ | ---------------------------- |
| *      | 0个或多个匹配                |
| +      | 1个或多个匹配（等于{1,}）    |
| ?      | 0个或1个匹配（等于{0,1}）    |
| {n}    | 指定数目的匹配               |
| {n,}   | 不少于指定数目的匹配         |
| {n,m}  | 匹配数目的范围（m不超过255） |

```sql
select prod_name from products where prod_name regexp '\\([0-9] sticks?\\)' order by prod_name;
```

输出示例

```
TNT (1 stick)
TNT (5 sticks)
```

分析：\\\\( 匹配 ( ,[0-9]匹配任意数字，sticks?匹配stick和sticks，\\\\)匹配)

#### 定位符

| 元字符  | 说明       |
| ------- | ---------- |
| ^       | 文本的开始 |
| $       | 文本的结尾 |
| [[:<:]] | 词的开始   |
| [[:>:]] | 词的结尾   |

如果想要找出以一个数（包括以小数点开始的数）**开始**的所有产品，使用[0-9\\\\.]不行，因为它在文本内任意位置查找匹配。解决方法是使用定位符^

```sql
select prod_name from products where prod_name regexp '^[0-9\\.]' order by prod_name;
```

**^的双重用途**：在集合中，用它来否定该集合，否则用来指串的开始处

### 创建计算字段

> 计算字段并不实际存在于数据库表中。计算字段是运行时在SELECT语句内创建的。

**字段**：基本上与列的意思相同

#### 拼接字段

> 拼接：将值联结到一起构成单个值

在MySQL的SELECT语句中，可使用**==Concat()函数==**来拼接两个列

```sql
select Concat(vend_name,'(',vend_country,')') from vendors order by vend_name;
```

Trim()函数：去除串左右两边的空格

LTrim()：去除左边的空格	RTrim()：去除右边的空格

**使用别名**

```sql
select Concat(vend_name,'(',vend_country,')') as vend_title from vendors order by vend_name;
```

#### 执行算术计算

```sql
select prod_id,
	   quantity,
	   item_price,
	   quantity*item_price AS expanded_price
from orderitems
where order_num = 20005;
```

### 使用数据处理函数

#### 文本处理函数

1. Trim() 
2. Upper()
3. Lower()
4. Soundex()：返回串的soundex值，能对串进行发音比较
5. ……

#### 日期和时间处理函数

日期格式必须为yyyy-mm-dd

MySQL中存在类型datetime，格式为：yyyy-MM-dd HH:mm:ss

如果要的只是日期，需要使用Date()函数，也存在一个Time()函数

#### 数值处理函数

1. Abs()
2. Cos()
3. Exp()
4. Mod()
5. ……

### 汇总数据

#### 聚集函数

> 运行在行组上，计算和返回单个值的函数

##### AVG()	 

> 返回某列的平均值

忽略列值为null的行

```sql
select AVG(prod_price) AS avg_price FROM products;
```

##### COUNT()    

> 返回某列的行数

如果指定列名，值为null的行将被忽略。如果使用COUNT(*)，则不忽略

```sql
select COUNT(*) AS num_cust FROM customers;
```

##### MAX()

> 返回某列的最大值，忽略值为null的行

一般用于数值或日期值。在用于文本数据时，如果数据按相应列排序，则MAX()返回最后一行

##### MIX()

> 返回某列的最小值,与MAX相反

##### SUN()

> 返回某列值值和

#### 聚集不同值

ALL为默认，对所有的行执行操作

DISTINCT，只包含不同的值

#### 组合聚集函数

可以同时使用多个聚集函数

```sql
select COUNT(*) AS num_item,
	   MIN(prod_price) AS price_min,
	   MAX(prod_price) AS price_max,
	   AVG(prod_price) AS price_avg
from products;
```

##### 取别名

> 不应该使用表中实际的列名。并非不合法，但是使用唯一的名字会使SQL更易于理解

### 分组数据

#### GROUP BY子句

> group by子句指示MySQL分组数据，然后对每个组而不是整个结果集进行聚集

```sql
select vend_id, COUNT(*) AS num_prods FROM products group by vend_id;
```

重要规定

- group by子句可以包含任意数目的列
- 如果在group by子句中嵌套了分组，数据将在最后规定的分组上进行汇总
- group by子句中列出的每个列都必须是检索列或有效的表达式（但是不能是聚集函数）
- 除聚集计算语句外，select语句中的每个列都必须在group by子句中给出
- 如果分组列中具有null值，则null将作为一个分组返回
- group by子句必须出现在where子句之后，order by子句之前

**使用ROLLUP**：使用关键字with rollup可以得到每个分组以及每个分组汇总级别的值

#### 过滤分组

where过滤指定的是行而不是分组

可以使用having子句过滤分组，having支持所有where操作符

```sql
select cust_id, COUNT(*) AS orders FROM orders group by cust_id HAVING COUNT(*)>=2;
```



#### 分组和排序

**一般在使用group by子句时，应该也给出order by子句**

#### select子句顺序

select→from→where→group by→having→order by→limit

### 使用子查询

> 子查询：嵌套在其他查询里的查询

子查询总是从内向外处理

一般与IN操作符结合使用

以下两条语句可合并为一条

```sql
SELECT order_num from orderitems where prod_id = 'TNT2'; -- 查询结果为 20005 20007
SELECT cust_id from orders where order_num IN(20005,20007);  -- 查询结果为10001 10004
```

使用子查询

```sql
SELECT cust_id 
from orders 
where order_num IN(SELECT order_num 
                   from orderitems 
                   where prod_id = 'TNT2');
```



### 连结表

> 联结是利用SQL的select能执行的最重要的操作

主键

外键

涉及两个表的查询，在引用的列可能出现二义性时，必须使用完全限定列名。

```sql
select vend_name, prod_name, prod_price
from vendors, products
where venders.vend_id = products.vend_id
order by vend_name, prod_name;
```

如果不使用where子句建立联结条件，那么返回的结果为笛卡尔积。

```sql
select vend_name, prod_name, prod_price
from vendors, products
order by vend_name, prod_name;
```

==应该保证所有的联结都有where子句==

#### 内部联结

> 内部联结，也称为等值联结，基于两表之间的相等测试

```sql
select vend_name, prod_name, prod_price
from vendors INNER JOIN products
ON venders.vend_id = products.vend_id;
```

使用`INNER JOIN`之后，用`ON`子句指定联结条件

#### 联结多个表

联结的表越多，性能下降的越厉害

```sql
select vend_name, prod_name, prod_price, quantity
from vendors, products,orderitems
where venders.vend_id = products.vend_id
  AND orderitems.prod_id = products.prod_id
  AND order_num = 20005;
```

### 创建高级联结

#### 使用表别名

- 缩短SQL语句
- 允许单条SELECT语句中多次使用相同表

```sql
SELECT cust_name, cust_contact
FROM customers AS c, orders AS o, orderitems AS oi
WHERE c.cust_id = o.cust_id
  AND oi.order_num = o.order_num
  AND prod_id = 'TNT2';
```

#### 使用不同类型的联结

##### 自联结

假如发现某物品（其ID为DTNTR）存在问题，因此想知道生产该物品的供应商生产的其他物品是否存在问题。

使用子查询

```sql
SELECT prod_id, prod_name
FROM products
WHERE vend_id = (SELECT vend_id
				 FROM products
				 WHERE prod_id = 'DTNTR');
```

使用联结的相同查询

```sql
SELECT prod_id, prod_name
FROM products AS p1, products AS p2
WHERE p1.vend_id = p2.vend_id
  AND p2.prod_id = 'DTNTR';
```

此查询中需要的两个表实际上是相同的表，通过使用别名避免二义性。

使用自联结比子查询效率更高。

##### 自然联结

> 对表进行联结，应该至少有一个列出现在不止一个表中，标准的联结返回所有数据，甚至相同的列多次出现。自然联结排除多次出现，使每个列只返回一次

一般通过对表使用通配符(SELECT *)，对其他表的列使用明确的子集来完成。

```sql
SELECT c.*, o.order_num, o.order_date, oi.prod_id, oi.quantity, oi.item_price
FROM customers AS c, orders AS o, orderitem AS oi
WHERE c.cust_id = o.cust_id
  AND oi.order_num = o.order_num
  AND prod_id = 'FB';
```

通配符只对第一个表使用。所有其他列明确列出，所以没有重复的列被检索出来。

##### 外部联结

```sql
SELECT customers.cust_id, order.order_num
FROM customers LEFT OUTER JOIN orders
ON customers.cust_id = orders.cust_id;
```

使用了`OUTER JOIN`来指定联结的类型。与内部联结不同的是外部联结还包括没有关联的行。

使用LEFT或RIGHT关键词指定包括其所有行的表

上面例子中使用LEFT OUTER JOIN 从customers表中选择所有行，即左（外）连接

下面展示右（外）连接

```sql
SELECT customers.cust_id, order.order_num
FROM customers RIGHT OUTER JOIN orders
ON customers.cust_id = orders.cust_id;
```

#### 使用带聚集函数的联结

```sql
SELECT customers.cust_name,
	   customers.cust_id,
	   COUNT(orders.order_num) AS num_ord
FROM customers INNER JOIN orders
ON customers.cust_id = orders.cust_id
GROUP BY customers.cust_id;
```

#### 使用联结和联结条件

- 注意使用的联结类型
- 保证使用正确的联结条件
- 应该总是提供联结条件，否则会得出笛卡尔积
- 在一个联结中可以包含多个表，甚至对于每个联结可以采用不同的联结类型

### 组合查询

有两种基本情况，其中需要使用组合查询：

- 在单个查询中从不同的表返回类似结构的数据
- 对单个表执行多个查询，按单个查询返回数据

#### 创建组合查询

##### UNION操作符

以下两条SELECT语句可使用UNION组合

```sql
SELECT vend_id, prod_id, prod_price
FROM products
WHERE prod_price <= 5;
SELECT vend_id, prod_id, prod_price
FROM products
WHERE vend_id IN (1001,1002);
```

组合

```sql
SELECT vend_id, prod_id, prod_price
FROM products
WHERE prod_price <= 5;
UNION
SELECT vend_id, prod_id, prod_price
FROM products
WHERE vend_id IN (1001,1002);
```

给出使用多条WHERE子句而不是使用UNION的相同查询

```sql
SELECT vend_id, prod_id, prod_price
FROM products
WHERE prod_price <= 5
	OR vend_id IN (1001,1002);
```

这里使用UNION可能比使用WHERE子句更复杂，但是对于更复杂的过滤条件，或者从多个表中检索数据的情形，使用UNION可能会使处理更简单

##### UNION规则

- 必须由两条或以上SELECT语句组成
- 每个查询必须包含相同的列，表达式或聚集函数（次序可不同）
- 列数据类型必须兼容：类型不必完全相同，但必须是DBMS可以隐含地转换的类型

##### 包含或取消重复的行

UNION从查询结果集中自动去除了重复的行

如果想匹配所有行，可使用`UNION ALL`而不是`UNION`

##### 对组合查询结果排序

SELECT语句的输出用ORDER BY子句排序。

在用UNION组合查询时，只能用一条ORDER BY子句，它必须出现在最后一条SELECT语句之后

### 全文本搜索

**并非所有引擎都支持全文本搜索**

MyISAM支持，InnoDB不支持

#### 使用全文本搜索

> 为了进行全文本搜索，必须索引被搜索的列，而且要随着数据的改变不断地重新索引

在对表列进行适当设计后，MySQL会自动进行所有的索引和重新索引

#### 启用全文本搜索支持

一般在创建表时启用全文本搜索。`CREATE TABLE`语句接收FULLTEXT子句，它给出被索引列的一个逗号分隔的列表。

```sql
CREATE TABLE productnotes
(
  note_id    int           NOT NULL AUTO_INCREMENT,
  prod_id    char(10)      NOT NULL,
  note_date datetime       NOT NULL,
  note_text  text          NULL ,
  PRIMARY KEY(note_id),
  FULLTEXT(note_text)
) ENGINE=MyISAM;
```

有一个名为`note_text`的列，为了进行全文本搜索，MySQL根据子句FULLTEXT(note_text)的指示对它进行索引。

#### 进行全文本搜索

使用两个函数Match()和Against()执行全文本搜索，其中Match()指定被搜索的列，Against()指定要使用的搜索表达式。

```sql
SELECT note_text
FROM productnotes
WHERE Match(note_text) Against('rabbit');
```

此select语句检索单个列note_text，由于WHERE子句，一个全文本搜索被执行。Match(note_text)指示MySQL针对的列进行搜索，Against('rabbit')指定词rabbit作为搜索文本。

Match()说明：传递给Match()的值必须与FULLTEXT()定义中的相同。如果指定多个列，则必须列出它们（而且次序正确）。

搜索不区分大小写：除非使用BINARY方式。

事实上，刚才的搜索可以简单的用like子句替换

```sql
SELECT note_text
FROM productnotes
WHERE note_text LIKE '%rabbit%';
```

LIKE以不特别有用的顺序返回数据。

全文本搜索返回以文本匹配的良好程度排序的数据。

#### 使用查询扩展

```sql
SELECT note_text
FROM productnotes
WHERE Match(note_text) Against('anvils' WITH EXPANSION);
```

行越多使用扩展查询返回的结果越好

#### 布尔文本搜索

```sql
SELECT note_text
FROM productnotes
WHERE Match(note_text) Against('heavy' IN BOOLEAN MODE);
```

#### 全文本搜索的使用说明

- 在索引全文本数据时，短词被忽略且从索引中排除
- MySQL带有一个内建的非用词（stopword）列表，这些词在索引全文本数据时总是被忽略
- 许多次出现的频率很高，搜索它们没有用处。MySQL制定了一条50%规则
- 如果表中的行数少于3行，则全文本搜索不返回结果
- 忽略词中的单引号
- 不具有词分隔符的语言不能恰当地返回全文本搜索结果
- 仅在MyISAM数据库索引中支持全文本搜索

### 插入数据

#### 插入完整的行

```sql
INSERT INTO Customers VALUES(NULL, 'Pep E. LaPew', '100 Main Street', 'Los Angeles', 'CA', '90046', 'USA', NULL, NULL);
```

更安全的方式

```sql
INSERT INTO Customers(cust_name, cust_contact, cust_email, cust_address, cust_city, cust_state, cust_zip, cust_conutry) 
VALUES('Pep E. LaPew', NULL, NULL, '100 Main Street', 'Los Angeles', 'CA', '90046', 'USA');
```

必须给出VALUES的正确数目。如果不提供列名，则必须给每个列提供一个值。如果提供列名，则必须对每个列出的列给出一个值。

**省略列**

- 该列定义为允许NULL值
- 在表定义中给出默认值

#### 插入多行

多条INSERT语句

或者

```sql
INSERT INTO customers(……) VALUES(……),(……);
```

#### 插入某些查询的结果

`INSERT SELECT`

### 更新和删除数据

#### 更新数据

- 更新特定行
- 更新所有行

**不要省略WHERE子句**

UPDATE语句三部分

1. 要更新的表
2. 列名和它们的新值
3. 确定要更新行的过滤条件

```sql
UPDATE customers SET cust_email = 'elmer@fudd.com' WHERE cust_id = 10005;
```

如果用UPDATE更新多行，更新时可能会出现错误，那么则整个更新操作都会被取消。

如果想即使发生错误也要继续更新，可以使用`IGNORE`关键字，示例`UPDATE IGNORE customers...`

#### 删除数据

- 删除特定行
- 删除所有行

**不要省略WHERE子句**

```sql
DELETE FROM customers WHERE cust_id = 10006;
```

**删除所有行**：使用`TRUNCATE TABLE` truncate实际是删除原来的表并重新创建一个表，而不是逐行删除数据

### 创建和操纵表

#### 创建表

```sql
create table customers(
    cust_id		 	int			NOT NULL AUTO_INCREMENT,
    cust_name	 	char(50)	NOT NULL,
    cust_address	char(50)	NULL,
    cust_city		char(50)	NULL,
    cust_state		char(5)		NULL,
    cust_zip		char(10)	NULL,
    cust_country	char(50)	NULL,
    cust_contact	char(50)	NULL,
    cust_email		char(255)	NULL,
    PRIMARY KEY	(cust_id)
)ENGINE=InnoDB;
```

确定AUTO_INCREMENT的值

使用 SELECT last_insert_id()函数，返回最后一个AUTO_INCREMENT值

#### 引擎类型

- InnoDB：可靠的事务处理引擎，不支持全文本搜索
- MEMORY：功能等同于MyISAM，但用于数据存储在内存（不是磁盘）中，速度很快（特别适合于临时表）
- MyISAM：性能极高的引擎，支持全文本搜索，但不支持事务处理

外键不能跨引擎

#### 更新表

`ALTER TABLE`

```sql
ALTER TABLE venders ADD vend_phone CHAR(20);	-- 添加列
ALTER TABLE venders DROP COLUMN vend_phone;		-- 删除列
```

常见用途：定义外键

```sql
ALTER TABLE orderitems
ADD CONSTRAINT fk_orderitems_orders
FOREIGN KEY (order_num) REFERENCES orders (order_num);
```

#### 删除表

```sql
DROP TABLE customers2;
```

#### 重命名表

```sql
RENAME TABLE customers2 TO customers;
```

### 使用视图

### 使用存储过程

### 使用游标

### 使用触发器

### :star:管理事务处理

#### 事务处理

**并非所有引擎都支持事务处理**：InnoDB支持，MyISAM不支持

> 维护数据库的完整性，保证成批的MySQL操作要么完全执行，要么完全不执行

几个术语：

- 事务（transaction）：指一组SQL语句
- 回退（rollback）：指撤销指定SQL语句的过程
- 提交（commit）：指将未存储的SQL语句写入数据库表
- 保留点（savepoint）：指事务发布中设置的临时占位符（placeholder），可以对它发布回退（与回退整个事务不同）

#### 控制事务处理

##### 使用ROLLBACK

```sql
SELECT * FROM ordertotals;
START TRANSACTION	-- 标识事务开始
DELETE FROM ordertotals;
SELECT * FROM ordertotals;
ROLLBACK;			-- 回滚
SELECT * FROM ordertotals;
```

事务处理用来管理`INSERT、UPDATE、DELETE`语句。不能回退`SELECT`语句（没意义），不能回退`CREATE、DROP`操作，事务管理中可以使用这两条语句，但是执行回退并不会被撤销。

##### 使用COMMIT

```sql
START TRANSACTION	-- 标识事务开始
DELETE FROM ordertotals WHERE order_num = 20010;
DELETE FROM orders WHERE order_num = 20010;
COMMIT;			-- 提交
```

当COMMIT或ROLLBACK语句执行后，事务会自动关闭（将来的更改会隐含提交）

##### 使用保留点

```sql
SAVEPOINT delete1;
ROLLBACK TO delete1;
```

保留点越多越好

保留点在事务处理完成后（执行一条）自动释放，MySQL 5以来，也可以使用RELEASE SAVEPOINT明确地释放保留点。

#### 更改默认的提交行为

默认MySQL行为是自动提交所有更改。为指示MySQL不自动提交更改，需要使用以下语句

```sql
SET autocommit=0;
```

autocommit标志决定是否自动提交更改，不管有没有commit语句。设置autocommit为0（假）指示MySQL不自动提交更改（直到autocommit被设置为真为止）

**autocommit标志是针对每个连接而不是服务器的**

### 全球化和本地化

#### 使用字符集和校对顺序

```sql
show character set;		-- 显示所有可用的字符集以及每个字符集的描述和默认校对
show collation;			-- 显示所有可用的校对，以及它们适用的字符集
```

### 安全控制

#### 管理用户

```sql
use mysql;
SELECT user FROM user;
```

##### 创建用户账号

```sql
create USER ben IDENTIFIED by 'p@$$w0rd';
```

重命名用户账号

```sql
RENAME USER ben TO bforta;
```

##### 删除用户账户

```sql
DROP USER bforta;
```

##### 设置访问权限

```sql
show grants for bforta;		-- 查看赋予用户的权限
-- 显示有一个权限USAGE ON *.* USAGE表示根本没有权限
```

设置权限，使用GRANT语句。至少要使用以下信息：

- 要授予的权限
- 被授予访问权限的数据库或表
- 用户名

```sql
GRANT SELECT ON crashcourse.* TO bforta;	-- 允许bforta用户在crashcourse.*上使用select
```

反操作`REVOKE`撤销权限

```sql
REVOKE SELECT ON crashcourse.* TO bforta;	-- 撤销bforta用户在crashcourse.*上使用select权限
```

几个层次上的控制访问权限

- 整个服务器：GRANT ALL和REVOKE ALL
- 整个数据库：使用ON database.*
- 特定的表：使用ON database.table
- 特定的列
- 特定的存储过程

##### 更改口令

```sql
set password for bforta = Password('n3w p@$$w0rd');
```

不指定用户名则更新当前登录用户的口令

### 数据库维护

#### 备份数据

#### 进行数据库维护

#### 诊断启动问题

#### 查看日志文件

### 改善性能