# SQL Report (MySQL)
This document is a technical report on **SQL (Structured Query Language)** with a focus on **MySQL**.  
It covers the **basics of SQL**, different types of **joins**, and common **aggregation functions** with clear code examples.

SQL is the most common language used to interact with relational databases. It allows us to store data in tables, retrieve it efficiently, combine multiple tables, and generate summary reports using grouping and aggregate functions.

---

## 1. Basics of SQL

SQL works mainly with tables. A table consists of rows (records) and columns (fields).  
The most common operations are:

* Creating a database/table
* Inserting records
* Reading records using SELECT
* Updating and deleting records

---

### 1.1 Creating a Database

```sql
CREATE DATABASE company_db;
USE company_db;
```

---

### 1.2 Creating Tables

In MySQL, we define tables using `CREATE TABLE`. Each column is assigned a data type and constraints if needed.

```sql
CREATE TABLE Employees (
    EmpID INT PRIMARY KEY,
    EmpName VARCHAR(100) NOT NULL,
    Department VARCHAR(50),
    Salary DECIMAL(10,2),
    JoinDate DATE
);

CREATE TABLE Projects (
    ProjectID INT PRIMARY KEY,
    ProjectName VARCHAR(100),
    EmpID INT,
    FOREIGN KEY (EmpID) REFERENCES Employees(EmpID)
);
```

Important notes:

* `PRIMARY KEY` uniquely identifies each row.
* `NOT NULL` ensures a column must have a value.
* `FOREIGN KEY` creates a relationship between two tables.

---

### 1.3 Inserting Data

The `INSERT INTO` statement is used to add new rows.

```sql
INSERT INTO Employees (EmpID, EmpName, Department, Salary, JoinDate) VALUES
(1, 'Alice', 'IT', 70000.00, '2023-01-10'),
(2, 'Bob', 'HR', 50000.00, '2022-08-15'),
(3, 'Charlie', 'IT', 60000.00, '2021-06-20'),
(4, 'David', 'Finance', 80000.00, '2020-03-12');

INSERT INTO Projects (ProjectID, ProjectName, EmpID) VALUES
(101, 'Payroll System', 1),
(102, 'Recruitment Portal', 2),
(103, 'Cloud Migration', 1),
(104, 'Budget Analysis', 4);
```

---

### 1.4 Reading Data using SELECT

The `SELECT` statement retrieves data from a table.

```sql
SELECT * FROM Employees;
```

Selecting specific columns:

```sql
SELECT EmpName, Salary FROM Employees;
```

Filtering results with `WHERE`:

```sql
SELECT EmpName, Department
FROM Employees
WHERE Department = 'IT';
```

Sorting results using `ORDER BY`:

```sql
SELECT EmpName, Salary
FROM Employees
ORDER BY Salary DESC;
```

---

## 2. SQL Joins

A join is used when we want to combine rows from multiple tables.  
Joins are extremely important because relational databases store data in separate tables to avoid duplication.

---

### 2.1 INNER JOIN

`INNER JOIN` returns only the rows where there is a match in both tables.

```sql
SELECT E.EmpName, P.ProjectName
FROM Employees E
INNER JOIN Projects P
ON E.EmpID = P.EmpID;
```

This will show only employees who have projects assigned.

---

### 2.2 LEFT JOIN

`LEFT JOIN` returns all rows from the left table and matching rows from the right table.  
If there is no match, the right-side columns return `NULL`.

```sql
SELECT E.EmpName, P.ProjectName
FROM Employees E
LEFT JOIN Projects P
ON E.EmpID = P.EmpID;
```

This will show all employees, even if they do not have any projects.

---

### 2.3 RIGHT JOIN

`RIGHT JOIN` returns all rows from the right table and matching rows from the left table.  
If there is no match, the left-side columns return `NULL`.

```sql
SELECT E.EmpName, P.ProjectName
FROM Employees E
RIGHT JOIN Projects P
ON E.EmpID = P.EmpID;
```

This ensures all projects are displayed, even if an employee record is missing.

---

### 2.4 FULL OUTER JOIN (MySQL Workaround)

MySQL does not directly support `FULL OUTER JOIN`.  
However, we can simulate it using `UNION` between a LEFT JOIN and RIGHT JOIN.

```sql
SELECT E.EmpName, P.ProjectName
FROM Employees E
LEFT JOIN Projects P
ON E.EmpID = P.EmpID

UNION

SELECT E.EmpName, P.ProjectName
FROM Employees E
RIGHT JOIN Projects P
ON E.EmpID = P.EmpID;
```

This returns:

* All employees (even without projects)
* All projects (even without matching employees)

---

## 3. Aggregations in SQL

Aggregations are used when we want summary information instead of raw data.

Common aggregate functions are:

* `COUNT()` - number of rows
* `SUM()` - total sum
* `AVG()` - average value
* `MIN()` - smallest value
* `MAX()` - largest value

---

### 3.1 COUNT()

Count how many employees exist:

```sql
SELECT COUNT(*) AS TotalEmployees
FROM Employees;
```

Count employees in each department:

```sql
SELECT Department, COUNT(*) AS DeptCount
FROM Employees
GROUP BY Department;
```

---

### 3.2 SUM()

Find the total salary paid to all employees:

```sql
SELECT SUM(Salary) AS TotalSalary
FROM Employees;
```

---

### 3.3 AVG()

Find the average salary:

```sql
SELECT AVG(Salary) AS AvgSalary
FROM Employees;
```

---

### 3.4 GROUP BY

`GROUP BY` is used to group rows and apply aggregation per group.

Example: Average salary per department

```sql
SELECT Department, AVG(Salary) AS AvgDeptSalary
FROM Employees
GROUP BY Department;
```

---

### 3.5 HAVING

The `HAVING` clause is used to filter grouped results.  
`WHERE` filters rows before grouping, while `HAVING` filters after aggregation.

Example: Show departments with more than 1 employee

```sql
SELECT Department, COUNT(*) AS DeptCount
FROM Employees
GROUP BY Department
HAVING COUNT(*) > 1;
```

---

## 4. Practical Report Query Example

A common real-world SQL requirement is to generate a department-level report showing:

* Department name
* Total employees
* Total salary
* Average salary

```sql
SELECT Department,
       COUNT(*) AS TotalEmployees,
       SUM(Salary) AS TotalSalary,
       AVG(Salary) AS AvgSalary
FROM Employees
GROUP BY Department
ORDER BY TotalSalary DESC;
```

This produces a clean department report which is useful for management dashboards.

---

## Conclusion

SQL is a fundamental skill for working with relational databases.  
The SQL basics help us create tables, insert data, and retrieve information. Joins allow us to combine multiple related tables. Aggregation functions such as `COUNT`, `SUM`, and `AVG` help generate meaningful reports. By combining joins with aggregation, SQL can produce powerful analytical outputs directly inside a database.

This makes SQL an essential tool for data analysis, backend development, and reporting systems.

---

## References

* https://dev.mysql.com/doc/
* https://www.w3schools.com/sql/
* https://www.w3schools.com/mysql/
* https://www.geeksforgeeks.org/sql-tutorial/
