---
title: 分数排名
date: 2019-03-22 16:25:35
tags: MYSQL
---

### 原题描述
编写一个 SQL 查询来实现分数排名。如果两个分数相同，则两个分数排名（Rank）相同。请注意，平分后的下一个名次应该是下一个连续的整数值。换句话说，名次之间不应该有“间隔”。

```
+----+-------+
| Id | Score |
+----+-------+
| 1  | 3.50  |
| 2  | 3.65  |
| 3  | 4.00  |
| 4  | 3.85  |
| 5  | 4.00  |
| 6  | 3.65  |
+----+-------+
```
<escape><!-- more --></escape>

例如，根据上述给定的 Scores 表，你的查询应该返回（按分数从高到低排列）：

```
+-------+------+
| Score | Rank |
+-------+------+
| 4.00  | 1    |
| 4.00  | 1    |
| 3.85  | 2    |
| 3.65  | 3    |
| 3.65  | 3    |
| 3.50  | 4    |
+-------+------+
```

----

题意看起来还是比较容易理解的，答案的核心也就是对sql的查询结果添加序号列。

### 解答

```sql
SELECT s.Score,t.Rank 
FROM Scores s
INNER JOIN (
    SELECT a.Score, @num := @num + 1 Rank 
    FROM (SELECT DISTINCT Score FROM Scores ORDER BY Score DESC) a, (SELECT @num := 0) n
    ) t 
ON s.Score = t.Score
ORDER BY s.Score DESC;
```
> 这个是利用子查询对每个Socre排序再回过来做一次关联。

### More

> 下面是评论里别人写的几种比较惊奇的思路：

#### 第一种：
```sql
SELECT Score, (SELECT COUNT(DISTINCT score) FROM Scores WHERE score >= s.score) AS Rank 
FROM Scores s ORDER BY Score DESC;
```

#### 第二种：
```sql
SELECT a.Score, SUM(CASE WHEN b.Score >= a.Score THEN 1 END ) AS Rank 
FROM Scores a, (SELECT DISTINCT Score FROM Scores ) b 
GROUP BY a.id 
ORDER BY a.Score DESC; 
```

#### 第三种：
```sql
SELECT Score, (SELECT COUNT(DISTINCT s.Score) + 1 FROM Scores AS s WHERE s.Score > Scores.Score) AS Rank 
FROM Scores 
ORDER BY Score DESC;
```