# LeetCode 596. Classes More Than 5 Students
## 题目
There is a table ```courses``` with columns: student and class

Please list out all classes which have more than or equal to 5 students.

For example, the table:

```     
+---------+------------+
| student | class      |
+---------+------------+
| A       | Math       |
| B       | English    |
| C       | Math       |
| D       | Biology    |
| E       | Math       |
| F       | Computer   |
| G       | Math       |
| H       | Math       |
| I       | Math       |
+---------+------------+
```     

Should output:

```     
+---------+
| class   |
+---------+
| Math    |
+---------+
```     

Note:
The students should not be counted duplicate in each course.

题目大意,找出选修人数超过5门的学生,每门课的学生不应该重复计算

## 解题思路

使用 group by 分组后 count(distinct 学生) 大于5的即为需要展示的内容

```sql
SELECT class FROM courses GROUP BY class  HAVING count(DISTINCT student) >= 5;
```
