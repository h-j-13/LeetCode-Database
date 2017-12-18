# LeetCode 262. Trips and Users

## 题目
The ```Trips``` table holds all taxi trips. Each trip has a unique Id, while Client_Id and Driver_Id are both foreign keys to the Users_Id at the ```Users``` table. Status is an ENUM type of (‘completed’, ‘cancelled_by_driver’, ‘cancelled_by_client’).
```
+----+-----------+-----------+---------+--------------------+----------+
| Id | Client_Id | Driver_Id | City_Id |        Status      |Request_at|
+----+-----------+-----------+---------+--------------------+----------+
| 1  |     1     |    10     |    1    |     completed      |2013-10-01|
| 2  |     2     |    11     |    1    | cancelled_by_driver|2013-10-01|
| 3  |     3     |    12     |    6    |     completed      |2013-10-01|
| 4  |     4     |    13     |    6    | cancelled_by_client|2013-10-01|
| 5  |     1     |    10     |    1    |     completed      |2013-10-02|
| 6  |     2     |    11     |    6    |     completed      |2013-10-02|
| 7  |     3     |    12     |    6    |     completed      |2013-10-02|
| 8  |     2     |    12     |    12   |     completed      |2013-10-03|
| 9  |     3     |    10     |    12   |     completed      |2013-10-03| 
| 10 |     4     |    13     |    12   | cancelled_by_driver|2013-10-03|
+----+-----------+-----------+---------+--------------------+----------+
```
The Users table holds all users. Each user has an unique Users_Id, and Role is an ENUM type of (‘client’, ‘driver’, ‘partner’).
```
+----------+--------+--------+
| Users_Id | Banned |  Role  |
+----------+--------+--------+
|    1     |   No   | client |
|    2     |   Yes  | client |
|    3     |   No   | client |
|    4     |   No   | client |
|    10    |   No   | driver |
|    11    |   No   | driver |
|    12    |   No   | driver |
|    13    |   No   | driver |
+----------+--------+--------+
```
Write a SQL query to find the cancellation rate of requests made by unbanned clients between Oct 1, 2013 and Oct 3, 2013. For the above tables, your SQL query should return the following rows with the cancellation rate being rounded to two decimal places.
```
+------------+-------------------+
|     Day    | Cancellation Rate |
+------------+-------------------+
| 2013-10-01 |       0.33        |
| 2013-10-02 |       0.00        |
| 2013-10-03 |       0.50        |
+------------+-------------------+
```

## 题目大意
要求找出每天unbaned clients的取消记录的概率。

首先第一个要解决的问题就是在SQL 进行四舍五入。
### ROUND 函数
简单的来说就是 round(需要四舍五入的数字，四舍五入的位数)

#### [MySQL 5.7 round 函数官方文档](https://dev.mysql.com/doc/refman/5.7/en/precision-math-rounding.html)

关于被取消的概率 我们只需要解决 1 每天非ban用户的总请求数 2 每天非ban用户的被取消的请求数 即可得到这个结果
首先一定要对每天的数据使用GROUP BY 分组
对于分组之后的数据，就可以计算取消率了
统计总数当然使用count(*) 即可得到
若是统计未完成的数量 CASE 和 用户变量均可以实现这一点


```sql
SELECT 
    request_at AS Day, 
    round(
        sum(
            CASE WHEN   -- 这里临时生成了一个字段，由status字段决定 若是完成字段值为0 反之为1  也就是是否被取消
                status="completed" THEN 0 
                ELSE 1 
            END )       -- 对这个字段使用sum函数即可求得被取消了的个数
            /count(*),  2)      -- 除以总个数后四舍五入2位即为被取消的概率
        AS "Cancellation Rate"
FROM  
    (SELECT * FROM Trips WHERE client_id NOT IN     -- 这里要把被 BAN 的乘客过滤掉
        (SELECT users_id FROM Users WHERE banned = "yes" AND role = "client")   -- 找出被BAN的乘客 
   AND request_at  BETWEEN "2013-10-01" AND "2013-10-03"    -- 限定日期
    ) AS t
GROUP BY request_at -- 对日期分组
ORDER BY request_at;
```

下面目前最快的写法
```sql
# Write your MySQL query statement below
select 
t.Request_at Day, 
round(sum(case when t.Status like 'cancelled_%' then 1 else 0 end)/count(*),2) as 'Cancellation Rate'
from Trips t 
inner join Users u 
on t.Client_Id = u.Users_Id and u.Banned='No'
where t.Request_at between '2013-10-01' and '2013-10-03'
group by t.Request_at
```

他这里直接通过内链接筛选条件去掉了被ban的乘客，要比先查下被ban乘客集合再使用not in进行筛选要来得快
但这里的字符串模糊搜索应该笔直接写 status="completed" 要慢一点

修改了一下之前的sql

#### 最终版本
```sql
SELECT 
    request_at AS Day, 
    round(
        sum(
            CASE WHEN   -- 这里临时生成了一个字段，由status字段决定 若是完成字段值为0 反之为1  也就是是否被取消
                status="completed" THEN 0 
                ELSE 1 
            END )       -- 对这个字段使用sum函数即可求得被取消了的个数
            /count(*),  2)      -- 除以总个数后四舍五入2位即为被取消的概率
        AS "Cancellation Rate"
FROM  
    Trips t INNER JOIN Users u ON t.Client_Id = u.Users_Id and u.Banned='No'    -- 限定为非ban用户
WHERE request_at  BETWEEN "2013-10-01" AND "2013-10-03"    -- 限定日期
GROUP BY request_at -- 对日期分组
ORDER BY request_at;
```