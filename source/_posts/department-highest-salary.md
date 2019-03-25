---
title: 部门工资最高的员工
date: 2019-03-25 16:51:35
tags: MYSQL
---

### 原题描述
Employee 表包含所有员工信息，每个员工有其对应的 Id, salary 和 department Id。


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

Department 表包含公司所有部门的信息。

```
+----+----------+
| Id | Name     |
+----+----------+
| 1  | IT       |
| 2  | Sales    |
+----+----------+
```

<escape><!-- more --></escape>

编写一个 SQL 查询，找出每个部门工资最高的员工。例如，根据上述给定的表格，Max 在 IT 部门有最高工资，Henry 在 Sales 部门有最高工资。

```
+------------+----------+--------+
| Department | Employee | Salary |
+------------+----------+--------+
| IT         | Max      | 90000  |
| Sales      | Henry    | 80000  |
+------------+----------+--------+
```

----
### 解答

#### 第一种：利用where in
```sql
SELECT 
    d.Name AS Department,
    e.Name AS Employee,
    e.Salary 
FROM 
    Employee e,Department d 
WHERE
    e.DepartmentId=d.id 
    AND
    (e.Salary,e.DepartmentId) IN (SELECT MAX(Salary), DepartmentId FROM Employee GROUP BY DepartmentId);
```

#### 第二种：利用表关联子查询
```sql
SELECT
    p. NAME AS Department,
    q. NAME AS Employee,
    q.Salary
FROM
    (
        SELECT
            e.DepartmentId,
            MAX(e.Salary) AS Salary,
            t. NAME
        FROM
            Employee e
        INNER JOIN Department t ON e.DepartmentId = t.id
        GROUP BY DepartmentId
    ) p
INNER JOIN Employee q ON p.DepartmentId = q.DepartmentId
AND p.Salary = q.Salary
ORDER BY p.DepartmentId;
```


> 感觉有更优的方案？