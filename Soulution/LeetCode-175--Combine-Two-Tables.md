# LeetCode 175. Combine Two Tables

## 题目
**Table: Person**
```
+-------------+---------+
| Column Name | Type    |
+-------------+---------+
| PersonId    | int     |
| FirstName   | varchar |
| LastName    | varchar |
+-------------+---------+
```
**PersonId** is the **primary key** column for this table.
**Table: Address**
```
+-------------+---------+
| Column Name | Type    |
+-------------+---------+
| AddressId   | int     |
| PersonId    | int     |
| City        | varchar |
| State       | varchar |
+-------------+---------+
```
**AddressId** is the **primary key** column for this table.

*Write a SQL query for a report that provides the following information for each person in the Person table, regardless if there is an address for each of those people:*

*FirstName, LastName, City, State*

题目大意 : 编写一个SQL查询，对于Person表中的每一个人，取出FirstName, LastName, City, State属性，无论其地址信息是否存在。 

## 解题思路
* **LEFT [OUT] JOIN**
表```Person``` 与表 ```Address``` 作左外连接查询
```sql
SELECT Person.FirstName, Person.LastName, Address.City, Address.State 
FROM Person LEFT JOIN Address ON Person.PersonId=Address.PersonId
```

## LEFT JOIN 官方文档
[LEFT JOIN and RIGHT JOIN](https://dev.mysql.com/doc/refman/5.7/en/left-join-optimization.html)
