# LeetCode 184. Department Highest Salary

## 题目
The ```Employee``` table holds all employees. Every employee has an Id, a salary, and there is also a column for the department Id.
```
+----+-------+--------+--------------+
| Id | Name  | Salary | DepartmentId |
+----+-------+--------+--------------+
| 1  | Joe   | 70000  | 1            |
| 2  | Henry | 80000  | 2            |
| 3  | Sam   | 60000  | 2            |
| 4  | Max   | 90000  | 1            |
+----+-------+--------+--------------+
```
The ```Department``` table holds all departments of the company.
```
+----+----------+
| Id | Name     |
+----+----------+
| 1  | IT       |
| 2  | Sales    |
+----+----------+
```
Write a SQL query to find employees who have the highest salary in each of the departments. For the above tables, Max has the highest salary in the IT department and Henry has the highest salary in the Sales department.
```
+------------+----------+--------+
| Department | Employee | Salary |
+------------+----------+--------+
| IT         | Max      | 90000  |
| Sales      | Henry    | 80000  |
+------------+----------+--------+
```

## 题目大意
编写一个SQL查询来查找每个部门中薪水最高的员工。对于上面的表格，Max在IT部门工资最高，Henry在销售部门工资最高。

## 解题思路
难得碰到一道简单一点的,简单记录一下思路

首先将 Employee 与 Department 通过 DepartmentId  相连,满足结果中的所需要的部门字段

然后就是要找出每个部门中工资最高的人的集合,也就是在临时表中查询输出每一部门最大的工资及部门号即可. 最后连结上述条件得

```sql
SELECT t.Department, p.Name AS Employee, t.Salary 
FROM Employee AS p,  
(SELECT d.Id,d.Name AS Department,MAX(e.Salary) AS Salary FROM Department AS d   
INNER JOIN Employee AS e ON d.Id = e.DepartmentId GROUP BY e.DepartmentId) AS t   
WHERE p.Salary = t.Salary AND p.DepartmentId = t.Id;  
```

看了一下结果details

这是目前最快的sql
```sql
select a.name Department, b.name Employee, Salary from employee b join department a on b.departmentid=a.id
where (departmentid, salary) in( (select departmentid, max(salary) from employee group by departmentid) )
```
