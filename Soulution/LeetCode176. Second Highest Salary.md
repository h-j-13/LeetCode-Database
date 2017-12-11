# LeetCode176. Second Highest Salary

# 题目
Write a SQL query to get the second highest salary from the ```Employee``` table.
```
+----+--------+
| Id | Salary |
+----+--------+
| 1  | 100    |
| 2  | 200    |
| 3  | 300    |
+----+--------+
```
For example, given the above Employee table, the query should return 200 as the second highest salary. If there is no second highest salary, then the query should return null.
```
+---------------------+
| SecondHighestSalary |
+---------------------+
| 200                 |
+---------------------+
```

题目大意 : 找出第二高的薪水

## 解题思路

首先很容易想到,先WHERE salary != 最高的薪水 然后在做排序 或者 取最大值就可获得第二高的薪水,但是若是第三 第N 高的薪水则要迭代此方法数次 造成效率下降,在只select 一次表的情况 排序后使用limit选择第N条记录 不失为一个更高效的选择

### LIMIT 
```sql
SELECT Salary AS 'SecondHighestSalary' FROM Employee ORDER BY Salary DESC LIMIT 1,1;
```
![](http://upload-images.jianshu.io/upload_images/5617720-be6e1556f0609a31.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

结果在只有一条记录的时候出错了...

因为从第一条记录向下偏移了一条, 导致本该的null的变成了无结果
再加一层select 筛选即可 因为 select 的无结果默认值为null
最后别忘了加上DISTINCT 因为同一工资的人可能有很多

进行修改
```sql
SELECT (
    SELECT DISTINCT Salary AS 'SecondHighestSalary' FROM Employee ORDER BY Salary DESC LIMIT 1 OFFSET 1
) AS 'SecondHighestSalary';  
```

