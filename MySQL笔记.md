---

typora-root-url: Typora图库\MySQL必知必会笔记
---

目录：
[TOC]
# MySQL必知必会

## 了解SQL

1. 数据库：一种以某种有组织的方式存储的数据集合。
2. 表：某种特定类型数据的结构化清单。
3. 模式：关于数据库和表的布局及特性的信息。
4. 列：表中的一个字段。所有表都是由一个或多个列组成的。
5. 数据类型：所容许的数据的类型。每个表列都有相应的数据类型，它限制（或容许）该列中存储的数据。
6. 行：表中的一个记录。
7. 主键：一列（或一组列），其值能够唯一区分表中每个行。

## 使用MySQL

```
USE databases;//选择数据库

SHOW DATABASES;//返回可用数据库的一个列表

SHOW TABLES;//返回当前选择的数据库内可用表的列表

show columns from tablename;//对每个字段返回一行，行中包含字段名、数据类型、是否允许NULL、键信息、默认值以及其他信息

DESCRIBE TABLENAME;//用DESCRIBE作为SHOW COLUMNS FROM的一种快捷方式

SHOW STATUS;//用于显示广泛的服务器状态信息

SHOW CREATE DATABASE databasename;//显示创建特定数据库

SHOW CREATE TABLE tablename;//显示创建特定表

SHOW GRANTS;//用来显示授予用户（所有用户或特定用户）的安全权限

SHOW ERRORS;//显示服务器错误

SHOW WARNINGS;//显示服务器警告消息
```
自动增量(auto_increment):某些表列需要唯一值。在每个行添加到表中时，MySQL可以自动地为每个行分配下一个可用编号，不用在添加一行时手动分配唯一值。如果需要它，则必须在**用CREATE语句创建表时把它作为表定义的组成部分**。

### 检索数据

```
select column from tablename;//检索单个列;如果没有明确排序查询结果,则返回的数据的顺序没有特殊意义。返回数据的顺序可能是数据被添加到表中的顺序，也可能不是。只要返回相同数目的行，就是正常的。

select column１,column2 from tablename;//检索多个列

select * from tablename;//检索所有列

select distinct column from tablename;//只返回不同值，即剔除重复的数据

select column form tablename limit 5;//限制语句，不多于5行

select column form tablename limit 5，5;//限制语句，指示MySQL返回从行5开始的5行。第一个数为开始位置，第二个数为要检索的行数.所以，带一个值的LIMIT总是从第一行开始，给出的数为返回的行数。注意：第一行的行号为0
```
SQL语句不区分大小写;
多条SQL语句必须以分号（；）分隔;
在处理SQL语句时，其中所有空格都被忽略;

通配符：一般，除非你确实需要表中的每个列，否则最好别使用\*通配符。虽然使用通配符可能会使你自己省事，不用明确列出所需列，但检索不需要的列通常会降低检索和应用程序的性能。
检索未知列:使用通配符有一个大优点。由于不明确指定列名（因为星号检索每个列），所以能检索出名字未知的列。

不能部分使用DISTINCT：DISTINCT关键字应用于所有列而不仅是前置它的列。如果给出SELECT DISTINCT vend_id,prod_price，除非指定的两个列都不同，否则所有行都将被检索出来。

行0：检索出来的第一行为行0而不是行1。
在行数不够时：LIMIT中指定要检索的行数为检索的最大行数。如果没有足够的行，MySQL将只返回它能返回的那么多行。

### 排序检索数据

其实，检索出的数据并不是以纯粹的随机顺序显示的。如果不排序，数据一般将以它在底层表中出现的顺序显示。这可以是数据最初添加到表中的顺序。但是，如果数据后来进行过更新或删除，则此顺序将会受到MySQL重用回收存储空间的影响。因此，如果不明确控制的话，不能（也不应该）依赖该排序顺序。
```
select column from tablename order by column;//用选择的列或非选择的列排序都是合法的。默认升序排列.

select column from tablename order by column DESC;//desc关键字降序，如果多个列排序，desc只应用到直接位于其前面的列名。

select column from tablename order by column1，column2;//按多个列排序，先按第一个排序，如果重复则继续按第二个排序。

select * from XXX order by limit 1;//可以找到最大值
```
在多个列上降序排序:如果想在多个列上进行降序排序，必须对每个列指定DESC关键字。
在字典（dictionary）排序顺序中，A被视为与a相同

### 过滤数据

```
select column from table where name ='zhangsan' order by ..;//指定搜索条件只检索所需要的数据

select colum1, colum2 from table where colum2 between 1 and 5;//指定两个值:所需范围的低端值和高端值。BETWEEN匹配范围中所有的值，包括指定的开始值和结束值。

select column from table where column is null;//空值检查
```
![Snipaste_2020-04-24_11-41-10](/Snipaste_2020-04-24_11-41-10.png)

NULL:无值（no value），它与字段包含0、空字符串或仅仅包含空格不同。
在通过过滤选择出不具有特定值的行时，你可能希望返回具有NULL值的行。但是，不行。因为未知具有特殊的含义，数据库不知道它们是否匹配，所以在匹配过滤或不匹配过滤时不返回它们。

```
select A from table where B=2 and C<>5;//条件交集

select A from table where B=2 or C<>5;//条件并集

select A from table where B in (2,4,6) order by ..;//IN操作符用来指定条件范围，范围中的每个条件都可以进行匹配。IN操作符完成与OR相同的功能.

Select A from table where B not in (2,4);//否定之后所跟的任何条件
```
where可以包含任意数目的AND和OR操作，允许两者结合以进行复杂和高级的过滤。但是AND的优先级比OR的优先级高，所以需要**使用圆括号**来保证计算次序。不要依赖默认的计算次序。

IN操作符优点具体如下:
- 在使用长的合法选项清单时，IN操作符的语法更清楚且更直观。
- 在使用IN时，计算的次序更容易管理（因为使用的操作符更少）。
- IN操作符一般比OR操作符清单执行更快。
- IN的最大优点是可以包含其他SELECT语句，使得能够更动态地建立WHERE子句。

LIKE操作符(谓词):利用通配符可以创建比较特定的数据搜索模式。
```
select * from table where name like 'zhang%';// %表示任何字符出现任意次数.通过 % 可以组合多种搜索模式；但是 % 不能匹配 NULL值
select * from table where name like '_hang';// _ 只匹配单个字符
```
通配符可在搜索模式中任意位置使用，并且可以使用多个通配符。重要的是要注意到，除了一个或多个字符外，%还能匹配0个字符。%代表搜索模式中给定位置的0个、1个或多个字符。

注意：
- 不要过度使用通配符。如果其他操作符能达到相同的目的，应该使用其他操作符。
- 在确实需要使用通配符时，除非绝对有必要，否则不要把它们用在搜索模式的开始处。
- 仔细注意通配符的位置。如果放错地方，可能不会返回想要的数据。

### 用正则表达式进行搜索

REGEXP操作符：用正则表达式进行搜索
```
select prod_name from products where prod_name regexp '.000' order by prod_name;//. 正则表达式语言中一个特殊的字符。它表示匹配任意一个字符.因此，1000和2000都匹配且返回。

select prod_name from products where prod_name regexp '1000|2000' order by prod_name;//| 为正则表达式的OR操作符。它表示匹配其中之一，因此1000和2000都匹配并返回

select prod_name from products where prod_name regexp '[123] Ton' order by prod_name;//[]是另一种形式的OR语句。事实上，正则表达式[123]Ton为[1|2|3]Ton的缩写

select prod_name from products where prod_name regexp '[^123] Ton' order by prod_name;//将匹配除指定字符外的任何东西。[123]匹配字符1、2或3，但[^123]却匹配除这些字符外的任何东西

select prod_name from products where prod_name regexp '[1-5] Ton' order by prod_name;//[1-5]定义了一个范围，这个表达式意思是匹配1到5

select prod_name from products where prod_name regexp '\\.' order by prod_name;//为了匹配特殊字符，必须用\\为前导
```
在LIKE和REGEXP之间有一个重要的差别：
```
SELECT prod_id , prod_name FROM products WHERE prod_name LIKE '1000';//返回空
SELECT prod_id , prod_name FROM products WHERE prod_name REGEXP'1000';//返回一行
```
LIKE匹配整个列。如果被匹配的文本仅在列值中出现，LIKE并不会找到它，相应的行也不会返回（当然，使用通配符除外）。而REGEXP在列值内进行匹配，如果被匹配的匹配的文本在列值中出现，REGEXP将会找到它，相应的行将被返回，这时一个非常重要的差别（当然，如果适应定位符号^和$，可以实现REGEXP匹配整个列而不是列的子集）。
使REGEXP起类似LIKE的作用:前面说过，LIKE和REGEXP的不同在于，LIKE匹配整个串而REGEXP匹配子串。利用定位符，通过用^开始每个表达式，用$结束每个表达式，可以使REGEXP的作用与LIKE一样。

MySQL中的正则表达式匹配不区分大小写（即，大写和小写都匹配）。
为区分大小写，可使用BINARY关键字
```
WHERE prod_name REGEXP BINARY 'JetPack .000'
```

正则表达式内具有特殊意义的所有字符都必须以\\转义。这包括.、|、[]以及迄今为止使用过的其他特殊字符。
为了匹配反斜杠（\）字符本身，需要使用\\\。

预定义的字符集:
![Snipaste_2020-04-24_14-49-33](/Snipaste_2020-04-24_14-49-33.png)

匹配多个实例:![Snipaste_2020-04-24_14-51-05](/Snipaste_2020-04-24_14-51-05.png)

定位符:![Snipaste_2020-04-24_14-52-16](/Snipaste_2020-04-24_14-52-16.png)

的双重用途:有两种用法。在集合中（用[和]定义），用它来否定该集合，否则，用来指串的开始处。

### 创建计算字段

计算字段:存储在数据库表中的数据一般不是应用程序所需要的格式。我们需要直接从数据库中检索出转换、计算或格式化过的数据；而不是检索出数据，然后再在客户机应用程序或报告程序中重新格式化。
重要的是要注意到，只有数据库知道SELECT语句中哪些列是实际的表列，哪些列是计算字段。从客户机（如应用程序）的角度来看，计算字段的数据是以与其他列的数据相同的方式返回的。

```
select Contact(vend_name, '(',vend_country,')') from table;//使用Concat()函数实现拼接。输出类似于:ACME(USA)。Concat()需要一个或多个指定的串，各个串之间用逗号分隔。

select Contact(vend_name, '(',vend_country,')') as vend_title from table;//AS关键字赋予别名,指示SQL创建一个包含指定计算的名为vend_title的计算字段。从输出中可以看到，结果与以前的相同，但现在列名为vend_title.

select A, B*C as BC from table;//创建一个新的计算字段，命名为BC，值为B*C
```
别名有时也称为导出列.
MySQL支持基本算术操作符为：+，-，*，/

### 使用数据处理函数

大多数SQL实现支持以下类型的函数：
- 用于处理文本串（如删除或填充值，转换值为大写或小写）的文本函数。
- 用于在数值数据上进行算术操作（如返回绝对值，进行代数运算）的数值函数。
- 用于处理日期和时间值并从这些值中提取特定成分（例如，返回两个日期之差，检查日期有效性等）的日期和时间函数。
- 返回DBMS正使用的特殊信息（如返回用户登录信息，检查版本细节）的系统函数。

#### 文本处理函数

常用的文本处理函数：
函数 | 说明
-- | --
Left()|返回串左边的字符
Length()|返回串的长度
Locate()|找出串的一个子串
Lower()|将串转换为小写
LTrim()|去掉串左边的空格
Right()|返回串右边的字符
RTrim()|去掉串右边的空格
Soundex()|返回串的SOUNDEX值
SubString()|返回子串的字符
Upper()|将串转换为大写

使用：
```
select RTrim(vend_name) from table;//RTrim()函数删除数据右侧多余的空格.

select Upper(vend_name) as vend_name_upcase from table;//Upper()将文本转换为大写

select cust_name, cust_contact from customers where Soundex(cust_contact) = Soundex('Y Lie');//SOUNDEX是一个将任何文本串转换为描述其语音表示的字母数字模式的算法。SOUNDEX考虑了类似的发音字符和音节，使得能对串进行发音比较而不是字母比较。该例使用Soundex()函数进行搜索，它匹配所有发音类似于Y.Lie的联系名
```
MySQL除了支持RTrim(),还支持LTrim()（去掉串左边的空格）以及Trim()（去掉串左右两边的空格）。

#### 日期和时间处理函数

常用日期和时间处理函数
函数|说明
--|--
AddDate() |增加一个日期（天、周等）
AddTime()| 增加一个时间（时、分等）
CurDate() |返回当前日期
CurTime() |返回当前时间
Date() |返回日期时间的日期部分
DateDiff() |计算两个日期之差
Date_Add() |高度灵活的日期运算函数
Date_Format() |返回一个格式化的日期或时间串
Day() |返回一个日期的天数部分
DayOfWeek() |对于一个日期，返回对应的星期几
Hour() |返回一个时间的小时部分
Minute() |返回一个时间的分钟部分
Month() |返回一个日期的月份部分
Now() |返回当前日期和时间
Second() |返回一个时间的秒部分
Time() |返回一个日期时间的时间部分
Year() |返回一个日期的年份部分

首先需要注意的是MySQL使用的日期格式。无论你什么时候指定一个日期，不管是插入或更新表值还是用WHERE子句进行过滤，日期必须为格式yyyy-mm-dd。因此，2005年9月1日，给出为2005-09-01。虽然其他的日期格式可能也行，但这是首选的日期格式，因为它排除了多义性。
应该总是使用4位数字的年份：支持2位数字的年份，MySQL处理00-69为2000-2069，处理70-99为1970-1999。虽然它们可能是打算要的年份，但使用完整的4位数字年份更可靠，因为MySQL不必做出任何假定。

```
select cust_id,order_num from orders where Date(order_date) = '2018-09-01';//如果要的是日期，请使用Date()，这样才能对应的是datetime类型

select cust_id,order_num from orders where Date(order_date) between '2018-09-01' and '2018-09-30';//匹配的日期范围

select cust_id,order_num from orders where Year(order_date) = 2019 and Month(order_date) = 9;//检索出order_date为2019年9月的所有行。
```
#### 数值处理函数

常用数值处理函数
函数|说明
--|--
Abs() |返回一个数的绝对值
Cos() |返回一个角度的余弦
Exp() |返回一个数的指数值
Mod() |返回除操作的余数
Pi() |返回圆周率
Rand() |返回一个随机数
Sin() |返回一个角度的正弦
Sqrt() |返回一个数的平方根
Tan() |返回一个角度的正切

#### 汇总数据

SQL聚集函数
函数|说明
--|--
AVG() |返回某列的平均值
COUNT() |返回某列的行数
MAX() |返回某列的最大值
MIN() |返回某列的最小值
SUM() |返回某列值之和

```
select count(*) as num_cust from products;//对表中行的数目进行计数，不管表列中包含的是空值（NULL）还是非空值
select count(prod_price) as num_cust from products;//对特定列中具有值的行进行计数，忽略NULL值

select sum(prod_price) as sum_price from products;//返回订单中所有物品价格之和
select sum(prod_price*quantity) as sum_price from products;//返回订单中所有物品价钱之和,得出总的订单金额

select avg(prod_price) as avg_price from products;//用来确定特定列或行的平均值
select avg(distinct prod_price) as avg_price from products;//只包含不同的值

select count(*) as num_items, min(prod_price) as price_min, max(prod_price) as price_max, avg(prod_price) as price_avg from products;//用单条SELECT语句执行了4个聚集计算，返回4个值（products表中物品的数目，产品价格的最高、最低以及平均值）。
```
AVG()只能用来确定特定数值列的平均值，而且列名必须作为函数参数给出。为了获得多个列的平均值，必须使用多个AVG()函数。AVG()函数忽略列值为NULL的行。

虽然MAX()一般用来找出最大的数值或日期值，但MySQL允许将它用来返回任意列中的最大值，包括返回文本列中的最大值。在用于文本数据时，如果数据按相应的列排序，则MAX()返回最后一行。MAX()函数忽略列值为NULL的行。
MIN()函数与MAX()函数类似，MySQL允许将它用来返回任意列中的最小值，包括返回文本列中的最小值。在用于文本数据时，如果数据按相应的列排序，则MIN()返回最前面的行。MIN()函数忽略列值为NULL的行。

如果指定列名，则DISTINCT只能用于COUNT()。DISTINCT不能用于COUNT(\*)，因此不允许使用COUNT（DISTINCT），否则会产生错误。类似地，DISTINCT必须使用列名，不能用于计算或表达式。

#### 分组数据

分组允许把数据分为多个逻辑组，以便能对每个组进行聚集计算。
```
select vend_id, count(*) as num_prods from products group by vend_id;//GROUP BY子句指示MySQL按vend_id排序并分组数据,这导致对每个vend_id而不是整个表计算num_prods一次
select vend_id, count(*) as num_prods from products group by vend_id with rollup;//将group by分组后的结果进行分类汇总(最下面添加一行总计之类的)

select vend_id, count(*) as num_prods from products group by vend_id having count(*) >= 2;//过滤是基于分组聚集值
select vend_id, count(*) as num_prods from products where prod_price >= 10 group by vend_id having count(*) >= 2;//WHERE子句过滤所有prod_price至少为10的行。然后按vend_id分组数据，HAVING子句过滤计数为2或2以上的分组。

select vend_id, count(*) as num_prods from products where prod_price >= 10 group by vend_id having count(*) >= 2 order by num_prods;//以ORDER BY子句排序输出
```
在具体使用GROUP BY子句前，需要知道一些重要的规定:
- GROUP BY子句可以包含任意数目的列。这使得能对分组进行嵌套，为数据分组提供更细致的控制。
- 在建立分组时，指定的所有列都一起计算（所以不能从个别的列取回数据）。
- GROUP BY子句中列出的每个列都必须是检索列或有效的表达式（但不能是聚集函数）.如果在SELECT中使用表达式，则必须在GROUP BY子句中指定相同的表达式。不能使用别名。
- 除聚集计算语句外，SELECT语句中的每个列都必须在GROUP BY子句中给出。
- 如果分组列中具有NULL值，则NULL将作为一个分组返回。
- **GROUP BY子句必须出现在WHERE子句之后，ORDER BY子句之前。**

HAVING非常类似于WHERE。事实上，目前为止所学过的所有类型的WHERE子句都可以用HAVING来替代。**唯一的差别是WHERE过滤行，而HAVING过滤分组**。

ORDER BY |GROUP BY
--|--
排序产生的输出| 分组行。但输出可能不是分组的顺序
任意列都可以使用（甚至非选择的列也可以使用）|只可能使用选择列或表达式列，而且必须使用每个选择列表达式
不一定需要| 如果与聚集函数一起使用列（或表达式），则必须使用

列出的第一项差别极为重要。我们经常发现用GROUP BY分组的数据确实是以分组顺序输出的。但情况并不总是这样，它并不是SQL规范所要求的。
一般在使用GROUP BY子句时，应该也给出ORDER BY子句。这是保证数据正确排序的唯一方法。千万不要仅依赖GROUP BY排序数据。

==SELECT子句及其顺序==
子句|说明|是否必须使用
--|--|--
SELECT |要返回的列或表达式| 是
FROM| 从中检索数据的表| 仅在从表选择数据时使用
WHERE| 行级过滤 |否
GROUP BY |分组说明 |仅在按组计算聚集时使用
HAVING |组级过滤 |否
ORDER BY| 输出排序顺序| 否
LIMIT |要检索的行数| 否

#### 使用子查询

```
select cust_id from orders where order_num in (select order_num from orderitems where prod_id = 'TNT2');//检索包含物品TNT2的所有订单的所有客户的ID

select cust_name, cust_state(select count(*) from orders where orders.cust_id = customers.cust_id) as orders from customers order by cust_name;//这条SELECT 语句对customers 表中每个客户返回3 列：cust_name、cust_state和orders
```
在实际使用时由于性能的限制，不能嵌套太多的子查询。
在WHERE子句中使用子查询（如这里所示），应该保证SELECT语句具有与WHERE子句中相同数目的列。

### 联结表
```
select vend_name, prod_name, prod_price from vendors, products where vendors.vend_id = products.vend_id order by vend_name, prod_name;//联结表举例
select vend_name, prod_name, prod_price from vendors inner join products on vendors.vend_id = products.vend_id;//与上一条的SELECT语句相同
```
外键：外键为某个表中的一列，它包含另一个表的主键值，定义了两个表之间的关系。

笛卡儿积：由没有联结条件的表关系返回的结果为笛卡儿积。检索出的行的数目将是第一个表中的行数乘以第二个表中的行数。

内部联结：目前为止所用的联结称为等值联结，它基于两个表之间的相等测试。这种联结也称为内部联结。SQL对一条SELECT语句中可以联结的表的数目没有限制。创建联结的基本规则也相同。首先列出所有表，然后定义表之间的关系。

### 创建高级联结
```
select vend_name, prod_name, prod_price from vendors as v, products as p where v.vend_id = p.vend_id order by vend_name, prod_name;//使用表别名
```
使用不同类型的联结
```
select p1.prod_id, p1.prod_name from p1, p2 where p1.vend_id = p2.vend_id and p2.prod_id = 'DTNTR';//自联结
select prod_id, prod_name from products where vend_id = (select vend_id from products where prod_id = 'DTNTR');//使用子联结，等同于上一条使用自联结

select c.*, o.order_num, oi.prod_id, oi.item_price from c, o, oi where c.cust_id = o.cust_id and oi.order_num = o.order_num and prod_id = 'FB';//自然联结，通配符只对第一个表使用。所有其他列明确列出，所以没有重复的列被检索出来。

select c.cust_name, c.cust_id, count(o.order_num) as num_ord from c inner join orders on c.cust_id = o.cust_id group by c.cust_id;//使用带聚集函数的联结
```
用自联结而不用子查询：自联结通常作为外部语句用来替代从相同表中检索数据时使用的子查询语句。虽然最终的结果是相同的，但有时候处理联结远比处理子查询快得多。应该试一下两种方法，以确定哪一种的性能更好。

自然联结排除多次出现，使每个列只返回一次。
怎样完成这项工作呢？自然联结是这样一种联结，其中你只能选择那些唯一的列。这一般是通过对表使用通配符（SELECT \*），对所有其他表的列使用明确的子集来完成的。

外部联结：许多联结将一个表中的行与另一个表中的行相关联。但有时候会需要包含没有关联行的那些行。

### 组合查询
可用UNION操作符来组合数条SQL查询。利用UNION，可给出多条SELECT语句，将它们的结果组合成单个结果集。
```
select vend_id, prod_id, prod_price from products where prod_price <= 5 union select vend_id, prod_id, prod_price from products where vend_id in (1001, 1002);//UNION指示MySQL执行两条SELECT语句，并把输出组合成单个查询结果集。
```
UNION规则:
- UNION必须由两条或两条以上的SELECT语句组成，语句之间用关键字UNION分隔
- UNION中的每个查询必须包含相同的列、表达式或聚集函数
- 列数据类型必须兼容：类型不必完全相同，但必须是DBMS可以隐含地转换的类型

UNION从查询结果集中自动去除了重复的行。
如果想返回所有匹配行，可使用UNION ALL而不是UNION。
在用UNION组合查询时，只能使用一条ORDER BY子句，它必须出现在最后一条SELECT语句之后。

### 全文本搜索
并非所有引擎都支持全文本搜索。两个最常使用的引擎为MyISAM和InnoDB，前者支持全文本搜索，而后者不支持。
一般在创建表时启用全文本搜索。。CREATE TABLE语句接受FULLTEXT子句。
```
create table productnotes
(
node_id int not null auto_increment,
prod_id char(10) not null,
note_date datetime not null,
note_text text null,
primary key(note_id),
fulltext(note_text)
)engine=myisam;
//这些列中有一个名为note_text的列，为了进行全文本搜索,MySQL根据子句FULLTEXT(note_text)的指示对它进行索引.

select note_text from productnotes where match(note_text) against('rabbit');//Match()指定被搜索的列，Against()指定要使用的搜索表达式。Match(note_text)指示MySQL针对指定的列进行搜索，Against('rabbit')指定词rabbit作为搜索文本。

select note_text from productnotes where match(note_text) against('anvils' with query expansion);//查询扩展。第一行包含词anvils，因此等级最高。第二行与anvils无关，但因为它包含第一行中的两个词（customer和recommend），所以也被检索出来。第3行也包含这两个相同的词，但它们在文本中的位置更靠后且分开得更远，因此也包含这一行，但等级为第三。第三行确实也没有涉及anvils（按它们的产品名）。
```
在定义之后，MySQL自动维护该索引。在增加、更新或删除行时，索引随之自动更新。
不要在导入数据时使用FULLTEXT：应该首先导入所有数据，然后再修改表，定义FULLTEXT。

搜索不区分大小写：除非使用BINARY方式，否则全文本搜索不区分大小写。

查询扩展：用来设法放宽所返回的全文本搜索结果的范围。举例：你想找出所有提到anvils的注释。只有一个注释包含词anvils，但你还想找出可能与你的搜索有关的所有其他行，即使它们不包含词anvils。
查询扩展极大地增加了返回的行数，但这样做也增加了你实际上并不想要的行的数目。

