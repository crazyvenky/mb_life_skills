# SQL Report (MySQL)

This document provides a comprehensive technical report on SQL (Structured Query Language) with a focus on MySQL implementation. The report covers fundamental SQL concepts, various types of joins, and aggregation functions with practical code examples.

SQL is the standard language for managing and manipulating relational databases. It enables users to store data in structured tables, retrieve information efficiently, combine multiple tables through relationships, and generate analytical reports using grouping and aggregate functions. Understanding SQL is essential for database administrators, backend developers, and data analysts.

---

## 1. Basics of SQL

SQL operates primarily with tables, which are organized collections of data. A table consists of rows (also called records or tuples) and columns (also called fields or attributes). The fundamental operations in SQL include creating databases and tables, inserting new records, querying existing data, updating records, and deleting data when necessary.

---

### 1.1 Creating a Database

Before working with tables, we must create a database to organize related tables together. A database acts as a container for all related data structures.

```sql
CREATE DATABASE retail_store;
USE retail_store;
```

The CREATE DATABASE statement creates a new database named retail_store. The USE statement tells MySQL to switch context to this database for subsequent operations.

---

### 1.2 Creating Tables

Tables are created using the CREATE TABLE statement. Each column must have a defined data type and optional constraints that enforce data integrity rules.

```sql
CREATE TABLE Employees (
    EmpID INT PRIMARY KEY,
    EmpName VARCHAR(100) NOT NULL,
    Department VARCHAR(50),
    Salary DECIMAL(10,2),
    City VARCHAR(50),
    JoinDate DATE
);

CREATE TABLE Sales (
    SaleID INT PRIMARY KEY,
    ProductName VARCHAR(100),
    SaleAmount DECIMAL(10,2),
    EmpID INT,
    SaleDate DATE,
    FOREIGN KEY (EmpID) REFERENCES Employees(EmpID)
);
```

Important concepts to understand:

1. PRIMARY KEY constraint uniquely identifies each row in the table. No two rows can have the same primary key value.
2. NOT NULL constraint ensures that a column must always contain a value and cannot be left empty.
3. FOREIGN KEY constraint establishes a relationship between tables and maintains referential integrity.
4. DECIMAL(10,2) data type stores numbers with up to 10 digits total and 2 decimal places, perfect for monetary values.
5. VARCHAR stores variable-length strings up to the specified maximum length.

---

### 1.3 Inserting Data

The INSERT INTO statement adds new rows to a table. We can insert single or multiple rows in one statement.

```sql
INSERT INTO Employees (EmpID, EmpName, Department, Salary, City, JoinDate) VALUES
(101, 'Rajesh Kumar', 'Sales', 45000.00, 'Mumbai', '2022-03-15'),
(102, 'Priya Sharma', 'IT', 65000.00, 'Bangalore', '2021-07-20'),
(103, 'Amit Shaw', 'Sales', 42000.00, 'Ahmedabad', '2023-01-10'),
(104, 'Arjun Reddy', 'Cardiologist', 172000.00, 'Hyderabad', '2023-11-05'),
(105, 'Vikram Singh', 'IT', 68000.00, 'Pune', '2021-09-18'),
(106, 'Kattappa', 'HR', 50000.00, 'Chennai', '2022-06-12');

INSERT INTO Sales (SaleID, ProductName, SaleAmount, EmpID, SaleDate) VALUES
(201, 'Laptop', 45000.00, 101, '2024-01-15'),
(202, 'Mobile Phone', 22000.00, 101, '2024-01-18'),
(203, 'Tablet', 18000.00, 103, '2024-01-20'),
(204, 'Headphones', 3500.00, 103, '2024-01-22'),
(205, 'Smart Watch', 12000.00, 101, '2024-01-25');
```

Notice that we specify column names explicitly in the INSERT statement. This is a best practice because it makes the code more maintainable and prevents errors if the table structure changes.

---

### 1.4 Reading Data using SELECT

The SELECT statement is the most frequently used SQL command. It retrieves data from one or more tables based on specified criteria.

Retrieve all columns and all rows:

```sql
SELECT * FROM Employees;
```

The asterisk symbol represents all columns. While convenient for testing, production queries should explicitly list required columns for better performance.

Selecting specific columns:

```sql
SELECT EmpName, Salary, City FROM Employees;
```

This query returns only the employee name, salary, and city columns, reducing the amount of data transferred.

Filtering results with WHERE clause:

```sql
SELECT EmpName, Department, Salary
FROM Employees
WHERE Department = 'Sales';
```

The WHERE clause filters rows based on specified conditions. This query returns only employees working in the Sales department.

Using multiple conditions:

```sql
SELECT EmpName, Salary, City
FROM Employees
WHERE Salary > 50000 AND City = 'Bangalore';
```

Multiple conditions can be combined using AND and OR operators to create complex filters.

Sorting results using ORDER BY:

```sql
SELECT EmpName, Salary, Department
FROM Employees
ORDER BY Salary DESC;
```

The ORDER BY clause sorts the result set. DESC means descending order (highest to lowest), while ASC means ascending order (lowest to highest).

---

## 2. SQL Joins

Joins are fundamental operations that combine rows from two or more tables based on related columns. Relational databases store data in separate tables to eliminate redundancy, and joins allow us to reconstruct meaningful relationships when querying data.

---

### 2.1 INNER JOIN

INNER JOIN returns only the rows where there is a matching value in both tables. This is the most commonly used join type.

```sql
SELECT E.EmpName, S.ProductName, S.SaleAmount, S.SaleDate
FROM Employees E
INNER JOIN Sales S
ON E.EmpID = S.EmpID;
```

This query shows employee names along with their sales records. Only employees who have made sales will appear in the result. Employees without sales and sales without matching employees are excluded.

Using table aliases (E for Employees and S for Sales) makes queries shorter and more readable.

---

### 2.2 LEFT JOIN

LEFT JOIN returns all rows from the left table and matching rows from the right table. When there is no match, the columns from the right table contain NULL values.

```sql
SELECT E.EmpName, E.Department, S.ProductName, S.SaleAmount
FROM Employees E
LEFT JOIN Sales S
ON E.EmpID = S.EmpID;
```

This query displays all employees regardless of whether they have made any sales. Employees without sales will show NULL in the ProductName and SaleAmount columns. This is useful for finding employees who have not yet made any sales.

---

### 2.3 RIGHT JOIN

RIGHT JOIN returns all rows from the right table and matching rows from the left table. When there is no match, the columns from the left table contain NULL values.

```sql
SELECT E.EmpName, S.ProductName, S.SaleAmount, S.SaleDate
FROM Employees E
RIGHT JOIN Sales S
ON E.EmpID = S.EmpID;
```

This query ensures all sales records are displayed even if the employee information is missing. This join type is less commonly used than LEFT JOIN because we can achieve the same result by reversing the table order in a LEFT JOIN.

---

### 2.4 FULL OUTER JOIN (MySQL Workaround)

MySQL does not natively support FULL OUTER JOIN syntax. However, we can simulate it by combining LEFT JOIN and RIGHT JOIN using the UNION operator.

```sql
SELECT E.EmpName, E.Department, S.ProductName, S.SaleAmount
FROM Employees E
LEFT JOIN Sales S
ON E.EmpID = S.EmpID

UNION

SELECT E.EmpName, E.Department, S.ProductName, S.SaleAmount
FROM Employees E
RIGHT JOIN Sales S
ON E.EmpID = S.EmpID;
```

This combined query returns all employees (even those without sales) and all sales records (even those without matching employee data). The UNION operator removes duplicate rows automatically.

---

## 3. Aggregations in SQL

Aggregation functions perform calculations on multiple rows and return a single summary value. These functions are essential for generating reports and analytical insights from raw data.

Common aggregate functions include:

1. COUNT() - Counts the number of rows or non-null values
2. SUM() - Calculates the total sum of numeric values
3. AVG() - Computes the average (mean) of numeric values
4. MIN() - Finds the smallest value
5. MAX() - Finds the largest value

---

### 3.1 COUNT()

The COUNT function returns the number of rows that match specified criteria.

Count total number of employees:

```sql
SELECT COUNT(*) AS TotalEmployees
FROM Employees;
```

The asterisk counts all rows including those with NULL values in any column.

Count employees in each department:

```sql
SELECT Department, COUNT(*) AS EmployeeCount
FROM Employees
GROUP BY Department;
```

This query groups employees by department and counts how many employees work in each department.

Count employees with salary above 50000:

```sql
SELECT COUNT(*) AS HighEarners
FROM Employees
WHERE Salary > 50000;
```

---

### 3.2 SUM()

The SUM function calculates the total of all values in a numeric column.

Find total salary expenditure:

```sql
SELECT SUM(Salary) AS TotalSalaryExpense
FROM Employees;
```

Calculate total sales revenue:

```sql
SELECT SUM(SaleAmount) AS TotalRevenue
FROM Sales;
```

Sum of sales by each employee:

```sql
SELECT E.EmpName, SUM(S.SaleAmount) AS TotalSales
FROM Employees E
INNER JOIN Sales S
ON E.EmpID = S.EmpID
GROUP BY E.EmpName;
```

---

### 3.3 AVG()

The AVG function computes the arithmetic mean of numeric values.

Find average salary across the company:

```sql
SELECT AVG(Salary) AS AverageSalary
FROM Employees;
```

Calculate average sale amount:

```sql
SELECT AVG(SaleAmount) AS AverageSaleValue
FROM Sales;
```

---

### 3.4 MIN() and MAX()

These functions find the smallest and largest values respectively.

```sql
SELECT MIN(Salary) AS LowestSalary, MAX(Salary) AS HighestSalary
FROM Employees;
```

Find the most recent hire:

```sql
SELECT EmpName, JoinDate
FROM Employees
WHERE JoinDate = (SELECT MAX(JoinDate) FROM Employees);
```

---

### 3.5 GROUP BY

The GROUP BY clause groups rows that have the same values in specified columns. It is almost always used with aggregate functions to perform calculations for each group.

Average salary per department:

```sql
SELECT Department, AVG(Salary) AS AverageSalary
FROM Employees
GROUP BY Department;
```

Count employees and total salary per city:

```sql
SELECT City, COUNT(*) AS EmployeeCount, SUM(Salary) AS TotalSalary
FROM Employees
GROUP BY City
ORDER BY TotalSalary DESC;
```

---

### 3.6 HAVING

The HAVING clause filters groups after aggregation. It is similar to WHERE but operates on grouped data rather than individual rows.

The key difference is that WHERE filters rows before grouping, while HAVING filters groups after aggregation.

Show departments with more than 1 employee:

```sql
SELECT Department, COUNT(*) AS EmployeeCount
FROM Employees
GROUP BY Department
HAVING COUNT(*) > 1;
```

Find departments where average salary exceeds 55000:

```sql
SELECT Department, AVG(Salary) AS AvgSalary
FROM Employees
GROUP BY Department
HAVING AVG(Salary) > 55000;
```

---

## 4. Practical Report Query Examples

Real-world applications often require combining multiple SQL concepts to generate comprehensive reports.

### 4.1 Department Summary Report

Generate a report showing department-level statistics:

```sql
SELECT Department,
       COUNT(*) AS TotalEmployees,
       SUM(Salary) AS TotalSalaryExpense,
       AVG(Salary) AS AverageSalary,
       MIN(Salary) AS MinimumSalary,
       MAX(Salary) AS MaximumSalary
FROM Employees
GROUP BY Department
ORDER BY TotalSalaryExpense DESC;
```

This query produces a comprehensive department analysis useful for budget planning and salary benchmarking.

### 4.2 Employee Performance Report

Combine employee data with sales performance:

```sql
SELECT E.EmpName,
       E.Department,
       E.City,
       COUNT(S.SaleID) AS TotalSales,
       SUM(S.SaleAmount) AS TotalRevenue,
       AVG(S.SaleAmount) AS AverageSaleValue
FROM Employees E
LEFT JOIN Sales S
ON E.EmpID = S.EmpID
GROUP BY E.EmpName, E.Department, E.City
ORDER BY TotalRevenue DESC;
```

This report shows individual employee performance with their sales metrics.

### 4.3 Monthly Sales Trend

Analyze sales patterns over time:

```sql
SELECT MONTH(SaleDate) AS SaleMonth,
       COUNT(*) AS NumberOfSales,
       SUM(SaleAmount) AS MonthlyRevenue,
       AVG(SaleAmount) AS AverageOrderValue
FROM Sales
WHERE YEAR(SaleDate) = 2024
GROUP BY MONTH(SaleDate)
ORDER BY SaleMonth;
```

This query extracts month from the date field and groups sales data monthly for trend analysis.

---

## 5. Best Practices

When writing SQL queries, following best practices ensures code quality and maintainability:

1. Use meaningful aliases for tables to improve readability.
2. Always specify column names in INSERT statements rather than relying on column order.
3. Use WHERE clause before GROUP BY to filter data early and improve performance.
4. Add appropriate indexes on columns frequently used in WHERE and JOIN conditions.
5. Avoid SELECT asterisk in production code and explicitly list required columns.
6. Use EXPLAIN statement to analyze query execution plans and optimize slow queries.
7. Write queries in uppercase for SQL keywords and lowercase for table/column names for better readability.
8. Add comments to complex queries explaining the business logic.

---

## Conclusion

SQL is an indispensable skill for anyone working with data. The basic SQL operations enable us to create database structures, insert data, and retrieve information through SELECT queries. Join operations allow us to combine related data from multiple tables, which is essential in normalized database designs. Aggregation functions like COUNT, SUM, and AVG transform raw data into meaningful business insights.

By combining joins with aggregations and grouping, SQL can produce sophisticated analytical reports directly within the database. This makes SQL a powerful tool for data analysis, business intelligence, backend development, and reporting systems. Mastering SQL opens doors to careers in database administration, data science, and software engineering.

Understanding these fundamental concepts provides a solid foundation for more advanced topics like subqueries, stored procedures, triggers, and database optimization.

---

## References

* [MySQL Official Documentation](https://dev.mysql.com/doc/)
* [W3Schools SQL Tutorial](https://www.w3schools.com/sql/)
* [W3Schools MySQL Tutorial](https://www.w3schools.com/mysql/)
* [GeeksforGeeks SQL Tutorial](https://www.geeksforgeeks.org/sql-tutorial/)
* [MySQL Tutorial by TutorialsPoint](https://www.tutorialspoint.com/mysql/index.htm)
* [SQL Joins Explained](https://www.sql-join.com/)
* [Learn SQL - Mode Analytics](https://mode.com/sql-tutorial/)
