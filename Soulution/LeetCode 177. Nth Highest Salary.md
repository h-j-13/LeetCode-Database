# LeetCode 177. Nth Highest Salary

## 题目

Write a SQL query to get the nth highest salary from the ```Employee``` table.

```
+----+--------+
| Id | Salary |
+----+--------+
| 1  | 100    |
| 2  | 200    |
| 3  | 300    |
+----+--------+
```

For example, given the above Employee table, the nth highest salary where n = 2 is 200. If there is no nth highest salary, then the query should return null.

```
+------------------------+
| getNthHighestSalary(2) |
+------------------------+
| 200                    |
+------------------------+
```

## 题目大意

写一个SQL函数来从表中获得第n 个最高工资

## 解题思路

## SQL 函数

```sql
CREATE FUNCTION getNthHighestSalary(N INT) 
RETURNS INT  -- 返回数据类型
BEGIN -- 函数体开始
-- 声明 : 变量 类型
DECLARE  M INT;
-- 变量赋值
SET M=N-1;

  RETURN (
       SELECT DISTINCT Salary FROM Employee ORDER BY Salary DESC LIMIT M,1);
 );  -- 返回这条语句的结果
END
```

SQL 语句并没有什么好说的,这里要详细学习一个新的概念,SQL 中的函数

[MySQL 5.7 CREATE FUNCTION Syntax for User-Defined Functions](https://dev.mysql.com/doc/refman/5.7/en/create-function-udf.html)

## MySQL 自定义函数详解

详见下文
[MySQL 自定义函数详解](http://blog.csdn.net/maoyuanming0806/article/details/77623323)

注意区分MySQL中自定义函数与存储过程的区别

[MySQL存储过程/存储过程与自定义函数的区别](http://www.cnblogs.com/caoruiy/p/4486249.html)