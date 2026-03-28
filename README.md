# 🗄️ SQL Self-Learning Journey

## Progress Tracker

| # | Topic | Status |
|---|-------|--------|
| 1 | Filtering & Aggregation — WHERE, GROUP BY, HAVING, TOP | ✅ Done |
| 2 | DDL — CREATE, ALTER, DROP TABLE | ✅ Done |
| 3 | DML — INSERT, UPDATE, DELETE, TRUNCATE | ✅ Done |
| 4 | Filtering Operators — AND, OR, NOT, BETWEEN, IN, LIKE | ✅ Done |
| 5 | JOINs — INNER, LEFT, RIGHT, FULL | ✅ Done |
| 6 | Advanced JOINs — NULL Filtering & CROSS JOIN | ✅ Done |
| 7 | Multi-Table JOINs (3+ Tables) | ✅ Done |
| 8 | Set Operators — UNION, UNION ALL, INTERSECT, EXCEPT | ✅ Done |
| 9 | String Functions — CONCAT, LOWER, UPPER, TRIM, REPLACE, LEN, LEFT, RIGHT, SUBSTRING | ✅ Done |
| 10 | Math Functions — ROUND, ABS | ✅ Done |
| 11 | Date Functions — DATETRUNC, EOMONTH, DATEPART, DATENAME, YEAR, MONTH, DAY | ✅ Done |
| 12 | Date Formatting & Type Conversion — FORMAT, CONVERT, CAST | ✅ Done |
| 13 | Date Arithmetic — DATEADD, DATEDIFF, ISDATE | ✅ Done |
| 14 | NULL Handling — COALESCE, NULLIF, IS NULL | ✅ Done |
| 15 | Data Quality & Cleaning — DATALENGTH, TRIM, Policy Patterns | ✅ Done |
| 16 | CASE WHEN — Conditional Logic & Conditional Aggregation | ✅ Done |
| 17 | Aggregate Functions — COUNT, SUM, AVG, MAX, MIN | ✅ Done |
| 18 | Window Functions — SUM OVER, RANK OVER, ROWS BETWEEN | ✅ Done |
| 19 | Window Functions — COUNT, AVG, MIN, MAX OVER (Real Project Patterns) | ✅ Done |
| 20 | Window Functions — ROW_NUMBER, RANK, DENSE_RANK, NTILE, CUME_DIST | ✅ Done |
| 21 | Window Functions — LAG, LEAD, FIRST_VALUE (Advanced Patterns) | ✅ Done |
| 22 | Schema Exploration — INFORMATION_SCHEMA | ✅ Done |
| 23 | Subqueries — FROM, SELECT, WHERE, IN, ANY, EXISTS | ✅ Done |
| 24 | CTEs — Standalone, Chained & Recursive | ✅ Done |
| 25 | Views — CREATE, DROP, JOIN Views | ✅ Done |
| 26 | CTAS — CREATE TABLE AS SELECT (Snapshot Tables) | ✅ Done |
| 27 | Temp Tables — CREATE, DELETE, SELECT INTO | ✅ Done |
| 28 | Indexes — CLUSTERED, NONCLUSTERED, Composite | ✅ Done |
| 29 | Columnstore Index — HEAP vs RowStore vs ColumnStore | ✅ Done |
| 30 | Indexes — UNIQUE & Filtered Index | ✅ Done |
| 31 | Indexing Strategy — When to Use Which Index | ✅ Done |
| 32 | Index Monitoring — Usage, Missing, Duplicate, Statistics, Fragmentation | ✅ Done |
| 33 | Execution Plan & Query Hints | ✅ Done |
| 34 | Indexing Strategy — Full Framework (4 Strategies) | ✅ Done |
| 35 | Table Partitioning — Function, Filegroup, Scheme, Partitioned Table | ✅ Done |
| 36 | Query Optimization Tips (Tip 1–30) | ✅ Done |
| 37 | Stored Procedures — Parameters, Variables, IF ELSE, Error Handling | ✅ Done |
| 38 | Triggers — DML Triggers (AFTER INSERT) | ✅ Done |
| 39 | Using AI for SQL — Prompting Strategies | ✅ Done |


---

## 1. Filtering & Aggregation — WHERE, GROUP BY, HAVING, TOP

```sql
-- Average score per country, filter out zero scores, only show groups where avg > 430
SELECT 
	avg(score) AS total_score,
	country	
FROM customers
WHERE score != 0
GROUP BY country
HAVING avg(score) > 430
ORDER BY total_score desc;

-- Show top 5 customers with the highest score
select top 5
*
from customers
order by score desc;

-- Show the 2 most recent orders
select top 2 *
from orders
order by order_date DESC;
```

---

## 2. DDL — CREATE, ALTER, DROP TABLE

```sql
-- Create a new table with columns and a primary key constraint
CREATE TABLE persons (
	id INT NOT NULL,
	person_name VARCHAR(50) NOT NULL,
	birth_date DATE,
	phone VARCHAR(15) NOT NULL,
	CONSTRAINT pk_persons PRIMARY KEY (id)
)

-- Add a new email column to the existing table
ALTER TABLE persons
ADD email VARCHAR(50) NOT NULL

-- Check current structure and data of the table
SELECT * FROM persons;

-- Remove the phone column from the table
ALTER TABLE persons
DROP COLUMN phone 

-- Permanently delete the table and all its data
DROP TABLE persons
```

---

## 3. DML — INSERT, UPDATE, DELETE, TRUNCATE

```sql
-- Insert 3 new rows into customers (some values are NULL)
INSERT INTO customers (id, first_name, country, score)
VALUES 
	(8, 'USA', 'MAX', NULL),
	(9, 'Andreas', 'Germany', NULL),
	(10, 'Sahra', NULL, NULL)

SELECT * FROM customers;

-- Insert into persons by selecting from customers (cross-table insert)
INSERT INTO persons(id, person_name, birth_date, phone)
SELECT
id,
first_name,
NULL,
'Unknown'
FROM customers;

SELECT * FROM persons

-- Find all customers where score is NULL
SELECT * 
FROM customers
WHERE score is NULL;;

-- Replace all NULL scores with 0
UPDATE customers
SET score = 0
WHERE score is NULL;

-- Delete customers with id greater than 5
DELETE FROM customers
WHERE id > 5;

-- Delete all rows from persons but keep the table structure
TRUNCATE TABLE persons

SELECT * FROM persons;
```

---

## 4. Filtering Operators — AND, OR, NOT, BETWEEN, IN, LIKE

```sql
-- Customers not from Germany
SELECT * 
FROM customers
WHERE country != 'Germany';

-- Customers with score 500 or higher
SELECT * 
FROM customers
WHERE score >= 500;

-- Customers from USA AND score 500 or higher (both must be true)
SELECT * 
FROM customers
WHERE country = 'USA'
AND score >= 500;

-- Customers from USA OR score above 500 (at least one must be true)
SELECT * 
FROM customers
WHERE country = 'USA'
OR score > 500;

-- Customers who are NOT from USA
SELECT * 
FROM customers
WHERE NOT country  = 'USA';

-- Customers with score between 350 and 500 (inclusive)
SELECT * 
FROM customers
WHERE score BETWEEN 350 AND 500;

-- Customers from USA or Germany only
SELECT * 
FROM customers
WHERE country  IN ('USA','Germany');

-- Customers whose name starts with M, ends with n, or contains r
SELECT * 
FROM customers
WHERE first_name LIKE 'M%'
OR first_name LIKE '%n'
OR first_name LIKE '%r%';
```

---

## 5. JOINs — INNER, LEFT, RIGHT, FULL

```sql
-- View all customers
SELECT * 
FROM customers;

-- View all orders
SELECT * 
FROM orders;

-- INNER JOIN: only customers who have orders (matched rows only)
SELECT 
	c.id,
	c.first_name,
	o.order_id,
	o.sales
FROM customers AS c
INNER JOIN orders AS o
on c.id = o.customer_id;

-- LEFT JOIN: all customers, with order data where available (NULL if no order)
SELECT 
	c.id,
	c.first_name,
	o.order_id,
	o.sales
FROM customers AS c
LEFT JOIN orders AS o
on c.id = o.customer_id;

-- LEFT JOIN flipped: all orders, with customer data where available
SELECT 
	c.id,
	c.first_name,
	o.order_id,
	o.sales
FROM orders AS o
LEFT JOIN customers AS c
on c.id = o.customer_id;

-- FULL JOIN: all rows from both tables, NULLs where there is no match on either side
SELECT 
	c.id,
	c.first_name,
	o.order_id,
	o.sales
FROM orders AS o
FULL JOIN customers AS c
on c.id = o.customer_id;
```

---

## 6. Advanced JOINs — NULL Filtering & CROSS JOIN

```sql
-- Customers who have NEVER placed an order (no match on right side)
SELECT 
	c.id,
	c.first_name,
	o.order_id,
	o.sales
FROM customers AS c
LEFT JOIN orders AS o
on c.id = o.customer_id
WHERE o.customer_id IS NULL;

-- Orders with no matching customer record (orphan orders)
SELECT 
	c.id,
	c.first_name,
	o.order_id,
	o.sales
FROM orders AS o
LEFT JOIN customers AS c
on c.id = o.customer_id
WHERE c.id IS NULL;

-- Rows from EITHER table that have no match on the other side
SELECT 
	c.id,
	c.first_name,
	o.order_id,
	o.sales
FROM orders AS o
FULL JOIN customers AS c
on c.id = o.customer_id
WHERE c.id IS NULL
OR o.customer_id IS NULL;

-- Matched rows only from a LEFT JOIN (same result as INNER JOIN)
SELECT 
	c.id,
	c.first_name,
	o.order_id,
	o.sales
FROM orders AS o
LEFT JOIN customers AS c
on c.id = o.customer_id
WHERE c.id IS NOT NULL;

-- CROSS JOIN: every customer paired with every order (all combinations)
SELECT *
FROM customers
CROSS JOIN orders;
```

---

## 7. Multi-Table JOINs (3+ Tables)

```sql
-- Join 4 tables: get order details with customer, employee, and product info
SELECT 
	o.OrderID, 
	o.Sales, 
	c.FirstName AS CustomerFirstName, 
	c.LastName AS CustomerLastName, 
	p.Product, 
	p.Price, 
	e.FirstName AS EmployeeFirstName, 
	e.LastName AS EmployeeLastName
FROM Sales.Orders as o
LEFT JOIN Sales.Customers AS c
ON o.CustomerID = c.CustomerID
LEFT JOIN Sales.Employees AS e
ON o.SalesPersonID = e.EmployeeID
LEFT JOIN Sales.Products AS p
ON o.ProductID = p.ProductID;

-- Preview all 4 tables individually
SELECT *
FROM Sales.Customers
SELECT *
FROM Sales.Employees
SELECT *
FROM Sales.Orders
SELECT *
FROM Sales.Products

-- Switch to the SalesDB database
USE SalesDB
```

---

## 8. Set Operators — UNION, UNION ALL, INTERSECT, EXCEPT

```sql
-- UNION: combine customer and employee IDs + last names, remove duplicates
SELECT 
	CustomerID as ID,
	LastName
FROM Sales.Customers
UNION
SELECT 
	EmployeeID,
	LastName
FROM Sales.Employees;

-- UNION ALL: combine first and last names from both tables, keep all duplicates
SELECT 
	FirstName,
	LastName
FROM Sales.Customers
UNION ALL
SELECT 
	FirstName,
	LastName
FROM Sales.Employees;

-- EXCEPT: names that exist in Employees but NOT in Customers
SELECT 
	FirstName,
	LastName
FROM Sales.Employees
EXCEPT
SELECT 
	FirstName,
	LastName
FROM Sales.Customers;

-- INTERSECT: names that exist in BOTH Employees and Customers
SELECT 
	FirstName,
	LastName
FROM Sales.Employees
INTERSECT
SELECT 
	FirstName,
	LastName
FROM Sales.Customers;

-- UNION: merge current orders and archive orders into one result, tag each row with its source table
SELECT
	'Orders' AS SourceTable,
	[OrderID]
      ,[ProductID]
      ,[CustomerID]
      ,[SalesPersonID]
      ,[OrderDate]
      ,[ShipDate]
      ,[OrderStatus]
      ,[ShipAddress]
      ,[BillAddress]
      ,[Quantity]
      ,[Sales]
      ,[CreationTime]
FROM Sales.Orders
UNION
SELECT
	'OrdersArchive' AS SourceTable,
	[OrderID]
      ,[ProductID]
      ,[CustomerID]
      ,[SalesPersonID]
      ,[OrderDate]
      ,[ShipDate]
      ,[OrderStatus]
      ,[ShipAddress]
      ,[BillAddress]
      ,[Quantity]
      ,[Sales]
      ,[CreationTime]
FROM Sales.OrdersArchive
ORDER BY OrderID;
```

---

## 9. String Functions — CONCAT, LOWER, UPPER, TRIM, REPLACE, LEN, LEFT, RIGHT, SUBSTRING

```sql
-- Combine first_name and country into one column, also show lower and upper case versions
SELECT 
	first_name,
	country,
	CONCAT(first_name,'-',country) AS name_country,
	LOWER(first_name) AS low_name,
	UPPER(first_name) AS up_name
FROM customers;

-- Find rows where first_name has extra leading or trailing spaces
SELECT
	first_name,
	LEN(first_name) - LEN(TRIM(first_name))
FROM customers
WHERE LEN(first_name) != LEN(TRIM(first_name));
--WHERE first_name != TRIM(first_name);

-- Replace dashes with slashes in a phone number
SELECT 
'123-456-789' AS phone,
REPLACE('123-456-789','-','/') AS clean_phone;

-- Replace file extension .txt with .csv
SELECT 
'report.txt' AS old_file,
REPLACE('report.txt','.txt','.csv') AS new_file;

-- Show the character length of each first_name
SELECT
	first_name,
	LEN(first_name) 
FROM customers;

-- Extract first 2 chars, last 2 chars, and everything except the first character
SELECT
	first_name,
	LEFT(TRIM(first_name), 2) AS first_2_char,
	RIGHT(TRIM(first_name), 2) AS last_2_char,
	SUBSTRING(TRIM(first_name), 2, LEN(first_name)) AS no_first_char
FROM customers;
```

---

## 10. Math Functions — ROUND, ABS

```sql
-- Round a number to 2, 1, and 0 decimal places; get the absolute value of a negative number
SELECT 
3.516,
ROUND(3.516, 2) AS round_2,
ROUND(3.516, 1) AS round_1,
ROUND(3.516, 0) AS round_0,
-10,
ABS(-10)
```

---

## 11. Date Functions — DATETRUNC, EOMONTH, DATEPART, DATENAME, YEAR, MONTH, DAY

```sql
-- Extract and truncate various date parts from a datetime column
SELECT 
	OrderID,
    	CreationTime,
	CAST(DATETRUNC(month, CreationTIme) AS DATE) AS SOMONTH,   -- first day of the month
	EOMONTH(CreationTime) AS EOMONTH,                          -- last day of the month
	DATETRUNC(year, CreationTime) AS year_dt,                  -- truncate to start of year
	DATETRUNC(day, CreationTime) AS day_dt,                    -- truncate to start of day
	DATETRUNC(minute, CreationTime) AS minute_dt,              -- truncate to start of minute
	'2026-3-17' AS HardCoded,                                  -- hardcoded date value
	GETDATE() AS Today,                                        -- current date and time
	DATEPART(year, CreationTime) AS year_dp,                   -- year as number
	DATEPART(month, CreationTime) AS month_dp,                 -- month as number
	DATEPART(day, CreationTime) AS day_dp,                     -- day as number
	DATEPART(hour, CreationTime) AS hour_dp,                   -- hour as number
	DATEPART(quarter, CreationTime) AS quarter_dp,             -- quarter as number
	DATEPART(weekday, CreationTime) AS weekday_dp,             -- weekday as number
	DATEPART(week, CreationTime) AS week_dp,                   -- week number of the year
	DATENAME(month, CreationTime) AS month_dn,                 -- month as text (e.g. March)
	DATENAME(weekday, CreationTime) AS weekday_dn,             -- weekday as text (e.g. Monday)
	YEAR(CreationTime) AS Year,                                -- shorthand for year
	MONTH(CreationTime) AS Month,                              -- shorthand for month
	DAY(CreationTime) AS Day                                   -- shorthand for day
FROM Sales.Orders;

-- Count orders per year
SELECT
	YEAR(OrderDate) ,
	COUNT(1) OrderEachYear
FROM Sales.Orders
GROUP BY YEAR(OrderDate)

-- Count orders per month using month name as text
SELECT
	DATENAME(month, OrderDate),
	COUNT(1) AS OrderEachMonth
FROM Sales.Orders
GROUP BY DATENAME(month, OrderDate) ;

--Use integer for faster process instead of string
-- Filter orders in February using integer (faster than using DATENAME)
SELECT
	*
FROM Sales.Orders
WHERE MONTH(OrderDate) = 2;
```

---

## 12. Date Formatting & Type Conversion — FORMAT, CONVERT, CAST

```sql
-- Display CreationTime in various date formats using FORMAT
SELECT
	OrderID,
	CreationTime,
	FORMAT(CreationTime, 'MM-dd-yyyy') USA_Format,   -- US format: month-day-year
	FORMAT(CreationTime, 'dd-MM-yyyy') EU_Format,    -- EU format: day-month-year
	FORMAT(CreationTime, 'dd') dd,                   -- day number only
	FORMAT(CreationTime, 'ddd') ddd,                 -- abbreviated day name (e.g. Mon)
	FORMAT(CreationTime, 'dddd') dddd,               -- full day name (e.g. Monday)
	FORMAT(CreationTime, 'MM') MM,                   -- month number only
	FORMAT(CreationTime, 'MMM') MMM,                 -- abbreviated month name (e.g. Mar)
	FORMAT(CreationTime, 'MMMM') MMMM               -- full month name (e.g. March)
FROM Sales.Orders;

-- Build a fully custom date label string
SELECT
	OrderID,
	CreationTime,
	'Day ' + FORMAT(CreationTime, 'ddd MM') +
	' Q' + DATENAME(quarter, CreationTime) + ' ' +
	FORMAT(CreationTime, 'yyyy:hh:mm:ss tt') CustomFormat
FROM Sales.Orders;

-- Group orders by month-year label (e.g. Jan 24, Feb 24)
SELECT 
FORMAT(OrderDate, 'MMM yy') OrderDate,
COUNT(1)
FROM Sales.Orders
GROUP BY FORMAT(OrderDate, 'MMM yy');

-- CONVERT: change data types using SQL Server style codes for date display
SELECT
	CONVERT(INT, '123') [String to INT],
	CONVERT(DATE, '2025-08-20') [String to DATE],
	CreationTime,
	CONVERT(DATE, CreationTime) [DateTime to DATE],
	CONVERT(VARCHAR, CreationTime, 32) [USAFormat Style:32],
	CONVERT(VARCHAR, CreationTime, 34) [EUFormat Style:34]
FROM Sales.Orders

-- CAST: standard SQL type conversion (works in all databases)
SELECT
	CAST('123' AS INT) [String to INT],
	CAST(123 AS VARCHAR) [INT to String],
	CAST('2025-08-20' AS DATE) [String to DATE],
	CAST('2025-08-20' AS DATETIME2) [String to Datetime],
	CreationTime,
	CAST(CreationTime AS DATE) [Datetime to DATE]
	FROM Sales.Orders;
```

---

## 13. Date Arithmetic — DATEADD, DATEDIFF, ISDATE

```sql
-- Add and subtract time intervals from a date
SELECT 
	OrderID,
	OrderDate,
	DATEADD(year, 2, OrderDate) TwoYearsLater,       -- add 2 years
	DATEADD(month, 3, OrderDate) ThreeMonthsLater,   -- add 3 months
	DATEADD(day, -10, OrderDate) TenDaysAgo          -- subtract 10 days
FROM Sales.Orders;

-- Calculate each employee's age from their birth date to today
SELECT
	FirstName,
	DATEDIFF(year, BirthDate, GETDATE()) Age,
FROM Sales.Employees;

-- Calculate how many days it took to ship each order
SELECT
	OrderID,
	OrderDate,
	ShipDate,
	DATEDIFF(day, OrderDate, ShipDate) Day2Ship
FROM Sales.Orders;

-- Average shipping days per month
SELECT
	MONTH(OrderDate) OrderDate,
	AVG(DATEDIFF(day, OrderDate, ShipDate)) AVG_Ship
FROM Sales.Orders
GROUP BY MONTH(OrderDate);

--Time Gap Analysis 
-- Show the gap in days between each order and the one before it
SELECT
	OrderID,
	OrderDate CurrentOrderDate,
	LAG(OrderDate) OVER (ORDER BY OrderDate) PreviousOrderDate,
	DATEDIFF(day, LAG(OrderDate) OVER (ORDER BY OrderDate), OrderDate) NrOfDays
FROM Sales.Orders;

-- Check which values are valid dates (returns 1 = valid, 0 = invalid)
SELECT
	ISDATE('123') DateCheck1,
	ISDATE('2025-08-20') DateCheck2,
	ISDATE('08-20-2025') DateCheck3,
	ISDATE('20-08-2025') DateCheck4,
	ISDATE('2025') DateCheck5,
	ISDATE('08') DateCheck6

-- Safely convert only valid date strings; flag bad ones with 9999-01-01
SELECT
	--CAST(OrderDate AS Date) OrderDate,
	OrderDate,
	ISDATE(OrderDate),
	CASE 
	WHEN ISDATE(OrderDate) = 1 THEN CAST(OrderDate AS DATE)
	ELSE '9999-01-01'
	END NewOrderDate
FROM
(
	SELECT '2025-08-20' AS OrderDate UNION
	SELECT '2025-08-21' UNION
	SELECT '2025-08-23' UNION
	SELECT '2025-08'
)t
--WHERE ISDATE(OrderDate) = 0
```

---

## 14. NULL Handling — COALESCE, NULLIF, IS NULL

```sql
-- Replace NULL scores with 0; compare average with and without NULLs
SELECT 
	CustomerID,
	Score,
	COALESCE(Score, 0) Score2,                       -- NULL becomes 0
	AVG(Score) OVER() AvgScores,                     -- avg ignores NULLs
	AVG(COALESCE(Score, 0)) OVER() AvgScores2        -- avg treats NULLs as 0
FROM Sales.Customers;

-- Build full name safely when LastName might be NULL; add bonus to score
SELECT 
	CustomerID,
	FirstName,
	LastName,
	FirstName + ' ' + COALESCE(LastName, '') FullName,   -- avoid NULL concat
	Score,
	COALESCE(Score, 0) + 10 ScoreWithBonus               -- treat NULL as 0 before adding
FROM Sales.Customers;

-- Sort rows by score ascending, push NULLs to the bottom
SELECT 
	CustomerID,
	Score
From Sales.Customers
ORDER BY CASE WHEN Score IS NULL THEN 1 ELSE 0 END, Score ASC;

-- Divide sales by quantity; return NULL instead of error when quantity is 0
SELECT 
	OrderId,
	Sales,
	Quantity,
	Sales / NULLIF(Quantity,0) Price
From Sales.Orders;

-- Show only customers who have a score (exclude NULLs)
SELECT 
	*
From Sales.Customers
WHERE Score IS NOT NULL;

-- Find customers who have no orders at all (NULL from LEFT JOIN)
SELECT 
	c.*,
	O.OrderID
From Sales.Customers c
LEFT JOIN Sales.Orders o
ON c.CustomerID = o.CustomerID
WHERE o.CustomerID IS NULL;
```

---

## 15. Data Quality & Cleaning — DATALENGTH, TRIM, Policy Patterns

```sql
-- Show the difference between NULL, empty string, and whitespace-only values
-- Apply 3 cleaning policies: raw length, trim, nullify blanks, replace with 'Unknown'
WITH Orders AS(
SELECT 1 Id, 'A' Category UNION
SELECT 2, NULL UNION
SELECT 3, '' UNION
SELECT 4, ' '
)
SELECT
	*,
	DATALENGTH(Category) CategoryLEN,               -- raw byte length (NULL=NULL, ''=0, ' '=1)
	DATALENGTH(TRIM(Category)) Policy1,             -- length after trimming spaces
	NULLIF(TRIM(Category), '') Policy2,             -- empty string and spaces become NULL
	COALESCE(NULLIF(TRIM(Category), ''), 'Unknown') Policy3  -- all blanks become 'Unknown'
FROM Orders;
```

---

## 16. CASE WHEN — Conditional Logic & Conditional Aggregation

```sql
-- Categorize each order as High / Medium / Low based on sales, then sum by category
SELECT 
	Category,
	SUM(Sales) TotalSales
FROM(
SELECT
	OrderID,
	Sales,
	CASE 
		WHEN Sales > 50 THEN 'High'
		WHEN Sales > 20 THEN 'Medium'
		ELSE 'Low'
	END Category
FROM Sales.Orders
)t
GROUP BY Category
ORDER BY TotalSales DESC;

-- Convert gender code (M/F) into full text label
SELECT
	EmployeeID,
	FirstName,
	LastName,
	Gender,
	CASE 
		WHEN Gender = 'F' THEN 'Female'
		WHEN Gender = 'M' THEN 'Male'
		ELSE 'Not Available'
	END GenderFullText
FROM Sales.Employees;

-- Map country name to abbreviation using shorthand CASE
SELECT
	CustomerID,
	FirstName,
	LastName,
	Country,
	CASE Country
		WHEN /*Country =*/'Germany' THEN 'DE'
		WHEN /*Country =*/ 'USA' THEN 'US'
		ELSE 'N/A'
	END CountryAbv
FROM Sales.Customers;

-- Show all unique countries in the customers table
SELECT 
	DISTINCT Country
FROM Sales.Customers;

-- Replace NULL scores with 0 using CASE WHEN; compare avg with and without NULLs
SELECT
	CustomerID,
	LastName,
	Score,
	CASE 
		WHEN Score IS NULL THEN 0
		ELSE Score
	END ScoreClean,
	AVG(CASE 
		WHEN Score IS NULL THEN 0
		ELSE Score
	END) OVER() AVGScoreClean,
	AVG(Score) OVER() AvgCustomer
FROM Sales.Customers;

-- Count how many orders per customer had sales above 30 vs total orders
SELECT
	CustomerID,
	SUM(CASE
		WHEN Sales > 30 THEN 1
		ELSE 0
	END) TotalOrdersHighSale,
	COUNT(1) TotalOrders
FROM Sales.Orders
GROUP BY CustomerID;
```

---

## 17. Aggregate Functions — COUNT, SUM, AVG, MAX, MIN

```sql
-- Get order summary stats per customer: total count, total sales, average, highest, lowest
SELECT 
	COUNT(1) TotalOrders,
	SUM(sales) total_sales,
	AVG(sales) avg_sales,
	MAX(sales) highest_sales,
	MIN(sales) lowest_sales
FROM orders
GROUP BY customer_id
```

---

## 18. Window Functions — SUM OVER, RANK OVER, ROWS BETWEEN

```sql
-- SUM at multiple partition levels alongside each individual row
SELECT 
	OrderID,
	OrderDate,
	ProductID,
	OrderStatus
	Sales,
	SUM(Sales) OVER() TotalSales,                                        -- grand total of all rows
	SUM(Sales) OVER(PARTITION BY ProductID) TotalSalesByProduct,         -- total per product
	SUM(Sales) OVER(PARTITION BY ProductID, OrderStatus) TotalSalesByProductAndStatus, -- total per product+status
	RANK() OVER(ORDER BY Sales DESC) RankBySales                         -- rank each row by sales
FROM Sales.Orders;

-- Rank all orders by sales descending (separate query for clarity)
SELECT 
	OrderID,
	OrderDate,
	ProductID,
	OrderStatus,
	Sales,
	RANK() OVER(ORDER BY Sales DESC) RankBySales
FROM Sales.Orders

-- Rolling sum: current row + next 2 rows within each order status group
SELECT 
	OrderID,
	OrderDate,
	OrderStatus,
	Sales,
	SUM(Sales) OVER(PARTITION BY OrderStatus ORDER BY OrderDate
	ROWS BETWEEN CURRENT ROW AND 2 FOLLOWING) TotalSales
FROM Sales.Orders;

-- Rank customers by their total sales (aggregate + window function together)
SELECT 
	CustomerID,
	SUM(Sales) TotalSales,
	RANK() OVER(ORDER BY SUM(Sales) DESC) CustomersRank
FROM Sales.Orders
GROUP BY CustomerID;
```

---

## 19. Window Functions — COUNT, AVG, MIN, MAX OVER (Real Project Patterns)

```sql
-- Count total orders and orders per customer, shown alongside each row
SELECT
	OrderID,
	OrderDate,
	CustomerID,
	COUNT(1) OVER() TotalOrders,                        -- total rows in the whole table
	COUNT(1) OVER(PARTITION BY CustomerID) OrderByCustomer  -- total per customer
FROM Sales.Orders;

-- Count all rows vs count of non-NULL scores only
SELECT
	*,
	COUNT(1) OVER() TotalCustomers,    -- counts all rows including NULLs
	COUNT(Score) OVER() TotalScores    -- counts only rows where Score is not NULL
FROM Sales.Customers;

--Used in Real Project
--Check whether the table 'Sales.Orders' contain any duplicate rows
-- Flag duplicate OrderIDs: any row with CheckPK > 1 is a duplicate
SELECT
	*
FROM(
SELECT 
	OrderID,
	COUNT(1) OVER(PARTITION BY OrderID) CheckPK
FROM Sales.OrdersArchive
)t
WHERE CheckPK > 1

-- Show total sales and sales per product alongside each order row
SELECT 
	OrderID,
	OrderDate,
	Sales,
	ProductID,
	SUM(Sales) OVER() TotalSales,
	SUM(Sales) OVER(PARTITION BY ProductID) SalesByProduct
FROM Sales.Orders

--If calculation result is zero change int to float then round it 2 decimal
-- Calculate each order's percentage of total sales (cast to float to avoid integer division)
SELECT 
	OrderID,
	OrderDate,
	Sales,
	ProductID,
	SUM(Sales) OVER() TotalSales,
	ROUND(CAST(Sales AS float) / SUM(Sales) OVER() * 100, 2) PercentageOfTotal
FROM Sales.Orders

-- Show average sales overall and per product alongside each row
SELECT
	OrderID,
	OrderDate,
	Sales,
	ProductID,
	AVG(Sales) OVER() AvgSales,
	AVG(Sales) OVER(PARTITION BY ProductID) AvgSalesByProduct
FROM Sales.Orders

-- Average score per customer (treating NULLs as 0), shown on each row
SELECT
	CustomerID,
	FirstName,
	LastName,
	Country,
	Score,
	AVG(COALESCE(Score, 0)) OVER() AvgScoresByID
FROM Sales.Customers

-- Show only orders where sales is above the overall average
SELECT
	*
FROM(
SELECT
	OrderID,
	ProductID,
	Sales,
	AVG(COALESCE(Sales, 0)) OVER() AvgSales
FROM Sales.Orders
)t 
WHERE Sales > AvgSales 

-- Show lowest and highest sales overall and per product for each row
SELECT
	OrderID,
	OrderDate,
	ProductID,
	Sales,
	MIN(COALESCE(Sales, 0)) OVER() LowestSales,
	MIN(COALESCE(Sales, 0)) OVER(PARTITION BY ProductID) LowestSalesByProduct,
	MAX(Sales) OVER() HighestSales,
	MAX(Sales) OVER(PARTITION BY ProductID) HighestSalesByProduct,
FROM Sales.Orders

-- Find the employee(s) with the highest salary
SELECT
	*
FROM(
SELECT
	*,
	MAX(Salary) OVER() HighestSalary
FROM Sales.Employees
)t
WHERE Salary = HighestSalary

-- Show how far each order's sales deviates from the lowest and highest
SELECT
	OrderID,
	OrderDate,
	ProductID,
	Sales,
	MIN(COALESCE(Sales, 0)) OVER() LowestSales,
	MAX(Sales) OVER() HighestSales,
	Sales - MIN(Sales) OVER() DeviationFromLowest,
	MAX(Sales) OVER() - Sales DeviationFromHighest
FROM Sales.Orders

--Moving Average (Running Total) and Rolling Total
-- Compare static average vs moving average vs 2-row rolling average per product
SELECT
	OrderID,
	OrderDate,
	ProductID,
	Sales,
	AVG(Sales) OVER(PARTITION BY ProductID) AvgByProduct,                          -- static avg per product
	AVG(Sales) OVER(PARTITION BY ProductID ORDER BY OrderDate) MovingAvg,          -- cumulative avg up to current row
	AVG(Sales) OVER(PARTITION BY ProductID ORDER BY OrderDate
	ROWS BETWEEN CURRENT ROW AND 1 FOLLOWING) RollingAvg                           -- avg of current + next row
FROM Sales.Orders
```

---

## 20. Window Functions — ROW_NUMBER, RANK, DENSE_RANK, NTILE, CUME_DIST

```sql
-- Compare all three ranking functions side by side on the same data
SELECT
	OrderID,
	ProductID,
	Sales,
	ROW_NUMBER() OVER(ORDER BY Sales DESC) SalesRank_Row,    -- always unique, no ties
	RANK() OVER(ORDER BY Sales DESC) SalesRank_Rank,         -- ties share rank, next rank skips
	DENSE_RANK() OVER(ORDER BY Sales DESC) SalesRank_Dense   -- ties share rank, no gap after
FROM Sales.Orders

--Use in Real Project (Top N & Bottom N Analysis)
-- Get the top 1 sale per product (highest sales row per product)
SELECT 
	*
FROM(
SELECT
	OrderID,
	ProductID,
	Sales,
	ROW_NUMBER() OVER(PARTITION BY ProductID ORDER BY Sales DESC) SalesRankByProduct
FROM Sales.Orders)t
WHERE SalesRankByProduct = 1

-- Get the bottom 2 customers by total sales
SELECT
	*
FROM(
SELECT
	CustomerID,
	SUM(Sales) TotalSales,
	ROW_NUMBER() OVER(ORDER BY SUM(Sales)) SalesRank   -- ascending = lowest first
FROM Sales.Orders
GROUP BY CustomerID)t
WHERE SalesRank <= 2

-- Assign a unique sequential ID to all rows in the archive table
SELECT
	ROW_NUMBER() OVER(ORDER BY OrderID, OrderDate) UniqueID,
	*
FROM Sales.OrdersArchive

-- Keep only the most recent version of each duplicate OrderID
SELECT
	*
FROM(
SELECT
	ROW_NUMBER() OVER(PARTITION BY OrderID ORDER BY CreationTime DESC) rn,
	*
FROM Sales.OrdersArchive)t
WHERE rn = 1 --fetching unique row
--WHERE rn > 1 (for detecting bad data)

-- Divide orders into 1, 2, and 3 equal buckets by sales
SELECT
	OrderID,
	Sales,
	NTILE(1) OVER(ORDER BY Sales DESC) OneBucket,    -- all rows in one bucket
	NTILE(2) OVER(ORDER BY Sales DESC) TwoBucket,    -- split into 2 halves
	NTILE(3) OVER(ORDER BY Sales DESC) ThreeBucket   -- split into 3 tiers
FROM Sales.Orders

-- Label each bucket as High / Medium / Low using CASE WHEN
SELECT
	*,
	CASE SalesBucket
		WHEN 1 THEN 'High'
		WHEN 2 THEN 'Medium'
		WHEN 3 THEN 'Low'
	END SalesSegmentation
FROM(		
SELECT
	OrderId,
	Sales,
	NTILE(3) OVER(ORDER BY Sales DESC) SalesBucket
FROM Sales.Orders)t

--To export table into another database (ntile -> union)
-- Split orders into 2 equal buckets for exporting to separate databases
SELECT
	NTILE(2) OVER(ORDER BY OrderID) Buckets,
	*
FROM Sales.Orders

-- Show cumulative distribution of product prices; filter to top 40% most expensive
SELECT
	*,
CONCAT(DistRank * 100, '%') DistRankPercentage
FROM(
SELECT
	Product,
	Price,
	CUME_DIST() OVER(Order by Price DESC) DistRank    -- what % of products cost <= this price
FROM Sales.Products)t
WHERE DistRank <= 0.4
```

---

## 21. Window Functions — LAG, LEAD, FIRST_VALUE (Advanced Patterns)

```sql
-- Month-over-month sales change: calculate absolute and percentage difference from previous month
SELECT
	*,
	CurrentMonthSales - PreviousMonthSales MoM_Change,
	ROUND(CAST((CurrentMonthSales - PreviousMonthSales) AS FLOAT) / PreviousMonthSales *100, 1) MoM_Percent
FROM(
SELECT
	MONTH(OrderDate) OrderMonth,
	SUM(Sales) CurrentMonthSales,
	LAG(SUM(Sales)) OVER(ORDER BY MONTH(OrderDate)) PreviousMonthSales  -- previous month's total
FROM Sales.Orders
GROUP BY MONTH(OrderDate))t

-- For each customer, find the next order date and days between orders; rank by average gap
SELECT
	CustomerID,
	AVG(DayUntilNextOrder) AvgDays,
	RANK() OVER(ORDER BY COALESCE(AVG(DayUntilNextOrder), 99)) RankAvg  -- NULLs ranked last via COALESCE
FROM(
SELECT
	OrderID,
	CustomerID,
	OrderDate CurrentOrder,
	LEAD(OrderDate) OVER(PARTITION BY CustomerID ORDER BY OrderDate) NextOrder,           -- next order date per customer
	DATEDIFF(day, OrderDate, LEAD(OrderDate) OVER(PARTITION BY CustomerID ORDER BY OrderDate)) DayUntilNextOrder  -- days to next order
FROM Sales.Orders)t
GROUP BY CustomerID

-- Show lowest and highest sales per product using FIRST_VALUE; also show deviation from lowest
SELECT
	OrderID,
	ProductID,
	Sales,
	FIRST_VALUE(Sales) OVER(PARTITION BY ProductID ORDER BY Sales) LowestSales,           -- first value when sorted ASC = lowest
	--LAST_VALUE(Sales) OVER(PARTITION BY ProductID ORDER BY Sales
	--ROWS BETWEEN CURRENT ROW AND UNBOUNDED FOLLOWING) HighestSales
	FIRST_VALUE(Sales) OVER(PARTITION BY ProductID ORDER BY Sales DESC) HighestSales,     -- first value when sorted DESC = highest
	MIN(Sales) OVER(PARTITION BY ProductID) LowestSales2,                                 -- alternative using MIN
	MAX(Sales) OVER(PARTITION BY ProductID) HighestSales2,                                -- alternative using MAX
	Sales - FIRST_VALUE(Sales) OVER(PARTITION BY ProductID ORDER BY Sales) SalesDiff      -- how much above the lowest
FROM Sales.Orders
```

---

## 22. Schema Exploration — INFORMATION_SCHEMA

```sql
-- List all unique table names in the current database using the built-in schema view
SELECT
	DISTINCT TABLE_NAME
FROM INFORMATION_SCHEMA.COLUMNS
```

---

## 23. Subqueries — FROM, SELECT, WHERE, IN, ANY, EXISTS

```sql
-- Subquery in FROM: filter products where price is above the overall average
SELECT
	*
FROM
--Subquery
	(SELECT
		ProductID,
		Price,
		AVG(Price) OVER() AvgPrice
	FROM Sales.Products)t
WHERE Price > AvgPrice

--MainQuery
-- Subquery in FROM: rank customers by their total sales using DENSE_RANK
SELECT
	*,
	DENSE_RANK() OVER(ORDER BY TotalSales DESC) CustomerRank
FROM(
--Subquery
	SELECT
		CustomerID,
		SUM(Sales) TotalSales
	FROM Sales.Orders
	GROUP BY CustomerID)t

--MainQuery
-- Subquery in SELECT: add a scalar total order count column to every product row
SELECT
	ProductID,
	Product,
	Price,
	--SubQuery
	(SELECT COUNT(1) FROM Sales.Orders) TotalOrders  -- single value returned and repeated on each row
FROM Sales.Products;

--MainQuery
-- Subquery in FROM used as a JOIN: attach each customer's total order count
SELECT
	c.*,
	o.TotalOrders
FROM Sales.Customers c
LEFT JOIN(
--SubQuery
	SELECT
		CustomerID,
		COUNT(1) TotalOrders
	FROM Sales.Orders
	GROUP BY CustomerID) o
ON c.CustomerID = o.CustomerID

--MainQuery
-- Subquery in WHERE: show products priced above the average product price
SELECT 
	ProductID,
	Price
FROM Sales.Products
WHERE Price > /*SubQuery*/(SELECT AVG(Price) FROM Sales.Products)

--MainQuery
-- Subquery with IN: show orders placed by customers from Germany only
SELECT 
	*
FROM Sales.Orders
WHERE CustomerID IN 
	(SELECT /*SubQuery*/
		CustomerID 
	FROM Sales.Customers
	WHERE Country = 'Germany')

--MainQuery
-- Subquery with ANY: female employees earning more than ANY male employee's salary
SELECT 
	EmployeeID,
	FirstName,
	Gender,
	Salary
FROM Sales.Employees
WHERE Salary > ANY (SELECT Salary FROM Sales.Employees WHERE Gender = 'M')	--SubQuery
AND Gender = 'F'

-- Correlated subquery in SELECT: count total orders per customer row by row
SELECT 
	*,
	(SELECT COUNT(1) FROM Sales.Orders o WHERE o.CustomerID = c.CustomerID) TotalSales
FROM Sales.Customers c

--MainQuery
-- Subquery with EXISTS: show orders where the customer is from Germany
SELECT
	*
FROM Sales.Orders o
WHERE EXISTS(SELECT
		1                          -- EXISTS only checks if a row exists, value doesn't matter
	     FROM Sales.Customers c
	     WHERE Country = 'Germany'
	     AND o.CustomerID = c.CustomerID)
```

---

## 24. CTEs — Standalone, Chained & Recursive

```sql
--Step1: Find the total Sales Per Customer (Standalone CTE)
WITH CTE_Total_Sales AS
(
SELECT 
	CustomerID,
	SUM(Sales) TotalSales
FROM Sales.Orders
GROUP BY CustomerID
)
--Step2: Find the last order date for each customer
, CTE_Last_Order AS 
(
SELECT 
	CustomerID,
	MAX(OrderDate) AS LastOrder
FROM Sales.Orders
GROUP BY CustomerID
)
--Step3: Rank Customers based on Total Sales Per Customer
, CTE_Customer_Rank AS
(
SELECT 
	CustomerID,
	TotalSales,
	RANK() OVER(ORDER BY TotalSales DESC) CustomerRank
FROM CTE_Total_Sales                       -- CTE referencing another CTE
)
--Step4: Segment customer based on their total sales
, CTE_Customer_Segments AS
(
SELECT
	CustomerID,
	CASE 
		WHEN TotalSales > 100 THEN 'High'
		WHEN TotalSales > 80 THEN 'Medium'
		ELSE 'Low'
	END CustomerSegment
FROM CTE_Total_Sales
)
--MainQuery
-- Join all CTEs together into one final customer report
SELECT
	c.CustomerID,
	c.FirstName,
	c.LastName,
	cts.TotalSales,
	clo.LastOrder,
	ccr.CustomerRank,
	ccs.CustomerSegment
FROM Sales.Customers c
LEFT JOIN CTE_Total_Sales cts
ON cts.CustomerID = c.CustomerID
LEFT JOIN CTE_Last_Order clo
ON clo.CustomerID = c.CustomerID
LEFT JOIN CTE_Customer_Rank ccr
ON ccr.CustomerID = c.CustomerID
LEFT JOIN CTE_Customer_Segments ccs
ON ccs.CustomerID = c.CustomerID
ORDER BY COALESCE(CustomerRanK, 99)        -- push NULLs to the bottom
-- 3 OR 5 CTE IN 1 QUERIES, MORE THAN THAT RETHINK UR QUERIES

-- Recursive CTE: generate a sequence of numbers from 1 to 20
WITH Series AS (
--AnchorQuery
SELECT
	1 MyNumber                             -- starting value
UNION ALL
--RecursiveQuery
SELECT
	MyNumber + 1                           -- increment by 1 each recursion
FROM Series
WHERE MyNumber < 20                        -- stop condition
)
--MainQuery
SELECT 
	*
FROM Series
OPTION (MAXRECURSION 200)                  -- override default recursion limit of 100

-- Recursive CTE: show employee hierarchy with their level in the org chart
WITH CTE_Emp_Hierarchy AS
(
--AnchorQuery
SELECT
	EmployeeID,
	FirstName,
	ManagerID,
	1 Level                                -- top-level employees start at level 1
FROM Sales.Employees
WHERE ManagerID IS NULL                    -- anchor: employees with no manager = top of hierarchy
UNION ALL
--RecursiveQuery
SELECT
	e.EmployeeID,
	e.FirstName,
	e.ManagerID,
	Level + 1                              -- each recursive step goes one level deeper
FROM Sales.Employees e
INNER JOIN CTE_Emp_Hierarchy ceh
ON e.ManagerID = ceh.EmployeeID           -- match employee's manager to already-found employees
)
--MainQuery
SELECT
	*
FROM CTE_Emp_Hierarchy
```

---

## 25. Views — CREATE, DROP, JOIN Views

```sql
--Use T-SQL extension if u want to do it in 1 go
-- Drop view if it already exists before recreating (T-SQL pattern)
IF OBJECT_ID('Sales.V_Monthly_Summary', 'V') IS NOT NULL
	DROP VIEW Sales.V_Monthly_Summary;
GO
--CREATE OR REPLACE VIEW (on other SQL)
--DROP VIEW Sales.V_Monthly_Summary
-- Create a view that summarizes monthly order totals
CREATE VIEW Sales.V_Monthly_Summary AS
(
SELECT
	DATETRUNC(month, OrderDate) OrderMonth,
	SUM(Sales) TotalSales,
	COUNT(OrderID) TotalOrder,
	SUM(Quantity) TotalQuantities
FROM Sales.Orders
GROUP BY DATETRUNC(month, OrderDate) 
)

-- Query the view and add a running total on top of it
SELECT
	OrderMonth,
	TotalSales,
	SUM(TotalSales) OVER(ORDER BY OrderMonth) RunningTotal  -- window function on top of a view
FROM Sales.V_Monthly_Summary

-- Query the view without schema prefix
SELECT
	*
FROM V_Monthly_Summary

-- Drop the view permanently
DROP VIEW V_Monthly_Summary

-- Create a detailed order view joining 4 tables
CREATE VIEW Sales.V_Order_Details AS (
	SELECT
		o.OrderID,
		o.OrderDate,
		p.Product,
		p.Category,
		COALESCE(c.FirstName, '') + ' ' + COALESCE(c.LastName, '') CustomerName,   -- handle NULL names
		c.Country CustomerCountry,
		COALESCE(e.FirstName, '') + ' ' + COALESCE(e.LastName, '') SalesPersonName,
		e.Department,
		o.Sales,
		o.Quantity
	FROM Sales.Orders o
	LEFT JOIN Sales.Products p
	ON P.ProductID = o.ProductID
	LEFT JOIN Sales.Customers c
	ON c.CustomerID = o.CustomerID
	LEFT JOIN Sales.Employees e
	ON e.EmployeeID = o.SalesPersonID
)

-- Query the EU-only view (created below)
SELECT
	*
FROM Sales.V_Order_Details_EU

-- Create a filtered view: same as V_Order_Details but only non-USA customers
CREATE VIEW Sales.V_Order_Details_EU AS (
	SELECT
		o.OrderID,
		o.OrderDate,
		p.Product,
		p.Category,
		COALESCE(c.FirstName, '') + ' ' + COALESCE(c.LastName, '') CustomerName,
		c.Country CustomerCountry,
		COALESCE(e.FirstName, '') + ' ' + COALESCE(e.LastName, '') SalesPersonName,
		e.Department,
		o.Sales,
		o.Quantity
	FROM Sales.Orders o
	LEFT JOIN Sales.Products p
	ON P.ProductID = o.ProductID
	LEFT JOIN Sales.Customers c
	ON c.CustomerID = o.CustomerID
	LEFT JOIN Sales.Employees e
	ON e.EmployeeID = o.SalesPersonID
	WHERE c.Country != 'USA'               -- filter to EU customers only
```

---

## 26. CTAS — CREATE TABLE AS SELECT (Snapshot Tables)

```sql
--TSQL
-- Drop the table if it already exists before recreating
IF OBJECT_ID('Sales.MonthlyOrders', 'U') IS NOT NULL
	DROP TABLE Sales.MonthlyOrders;
GO
--CTAs
-- Create a new physical table from a SELECT result (snapshot of aggregated data)
SELECT
	DATENAME(month, OrderDate) OrderMonth,
	COUNT(OrderID) TotalOrder
INTO Sales.MonthlyOrders                   -- creates and populates the table in one step
FROM Sales.Orders
GROUP BY DATENAME(month, OrderDate)
--Use for CTAs also for snapshot (u can keep analyzing when table keep getting updated real-time)
--If u thought view speed is too slow for project, prepare CTAs in advance
```

---

## 27. Temp Tables — CREATE, DELETE, SELECT INTO

```sql
-- Select all orders into a temp table (# prefix = temp table, lives only in current session)
SELECT
	*
--INTO #Orders
FROM Sales.Orders

-- Delete delivered orders from the temp table
DELETE FROM #Orders
WHERE OrderStatus = 'Delivered'

-- Persist the cleaned temp table data into a permanent table
SELECT 
	*
INTO Sales.OrdersTest
FROM #Orders
```

---

## 28. Indexes — CLUSTERED, NONCLUSTERED, Composite

```sql
-28.
-- Query a table; note: filter columns should match index columns for best performance
SELECT
	*
--INTO Sales.DBCustomers
--FROM Sales.Customers
FROM Sales.DBCustomers
WHERE Country = 'USA' AND Score > 500 --When searching condition make sure to match index to be faster & index is using leftmost prefix

-- Create a clustered index on CustomerID (physically sorts the table by this column)
CREATE CLUSTERED INDEX idx_DBCustomers_CustomerID
ON Sales.DBCustomers(CustomerID)

-- Create a nonclustered index on LastName (separate lookup structure)
CREATE NONCLUSTERED INDEX idx_DBCustomers_LastName
ON Sales.DBCustomers(LastName)

-- Create a composite nonclustered index on Country + Score (covers both filter columns)
CREATE INDEX idx_DBCustomers_CountryScore --NONCLUSTERED INDEX IS DEFAULTss
ON Sales.DBCustomers(Country,Score) --Index will be used in 1 condition search if only the left one is used

--Clustered index one per table -> if u accidentaly build wrong index -> drop then create again
-- Drop a clustered index (must drop before creating a different one on the same table)
DROP INDEX idx_DBCustomers_CustomerID ON Sales.DBCustomers
```

---

## 29. Columnstore Index — HEAP vs RowStore vs ColumnStore

```sql
-- Drop existing clustered rowstore index to make room for columnstore
DROP INDEX idx_DBCustomers_CustomerID ON Sales.DBCustomers

-- Create a clustered columnstore index (best for analytical/aggregation queries on large tables)
CREATE CLUSTERED COLUMNSTORE INDEX idx_DBCustomers_CS
ON Sales.DBCustomers

--HEAP
-- Create a heap table (no index at all, fastest inserts, used for staging)
SELECT *
INTO FactInternetSales_HP
FROM FactInternetSales

--RowStore
-- Create a rowstore table then add a clustered index (best for OLTP / row lookups)
SELECT *
INTO FactInternetSales_RS
FROM FactInternetSales

CREATE CLUSTERED INDEX idx_FactInternetSales_RS_PK
ON FactInternetSales_RS (SalesOrderNumber, SalesOrderLineNumber)

--ColumnStore
-- Create a columnstore table (best for OLAP / aggregation on large fact tables)
SELECT *
INTO FactInternetSales_CS
FROM FactInternetSales

CREATE CLUSTERED COLUMNSTORE INDEX idx_FactInternetSales_CS_PK
ON FactInternetSales_CS
```

---

## 30. Indexes — UNIQUE & Filtered Index

```sql
-- View all products
SELECT * FROM Sales.Products

-- Create a unique index: enforce no duplicate product names
CREATE UNIQUE NONCLUSTERED INDEX idx_Products_Product
ON Sales.Products(Product)

-- Try inserting a row; will fail if 'Caps' already exists (unique index enforces it)
INSERT INTO Sales.Products(ProductID, Product)
VALUES(106, 'Caps')

-- View USA customers only
SELECT * FROM Sales.Customers
WHERE Country = 'USA'

--Filtered Index
-- Create a filtered index: only indexes USA rows, smaller and faster for USA-only queries
CREATE NONCLUSTERED INDEX idx_Customers_Country
ON Sales.Customers(Country)
WHERE Country = 'USA' 
```

---

## 31. Indexing Strategy — When to Use Which Index

```sql
/*When to use index
HEAP
Fast Inserts
(For Staging Tables)

Clustered Rowstore Index(OLTP -> transaction)
For Primary keys,if not then for date columns

Columnstore Index(OLAP -> DWH Reporting System)
For Analytical Queries Reduce Size of Large Table

Non-Clustered Index
For non-PK columns (Foreign keys, Joins, and Filters)

Filtered Index
Target Subset of Data, Reduce Size oof Index

Unique Index
Enforce Uniqueness, Improve Query Speed
```

---

## 32. Index Monitoring — Usage, Missing, Duplicate, Statistics, Fragmentation

```sql
--List all index on specific table
sp_helpindex 'Sales.DBCustomers'

--Monitoring Index Usage
--If theres similar index and only one get used a lot and other one unused(NULL) then u can drop it
--For now im using Express edition locally so the number might be reset every session(lots of NULL) but in real project the number gonna be totally different than here(more realistic)
-- Monitor all indexes: usage stats (seeks, scans, lookups, updates) per table
SELECT 
	tbl.name TableName,
	idx.name IndexName,
	idx.type_desc IndexType,
	idx.is_primary_key IsPrimaryKey,
	idx.is_unique IsUnique,
	idx.is_disabled IsDisabled,
	s.user_seeks UserSeeks,       -- how many times index was used for seek (good)
	s.user_scans UserScans,       -- how many times full scan was done (less ideal)
	s.user_lookups UserLookups,   -- key lookups (may indicate missing included columns)
	s.user_updates UserUpdates,   -- how many times index was updated (write cost)
	COALESCE(s.last_user_seek,s.last_user_scan) LastUpdate
FROM sys.indexes idx
JOIN sys.tables tbl
ON idx.object_id = tbl.object_id
LEFT JOIN sys.dm_db_index_usage_stats s
ON s.object_id = idx.object_id
AND s.index_id = idx.index_id
ORDER BY tbl.name, idx.name

-- View raw index usage stats from the DMV
SELECT * FROM sys.dm_db_index_usage_stats
--If theres a lot unused index u need make decision about it (drop it or not)

--Monitor Missing Indexes
-- Run a query that might benefit from an index (SQL Server will detect it internally)
SELECT 
	fs.SalesOrderNumber,
	dp.EnglishProductName,
	dp.color
FROM FactInternetSales fs
INNER JOIN DimProduct dp
ON fs.ProductKey = dp.ProductKey
WHERE dp.Color = 'Black'
AND fs.OrderDateKey BETWEEN 20101229 AND 20101231

--Find the recommendation of missing indexes from SQL database
-- View SQL Server's recommendations for missing indexes
SELECT * FROM sys.dm_db_missing_index_details

--Monitor Duplicate Indexes
-- Find columns that have more than one index — potential duplicates to clean up
SELECT 
	tbl.name TableName,
	col.name IndexColumn,
	idx.name IndexName,
	idx.type_desc IndexType,
	COUNT(1) OVER (PARTITION BY tbl.name, col.name) ColumnCount  -- count indexes per column
FROM sys.indexes idx
JOIN sys.tables tbl ON idx.object_id = tbl.object_id
JOIN sys.index_columns ic ON idx.object_id = ic.object_id AND idx.index_id = ic.index_id
JOIN sys.columns col ON ic.object_id = col.object_id AND ic.column_id = col.column_id
ORDER BY ColumnCount DESC

--Update Statistics
-- View all statistics and how stale they are (how many rows changed since last update)
SELECT
	SCHEMA_NAME(t.schema_id) SchemaName,
	t.name TableName,
	s.name StatisticName,
	sp.last_updated LastUpdate,
	DATEDIFF(day, sp.last_updated, GETDATE()) LastUpdateDay,
	sp.rows 'Rows',
	sp.modification_counter ModificationsSinceLastUpdate  -- high number = stale statistics
FROM sys.stats s
JOIN sys.tables t
ON s.object_id = t.object_id
CROSS APPLY sys.dm_db_stats_properties(s.object_id, s.stats_id) sp
ORDER BY 
sp.modification_counter DESC;

-- Manually update statistics for one table
UPDATE STATISTICS Sales.Customers --_WA_Sys_00000002_48CFD27E (if not specify its gonna update whole table statistics)

-- Update statistics for all tables in the database
EXEC sp_updatestats

/*When to update statistics
1.Weekly job to update statistics on weekends
2.After migrating data
*/

--Monitor Fragmentation
-- View fragmentation level for all indexes in the database
SELECT *
FROM sys.dm_db_index_physical_stats(DB_ID(), NULL, NULL, NULL, 'LIMITED')

/*avg_fragmentation_in_percent
indicate how out-of-order pages are within the index
<10% means no fragmentation (NO ACTION NEEDED) -> 10-30% (REORGANIZE) -> >30% (REBUILD WHOLE INDEX)*/

-- View fragmentation with table and index names for easier reading
SELECT
	tbl.name TableName,
	idx.name IndexName,
	s.avg_fragmentation_in_percent,  -- fragmentation % (higher = more disorder)
	s.page_count
FROM sys.dm_db_index_physical_stats(DB_ID(), NULL, NULL, NULL, 'LIMITED') s
INNER JOIN sys.tables tbl
ON s.object_id = tbl.object_id
INNER JOIN sys.indexes idx
ON idx.object_id = s.object_id
AND idx.index_id = s.index_id
ORDER BY s.avg_fragmentation_in_percent DESC

--Reorganize
-- Reorganize index: online, lightweight fix for 10-30% fragmentation
ALTER INDEX idx_DBCustomers_CS ON Sales.DBCustomers REORGANIZE --(After this avg_fragmentation_in_percent should be 0%)

--Rebuild (do this if above >30%)
-- Rebuild index: heavier operation, fully reconstructs the index, use for >30% fragmentation
ALTER INDEX idx_Customers_Country ON Sales.Customers REBUILD
```

---

## 33. Execution Plan & Query Hints

```sql
-- Run a query and check the execution plan in SSMS (Ctrl+M or Estimated Plan button)
SELECT *
FROM FactResellerSales
WHERE CarrierTrackingNumber = '4911-403C-98'

-- Add an index to improve the query above; then re-check the execution plan
CREATE NONCLUSTERED INDEX idx_FactReseller_CTA
ON FactResellerSales (CarrierTrackingNumber)

-- Force SQL Server to use a specific index or join strategy using hints
SELECT 
	o.Sales,
	c.Country
FROM Sales.Orders o
LEFT JOIN Sales.Customers c WITH (INDEX([])) --WITH (FORCESEEK)  -- hint: force an index seek
ON o.CustomerID = c.CustomerID
--OPTION (HASH JOIN)                                              -- hint: force hash join algorithm
```

---

## 34. Indexing Strategy — Full Framework (4 Strategies)

```sql
/*34.Indexing Strategy
Avoid over indexing (less is more)
*/


/*1.Initial Indexing Strategy
-OLAP (Analytical)
Database >ETL> DWH -> Dashboard
-OLTP (Online Transaction Processing -> e-commerce,finance,banking)
Database(write from app) -> App(read from database)

Two Strategies
-Optimize READ Performace (OLAP) -> COLUMNSTORE INDEX or
-Optimize WRITE Performance (OLTP) -> CLUSTERED INDEX PK
*/

/*2.Usage Pattern Indexing
[-]Identify frequently used Table and Columns(Use AI to help)
Analyzing the following SQL queries and generate a report on table and column usage statistics.
For each table, provide:
-The total number of times the table is used across all queries.
-A break down of each column appears.
-The number of times each column appears.
The primary purpose of the column's usage(e.g., filterin, joining, grouping, aggregating).
Sort the tables in descending order based on their total usage.

[-]Choose Right Index
[-]Test Index
*/

/*3.Scenario Based Indexing
[-]Identify Slow Queries*/
-- Find the top 10 slowest queries by total elapsed time using query stats DMV
SELECT 
	TOP 10
	qs.total_elapsed_time TotalElapsedTime,    -- total time spent on this query
	qs.execution_count,                        -- how many times it ran
	qs.total_worker_time TotalCPUTime,         -- total CPU time used
	qs.last_execution_time,
	qt.text QueryText                          -- the actual SQL text
FROM
	sys.dm_exec_query_stats qs
CROSS APPLY
	sys.dm_exec_sql_text(qs.sql_handle) qt    -- get SQL text from the handle
ORDER BY
		qs.total_elapsed_time DESC;

/*
{-]Check Execution Plan
[-]Choose Right Index
[-](Test)Compare Execution Plan
*/

/*4.Monitoring and Maintenance
[-]Monitor Index Usage
[-]Monitor Missing Index	
[-]Monitor Duplicate Index
[-]Update Statistics

[-]Monitor Fragmentation
Build autoomated dashboard in PowerBI or Tableau for database metadata 
in ordeer to monitor it's health or using other tools that more advance
*/
```

---

## 35. Table Partitioning — Function, Filegroup, Scheme, Partitioned Table

```sql
--Step1 : Create a Partition Function
-- Define how data is split: RANGE LEFT means boundary value belongs to the left partition
CREATE PARTITION FUNCTION PartitionByYear (DATE)
AS RANGE LEFT FOR VALUES ('2023-12-31', '2024-12-31', '2025-12-31')

--Query lists all existing Partition Function
-- Check all partition functions in the database
SELECT
	name,
	function_id,
	type,
	type_desc,
	boundary_value_on_right
FROM sys.partition_functions

-- Remove partition function (must drop scheme first if it exists)
DROP PARTITION FUNCTION PartitionByYear

--Step2 : Create Filegroups
-- Add separate filegroups for each year's data (physical storage separation)
ALTER DATABASE SalesDB ADD FILEGROUP FG_2023;
ALTER DATABASE SalesDB ADD FILEGROUP FG_2024;
ALTER DATABASE SalesDB ADD FILEGROUP FG_2025;
ALTER DATABASE SalesDB ADD FILEGROUP FG_2026;

-- Remove a filegroup (must be empty first)
ALTER DATABASE SalesDB REMOVE FILEGROUP FG_2023;

--Query lists all existing Filegroups
-- View all filegroups in the current database
SELECT *
FROM sys.filegroups
WHERE type = 'FG'

--Step3 : Add .ndf Files to Each Filegroup
-- Attach a physical .ndf file to a filegroup so it can store data
ALTER DATABASE SalesDB ADD FILE
(
	NAME = P_2026, --Logical Name
	FILENAME = 'D:\Program Files\Microsoft SQL Server\MSSQL17.SQLEXPRESS\MSSQL\DATA\P_2026.ndf'
) TO FILEGROUP FG_2026

--Query lists all existing Filegroups and Files
-- View filegroup names, logical file names, physical paths, and sizes
SELECT
	fg.name FileGroupName,
	mf.name LogicalFileName,
	mf.physical_name PhysicalFilePath,
	mf.size / 128 SizeInMB              -- convert pages to MB (1 page = 8KB, 128 pages = 1MB)
FROM
	sys.filegroups fg
JOIN
	sys.master_files mf ON fg.data_space_id = mf.data_space_id
WHERE 
	mf.database_id = DB_ID('SalesDB');

--Step4 : Create Partition Scheme
-- Map each partition from the function to a filegroup
CREATE PARTITION SCHEME SchemePartitionByYear
AS PARTITION PartitionByYear
TO (FG_2023, FG_2024, FG_2025, FG_2026)  -- 4 filegroups for 3 boundaries = 4 partitions

-- Remove the partition scheme
DROP PARTITION SCHEME SchemePartitionByYear;

--Query lists all Partition Scheme
-- View all partition schemes and which filegroup each partition maps to
SELECT
	ps.name PartitionScheme,
	pf.name PartitionFunctionName,
	ds.destination_id PartitionNumber,
	fg.name FileGroupName
FROM 
	sys.partition_schemes ps
JOIN
	sys.partition_functions pf ON ps.function_id = pf.function_id
JOIN
	sys.destination_data_spaces ds ON ps.data_space_id = ds.partition_scheme_id
JOIN
	sys.filegroups fg ON ds.data_space_id = fg.data_space_id

--Step5 : Create the Partitioned Table
-- Create a table that uses the partition scheme; data is routed by OrderDate
CREATE TABLE Sales.Order_Partitioned
(
	OrderID INT,
	OrderDate DATE,
	Sales INT
) ON SchemePartitionByYear (OrderDate)    -- OrderDate determines which partition each row goes to

--Step6 : Insert Data Into the Partitioned Table	
-- Each row goes to its partition automatically based on OrderDate
INSERT INTO Sales.Order_Partitioned VALUES(1, '2023-05-15', 100);
INSERT INTO Sales.Order_Partitioned VALUES(1, '2024-05-15', 100);
INSERT INTO Sales.Order_Partitioned VALUES(1, '2025-05-15', 100);
INSERT INTO Sales.Order_Partitioned VALUES(1, '2026-05-15', 100);

-- View all rows in the partitioned table
SELECT * FROM Sales.Order_Partitioned

--Query lists all partitioned table
-- Verify each partition number, its filegroup, and how many rows it holds
SELECT
	p.partition_number PartitionNumber,
	f.name PartitionFileGroup,
	p.rows NumberOfRows
FROM sys.partitions p
JOIN sys.destination_data_spaces dds ON p.partition_number = dds.destination_id
JOIN sys.filegroups f ON dds.data_space_id = f.data_space_id
WHERE OBJECT_NAME(p.object_id) = 'Order_Partitioned';
```

---

## 36. Query Optimization Tips (Tip 1–30)

```sql
--Tip1 : Select Only What You Need
-- Avoid SELECT * in production; only pull the columns you actually need
SELECT TOP 3
* 
FROM Sales.Customers

--Tip2 : Avoid unecessary DISTINCT and ORDER BY
--Tip3 : Create NONCLUSTERED [INDEX] on frequently used Columns in WHERE clause
-- Filter by OrderStatus; create an index to speed this up
SELECT * FROM Sales.Orders WHERE OrderStatus = 'Delivered'

CREATE NONCLUSTERED INDEX Idx_Orders_OrderStatus ON Sales.Orders(OrderStatus)

--Tip4 : Avoid applying functions to columns in WHERE clauses (stop u from using index)
-- LIKE with leading character still allows index use if the wildcard is at the end
SELECT * FROM Sales.Orders
WHERE FirstName LIKE 'A%'

--Tip5 : Use leading wildcards occasionaly '%...' (stop u from using index)
--Tip6 : Use IN instead of multiple OR
--Tip7 : INNER JOIN is the best performance -> RIGHT/LEFT JOIN -> OUTER JOIN (Use INNER when possible)
--Tip8 : Use Explicit Join (ANSI Join) instead of Implicit Join (non-ANSI Join)
-- Explicit JOIN syntax (preferred): clearly shows the join condition
SELECT o.OrderID, c.FirstName
FROM Sales.Customers c
INNER JOIN Sales.Orders o
ON c.CustomerID = o.CustomerID

--Tip9 : Make sure to [INDEX] the columns used in the ON clause
-- Index the foreign key column used in JOIN to avoid table scans
CREATE NONCLUSTERED INDEX Idx_Orders_CustomerID ON Sales.Orders(CustomerID)

--Tip10 : Filter before joining 
--Small/Medium Tables (Filter after JOIN)
-- For smaller tables: filter in WHERE after the join is fine
SELECT c.FirstName, o.OrderID
FROM Sales.Customers c
INNER JOIN Sales.Orders o
ON c.CustomerID = o.CustomerID
WHERE o.OrderStatus = 'Delivered'
--Big Tables (Filter before JOIN [Subquery])
-- For large tables: pre-filter inside a subquery before joining to reduce rows early
SELECT c.FirstName, o.OrderID
FROM Sales.Customers c
INNER JOIN (SELECT OrderID, CustomerID FROM Sales.Orders WHERE OrderStatus = 'Delivered') o
ON c.CustomerID = o.CustomerID

--Tip11 : Aggregate Before Joining
--Small/Medium Tables (Grouping and Joining)
-- For smaller tables: group after joining
SELECT c.CustomerID, c.FirstName, COUNT(o.OrderID) OrderCount
FROM Sales.Customers c
INNER JOIN Sales.Orders o
ON c.CustomerID = o.CustomerID
GROUP BY c.CustomerID, c.FirstName
--Big Tables (Pre-aggregated Subquery)
-- For large tables: aggregate first in a subquery, then join the summary
SELECT c.CustomerID, c.FirstName, o.OrderCount
FROM Sales.Customers c
INNER JOIN (
	SELECT CustomerID, COUNT(OrderID) OrderCount
	FROM Sales.Orders
	GROUP BY CustomerID
) o
ON c.CustomerID = o.CustomerID

--Tip 12 : Use UNION instead of OR in Joins
-- Separate join conditions into two queries with UNION instead of OR in ON clause
SELECT o.OrderID, c.FirstName
FROM Sales.Customers c
INNER JOIN Sales.Orders o
ON c.CustomerID = o.CustomerID
UNION 
SELECT o.OrderID, c.FirstName
FROM Sales.Customers c
INNER JOIN Sales.Orders o
ON c.CustomerID = o.SalesPersonID

--Tip13 : Check for Nested Loops and use SQL HINTS (check Execution Plan if Nested Loops being used or not)
-- Force SQL Server to use hash join instead of nested loops (useful for large tables)
SELECT o.OrderID, c.FirstName
FROM Sales.Customers c
INNER JOIN Sales.Orders o
ON c.CustomerID = o.CustomerID
OPTION (HASH JOIN)

--Tip14 : Use UNION ALL instead of using UNION | duplicates are acceptable
--Tip15 : Use UNION ALL + DISTINCT instead of using UNION | duplicates are NOT acceptable
-- UNION ALL is faster than UNION; add DISTINCT only if you truly need unique rows
SELECT DISTINCT CustomerID
FROM (
	SELECT CustomerID FROM Sales.Orders
	UNION ALL
	SELECT CustomerID FROM Sales.OrdersArchive
) CombinedData

--Tip16 : Use COLUMNSTORE [INDEX] for Aggregations on Large Table
-- Aggregation query that benefits from columnstore index on large tables
SELECT CustomerID, COUNT(OrderID) OrderCount
FROM Sales.Orders
GROUP BY CustomerID

CREATE CLUSTERED COLUMN INDEX Idx_Orders_Columnstore ON Sales.Orders  -- enables batch mode processing

--Tip17 : Pre-Aggregate Data and store it in new Table for Reporting
-- Pre-compute and store aggregated results so reports don't recalculate every time
SELECT MONTH(OrderDate) OrderMonth, SUM(Sales) TotalSales
INTO Sales.SalesSummary
FROM Sales.Orders
GROUP BY MONTH(OrderDate)

-- Query the pre-aggregated summary table instead of the raw orders table
SELECT OrderMonth, TotalSales FROM Sales.SalesSummary

--Tip18 : JOIN VS EXISTS
--JOIN (Best Practice : If the Performance equals to EXISTS)
-- Use JOIN when you also need columns from the joined table
SELECT o.OrderID, o.Sales
FROM Sales.Orders o
INNER JOIN Sales.Customers c
ON o.CustomerID = c.CustomerID
WHERE c.Country = 'USA'
--EXISTS (Best Practice : Use it for Large Tables)
-- Use EXISTS when you only need to check if a match exists (no columns needed from the other table)
SELECT o.OrderID, o.Sales
FROM Sales.Orders o
WHERE EXISTS (
	SELECT 1                              -- value doesn't matter; only checks row existence
	FROM Sales.Customers c
	WHERE c.CustomerID = o.CustomerID
	AND c.Country = 'USA'
)

--Tip19 : Avoid Redundant Logic in Your Query (Use CASE WHEN END)
-- Calculate salary status in one pass using CASE WHEN instead of multiple queries
SELECT
	EmployeeID,
	FirstName,
	CASE 
		WHEN Salary > AVG(Salary) OVER() THEN 'Above Average'
		WHEN Salary < AVG(Salary) OVER() THEN 'Below Average'
		ELSE 'Average'
	END Status
FROM Sales.Employees

--Tip20 : Avoid Data Types VARCHAR & TEXT if Possible (VARCHAR is better than TEXT)
--VARCHAR, TEXT, INT, FLOAT, DATE
-- Use appropriate data types; avoid VARCHAR(MAX) unless truly needed
CREATE TABLE CustomersInfo(
	CustomerID INT PRIMARY KEY CLUSTERED,
	FirstName VARCHAR(MAX) NOT NULL, --VARCHAR(50)       -- too large, should be VARCHAR(50)
	LastName VARCHAR(50) NOT NULL,
	Country VARCHAR(255) NOT NULL, --VARCHAR(50)         -- too large, should be VARCHAR(50)
	TotalPurchases FLOAT,
	Score INT,
	BirthDate DATE,
	EmployeeID INT,
	CONSTRAINT FK_CustomersInfo_EmployeeID FOREIGN KEY (EmployeeID)
		REFERENCES Sales.Employees(EmployeeID)
)

-- Index the foreign key column for faster joins
CREATE NONCLUSTERED INDEX IX_Good_Customers_EmployeeID 
ON CustomersInfo(EmployeeID)
--Tip21 : Avoid (MAX) unnecessarily large lengths in data types
--Tip22 : Use the NOT NULL constraint where applicable
--Tip23 : Ensure all your tables have a Clustered Primary Key
--Tip24 : Create a NONCLUSTERED INDEX for foreign keys that are used frequently
--Tip25 : Avoid Over Indexing
--Tip26 : Drop unused Indexes
--Tip27 : Update Statistics (Weekly)
--Tip28 : Reorganize and Rebuild Indexes (Weekly)
--Tip29 : Partition Large Tables (Facts) to improve performance -> apply COLUMNSTORE INDEX for best results
--Tip30 : Focus on Writing Clear Queries, Optimize Performance only when Necessary, Always Test Using Execution Plan
```

---

## 37. Stored Procedures — Parameters, Variables, IF ELSE, Error Handling

```sql
--Step1 : Write a Query
-- For US Customers Find the Total Number of Customers and the Average Score
SELECT
	COUNT(1) TotalCustomers,
	AVG(Score) AvgScore
FROM Sales.Customers
WHERE Country = 'USA'

--Step2 : Turning the Query Into a Stored Procedure (Parameters, Variables, Control Flow[IF ELSE], Error Handling, Styling)
--CREATE PROCEDURE GetCustomerSummary AS
-- ALTER lets you update the procedure without dropping it first; default country = 'USA'
ALTER PROCEDURE GetCustomerSummary @Country NVARCHAR (50) = 'USA'
AS
BEGIN
	BEGIN TRY
		DECLARE @TotalCustomers INT, @AvgScore FLOAT;  -- declare variables to store results

		--Step1 : Prepare & Cleanup Data
		--==============================
		-- Check if any NULL scores exist for this country before calculating
		IF EXISTS (SELECT 1 FROM Sales.Customers WHERE Score IS NULL AND Country = @Country)
		BEGIN
			PRINT('Updating NULL Scores to 0');
			UPDATE Sales.Customers
			SET Score = 0
			WHERE Score IS NULL AND Country = @Country;  -- clean NULLs before aggregating
		END

		ELSE
		BEGIN
			PRINT('No NULL Scores found')
		END;

		--Step2 : Generating Reports
		--==========================
		--Calculate Total Customers and Average Score for specific Country
		-- Store results into variables using SELECT @var = value pattern
		SELECT
			@TotalCustomers = COUNT(1),
			@AvgScore = AVG(Score)
		FROM Sales.Customers
		WHERE Country = @Country;

		-- Print summary to the messages tab
		PRINT 'Total Customers from ' + @Country + ':' + CAST(@TotalCustomers AS NVARCHAR);
		PRINT 'Average Score from ' + @Country + ':' + CAST(@AvgScore AS NVARCHAR);

		--Calculate Total Orders and Total Sales for specific Country
		-- Return a result set for orders joined to the filtered country
		SELECT
			COUNT(OrderID) TotalOrders,
			SUM(Sales) TotalSales
		FROM Sales.Orders o
		JOIN Sales.Customers c
		ON c.CustomerID = o.CustomerID
		WHERE Country = @Country;

	END TRY
	BEGIN CATCH
		--Error Handling
		--==============
		-- If anything inside TRY fails, catch and print the error details
		PRINT ('An error occured.');
		PRINT ('Error Massage: ' + ERROR_MESSAGE());
		PRINT ('Error Number: ' + CAST(ERROR_NUMBER() AS NVARCHAR));
		PRINT ('Error Line: ' + CAST(ERROR_LINE() AS NVARCHAR));
		PRINT ('Error Procedure: ' + ERROR_PROCEDURE());

	END CATCH
END
GO

--Step3: Execute the Stored Procedure
-- Run the procedure with a custom country parameter
EXEC GetCustomerSummary @Country = 'Germany'
```

---

## 38. Triggers — DML Triggers (AFTER INSERT)

```sql
-- Create a log table to record when new employees are added
CREATE TABLE Sales.EmployeeLogs (
	LogID INT IDENTITY(1,1) PRIMARY KEY,
	EmployeeID INT,
	LogMessage VARCHAR(255),
	LogDate DATE
)

-- Create a trigger that fires automatically after any INSERT on Sales.Employees
CREATE TRIGGER trg_AfterInsertEmployee ON Sales.Employees
AFTER INSERT
AS
BEGIN
	-- INSERTED is a special virtual table holding the newly inserted rows
	INSERT INTO Sales.EmployeeLogs (EmployeeID, LogMessage, LogDate)
	SELECT
		EmployeeID,
		'New Employee Added =' + CAST(EmployeeID AS VARCHAR),  -- log message with employee ID
		GETDATE()
	FROM INSERTED 
END

-- Check the log table (should be empty before any insert)
SELECT * FROM Sales.EmployeeLogs

-- Insert a new employee; the trigger will fire automatically and write to EmployeeLogs
INSERT INTO Sales.Employees
VALUES
(
6, 'Maria', 'Doe', 'HR', '1988-01-12', 'F', 80000, 3
)
```

---

## 39. Using AI for SQL — Prompting Strategies

```sql
--39.Using AI for
/*
-Brainstorming & Ideas
-Project Planning
-Learning knowledge and Research
-Generate Documentations (Always Review It)
-Discussing Architecture
-Exploring Best Practices and The Coming Pitfalls
-Complex Problem Solving (Breaking it to small pieces and start finding the solution)
-Coding Assistance
-Debugging
-Refactoring
-Inline Comments
-Styling & Formating Code

Prompt
You are a senior SQL Expert (Role), and i am data analyst working on an SQL project using SQL Server.(Context)
Explain the concept of SQL Windows Functions and do the following:(Task)

-Explain each Window Function and show the Syntax
-Describe why they are important and when to use them (Specification)
-List the top 3 use cases.

The tone should be conversational and direct, as if you're speaking to me one-on-one (Tone)

Frequently Used Prompt
-Solve an SQL Task
-Improve Readability
-Optimize the Performance of Query
-Optimize Execution Plan
-Debugging
-Explain the Result
-Styling & Formating
-Documentations & Comments
-Improve Database DDL
-Generate Test Dataset
-Translation of SQL Queries (SQL Server -> MySQL)

Beginner Friendly Prompt
-Create SQL Course
-Understand SQL Concept
-Comparing SQL Concepts
-Practice SQL
-Prepare for a SQL Interviews
*/
```

---

---

## 🧠 SQL Domain Knowledge — Summary & Refresher

Quick reference built from your 1–39 material. Each section shows the concept, the query pattern, and which topic number it came from.

---

### 1. SQL Execution Order

> The order SQL **processes** a query — not the order you write it.

```
FROM → JOIN → WHERE → GROUP BY → HAVING → SELECT → DISTINCT → ORDER BY → TOP/LIMIT
```

```sql
-- This fails because WHERE runs before SELECT — alias doesn't exist yet
SELECT SUM(Sales) AS TotalSales
FROM Sales.Orders
WHERE TotalSales > 1000   -- ❌ can't reference alias here

-- Fix: use HAVING (runs after GROUP BY, after SELECT aliases exist)
SELECT CustomerID, SUM(Sales) AS TotalSales   -- §1
FROM Sales.Orders
GROUP BY CustomerID
HAVING SUM(Sales) > 1000                       -- ✅ filter on aggregated result
```

---

### 2. WHERE vs HAVING

> `WHERE` filters **rows before** grouping. `HAVING` filters **groups after** aggregation.

```sql
-- WHERE runs first: excludes score=0 rows before averaging
-- HAVING runs after: only keeps country groups where avg > 430
SELECT AVG(score) AS total_score, country       -- §1
FROM customers
WHERE score != 0                                -- filter rows first
GROUP BY country
HAVING AVG(score) > 430                         -- then filter groups
ORDER BY total_score DESC;
```

---

### 3. All JOIN Types in One Place

```sql
-- INNER: only matched rows on both sides
SELECT c.first_name, o.order_id                 -- §5
FROM customers c
INNER JOIN orders o ON c.id = o.customer_id;

-- LEFT + IS NULL: customers who NEVER ordered (anti-join)
SELECT c.first_name                             -- §6
FROM customers c
LEFT JOIN orders o ON c.id = o.customer_id
WHERE o.customer_id IS NULL;

-- FULL + IS NULL: rows unmatched on EITHER side
SELECT c.first_name, o.order_id                -- §6
FROM orders o
FULL JOIN customers c ON c.id = o.customer_id
WHERE c.id IS NULL OR o.customer_id IS NULL;

-- CROSS: every combination (no ON clause needed)
SELECT * FROM customers CROSS JOIN orders;      -- §6

-- Multi-table: chain LEFT JOINs for 4 tables
SELECT o.OrderID, c.FirstName, p.Product, e.FirstName  -- §7
FROM Sales.Orders o
LEFT JOIN Sales.Customers c  ON o.CustomerID   = c.CustomerID
LEFT JOIN Sales.Products  p  ON o.ProductID    = p.ProductID
LEFT JOIN Sales.Employees e  ON o.SalesPersonID = e.EmployeeID;
```

---

### 4. Aggregate Functions — GROUP BY Collapses Rows

```sql
-- One summary row per customer                 -- §17
SELECT
    CustomerID,
    COUNT(1)   AS TotalOrders,
    SUM(Sales) AS TotalSales,
    AVG(Sales) AS AvgSale,
    MAX(Sales) AS Highest,
    MIN(Sales) AS Lowest
FROM Sales.Orders
GROUP BY CustomerID;

-- Conditional aggregate: count only high-value orders   -- §16
SELECT
    CustomerID,
    COUNT(1)                                        AS TotalOrders,
    SUM(CASE WHEN Sales > 30 THEN 1 ELSE 0 END)    AS HighSaleOrders
FROM Sales.Orders
GROUP BY CustomerID;
```

---

### 5. Window Functions — Keep All Rows

```sql
-- GROUP BY collapses → Window keeps every row   -- §18, §19
SELECT
    OrderID,
    CustomerID,
    Sales,
    SUM(Sales)   OVER()                          AS GrandTotal,
    SUM(Sales)   OVER(PARTITION BY CustomerID)   AS CustomerTotal,
    AVG(Sales)   OVER(PARTITION BY CustomerID)   AS CustomerAvg,
    MAX(Sales)   OVER()                          AS HighestSale,
    Sales - AVG(Sales) OVER(PARTITION BY CustomerID) AS DiffFromAvg
FROM Sales.Orders;

-- Running total per customer ordered by date    -- §18
SELECT
    OrderID, CustomerID, OrderDate, Sales,
    SUM(Sales) OVER(
        PARTITION BY CustomerID
        ORDER BY OrderDate
        ROWS BETWEEN UNBOUNDED PRECEDING AND CURRENT ROW
    ) AS RunningTotal
FROM Sales.Orders;

-- Moving avg (cumulative) vs rolling avg (fixed window)  -- §19
SELECT
    OrderID, ProductID, OrderDate, Sales,
    AVG(Sales) OVER(PARTITION BY ProductID ORDER BY OrderDate)       AS MovingAvg,
    AVG(Sales) OVER(PARTITION BY ProductID ORDER BY OrderDate
        ROWS BETWEEN CURRENT ROW AND 1 FOLLOWING)                    AS RollingAvg
FROM Sales.Orders;
```

---

### 6. Ranking Functions — ROW_NUMBER vs RANK vs DENSE_RANK

```sql
-- Side-by-side comparison                       -- §20
SELECT
    OrderID, Sales,
    ROW_NUMBER()  OVER(ORDER BY Sales DESC) AS RowNum,    -- 1,2,3,4 always unique
    RANK()        OVER(ORDER BY Sales DESC) AS Rnk,       -- 1,1,3 (skips after tie)
    DENSE_RANK()  OVER(ORDER BY Sales DESC) AS DenseRnk   -- 1,1,2 (no skip)
FROM Sales.Orders;

-- Top 1 sale per product (most common real use)  -- §20
SELECT * FROM (
    SELECT OrderID, ProductID, Sales,
        ROW_NUMBER() OVER(PARTITION BY ProductID ORDER BY Sales DESC) AS rn
    FROM Sales.Orders
) t WHERE rn = 1;

-- De-duplicate archive: keep latest row per OrderID  -- §20
SELECT * FROM (
    SELECT *,
        ROW_NUMBER() OVER(PARTITION BY OrderID ORDER BY CreationTime DESC) AS rn
    FROM Sales.OrdersArchive
) t WHERE rn = 1;
```

---

### 7. LAG, LEAD, FIRST_VALUE

```sql
-- Month-over-month sales change                 -- §21
SELECT
    OrderMonth,
    CurrentMonthSales,
    PreviousMonthSales,
    CurrentMonthSales - PreviousMonthSales AS MoM_Change,
    ROUND(CAST(CurrentMonthSales - PreviousMonthSales AS FLOAT)
          / PreviousMonthSales * 100, 1)         AS MoM_Percent
FROM (
    SELECT
        MONTH(OrderDate)                                        AS OrderMonth,
        SUM(Sales)                                             AS CurrentMonthSales,
        LAG(SUM(Sales)) OVER(ORDER BY MONTH(OrderDate))        AS PreviousMonthSales
    FROM Sales.Orders
    GROUP BY MONTH(OrderDate)
) t;

-- Days between consecutive orders per customer  -- §21
SELECT OrderID, CustomerID, OrderDate,
    LEAD(OrderDate) OVER(PARTITION BY CustomerID ORDER BY OrderDate) AS NextOrder,
    DATEDIFF(day, OrderDate,
        LEAD(OrderDate) OVER(PARTITION BY CustomerID ORDER BY OrderDate)
    ) AS DaysUntilNext
FROM Sales.Orders;

-- Lowest and highest per product using FIRST_VALUE  -- §21
SELECT OrderID, ProductID, Sales,
    FIRST_VALUE(Sales) OVER(PARTITION BY ProductID ORDER BY Sales)       AS Lowest,
    FIRST_VALUE(Sales) OVER(PARTITION BY ProductID ORDER BY Sales DESC)  AS Highest
FROM Sales.Orders;
```

---

### 8. NULL Handling

```sql
-- NULL in calculations — always handle it       -- §14
SELECT
    CustomerID,
    Score,
    COALESCE(Score, 0)              AS ScoreNoNull,   -- replace NULL with 0
    COALESCE(Score, 0) + 10         AS ScoreWithBonus,
    NULLIF(Quantity, 0)             AS SafeQty,       -- prevent divide-by-zero
    Sales / NULLIF(Quantity, 0)     AS PricePerUnit
FROM Sales.Customers;

-- Standard cleaning: handle NULL + empty + spaces  -- §15
SELECT
    COALESCE(NULLIF(TRIM(Category), ''), 'Unknown') AS CleanCategory
FROM Orders;

-- Sort NULLs to the bottom                      -- §14
SELECT CustomerID, Score
FROM Sales.Customers
ORDER BY CASE WHEN Score IS NULL THEN 1 ELSE 0 END, Score ASC;

-- AVG ignores NULLs — use COALESCE to include them  -- §14
SELECT
    AVG(Score)                   AS AvgExcludesNulls,
    AVG(COALESCE(Score, 0))      AS AvgIncludesNulls
FROM Sales.Customers;
```

---

### 9. CASE WHEN — 4 Patterns

```sql
-- Pattern 1: Bucket values into categories       -- §16
SELECT OrderID, Sales,
    CASE
        WHEN Sales > 50 THEN 'High'
        WHEN Sales > 20 THEN 'Medium'
        ELSE 'Low'
    END AS SalesCategory
FROM Sales.Orders;

-- Pattern 2: Shorthand — map one column to labels  -- §16
SELECT CustomerID, Country,
    CASE Country
        WHEN 'Germany' THEN 'DE'
        WHEN 'USA'     THEN 'US'
        ELSE 'N/A'
    END AS CountryCode
FROM Sales.Customers;

-- Pattern 3: Conditional COUNT inside GROUP BY   -- §16
SELECT CustomerID,
    COUNT(1)                                    AS TotalOrders,
    SUM(CASE WHEN Sales > 30 THEN 1 ELSE 0 END) AS HighSaleOrders
FROM Sales.Orders
GROUP BY CustomerID;

-- Pattern 4: CASE inside window function         -- §19, §16
SELECT CustomerID, Score,
    CASE
        WHEN Score > AVG(Score) OVER() THEN 'Above Avg'
        WHEN Score < AVG(Score) OVER() THEN 'Below Avg'
        ELSE 'Average'
    END AS ScoreStatus
FROM Sales.Customers;
```

---

### 10. Date Patterns

```sql
-- Extract parts, truncate, and get today         -- §11
SELECT
    OrderID,
    OrderDate,
    YEAR(OrderDate)                              AS Yr,
    MONTH(OrderDate)                             AS Mo,
    DATENAME(month, OrderDate)                   AS MonthName,
    DATEPART(quarter, OrderDate)                 AS Quarter,
    CAST(DATETRUNC(month, OrderDate) AS DATE)    AS StartOfMonth,
    EOMONTH(OrderDate)                           AS EndOfMonth,
    GETDATE()                                    AS Today
FROM Sales.Orders;

-- Date arithmetic                                -- §13
SELECT
    OrderID, OrderDate, ShipDate,
    DATEDIFF(day, OrderDate, ShipDate)           AS DaysToShip,
    DATEADD(day, 30, OrderDate)                  AS Due30Days,
    DATEDIFF(year, BirthDate, GETDATE())         AS Age
FROM Sales.Orders;

-- Time gap between consecutive orders            -- §13, §21
SELECT
    OrderID, OrderDate,
    LAG(OrderDate) OVER(ORDER BY OrderDate)      AS PrevOrder,
    DATEDIFF(day,
        LAG(OrderDate) OVER(ORDER BY OrderDate),
        OrderDate)                               AS DaysBetween
FROM Sales.Orders;

-- Safe date conversion with ISDATE              -- §13
SELECT OrderDate,
    CASE
        WHEN ISDATE(OrderDate) = 1 THEN CAST(OrderDate AS DATE)
        ELSE '9999-01-01'
    END AS CleanDate
FROM (SELECT '2025-08-20' AS OrderDate UNION SELECT '2025-08') t;
```

---

### 11. String Patterns

```sql
-- Clean, combine, and extract strings           -- §9
SELECT
    first_name,
    TRIM(first_name)                              AS Trimmed,
    UPPER(first_name)                             AS Upper,
    LOWER(first_name)                             AS Lower,
    LEN(first_name)                               AS Length,
    LEFT(TRIM(first_name), 2)                     AS First2,
    RIGHT(TRIM(first_name), 2)                    AS Last2,
    CONCAT(first_name, '-', country)              AS Combined,
    REPLACE(first_name, 'a', '@')                 AS Replaced
FROM customers;

-- Find rows with hidden spaces                  -- §9
SELECT first_name,
    LEN(first_name) - LEN(TRIM(first_name))      AS ExtraSpaces
FROM customers
WHERE LEN(first_name) != LEN(TRIM(first_name));
```

---

### 12. Subquery vs CTE — Same Result, Different Readability

```sql
-- SUBQUERY version: harder to read as it grows  -- §23
SELECT * FROM (
    SELECT CustomerID, SUM(Sales) AS TotalSales
    FROM Sales.Orders GROUP BY CustomerID
) t
WHERE TotalSales > (SELECT AVG(Sales) FROM Sales.Orders);

-- CTE version: same result, named and readable  -- §24
WITH CustomerSales AS (
    SELECT CustomerID, SUM(Sales) AS TotalSales
    FROM Sales.Orders GROUP BY CustomerID
),
AvgSales AS (
    SELECT AVG(Sales) AS AvgSale FROM Sales.Orders
)
SELECT cs.*
FROM CustomerSales cs
CROSS JOIN AvgSales a
WHERE cs.TotalSales > a.AvgSale;

-- EXISTS vs IN: use EXISTS for large tables      -- §23, §36
-- IN version
SELECT * FROM Sales.Orders
WHERE CustomerID IN (
    SELECT CustomerID FROM Sales.Customers WHERE Country = 'Germany'
);
-- EXISTS version (faster on large tables — stops at first match)
SELECT * FROM Sales.Orders o
WHERE EXISTS (
    SELECT 1 FROM Sales.Customers c
    WHERE c.CustomerID = o.CustomerID AND c.Country = 'Germany'
);
```

---

### 13. Set Operators — Combining Result Sets

```sql
-- UNION: combine and remove duplicates          -- §8
SELECT FirstName, LastName FROM Sales.Customers
UNION
SELECT FirstName, LastName FROM Sales.Employees;

-- UNION ALL: combine and keep duplicates (faster)  -- §8
SELECT CustomerID FROM Sales.Orders
UNION ALL
SELECT CustomerID FROM Sales.OrdersArchive;

-- UNION ALL + DISTINCT instead of UNION         -- §36
SELECT DISTINCT CustomerID FROM (
    SELECT CustomerID FROM Sales.Orders
    UNION ALL
    SELECT CustomerID FROM Sales.OrdersArchive
) t;

-- EXCEPT: in Employees but NOT in Customers     -- §8
SELECT FirstName, LastName FROM Sales.Employees
EXCEPT
SELECT FirstName, LastName FROM Sales.Customers;

-- Tag source then merge current + archive        -- §8
SELECT 'Current' AS Source, OrderID, Sales FROM Sales.Orders
UNION ALL
SELECT 'Archive' AS Source, OrderID, Sales FROM Sales.OrdersArchive
ORDER BY OrderID;
```

---

### 14. View vs CTE vs Temp Table vs CTAS

```sql
-- VIEW: saved query, no data stored, reusable across sessions  -- §25
CREATE VIEW Sales.V_Monthly_Summary AS
SELECT
    DATETRUNC(month, OrderDate) AS OrderMonth,
    SUM(Sales)   AS TotalSales,
    COUNT(OrderID) AS TotalOrders
FROM Sales.Orders
GROUP BY DATETRUNC(month, OrderDate);

-- Query the view + add window function on top
SELECT OrderMonth, TotalSales,
    SUM(TotalSales) OVER(ORDER BY OrderMonth) AS RunningTotal
FROM Sales.V_Monthly_Summary;

-- CTE: temporary, lives only inside the query    -- §24
WITH MonthlySales AS (
    SELECT MONTH(OrderDate) AS Mo, SUM(Sales) AS Total
    FROM Sales.Orders GROUP BY MONTH(OrderDate)
)
SELECT * FROM MonthlySales WHERE Total > 500;

-- TEMP TABLE: session-based physical table       -- §27
SELECT * INTO #Orders FROM Sales.Orders;
DELETE FROM #Orders WHERE OrderStatus = 'Delivered';
SELECT * INTO Sales.CleanOrders FROM #Orders;

-- CTAS: permanent snapshot table                 -- §26
SELECT DATENAME(month, OrderDate) AS Month, COUNT(OrderID) AS Total
INTO Sales.MonthlySnapshot
FROM Sales.Orders
GROUP BY DATENAME(month, OrderDate);
```

---

### 15. Recursive CTE — Hierarchy & Sequences

```sql
-- Generate numbers 1 to 20                       -- §24
WITH Series AS (
    SELECT 1 AS MyNumber
    UNION ALL
    SELECT MyNumber + 1 FROM Series WHERE MyNumber < 20
)
SELECT * FROM Series OPTION (MAXRECURSION 200);

-- Employee org chart with levels                 -- §24
WITH Hierarchy AS (
    SELECT EmployeeID, FirstName, ManagerID, 1 AS Level
    FROM Sales.Employees WHERE ManagerID IS NULL  -- top of tree
    UNION ALL
    SELECT e.EmployeeID, e.FirstName, e.ManagerID, h.Level + 1
    FROM Sales.Employees e
    INNER JOIN Hierarchy h ON e.ManagerID = h.EmployeeID
)
SELECT * FROM Hierarchy ORDER BY Level;
```

---

### 16. NTILE & CUME_DIST — Segmentation

```sql
-- Divide customers into 3 tiers by total sales  -- §20
SELECT *, 
    CASE SalesBucket
        WHEN 1 THEN 'High'
        WHEN 2 THEN 'Medium'
        WHEN 3 THEN 'Low'
    END AS Segment
FROM (
    SELECT CustomerID, SUM(Sales) AS TotalSales,
        NTILE(3) OVER(ORDER BY SUM(Sales) DESC) AS SalesBucket
    FROM Sales.Orders GROUP BY CustomerID
) t;

-- Top 40% most expensive products               -- §20
SELECT Product, Price,
    CONCAT(ROUND(CUME_DIST() OVER(ORDER BY Price DESC) * 100, 0), '%') AS Percentile
FROM Sales.Products
WHERE CUME_DIST() OVER(ORDER BY Price DESC) <= 0.4;
```

---

### 17. Percentage of Total & Deviation Analysis

```sql
-- Each order as % of grand total                -- §19
SELECT
    OrderID, ProductID, Sales,
    SUM(Sales) OVER()                                          AS GrandTotal,
    ROUND(CAST(Sales AS FLOAT) / SUM(Sales) OVER() * 100, 2)  AS PctOfTotal
FROM Sales.Orders;

-- Each product's % within its category         -- §19
SELECT
    OrderID, ProductID, Sales,
    SUM(Sales) OVER(PARTITION BY ProductID)                    AS ProductTotal,
    ROUND(CAST(Sales AS FLOAT)
        / SUM(Sales) OVER(PARTITION BY ProductID) * 100, 2)   AS PctOfProduct
FROM Sales.Orders;

-- Deviation from min and max                    -- §19
SELECT
    OrderID, Sales,
    MIN(Sales) OVER() AS Lowest,
    MAX(Sales) OVER() AS Highest,
    Sales - MIN(Sales) OVER() AS AboveLowest,
    MAX(Sales) OVER() - Sales AS BelowHighest
FROM Sales.Orders;
```

---

### 18. Duplicate Detection Patterns

```sql
-- Find duplicate OrderIDs in archive            -- §19
SELECT * FROM (
    SELECT OrderID,
        COUNT(1) OVER(PARTITION BY OrderID) AS DupCount
    FROM Sales.OrdersArchive
) t WHERE DupCount > 1;

-- Keep only the latest row per duplicate        -- §20
SELECT * FROM (
    SELECT *,
        ROW_NUMBER() OVER(PARTITION BY OrderID ORDER BY CreationTime DESC) AS rn
    FROM Sales.OrdersArchive
) t WHERE rn = 1;

-- Assign unique ID to a table without PK        -- §20
SELECT
    ROW_NUMBER() OVER(ORDER BY OrderID, CreationTime) AS UniqueID, *
FROM Sales.OrdersArchive;
```

---

### 19. Index Quick Reference

```sql
-- Clustered: physically sorts table (1 per table, use on PK)  -- §28
CREATE CLUSTERED INDEX idx_Orders_PK ON Sales.Orders(OrderID);

-- Nonclustered: separate lookup structure (use on FK, filter, join columns)  -- §28
CREATE NONCLUSTERED INDEX idx_Orders_CustomerID ON Sales.Orders(CustomerID);

-- Composite: covers multi-column WHERE (leftmost prefix rule)  -- §28
CREATE INDEX idx_Customers_CountryScore ON Sales.Customers(Country, Score);

-- Columnstore: best for aggregation on large fact tables  -- §29
CREATE CLUSTERED COLUMNSTORE INDEX idx_Orders_CS ON Sales.Orders;

-- Filtered: smaller, faster index for a specific subset  -- §30
CREATE NONCLUSTERED INDEX idx_Customers_USA
ON Sales.Customers(Country) WHERE Country = 'USA';

-- Unique: enforce no duplicate values           -- §30
CREATE UNIQUE NONCLUSTERED INDEX idx_Products_Name ON Sales.Products(Product);

-- Monitor fragmentation and rebuild if needed  -- §32
SELECT tbl.name, idx.name, s.avg_fragmentation_in_percent
FROM sys.dm_db_index_physical_stats(DB_ID(), NULL, NULL, NULL, 'LIMITED') s
JOIN sys.tables  tbl ON s.object_id = tbl.object_id
JOIN sys.indexes idx ON idx.object_id = s.object_id AND idx.index_id = s.index_id
ORDER BY s.avg_fragmentation_in_percent DESC;
-- < 10% → do nothing | 10–30% → REORGANIZE | > 30% → REBUILD
```

---

### 20. Optimization — Filter & Aggregate Early

```sql
-- Small tables: filter after JOIN              -- §36
SELECT c.FirstName, o.OrderID
FROM Sales.Customers c
INNER JOIN Sales.Orders o ON c.CustomerID = o.CustomerID
WHERE o.OrderStatus = 'Delivered';

-- Large tables: filter BEFORE JOIN (subquery)  -- §36
SELECT c.FirstName, o.OrderID
FROM Sales.Customers c
INNER JOIN (
    SELECT OrderID, CustomerID FROM Sales.Orders
    WHERE OrderStatus = 'Delivered'             -- filter inside before joining
) o ON c.CustomerID = o.CustomerID;

-- Large tables: aggregate BEFORE JOIN          -- §36
SELECT c.CustomerID, c.FirstName, o.OrderCount
FROM Sales.Customers c
INNER JOIN (
    SELECT CustomerID, COUNT(OrderID) AS OrderCount
    FROM Sales.Orders GROUP BY CustomerID       -- summarize first, then join
) o ON c.CustomerID = o.CustomerID;
```

---

### 21. Stored Procedure — Reusable Query with Logic

```sql
-- Template: parameter + IF ELSE + TRY CATCH   -- §37
ALTER PROCEDURE GetCustomerSummary
    @Country NVARCHAR(50) = 'USA'
AS
BEGIN
    BEGIN TRY
        DECLARE @Total INT, @Avg FLOAT;

        IF EXISTS (SELECT 1 FROM Sales.Customers WHERE Score IS NULL AND Country = @Country)
        BEGIN
            UPDATE Sales.Customers SET Score = 0
            WHERE Score IS NULL AND Country = @Country;
        END;

        SELECT @Total = COUNT(1), @Avg = AVG(Score)
        FROM Sales.Customers WHERE Country = @Country;

        PRINT 'Total: ' + CAST(@Total AS NVARCHAR);

        SELECT COUNT(OrderID) AS Orders, SUM(Sales) AS Sales
        FROM Sales.Orders o
        JOIN Sales.Customers c ON c.CustomerID = o.CustomerID
        WHERE c.Country = @Country;

    END TRY
    BEGIN CATCH
        PRINT ERROR_MESSAGE();
    END CATCH
END
GO

EXEC GetCustomerSummary @Country = 'Germany';
```

---

### 22. Key Comparisons — Quick Recall

| Concept | Option A | Option B | Winner |
|---|---|---|---|
| Filter rows | `WHERE` | `HAVING` | WHERE (before GROUP BY), HAVING (after) |
| Remove dupes | `UNION` | `UNION ALL + DISTINCT` | UNION ALL + DISTINCT (faster) |
| Check existence | `IN` | `EXISTS` | EXISTS on large tables |
| Count all rows | `COUNT(1)` | `COUNT(column)` | COUNT(1) for all, COUNT(col) skips NULLs |
| Summarize | `GROUP BY` | Window `OVER()` | GROUP BY collapses, OVER() keeps rows |
| Temporary logic | Subquery | CTE | CTE when reused or complex |
| Reusable query | View | Temp Table | View = no data stored, Temp = data stored |
| Analytical index | Rowstore | Columnstore | Columnstore for aggregations on large tables |
| Remove all rows | `DELETE` | `TRUNCATE` | TRUNCATE (faster, not row-logged) |
| Remove everything | `TRUNCATE` | `DROP` | DROP removes the table structure too |

---

### 23. Real Project Checklist

```
Before Writing Any Query
☐ Are you in the right database? (USE DatabaseName)
☐ Do you know the table structure? (sp_helpindex, INFORMATION_SCHEMA)

Writing the Query
☐ Start with FROM + JOIN, then WHERE, then SELECT
☐ Filter early on large tables (subquery before JOIN)
☐ Aggregate before joining on large tables
☐ Handle NULLs in joins, aggregates, and string concat
☐ Use COALESCE(NULLIF(TRIM(col), ''), 'default') for cleaning
☐ Limit SELECT columns — no SELECT * in production

Window Functions
☐ Need all rows? Use OVER() not GROUP BY
☐ Running total? ROWS BETWEEN UNBOUNDED PRECEDING AND CURRENT ROW
☐ Top N per group? ROW_NUMBER() OVER(PARTITION BY ... ORDER BY ...)
☐ Previous row? LAG() | Next row? LEAD()

Performance
☐ Check Execution Plan (Ctrl+M in SSMS)
☐ Index columns in WHERE, JOIN ON, ORDER BY
☐ One clustered index per table
☐ Use EXISTS not JOIN when only checking existence
☐ Avoid functions on columns in WHERE clause
☐ Avoid leading wildcard: '%value' kills index

Maintenance (Weekly)
☐ UPDATE STATISTICS
☐ Check fragmentation → REORGANIZE (10-30%) or REBUILD (>30%)
☐ Review unused indexes (sys.dm_db_index_usage_stats)
☐ Check missing indexes (sys.dm_db_missing_index_details)
```
