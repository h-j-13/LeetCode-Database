# LeetCode 183. Customers Who Never Order

## 题目
Suppose that a website contains two tables, the Customers table and the Orders table. Write a SQL query to find all customers who never order anything.

Table: ```Customers```
```database
+----+-------+
| Id | Name  |
+----+-------+
| 1  | Joe   | 
| 2  | Henry | 
| 3  | Sam   | 
| 4  | Max   | 
+----+-------+
```
Table: ```Orders```

```database
+----+------------+
| Id | CustomerId |
+----+------------+
| 1  | 3          |
| 2  | 1          |
+----+------------+
```

Using the above tables as example, return the following:
```
+-----------+
| Customers |
+-----------+
| Henry     |
| Max       |
+-----------+
```

题目大意 : 
假设一个网站包含两个表， 顾客表Customers和订单表Orders。编写一个SQL查询找出所有从未下过订单的顾客。


### NOT IN
```sql
SELECT `Name` AS 'Customers' FROM Customers WHERE Id NOT IN (SELECT CustomerId FROM Orders)
``` 
*运行时间(约) : 648 ms*

### NOT EXISTS 
```sql
SELECT  `Name` AS 'Customers' FROM Customers WHERE Id NOT EXISTS (SELECT CustomerId FROM Orders)
``` 
*运行时间(约) : 603 ms*

### LEFT JOIN
```SQL
SELECT  `Name` AS 'Customers' FROM Customers LEFT JOIN Orders ON Customers.Id = Orders.CustomerId WHERE Orders.Id IS NULL
```
*运行时间(约) : 678 ms*

#### 有关 NOT IN 于 EXIST 的比较

* **EXISTS  / IN**
强调的是是否返回结果集，不要求知道返回什么, 比如：
```select name from student where sex = 'm' and mark exists(select 1 from grade where ...)``` ,只要**exists**引导的子句有结果集返回，那么exists这个条件就算成立了,大家注意返回的字段始终为1，如果改成“select 2 from grade where ...”，那么返回的字段就是2，这个数字没有意义。**所以exists子句不在乎返回什么，而是在乎是不是有结果集返回。**
EXISTS检查是否有结果，判断是否有记录，返回的是一个布尔型（TRUE/FALSE）。
**而 exists 与 in 最大的区别在于 in引导的子句只能返回一个字段**，
比如：
``select name from student where sex = 'm' and mark in (select 1,2,3 from grade where ...)`` ，in子句返回了三个字段，这是不正确的，exists子句是允许的，但in只允许有一个字段返回，在1，2，3中随便去了两个字段即可。
*通常情况下，EXISTS 的性能要比 IN 略好*

* **NOT EXISTS / NOT IN**
而not exists 和not in 分别是exists 和 in 的 对立面。
exists （sql 返回结果集为真） 
not exists (sql 不返回结果集为真）
 
* EXISTS与IN执行计划
exists：*先执行外表，然后与内表去匹配*。**适合于外表小，内表大**。
in：*先执行内表，然后与外表去匹配*。**适合于外表大，内表小**。

以下参考 http://www.cnblogs.com/seasons1987/archive/2013/07/03/3169356.html

**1、in和exists**
in是把外表和内表作hash连接，而exists是对外表作loop循环，每次loop循环再对内表进行查询，一直以来认为exists比in效率高的说法是不准确的。如果查询的两个表大小相当，那么用in和exists差别不大；如果两个表中一个较小一个较大，则子查询表大的用exists，子查询表小的用in；
例如：**表A(小表)，表B(大表)**

```sql
select * from A where cc in(select cc from B)　　-->效率低，用到了A表上cc列的索引；
select * from A where exists(select cc from B where cc=A.cc)　　-->效率高，用到了B表上cc列的索引。
```
相反的：
```sql
select * from B where cc in(select cc from A)　　-->效率高，用到了B表上cc列的索引
select * from B where exists(select cc from A where cc=B.cc)　　-->效率低，用到了A表上cc列的索引。
```

 
**2、not in 和not exists**
```sql
create table #t1(c1 int,c2 int);
create table #t2(c1 int,c2 int);

insert into #t1 values(1,2);
insert into #t1 values(1,3);
insert into #t2 values(1,2);
insert into #t2 values(1,null);

select * from #t1 where c2 not in(select c2 from #t2);　　-->执行结果：无
select * from #t1 where not exists(select 1 from #t2 where #t2.c2=#t1.c2)　　-->执行结果：1　　3
```
以上内容基于 Oracle

正如所看到的，not in出现了不期望的结果集，存在逻辑错误。如果看一下上述两个select 语句的执行计划，也会不同，后者使用了hash_aj，所以，**请尽量不要使用not in(它会调用子查询)，而尽量使用not exists（它会调用关联子查询）**。如果子查询中返回的任意一条记录含有空值，则查询将不返回任何记录。如果子查询字段有非空限制，这时可以使用not in，并且可以通过提示让它用hasg_aj或merge_aj连接。
**如果查询语句使用了not in，那么对内外表都进行全表扫描，没有用到索引**；
**而not exists的子查询依然能用到表上的索引。所以无论哪个表大，用not exists都比not in 要快。**
 
**3、in 与 = 的区别**
```sql
select name from student where name in('zhang','wang','zhao');
```
与
```sql
select name from student where name='zhang' or name='wang' or name='zhao'
```
的结果是相同的。
