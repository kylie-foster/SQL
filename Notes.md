```distinct```

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

## Other Notes

Can use www.poorsql.com to automatically format and indent SQL code.
