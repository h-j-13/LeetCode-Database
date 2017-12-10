# LeetCode 197. Rising Temperature

## 题目
Given a ```Weather``` table, write a SQL query to find all dates' Ids with higher temperature compared to its previous (yesterday's) dates.
```
+---------+------------+------------------+
| Id(INT) | Date(DATE) | Temperature(INT) |
+---------+------------+------------------+
|       1 | 2015-01-01 |               10 |
|       2 | 2015-01-02 |               25 |
|       3 | 2015-01-03 |               20 |
|       4 | 2015-01-04 |               30 |
+---------+------------+------------------+
```
For example, return the following Ids for the above ```Weather``` table:
```
+----+
| Id |
+----+
|  2 |
|  4 |
+----+
```
题目大意 : 
找出温度比昨天高的那一天对应的ID。


### INNER JOIN
```sql
SELECT w1.Id FROM Weather AS w1 INNER JOIN Weather AS w2 ON
TO_DAYS(w1.Date)=TO_DAYS(w2.Date)+1 AND w1.Temperature > w2.Temperature
```

### [TO_DAYS 及其他MySQL事件处理函数官方文档](https://dev.mysql.com/doc/refman/5.7/en/date-and-time-functions.html) 

