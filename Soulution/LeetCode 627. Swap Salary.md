# LeetCode 627. Swap Salary

## 题目
Given a table salary, such as the one below, that has m=male and f=female values. Swap all f and m values (i.e., change all f values to m and vice versa) with a single update query and no intermediate temp table.

For example:
```
| id | name | sex | salary |
|----|------|-----|--------|
| 1  | A    | m   | 2500   |
| 2  | B    | f   | 1500   |
| 3  | C    | m   | 5500   |
| 4  | D    | f   | 500    |
```
After running your query, the above salary table should have the following rows:
```
| id | name | sex | salary |
|----|------|-----|--------|
| 1  | A    | f   | 2500   |
| 2  | B    | m   | 1500   |
| 3  | C    | f   | 5500   |
| 4  | D    | m   | 500    |
```

题目大意:不使用临时表的情况下,用一条更新语句交换人的性别

## 解体思路
对每一行数据进行判断 男->女,女->男

### IF/CASE
```sql
UPDATE salary SET sex = IF(sex='m','f','m')
```

```sql
UPDATE salary SET sex = (
CASE WHEN sex = 'm' THEN 'f'
ELSE 'm'
END CASE )
```
```sql
-- CASE 标准格式
CASE case_value
    WHEN when_value THEN statement_list
    [WHEN when_value THEN statement_list] ...
    [ELSE statement_list]
END 
-- CASE 标准格式 (case后不接value)
CASE
    WHEN search_condition THEN statement_list
    [WHEN search_condition THEN statement_list] ...
    [ELSE statement_list]
END CASE
```

[MySQL 5.7 Reference Manual - 13.6.5.1 CASE Syntax](https://dev.mysql.com/doc/refman/5.7/en/case.html)

[SQL 中的if else case](https://www.cnblogs.com/kungfupanda/p/4255630.html)

### SQL 中的位运算
```sql
UPDATE salary SET sex = CHAR(ASCII('f') ^ ASCII('m') ^ ASCII(sex));  
```

位运算有些时候真的是解题利器,尤其在交换与去重的有关题目中

SQL 中也是支持位运算的

![image.png](http://upload-images.jianshu.io/upload_images/5617720-eb82071e9100357b.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)




