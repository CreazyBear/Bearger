* 以下大多是抄录，用于分析学习

```sql
SELECT 
    FirstName, LastName, City, State
FROM 
    Person 
    left join 
    Address
    ON Person.PersonId = Address.PersonId
-- left join
```

```sql
SELECT
    IFNULL(
      (SELECT DISTINCT Salary
       FROM Employee
       ORDER BY Salary DESC
        LIMIT 1 OFFSET 1),
    NULL) AS SecondHighestSalary
-- limit offset ifnull
```

```sql
CREATE FUNCTION getNthHighestSalary(N INT) RETURNS INT
BEGIN
  set n = N -1;
  RETURN (
      # Write your MySQL query statement below.
        SELECT
        IFNULL(
          (SELECT DISTINCT Salary
           FROM Employee
           ORDER BY Salary DESC
            LIMIT 1 OFFSET n),
        NULL) AS SecondHighestSalary
  );
END
-- SET 
```

```sql
select 
    s.Score,
    (select 
        count(distinct Score) 
     from Scores 
     where Score>=s.Score)  
     as Rank 
from Scores s 
order by s.Score desc
-- 使用窗口函数可以简单解决，但不是所有数据库都支持
-- SQL的执行顺序：先From,再select。第一下所有行已经出来了，再执行select


SELECT
  a.score AS Score,
  COUNT(DISTINCT b.score) AS Rank
FROM
  scores a,
  scores b
WHERE b.score >= a.score
GROUP BY a.id
ORDER BY a.score DESC
```

```sql
SELECT DISTINCT
    l1.Num AS ConsecutiveNums
FROM
    Logs l1,
    Logs l2,
    Logs l3
WHERE
    l1.Id = l2.Id - 1
    AND l2.Id = l3.Id - 1
    AND l1.Num = l2.Num
    AND l2.Num = l3.Num
;

-- 长见识了
```

```sql
SELECT
    a.Name AS 'Employee'
FROM
    Employee AS a,
    Employee AS b
WHERE
    a.ManagerId = b.Id
        AND a.Salary > b.Salary
;

SELECT
     a.NAME AS Employee
FROM Employee AS a JOIN Employee AS b
     ON a.ManagerId = b.Id
     AND a.Salary > b.Salary
;
```


```sql
select Email
from
(
select Email,count(*) as email_count
from Person
group by Email
) as a
where email_count > 1
```


```sql
select customers.name as 'Customers'
from customers
where customers.id not in
(
    select customerid from orders
);

-- not in
```


```sql
SELECT
    Department.name AS 'Department',
    Employee.name AS 'Employee',
    Salary
FROM
    Employee
        JOIN
    Department ON Employee.DepartmentId = Department.Id
WHERE
    (Employee.DepartmentId , Salary) IN
    (   SELECT
            DepartmentId, MAX(Salary)
        FROM
            Employee
        GROUP BY DepartmentId
	)
;
-- 思路
```

```sql
SELECT
    d.Name AS 'Department', e1.Name AS 'Employee', e1.Salary
FROM
    Employee e1
        JOIN
    Department d ON e1.DepartmentId = d.Id
WHERE
    3 > (SELECT
            COUNT(DISTINCT e2.Salary)
        FROM
            Employee e2
        WHERE
            e2.Salary > e1.Salary
                AND e1.DepartmentId = e2.DepartmentId
        )
;
```