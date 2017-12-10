# LeetCode 181. Employees Earning More Than Their Managers

## 题目
The **Employee** table holds all employees including their managers. Every employee has an Id, and there is also a column for the manager Id.
```database
+----+-------+--------+-----------+
| Id | Name  | Salary | ManagerId |
+----+-------+--------+-----------+
| 1  | Joe   | 70000  | 3         |
| 2  | Henry | 80000  | 4         |
| 3  | Sam   | 60000  | NULL      |
| 4  | Max   | 90000  | NULL      |
+----+-------+--------+-----------+
```
Given the **Employee** table, write a SQL query that finds out employees who earn more than their managers. For the above table, Joe is the only employee who earns more than his manager.

```database
+----------+
| Employee |
+----------+
| Joe      |
+----------+
```

题目大意 : 
雇员表记录了所有雇员的信息，包括他们的经理在内。每一个雇员都有一个Id，和他的经理的Id。

给定雇员表，编写一个SQL查询找出薪水大于经理的员工姓名。对于上表来说，Joe是唯一收入大于经理的员工。



## 解题思路
*注意通过as操作将结果字段名设置为Employee*
* 双表条件查询 *(双表笛卡尔积)*
```sql
SELECT a.NAME AS Employee FROM Employee a, Employee b 
WHERE a.ManagerId = b.Id AND a.Salary > b.Salary
```

* 自连接 *(只返回连接条件的数据)*
```sql
SELECT SELECT a.NAME FROM Employee a INNER JOIN  Employee b
ON a.ManagerId = b.Id AND a.Salary > b.Salary
```

*两种方法速度差别不大*

## 相关学习文档
[MySQL官方文档 JOIN 规范](https://dev.mysql.com/doc/refman/5.7/en/join.html)
[INNER JOIN,LEFT JION,RIGHT JION 三种表连接方法比较](http://www.cnblogs.com/pcjim/articles/799302.html)

以下内容摘自 : http://blog.csdn.net/scythe666/article/details/51881235

以下图两张表举例
![Table A 是左边的表。Table B 是右边的表。](http://upload-images.jianshu.io/upload_images/5617720-ba39fb2cc54ae1cd.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

1.**INNER JOIN** 产生的结果是AB的**交集**
```sql
SELECT * FROM TableA INNER JOIN TableB ON TableA.name = TableB.name
```
![INNER JOIN 产生的结果是AB的交集](http://upload-images.jianshu.io/upload_images/5617720-659aadb32e8e0e48.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)          
![INNER JOIN 产生的结果是AB的交集](http://upload-images.jianshu.io/upload_images/5617720-128f955fabed49e1.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
2.**LEFT [OUTER] JOIN** 产生表A的完全集，而B表中匹配的则有值，**没有匹配的则以null值取代**。
```sql
SELECT * FROM TableA LEFT OUTER JOIN TableB ON TableA.name = TableB.name!
```
![LEFT [OUTER] JOIN 产生表A的完全集，而B表中匹配的则有值](http://upload-images.jianshu.io/upload_images/5617720-e94dd10c25bcf3fe.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

3.**RIGHT [OUTER] JOIN **产生表B的完全集，而A表中匹配的则有值，没有匹配的则以null值取代。
```sql
SELECT * FROM TableA RIGHT OUTER JOIN TableB ON TableA.name = TableB.name
```
图表示如left join类似。

4.**FULL [OUTER] JOIN **产生A和B的**并集**。*对于没有匹配的记录，则会以null做为值*。
```sql
SELECT * FROM TableA FULL OUTER JOIN TableB ON TableA.name = TableB.name 
```
*你可以通过is NULL将没有匹配的值找出来：*
```sql
SELECT * FROM TableA FULL OUTER JOIN TableB ON TableA.name = TableB.nameWHERE TableA.id IS null OR TableB.id IS null 
```
 ![FULL [OUTER] JOIN 产生A和B的并集](http://upload-images.jianshu.io/upload_images/5617720-4dc8f1cf420b7dd8.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)           ![FULL [OUTER] JOIN 产生A和B的并集](http://upload-images.jianshu.io/upload_images/5617720-f29d7db412bb606a.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

5. **CROSS JOIN** 把表A和表B的数据进行一个N\*M的组合，即**笛卡尔积**。如本例会产生4*4=16条记录，在开发过程中我们肯定是要过滤数据，所以这种很少用。
