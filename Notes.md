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
