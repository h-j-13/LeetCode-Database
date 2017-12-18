# LeetCode 185. Department Top Three Salaries

## 题目
The Employee table holds all employees. Every employee has an Id, and there is also a column for the department Id.
```
+----+-------+--------+--------------+
| Id | Name  | Salary | DepartmentId |
+----+-------+--------+--------------+
| 1  | Joe   | 70000  | 1            |
| 2  | Henry | 80000  | 2            |
| 3  | Sam   | 60000  | 2            |
| 4  | Max   | 90000  | 1            |
| 5  | Janet | 69000  | 1            |
| 6  | Randy | 85000  | 1            |
+----+-------+--------+--------------+
```
The Department table holds all departments of the company.
```
+----+----------+
| Id | Name     |
+----+----------+
| 1  | IT       |
| 2  | Sales    |
+----+----------+
```
Write a SQL query to find employees who earn the top three salaries in each of the department. For the above tables, your SQL query should return the following rows.
```
+------------+----------+--------+
| Department | Employee | Salary |
+------------+----------+--------+
| IT         | Max      | 90000  |
| IT         | Randy    | 85000  |
| IT         | Joe      | 70000  |
| Sales      | Henry    | 80000  |
| Sales      | Sam      | 60000  |
+------------+----------+--------+
```
## 题目大意

编写一个SQL查询来查找在每个部门中获得前三名工资的员工。对于上面的表，你的SQL查询应该返回下列行。

## 解题思路

这道题有点像 LeetCode 184. Department Highest Salary 的升级版
只不过这一次要找出每个部门最高的三个薪水

首先还是要用内联两表 满足 Departemnt字段的数据需求
```sql
... FROM Employee INNER JOIN Department ON Employee.DepartmentId  = Department.Id 
```
然后，既然要找出工资排名前三的人，就要满足两个查询条件 一是部门id相同 表示在同一个部门，二是比当前高的工资个数要小于 3 个。 按照这个思路，再使用相关子查询筛选结果

```sql
SELECT d.name AS Department, e.Name AS Employee, e.Salary 
FROM Employee AS e INNER JOIN Department AS d ON e.DepartmentId  = d.Id 
WHERE (SELECT count(Salary) FROM Employee WHERE DepartmentId = d.Id AND Salary > e.Salary ) < 3
ORDER BY e.Salary  DESC;
```

尝试提交结果报错，发现对于
![](http://upload-images.jianshu.io/upload_images/5617720-1a5634feb09f3c27.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
这张表而言，本应输出![](http://upload-images.jianshu.io/upload_images/5617720-fb585fb3768ac35e.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
实际确输出了![](http://upload-images.jianshu.io/upload_images/5617720-ccc222021ba8d5c9.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

这个测试实例里有两个相同的薪水，而且是符合前三条件的。 很明显，LeetCode 这里要求吧所有工资的人视为一个人，那么在salary 前加上 DISTINCT 即可

```sql
SELECT d.name AS Department, e.Name AS Employee, e.Salary 
FROM Employee AS e INNER JOIN Department AS d ON e.DepartmentId  = d.Id 
WHERE (SELECT count(DISTINCT Salary) FROM Employee WHERE DepartmentId = d.Id AND Salary > e.Salary ) < 3
ORDER BY e.Salary  DESC;
```

下面整理了几种其他的解法

[[[LeetCode] Department Top Three Salaries 系里前三高薪水](http://www.cnblogs.com/grandyang/p/5367670.html)
](https://www.cnblogs.com/grandyang/p/5367670.html)


最后看了一下目前最快的解法
```sql
# Write your MySQL query statement below
SET @salary_rank=0;
SET @current_id=NULL;
SET @previous_salary=-1;

SELECT Department, Employee, Salary
FROM (SELECT Department, Employee, Salary,
	/*RANK() OVER (PARTITION BY E.Department ORDER BY E.Salary DESC) SalaryRank*/
	@salary_rank := IF(@current_id = Department, 
                       IF(@previous_salary = Salary, @salary_rank, @salary_rank + 1), 
                       1
                    ) AS SalaryRank,
    @current_id := Department,
    @previous_salary := Salary
	FROM (SELECT D.Name Department, E.Name Employee, E.Salary Salary
          FROM Employee E
	      INNER JOIN Department D
	        ON D.Id = E.DepartmentId
          Group By D.Name, E.Salary, E.Name
          Order By D.Name ASC, E.Salary DESC) E
	) R
WHERE SalaryRank <= 3
/*ORDER BY Department ASC*/
```
这里使用用户自定义变量来对每一行生成了一个薪水排名（salaryrank）避免反复查表，优化了速度。









