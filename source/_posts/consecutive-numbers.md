---
title: 连续出现的数字
date: 2019-03-25 09:56:19
tags: MYSQL
---

### 原题描述
编写一个 SQL 查询，查找所有至少连续出现三次的数字。

```
+----+-----+
| Id | Num |
+----+-----+
| 1  |  1  |
| 2  |  1  |
| 3  |  1  |
| 4  |  2  |
| 5  |  1  |
| 6  |  2  |
| 7  |  2  |
+----+-----+
```
<escape><!-- more --></escape>

例如，给定上面的 Logs 表， 1 是唯一连续出现至少三次的数字。

```
+-----------------+
| ConsecutiveNums |
+-----------------+
| 1               |
+-----------------+
```

----

乍一看，似乎这题似乎简单，不过想了一下，根据至今所学经验还确实搞不定，尴尬的一批...
一开始还想着用`GROUP BY`的同时`HAVING`出来`MAX - MIN`的`id`跟`COUNT`的大小一致且`COUNT`大于2的值，看到运行结果就明白故事结束了，好在最后没有无疾而终，一起研究下别个大佬的SQL

### 解答

```sql
SELECT DISTINCT Num AS ConsecutiveNums
FROM (
  SELECT Num, 
    CASE 
      WHEN @prev = Num THEN @count := @count + 1
      WHEN (@prev := Num) IS NOT NULL THEN @count := 1
    END AS CNT
  FROM Logs, (SELECT @prev := NULL, @count := NULL) AS t
) AS temp
WHERE temp.CNT >= 3;
```

> 理了理确实简单，跟上一个题差不多，都是利用变量组合一个临时表然后SELECT出来。稍微解释一下？


先说from里面的后一个t表:

```sql
SELECT @prev := null, @count := null
```

其中`(@prev := null)`这个是表示变量赋值，@prev是变量名，值初始化为NULL，所以单纯看t表就是建了两个NULL变量SELECT出来而已，单独执行这个t表出来的结果就是一行两列都是NULL的值,弄这么一个t表的作用就是：
两个表关联后的每行增加了两列由变量控制的列，举个例子，在第一行中prev=0，那`SELECT @prev := @prev + 1`每一行的结果中prev都是前一行的prev的值+1


本来`case when`只是一个条件表达式这个没啥好讲的，然后这个加了变量的表达式似乎高级了一点，像极了编程……

```sql
CASE 
    WHEN @prev = Num THEN @count := @count + 1
    WHEN (@prev := Num) IS NOT NULL THEN @count := 1
END AS CNT
```

先说后一个`CASE WHEN`： `(@prev := Num) is not null`，这里做了两件事情，先给@prev重置为Num字段的值（每一行的@prev在一开始的时候是在t表初始化为null的），然后再看它是不是为null，不是null的时候给@count初始化为值为1，但这个是恒成立的条件，因此前面一个CASE WHEN没走的话必然走这个，也就是说这个CASE WHEN只能放在前一个的后面；

再回过来看前一个`CASE WHEN`： 当@prev等于Num的时候（在单个sql里面`@xxx:=xx`这种结构表示变量赋值，在表达式中如果等号没有冒号@xxx=xx这种结构就是作比较而已，比如Num=1这样），就是说前一行的Num跟当前行的@prev相等的时候，因为当前行的@prev是在上一行`@prev := Num`的时候赋值为Num的，所以跟当前行的Num不一定一样，把COUNT自增一个数。

举例解释下这个@prev，第一次在t表初始化为null，然后第一次经过`CASE WHEN`的时候走后面一个条件变成了当前行的Num，再次循环下一行，跟前一行一样的时候计数@count加1，不一样的时候又回到走后面一个`CASE WHEN`的分支重新初始化@prev为新的Num值重新开始计数直到扫完全表。

最后再筛选出计数值大于等于3的去重就完成了。

### More

> 这种利用变量做子表执行的query还没有实战用过，看来还是井底之蛙，希望下次遇到有类似的场景能想起来…