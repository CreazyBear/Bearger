# 定义

1. DB:将大量数据保存起来，通过计算机加工而成的可以进行高效访问的数据集合称为数据库(Database，DB)
2. DBMS:用来管理数据库的计算机系统称为数据库管理系统;实现多个用户同时安全简单地操作大量数据
3. SQL:Structured Query Language，结构化查询语言
4. 根据 SQL 语句的内容返回的数据同样必须是二维表的形式，这也是关系数据库的特征之一。返回结果如果不是二维表的 SQL 语句则无法执行。、
5. 关系数据库必须以行为单位进行数据读写
6. 一个单元格中只能输入一个数据
7. 国际标准化组织(ISO)为 SQL 制定了相应的标准，以此为基准的 SQL 称为标准 SQL
8. DML:(Data Manipulation Language，数据操纵语言)用来查询或者变更 表中的记录。
9. 一条 SQL 语句可以描述一个数据库操作。在 RDBMS 当中，SQL 语句也是逐条执行的。SQL 语句则使用分号(;)结尾
10. SQL不区分关键字的大小写。例如，不管写成 SELECT 还是 select，解释都是一样的。表名和列名也是如此。(书上是这么说的，但=.=)
11. 字符串和日期常数需要使用单引号(')括起来。
12. 所有的列都必须指定数据类型。每一列 都不能存储与该列数据类型不符的数据
13. eg
```sql
CREATE TABLE <表名>
(
<列名1> <数据类型> <该列所需约束>，
<列名2> <数据类型> <该列所需约束>， 
<列名3> <数据类型> <该列所需约束>，
<列名4> <数据类型> <该列所需约束>，
......
<该表的约束1>， <该表的约束2>，......
);

create table Product
(
product_id char(4) NOT NULL,
product_name VARCHAR(200) NOT NULL,
product_type VARCHAR(32) NOT NULL,
sale_price INTEGER,
purchase_price INTEGER,
regist_date DATE,
PRIMARY KEY (product_id)
);

INSERT INTO Product VALUES ('0001', 'T恤衫', '衣服', 1000, 500, '2009-09-20');
INSERT INTO Product VALUES ('0002', '打孔器', '办公用品', 500, 320, '2009-09-11');
INSERT INTO Product VALUES ('0003', '运动T恤','衣服', 4000, 2800, NULL);
INSERT INTO Product VALUES ('0004', '菜刀', '厨房用具',3000, 2800, '2009-09-20');
INSERT INTO Product VALUES ('0005', '高压锅','厨房用具', 6800, 5000, '2009-01-15');
INSERT INTO Product VALUES ('0006', '叉子', '厨房用具',500, NULL, '2009-09-20');
INSERT INTO Product VALUES ('0007', '擦菜板', '厨房用具',880, 790, '2008-04-28');
INSERT INTO Product VALUES ('0008', '圆珠笔','办公用品', 100, NULL,'2009-11-11');

CREATE TABLE ProductIns (
product_id char(4) not null,
product_name varchar(100) not null,
product_type varchar(32) not null,
sale_price integer default(0),
purchase_price INTEGER ,
regist_date DATE ,
PRIMARY KEY (product_id));

CREATE TABLE ProductType (
product_type VARCHAR(32) not null,
sum_sale_price INTEGER,
sum_purchase_price INTEGER ,
PRIMARY KEY (product_type));
```

# 查询基础

1. 基本格式
    ```
    SELECT <列名>，......
    FROM <表名>;
    ```

    SELECT 子句中列举了希望从表中查询出的列的名称

    FROM 子句 则指定了选取出数据的表的名称。

2. SQL 语句可以使用 AS 关键字为列设定别名

    ```sql
    SELECT 
    product_id AS "中文双引号", 
    product_name AS '名称', 
    purchase_price AS price
    FROM Product;
    ```
    虽然书上说：中文需要双引号，但=。=不绝对

2. 常量
    ```sql
    SELECT 
        '商品' AS string, 
        38 AS number, 
        '2009-02-24' AS date, 
        product_id, 
        product_name
    FROM Product;
    ```

3. 去重
    ```sql
    SELECT DISTINCT product_type, regist_date FROM Product;
    ```
    * 将多个列的数据进行组合，将重复的数据合并为一条;
    * DISTINCT 关键字只能用在第一个列名之前。

4. 算术运算符(+、-、*、/)和比较运算符(=,<,>,>=,<=,<>)
5. 所有包含 NULL 的计算，结果肯定是 NULL
6. 不能对 NULL 使用比较运算符;希望选取 NULL 记录时，需要在条件表达式中使用 IS NULL 运算符。希望选取不 是 NULL 的记录时，需要在条件表达式中使用 IS NOT NULL 运算符。
    ```sql
    SELECT product_name, purchase_price FROM Product
    WHERE purchase_price IS NULL;
    ```
7. 逻辑运算符:NOT,AND,OR
    ```SQL
    SELECT product_name, product_type, regist_date FROM Product
    WHERE product_type = '办公用品'
    AND ( regist_date = '2009-09-11' OR regist_date = '2009-09-20');
    ```

# 聚合

* COUNT:计算表中的记录数(行数)
* SUM : 计算表中数值列中数据的合计值 
* AVG : 计算表中数值列中数据的平均值 
* MAX: 求出表中任意列中数据的最大值 
* MIN : 求出表中任意列中数据的最小值

1. 只有SELECT子句和HAVING子句(以及ORDER BY子句)中能够使用聚合函数。

# 分组

**GROUP BY**

1. 使用 GROUP BY 子句时，SELECT 子句中不能出现聚合键之外的列名；但常数和聚合函数是可以的
2. 在 GROUP BY 子句中不能使用 SELECT 子句中定义的别名，因为执行顺序。
3. GROUP BY 子句结果的显示是无序的
4. 只有SELECT子句和HAVING子句(以及ORDER BY子句)中能够使用聚合函数。
6. having中只能存在常数，聚合函数，和聚合健
7. WHERE 子句 = 指定行所对应的条件 HAVING 子句 = 指定组所对应的条件

# 排序

**order by**

1. 可以在ORDER BY子句中同时指定多个排序键了。 规则是优先使用左侧的键，如果该列存在相同值的话，再接着参考右侧的 键
2. 排序键中包含 NULL 时，会在开头或末尾进行汇总。

# 插入
```sql
INSERT 
INTO ProductIns 
(product_id,product_name,product_type,sale_price,purchase_price,regist_date) 
VALUES 
('0001','T恤衫','衣服',1000,500,'2009-09-20');
```
1. 对表进行全列 INSERT 时，可以省略表名后的列清单。这时 VALUES 子句的值会默认按照从左到右的顺序赋给每一列。
    ```sql
    INSERT INTO ProductIns VALUES ('0005', '高压锅', '厨房用具',6800, 5000, '2009-01-15');
    ```
2. 省略 INSERT 语句中的列名，就会自动设定为该列的默认值(没有默认值时会设定 为 NULL)。
3. 从其他表中复制数据
    ```sql
    INSERT INTO ProductIns (product_id, product_name, product_type,sale_price, purchase_price, regist_date)
    SELECT product_id, product_name, product_type, sale_price, purchase_price, regist_date
    FROM Product;


    INSERT INTO ProductType (product_type, sum_sale_price, sum_purchase_price)
    SELECT product_type, SUM(sale_price), SUM(purchase_price)
    FROM Product
    GROUP BY product_type;
    ```
4. INSERT语句的SELECT语句中，可以使用WHERE子句或者GROUP BY子句等任 何 SQL 语法(但使用 ORDER BY 子句并不会产生任何效果)

# 删除
1. `DELETE FROM <表名>;`
2. DELETE 语句的删除对象并不是表或者列，而是记录(行)。
3. `DELETE FROM <表名> WHERE <条件>;`

# 更新
1. `UPDATE <表名> SET <列名> = <表达式> WHERE <条件>;;`
2. 更新多行
    ```sql
    -- 使用逗号对列进行分隔排列 
    UPDATE Product
    SET sale_price = sale_price * 10, purchase_price = purchase_price / 2
    WHERE product_type = '厨房用具';

    -- 将列用()括起来的清单形式 
    UPDATE Product
    SET (sale_price, purchase_price) = (sale_price * 10, purchase_price / 2)
    WHERE product_type = '厨房用具';
    ```

# 事务
1. 事务是需要在同一个处理单元中执行的一系列更新处理的集合。
2. 格式
    ```sql
    事务开始语句 ;
    DML 语句1 ; 
    DML 语句2 ; 
    DML 语句3 ;
    .
    .
    .
    事务结束语句(COMMIT 或者 ROLLBACK);
    ```

    ```sql
    -- SQL Server PostgreSQL
    BEGIN TRANSACTION;
    -- 将运动T恤的销售单价降低1000日元 
    UPDATE Product
    SET sale_price = sale_price WHERE product_name = '运动T恤';
    -- 将T恤衫的销售单价上浮1000日元 
    UPDATE Product
    SET sale_price = sale_price WHERE product_name = 'T恤衫';
    COMMIT;

    -- MySQL
    START TRANSACTION;
    -- 将运动T恤的销售单价降低1000日元 
    UPDATE Product
    SET sale_price = sale_price WHERE product_name = '运动T恤';
    -- 将T恤衫的销售单价上浮1000日元 
    UPDATE Product
    SET sale_price = sale_price WHERE product_name = 'T恤衫';
    COMMIT;

    -- Oracle DB2
    -- 将运动T恤的销售单价降低1000日元 
    UPDATE Product
    SET sale_price = sale_price WHERE product_name = '运动T恤';
    -- 将T恤衫的销售单价上浮1000日元 
    UPDATE Product
    SET sale_price = sale_price WHERE product_name = 'T恤衫';
    COMMIT;

    ```

3. ROLLBACK——取消处理
4. ACID 特性
    1. 原子性是指在事务结束时，其中所包含的更新处理要么全部执行，要
么完全不执行，也就是要么占有一切要么一无所有
    2. 一致性指的是事务中包含的处理要满足数据库提前设置的约束
    3. 隔离性指的是保证不同事务之间互不干扰的特性
    4. 持久性也可以称为耐久性，指的是在事务(不论是提交还是回滚)结
束后，DBMS 能够保证该时间点的数据状态会被保存的特性

# 复杂查询
**视图**

1. 表中存储的是实际数据，而视图中保存的是从表中取出数据所使用的 SELECT 语句。
2. SELECT 语句需要书写在 AS 关键字之后。SELECT 语句中列的排列 顺序和视图中列的排列顺序相同
    ```sql
    CREATE VIEW 视图名称(<视图列名1>, <视图列名2>, ......) AS
    <SELECT 语句 >
    ```
3. 应该尽量避免在视图的基础上创 建视图。这是因为对多数 DBMS 来说，多重视图会降低 SQL 的性能
4. 定义视图时不能使用ORDER BY子句
5. 视图和表需要同时进行更新，因此通过汇总得到的视图无法进行更新。
6. 删除视图
    ```sql
    DROP VIEW 视图名称(<视图列名1>, <视图列名2>, ......)
    --使用 CASCADE 选项来删除关联视图
    DROP VIEW ProductSum CASCADE;
    ```

**子查询**

1. 子查询的特点概括起来就是一张一次性视图
2. eg:
    ```sql
    --SQL Server DB2 PostgreSQL MySQL
    SELECT product_type, cnt_product
    FROM(
        SELECT product_type, COUNT(*) AS cnt_product 
            FROM Product
        GROUP BY product_type
        ) AS ProductSum;

    --在 Oracle 的 FROM 子句中，不能使用 AS(会发生错误)，因此，在 Oracle 中
    SELECT product_type, cnt_product
    FROM(
        SELECT product_type, COUNT(*) AS cnt_product 
            FROM Product
        GROUP BY product_type
        ) ProductSum;
    ```
3. 首先会执行 FROM 子句 中的 SELECT 语句，然后才会执行外层的 SELECT 语句
4. 标量子查询就是返回单一值的子查询
    ```sql
    SELECT product_id, product_name, sale_price 
    FROM Product
    WHERE sale_price >(SELECT AVG(sale_price) 
                        FROM Product);
    ```
5. 能够使用常数或者列名的 地方，无论是 SELECT 子句、GROUP BY 子句、HAVING 子句，还是 ORDER BY子句，几乎所有的地方都可以使用。
6. 子查询 绝对不能返回多行结果

**关联子查询**

1. 可以理解为分类，但?? 执行步骤是什么样的？？
2. 子查询内部设定的关联名称，只能在该子查询内部使用
3. 关联名称的作用域

    ```sql
    SELECT product_type, product_name, sale_price
    FROM Product AS P1
    WHERE sale_price > (SELECT AVG(sale_price)
                        FROM Product AS P2
                        WHERE P1.product_type = P2.product_type 
                        GROUP BY product_type);
    ```

# 函数

函数大致可以分为以下几种。
* 算术函数(用来进行数值计算的函数)
* 字符串函数(用来进行字符串操作的函数)
* 日期函数(用来进行日期操作的函数)
* 转换函数(用来转换数据类型和值的函数) 
* 聚合函数(用来进行数据聚合的函数)——COUNT、SUM、AVG、MAX、MIN

**算术函数**
1. ABS( 数值 )
2. MOD( 被 除 数 ，除 数 )
3. ROUND( 对象数值，保留小数的位数 )

**字符串函数**
1. 字符串 1|| 字符串 2  //拼接 eg:`str1 | | str2 | | str3`
    ```sql
    --  Oracle DB2 PostgreSQL
    SELECT str1, str2, str3, str1 | | str2 | | str3
    FROM SampleStr 
    WHERE str1 = '山田';

    -- SQL Server
    SELECT str1, str2, str3,
    str1 + str2 + str3 AS str_concat
    FROM SampleStr;

    -- MySQL SQL Server 2012 及之后
    SELECT str1, str2, str3,
    CONCAT(str1, str2, str3) AS str_concat FROM SampleStr;
    ```
2. LENGTH( 字符串 )
    ```sql
    -- Oracle DB2 PostgreSQL MySQL
    SELECT str1,
    LENGTH(str1) AS len_str
    FROM SampleStr;

    -- SQL Server
    SELECT str1,
    LEN(str1) AS len_str
    FROM SampleStr;
    ```
3. LOWER( 字符串 ) ——小写转换
4. UPPER( 字符串 )
4. REPLACE( 对象字符串，替换前的字符串，替换后的字符串 )
5. SUBSTRING(对象字符串 FROM 截取的起始位置 FOR 截取的字符数)
    ```sql
    --PostgreSQL MySQL
    SELECT str1,
    SUBSTRING(str1 FROM 3 FOR 2) AS sub_str
    FROM SampleStr;

    --SQL Server
    SELECT str1,
    SUBSTRING(str1, 3, 2) AS sub_str
    FROM SampleStr

    --Oracle DB2
    SELECT str1,
    SUBSTR(str1, 3, 2) AS sub_str
    FROM SampleStr;
    ```

**日期函数**

**转换函数**
1. CAST(转换前的值 AS 想要转换的数据类型)

# 谓词——相当于c/oc中的谓词函数
```
● LIKE
● BETWEEN
● IS NULL、IS NOT NULL 
● IN
● EXISTS
```
**谓词**
1. =、<、>、<> 等比较运算符，其正式的名称就是比较谓词
2. 返回值是真值
3. 判断是否存在满足某种条件的记录

**LIKE——字符串的部分一致查询**
1.  _(下划线)代表了“任意 1 个字符”
2.  % 是代表“0 字符以上的任意字符串”的特殊符号

**BETWEEN——范围查询**
```sql
SELECT product_name, sale_price 
FROM Product
WHERE sale_price BETWEEN 100 AND 1000;

SELECT product_name, sale_price FROM Product
WHERE sale_price >= 100 
AND sale_price =< 1000;
```
1. BETWEEN 的特点就是结果中会包含临界值

**IS NULL、IS NOT NULL——判断是否为NULL**

**IN谓词——OR的简便用法**
```sql
SELECT product_name, purchase_price 
FROM Product
WHERE purchase_price IN (320, 500, 5000);

SELECT product_name, purchase_price 
FROM Product
WHERE purchase_price = 320 
OR purchase_price = 500 
OR purchase_price = 5000;


SELECT product_name, purchase_price FROM Product
WHERE purchase_price NOT IN (320, 500, 5000);
```

**使用子查询作为 IN 谓词的参数**
```sql
SELECT product_name, sale_price
FROM Product
WHERE product_id IN (SELECT product_id
                        FROM ShopProduct
                    WHERE shop_id = '000C');
```

**EXISTS 谓词**
* 不太好解释，意会一下好了
    ```sql
    --SQL Server DB2 PostgreSQL MySQL
    SELECT product_name, sale_price
        FROM Product AS P
    WHERE EXISTS (SELECT *
                        FROM ShopProduct AS SP
                    WHERE SP.shop_id = '000C'
                    AND SP.product_id = P.product_id);
    ```

# case
1. eg:
    ```sql
    CASE 
        WHEN <求值表达式> THEN <表达式> 
        WHEN <求值表达式> THEN <表达式> 
        WHEN <求值表达式> THEN <表达式>
        .
        .
        ELSE <表达式> 
    END


    SELECT product_name,
    CASE 
        WHEN product_type = '衣服' THEN 'A :' | | product_type 
        WHEN product_type = '办公用品' THEN 'B :' | | product_type 
        WHEN product_type = '厨房用具' THEN 'C :' | | product_type 
        ELSE NULL
    END AS abc_product_type 
    FROM Product;
    ```
2. CASE 表达式的便利之处就在于它是一个表达式。之所以这么说，是因为表达式可以书写在任意位置
    ```sql
    SELECT product_type, SUM(sale_price) AS sum_price
    FROM Product
    GROUP BY product_type;

    -- 对按照商品种类计算出的销售单价合计值进行行列转换 
    SELECT 
    SUM(CASE WHEN product_type = '衣服' THEN sale_price ELSE 0 END) AS sum_price_clothes, 
    SUM(CASE WHEN product_type = '厨房用具' THEN sale_price ELSE 0 END) AS sum_price_kitchen, 
    SUM(CASE WHEN product_type = '办公用品' THEN sale_price ELSE 0 END) AS sum_price_office
    FROM Product;
    ```

# 表的加减法 
1. 以行方向为单位进行操作
2. 通过集 合运算，可以得到两张表中记录的集合或者公共记录的集合，又或者其中 某张表中的记录的集合
2. 作为运算对象的记录的列数必须相同
3. 作为运算对象的记录中列的类型必须一致
4. 可以使用任何SELECT语句，但ORDER BY子句只 能在最后使用一次

**UNION(并集)**
1. UNION 等集合运算符 通常(UNION ALL)都会除去重复的记录。

**INTERSECT(交集)**
```sql
--Oracle SQL Server DB2 PostgreSQL
SELECT product_id, product_name FROM Product
INTERSECT
SELECT product_id, product_name
FROM Product2 
ORDER BY product_id;
```

**EXCEPT(差集)**
```sql
-- SQL Server DB2 PostgreSQL
SELECT product_id, product_name FROM Product
EXCEPT
SELECT product_id, product_name FROM Product2 
ORDER BY product_id;
```

# 联结 
1. 将其他表中的 列添加过来，进行“添加列”的运算

**内联结——INNER JOIN**

**eg:**

```sql
SELECT SP.shop_id, SP.shop_name, SP.product_id, P.product_name, P.sale_price
FROM ShopProduct AS SP 
    INNER JOIN Product AS P 
        ON SP.product_id = P.product_id;


SELECT 
    SP.shop_id, 
    SP.shop_name, 
    SP.product_id, 
    P.product_name, 
    P.sale_price, 
    IP.inventory_quantity
FROM 
    ShopProduct AS SP 
    INNER JOIN 
    Product AS P 
    ON SP.product_id = P.product_id
        INNER JOIN 
        InventoryProduct AS IP 
        ON SP.product_id = IP.product_id
WHERE IP.inventory_id = 'P001';
```
1. 进行联结时需要在 FROM 子句中使用多张表。
2. 进行内联结时必须使用 ON 子句，并且要书写在 FROM 和 WHERE 之间。
3. 可以 将联结之后的结果想象为新创建出来的一张表，然后WHERE、 GROUP BY、HAVING、ORDER BY等都按原来的使用方式
4. 内联结只能选取出同时存在于两张表中的数据

**外联结——OUTER JOIN**
1. 只要数据存在于某一张表当中，就能够读取出来

**主表**
1. 最终的结果中会包含主表内所有的数据
3. 指定主表的关键字是 LEFT 和 RIGHT

# 窗口函数(OLAP:OnLine Analytical Processing)

**eg:**

```sql
SELECT product_name, product_type, sale_price, 
    RANK () OVER (
                PARTITION BY product_type
                ORDER BY sale_price) AS ranking
FROM Product;
```

1. 并不是所有数据库都支持
2. 通过PARTITION BY分组后的记录集合称为窗口
3. 将数据进行分组，和Group by 不同，Group by结果被归成一行

**种类**
* RANK 函数
* DENSE_RANK 函数 
* ROW_NUMBER 函数

#  GROUPING 运算符
GROUPING 运算符包含以下 3 种。
* ROLLUP:ROLLUP 可以同时得出合计和小计
* CUBE 
* GROUPING SETS

```sql
SELECT product_type, SUM(sale_price) AS sum_price 
FROM Product
GROUP BY ROLLUP(product_type);
```

窗口函数和GROUPING 运算符都是比较新的内容，并不是所有数据库都支持。


# 语法顺序

**书写顺序**

`1.SELECT子句→2.FROM子句→3.WHERE子句→4.GROUP BY子句→ 5. HAVING 子句 → 6. ORDER BY 子句`

**执行顺序**

1. `FROM → WHERE → GROUP BY → HAVING → SELECT → ORDER BY`
2. 子查询是从内层开始执行的

# End

以上只是基础，看书的一点摘录。虽然不是BI，但在日常工作中还是经常会需要跑数据的。一般也不会有太复杂的需求。复杂的SQL，自己没把握的话，对跑出来的数据的准确性也没有信心，也不敢对外公布。

LeeCode上有几着题，可以练习一下： https://leetcode-cn.com/problemset/database/