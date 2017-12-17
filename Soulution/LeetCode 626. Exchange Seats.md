# LeetCode 626. Exchange Seats

## 题目
Mary is a teacher in a middle school and she has a table ```seat``` storing students' names and their corresponding seat ids.

The column id is continuous increment.
Mary wants to change seats for the adjacent students.
Can you write a SQL query to output the result for Mary?
```
+---------+---------+
|    id   | student |
+---------+---------+
|    1    | Abbot   |
|    2    | Doris   |
|    3    | Emerson |
|    4    | Green   |
|    5    | Jeames  |
+---------+---------+
```
For the sample input, the output is:
```
+---------+---------+
|    id   | student |
+---------+---------+
|    1    | Doris   |
|    2    | Abbot   |
|    3    | Green   |
|    4    | Emerson |
|    5    | Jeames  |
+---------+---------+
```
Note:
If the number of students is odd, there is no need to change the last one's seat.

## 题目大意 
玛丽编写一个查询以改变邻近学生的座位,如果学生人数是奇数，则不需要改变最后一个座位。

## 解题思路
与其变换名称,不如将id为2n变换为2n-1,2n-1变换为2n (n=1,2,3....)
最好讲结果分成两部分,一部分处理奇数id的变换 一部分处理偶数id的变化
若不考虑边界情况,则易得sql如下
```sql
SELECT tmp.id, tmp.student FROM
(
    SELECT id-1 AS id, student FROM seat WHERE id%2 = 0  -- 偶数 id -1
    UNION
    SELECT id+1 AS id, student FROM seat WHERE id%2 = 1  -- 奇数 id +1
    
) tmp
ORDER BY tmp.id 
```
考虑边界则要在update中去除一个id,这个id必须满足是奇数并且是最大的id

则得sql
```sql
SELECT tmp.id, tmp.student FROM
(
    SELECT id-1 AS id, student FROM seat WHERE id%2 = 0  -- 偶数 id -1
    UNION
    SELECT CASE 
        WHEN id = max_id  AND max_id%2 = 1 THEN id 
        ELSE id + 1 END -- 使用END 结束case
            AS id, 
    student 
    FROM seat,(SELECT max(id) as max_id FROM seat) AS t_max_id -- 这里实际上seat表和一个只有一个元组的seat最大临时表做了笛卡儿积 
    WHERE id%2 = 1  -- 奇数 id +1
) tmp
ORDER BY tmp.id 
```

这里值得注意的是,若你是使用end case 结束case则会报错
![END case 报错](http://upload-images.jianshu.io/upload_images/5617720-bafc4b5ed9c2b24b.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

而实际上 MySQL 5.7 规定的标准格式为CASE...END CASE
![](http://upload-images.jianshu.io/upload_images/5617720-c0abc279d67e4e8c.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

详见[MySQL 5.7 Reference Manual - 13.6.5.1 CASE Syntax](https://dev.mysql.com/doc/refman/5.7/en/case.html)

然后特意找了一台5.7的数据库服务器上重新运行了一下,结果竟然还是报错了
![](http://upload-images.jianshu.io/upload_images/5617720-4dd5aada93e74181.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

并且发在网上查阅资料的时候只见到了case... end 的写法 没有case... end case

难道是只在begin ... end 内才这么写 case end 用于区分 不同的结束标志吗?
详细原因还需要再继续学习,若你有正确的认识或想法也请留言或联系我
