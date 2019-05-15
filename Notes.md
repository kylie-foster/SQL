## Grouping: Using ```GROUP BY``` and ```HAVING```

Example of how to use ```GROUP BY```:
```SQL
SELECT
Region -- column to select
,COUNT(CustomerID) AS total_customers -- using COUNT function and naming new column total_customers
FROM Customers -- table to import data from
GROUP BY Region; -- ensures that count is applied after grouping by region, rather than counting the whole table
```
```GROUP BY``` clauses can also contain multiple columns.

NULL values will be aggregated into their own group.

Example of how to use ```HAVING```:
```SQL
SELECT
CustomerID -- column to select
,COUNT (*) AS orders -- would normally be counting over entire column, but this is affected by the following clauses
FROM Orders -- table to import data from
GROUP BY CustomerID -- ensures that count is applied after grouping by CustomerID (looks at each individual customer)
HAVING COUNT (*) >= 2 -- ensures count is only applied to groups (CustomerID) with counts greater than or equal to 2
```

```WHERE``` filters before the data is grouped, ```HAVING``` filters after the data is grouped, so rows eliminated 
by a ```WHERE``` clause will not be included in a group.

## Summary

| Clause  | Description | 
| ------------- | ------------- |
| SELECT  | Columns or expressions to be returned. Always required  | 
| FROM  | Table from which to retrieve data | 
| WHERE | Row-level filtering | 
| GROUP BY | Columns to use for grouping | 
| HAVING | Filter applied to groups | 
| ORDER BY | Output sort order |

## Using Subqueries

Subqueries are queries embedded inside other queries.

Useful for merging data from two or more tables.

Example  :
```SQL
-- Need to know the region each customer is from (info in Customers table) 
-- who has had an order with freight over 100 (info in Orders table)
SELECT -- columns to select
CustomerID,
CompanyName,
Region
FROM Customers -- table to import data from
WHERE customerID in (SELECT customerID-- start of subquery
      FROM Orders
      WHERE Freight > 100) -- subquery gives customerID values for which Freight is greater than 
                           -- 100 using data in table Orders rather than Customers
```
The innermost ```SELECT``` statement is always performed first.

Subquery selects can only retrieve a single column.

Example of using subqueries for calculations:
```SQL
-- Need to know the total number of orders (info in Orders table) 
-- placed be every customer, including their name and state (info in Customers table)
SELECT -- columns to select
     customer_name,
     customer_state -- possibly missing a comma here
     (SELECT COUNT(*) AS orders -- subquery counting all orders for customer IDs in 
                                -- both Orders and Customer tables, to use as a column for outer
                                -- SELECT statement
     FROM Orders
     WHERE Orders.customer_id = Customer.customer_id) AS orders
FROM customers -- table to import data from
ORDER BY customer_name
```
## Joining Tables
### Cartesian (Cross) Join

Each row from the first table joins with all the rows of the second table. e.g if your first table as x rows and your second table as y rows, the merged table will have x*y rows.

<img src="https://www.w3resource.com/w3r_images/cross-join-round.png" width="500"/>
Source: https://www.w3resource.com/w3r_images/cross-join-round.png

Example:
```SQL
SELECT -- columns to select be selected from multiple tables
     product_name,
     unit_price,
     company_name
     FROM suppliers CROSS JOIN products; -- joins all rows of selected columns in Supplier table
                                         -- with all rows of selected columns in products table
```
Not used very frequently (very computationally expensive and there is no key for matching data).

### Inner Joins
Selects records that have matching values in both tables. Need to specify how we want the tables linked (the key).

<img src="https://www.w3resource.com/w3r_images/sql-inner-jon.gif" width="300"/>
Source: https://www.w3resource.com/w3r_images/sql-inner-jon.gif

Example:
```SQL
SELECT Suppliers.CompanyName -- CompanyName is present in both the tables were are using so use 
                             -- "Suppliers." to specify which table to take this column from
     product_name,
     unit_price
     FROM Suppliers INNER JOIN Products
     ON Suppliers.supplierid = Products.supplierid -- the key to use for joining. "Suppliers." and "Products." 
                                                   -- specify which tables the "supplierid" should be taken from
```
You can join multiple tables, there is no limit.

Example:
```SQL
SELECT o.OrderID -- good idea to prequalify all column names (i.e. specify which table they come from). 
     c.CompanyName,
     e.LastName
     FROM ((Orders o INNER JOIN Customers c -- specifies which tables the prequalifiers "o" and "c"
                                            -- refer to
            ON o.CustomrerID = c.CustomerID)
            INNER JOIN Employees e ON o.EmployeeID = e.EmployeeID);
```

Very commonly used.

### Aliases and Self Joins
Aliases are when you give a table or column a temporary name (it only exists for the duration of the query).

Self Join is when you join a table to itself.

Example using a self join to match customers that are from the same city:
```SQL
SELECT A.CustomerName AS CustomerName1, -- using AS to create an alias
     B.CustomerName AS CustomerName2
     FROM Customers A, Customers B -- giving the same table (Customers) two different aliases (A and B)
     WHERE A.CustomerID = B.CustomerID
     AND A.City = B.City;
```

### Left Joins
Returns all records from the left table, and the matched records from the right table. If there is no record in the right table that matches a given record in the left table the result for that row on the right side is a NULL.

<img src="https://www.w3resource.com/w3r_images/sql-left-jon.png" width="300"/>
Source: https://www.w3resource.com/w3r_images/sql-left-jon.png

Example:
```SQL
SELECT C.CustomerName,
       O.OrderID
FROM Customers C LEFT JOIN Orders O -- syntax is the same for FULL OUTER JOIN
ON C.CustomerID = O.CustomerID

```

### Right Joins
Returns all records from the right table, and the matched records from the left table.

### Full Outer Join
Returns all records in both tables.

### Unions
Combines tables by stacking them, similar to rbind() in R. They must have the same number of columns, of the same data types in the same order.

Basic synatx:
```SQL
SELECT column_name(s) -- first SELECT statement
FROM table1
-- (can also include WHERE statement here)
UNION
SELECT column_name(s) -- second SELECT statement
FROM table2;
-- (can also include WHERE statement here)
```
Not used very often, but still useful.

## Strings

### Concatenate
Join multiple strings. Use || (or in SQL server +).

Example:
```SQL
SELECT CompanyName,
ContactName,
CompanyName || ' ('|| ContactName||')' -- results in CompanyName (ContactName)
FROM customers
```
### Trim
Can remove parts at the start or end of a string.

Example:
```SQL
SELECT TRIM("   You the best.   ") -- removes the leading and trailing spaces.
AS TrimmedString
```
### Substring
Returns the specified number of characters from a particular position of a given string.

Syntax:
```SQL
SUBSTR(string name, string position, number of characters to be returned);
```
Example:
```SQL
SELECT first_name, 
SUBSTR (first_name,3,4) -- starts at the 3rd character in the string first_name 
                        -- and takes four characters in total
FROM employees;
```
### Changing the case of strings
Use UPPER or UCASE (both change all strings to uppercase), LOWER (changes all strings to lowercase). 
They all have the same syntax:

```SQL
SELECT UPPER(column_name) FROM table_name;
```
### Date and time strings
Date formats in SQLlite:

DATE: YYYY-MM-DD

DATETIME: YYYY-MM-DD HH:MI:SS

TIMESTAMP: YYYY-MM-DD HH:MI:SS

#### STRFTIME
String format time.

Example:
```SQL
SELECT Birthdate,
STRFTIME('%Y', Birthdate) AS Year, -- creates column containing the year part of the date in Birthdate
STRFTIME('%m', Birthdate) AS Month, -- creates column containing the date in Birthdate
STRFTIME('%d', Birthdate) AS Day -- creates column containing the date in Birthdate
FROM employees
```

#### Compute current date

```SQL
SELECT DATE('now')

SELECT STRFTIME('%Y %m %d', 'now') -- computes the current year, month and date

SELECT STFRTIME('%H %M %S %s', 'now') -- computes the current hour, minute, second and millisecond
```

Example of how to use 'now' in a calculation:

```SQL
SELECT Birthdate,
DATE(('now') - Birthdate) AS Age -- calculates a persons age based on their birthdate and the current date
FROM employees
```

## Case Statements
Mimics if-then-else statement.

Can be used in SELECT, INSERT, UPDATE and DELETE statements.

Syntax:

```SQL
CASE input_expression
    WHEN when_expression THEN result_expression
    WHEN when_expression2 THEN result_expression2
    ...
    [ELSE else_result_expression]
END
```

Example:
```SQL
-- Creating a new column that (Calgary) that has the value Calgary or Other 
-- based on the value of the City column
SELECT employeeid,
firstname,
lastname,
city
CASE city WHEN 'Calgary' THEN 'Calgary'
ELSE 'Other'
    END calgary -- names the column
FROM Employees
```
## Views
A VIEW is a stored query (temporary table). The view will be removed after database connection has ended (does not make a permanent change to the database)

## Other Notes

Can use www.poorsql.com to automatically format and indent SQL code.
