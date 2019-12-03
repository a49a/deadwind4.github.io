---
title: "SQL笔记"
date: 2018-03-10T01:15:09+08:00
draft: false
tags: ["编程语言"]
reward: true
---

# 基础知识

## SQL IN

```
SELECT column_name
FROM table_name 
WHERE column_name IN (value1, value2);
```

作用：筛选column等于value1或value2。

## SQL BETWEEN

```
SELECT column_name(s)
FROM table_name
WHERE column_name IN (value1,value2);
```

作用：筛选大于等于value小于value2。
value可为字符串。

## ALTER

添加列
```
ALTER TABLE table_name
ADD column_name datatype
```

删除列
```
ALTER TABLE table_name
DROP COLUMN column_name
```

改变列数据类型
```
ALTER TABLE table_name
ALTER COLUMN column_name datatype
```

# 常用记不住的SQL语句收集

创建行时create_at字段自动填写为当前时间。
ALTER TABLE foo MODIFY create_at DATETIME NOT NULL DEFAULT CURRENT_TIMESTAMP;
更新行时update_at字段自动填写为当前时间。
ALTER TABLE foo MODIFY update_at DATETIME NOT NULL DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP;

# 常用且记住了的SQL语句收集

## 单表关联查询

SELECT id FROM foo WHERE set_id = (SELECT set_id TABLE foo WHERE id = 1);
SELECT city FROM weather WHERE temp_lo = (SELECT max(temp_lo) FROM weather);

## 分组过滤
SELECT city, max(temp_lo)
    FROM weather
    GROUP BY city
    HAVING max(temp_lo) < 40;

# WHERE和HAVING区别
和SQL的WHERE以及HAVING子句之间的关系对我们非常重要。WHERE和HAVING的基本区别如下：WHERE在分组和聚集计算之前选取输入行（因此，它控制哪些行进入聚集计算）， 而HAVING在分组和聚集之后选取分组行。因此，WHERE子句不能包含聚集函数； 因为试图用聚集函数判断哪些行应输入给聚集运算是没有意义的。相反，HAVING子句总是包含聚集函数（严格说来，你可以写不使用聚集的HAVING子句， 但这样做很少有用。同样的条件用在WHERE阶段会更有效）。

# 更新
UPDATE weather
    SET temp_hi = temp_hi - 2,  temp_lo = temp_lo - 2
    WHERE date > '1994-11-28';
