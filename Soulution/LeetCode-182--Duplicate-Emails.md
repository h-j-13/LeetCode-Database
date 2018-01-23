# LeetCode 182. Duplicate Emails

### 题目
Write a SQL query to find all duplicate emails in a table named Person.

```
+----+---------+
| Id | Email   |
+----+---------+
| 1  | a@b.com |
| 2  | c@d.com |
| 3  | a@b.com |
+----+---------+
```
For example, your query should return the following for the above table:

```
+---------+
| Email   |
+---------+
| a@b.com |
+---------+
```
Note: All emails are in lowercase.

题目大意:编写一个SQL查询从Person表中找出所有重复的邮箱地址。

## 解题思路
* GROUP BY HAVING
```SQL
SELECT `Email` FROM `Person` GROUP BY `Email` HAVING COUNT(*) > 1 
```

* 做笛卡尔积

1. DISTINCT
```SQL
 SELECT DISTINCT t1.`Email`  FROM `Person` AS t1, `Person` AS  t2   WHERE t1.id != t2.id and t1.`Email` = t2.`Email` ```
```
  
2. INNER JOIN

```SQL
SELECT DISTINCT p.Email FROM Person p INNER JOIN Person q ON p.Id != q.Id AND p.Email = q.Email;
```

*测试下来使用group by 会更快一点*

## GROUP BY HAVING 官方文档 

[GROUP BY](https://dev.mysql.com/doc/refman/5.7/en/group-by-functions.html) 数据分组(聚合)
[HAVING](https://dev.mysql.com/doc/refman/5.7/en/group-by-modifiers.html) 数据聚合后的筛选
