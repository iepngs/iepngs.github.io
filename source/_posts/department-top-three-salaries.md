---
title: 部门工资前三高的员工
date: 2019-03-26 11:09:01
tags: MYSQL
---

### 原题描述
Employee 表包含所有员工信息，每个员工有其对应的 Id, salary 和 department Id 。

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
<escape><!-- more --></escape>

Department 表包含公司所有部门的信息。

```
+----+----------+
| Id | Name     |
+----+----------+
| 1  | IT       |
| 2  | Sales    |
+----+----------+
```

编写一个 SQL 查询，找出每个部门工资前三高的员工。例如，根据上述给定的表格，查询结果应返回：

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

----

这种题总是有一点意外情况，在测试用例中会出现，同一个部门中工资相同的情况，所以，说是前三高但并不代表结果集中每个部门只有三个人……

### 解答

```sql
SELECT d.Name Department, t.Name Employee, Salary 
FROM (
    SELECT 
        Name,
        Salary,
        DepartmentId, 
        CASE
            WHEN @did=DepartmentId AND @pres=Salary THEN @count=@count
            WHEN @did=DepartmentId AND @pres:=Salary THEN @count:=@count+1
            WHEN (@did:=DepartmentId) IS NOT NULL THEN @count:=1
        END AS co
    FROM 
        (SELECT Name, Salary, DepartmentId FROM Employee ORDER BY DepartmentId, Salary DESC) a, 
        (SELECT @did:=NULL, @count:=NULL, @pres:=NULL) b
    ) t 
JOIN Department d ON d.Id=t.DepartmentId 
WHERE t.co < 4;
```

> 这大概是[上一个题](/department-highest-salary/)的个升级版吧，所以就按照上题的思路做了，显得格外复杂有没有，看懂跟会写还是有差别的……

### More

> 再看两种大佬写的


```sql
SELECT P2.Name AS Department,P3.Name AS Employee,P3.Salary AS Salary
FROM Employee AS P3
INNER JOIN Department AS P2 ON P2.Id = P3.DepartmentId 
WHERE (
    SELECT COUNT(DISTINCT Salary)
    FROM Employee AS P4
    WHERE P3.DepartmentId = P4.DepartmentId AND P4.Salary >= P3.Salary
) <= 3
ORDER BY DepartmentId, Salary DESC;
```

```sql
SELECT t.NAME Department, d.NAME Employee , t.Salary Salary 
FROM Employee t 
LEFT JOIN (SELECT DepartmentId, Salary FROM Employee GROUP BY DepartmentId, Salary) E1 
    ON t.DepartmentId = E1.DepartmentId AND t.Salary < E1.Salary
INNER JOIN Department d 
    ON t.DepartmentId = d.ID
GROUP BY d.NAME, t.NAME, t.Salary
HAVING COUNT(E1.DepartmentId) < 3
ORDER BY d.NAME, t.Salary DESC;
```