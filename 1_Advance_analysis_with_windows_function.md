### Advance Analysis with windows function

* In SQL, **window functions** are a powerful feature used to perform calculations across a set of table rows that are related to the current row.

* Unlike aggregate functions, window functions do not collapse the result set but instead allow you to perform calculations while keeping the individual rows.

#### Key Types of Window Functions:

1. **Ranking Functions**: Assign ranks or other numeric values to rows.
2. **Aggregate Window Functions**: Apply aggregate functions like `SUM`, `AVG`, etc., over a window of rows.
3. **Analytic Functions**: Perform analysis across a set of rows, like calculating running totals, moving averages, etc.

```sql
SELECT
  column_name,
  window_function() OVER ([PARTITION BY column_name] [ORDER BY column_name]) AS alias_name
FROM table_name;
```

#### Example : Windows Function

```sql
select BusinessEntityID,TerritoryID,
SalesQuota,Bonus,CommissionPct,
sum(SalesYTD) over(partition by businessentityid) as Total_YTD,
max(SalesYTD) over() as Max_YTD,
SalesYTD*100/max(SalesYTD) over() as '% of best performer'
from Sales.SalesPerson
order by '% of best performer' DESC
```

![Advance Analysis with windows function](https://github.com/HBISHT233/Advance_SQL/blob/main/Images/2024-10-13-18-37-53-image.png)

* Here instead of collapsing rows windows function added a new column for us to do calculation e.g. Aggregare functions like `sum()/max()` will simply sum all and collapse every row....but in `windows function sum() or max() are  in a separate columns`

#### OVER()

In SQL, the `OVER()` clause is used in conjunction with **window functions** to define the window or set of rows over which the function operates. It allows you to apply aggregate, ranking, and analytic functions across a specific window of rows without collapsing the result set, as would happen with a regular `GROUP BY` clause.

#### Partition by()

* In SQL, the **`PARTITION BY`** clause is used with **window functions** to divide the result set into partitions (or subsets of rows). 

* The window function is then applied independently to each partition. 

* It's similar to a **`GROUP BY`** clause but doesn't collapse the rows; instead, it allows window functions to work on individual rows within each partition.

#### ROW_NUMBER()

In SQL, the **`ROW_NUMBER()`** function is a window function that assigns a unique sequential integer to rows within a partition of a result set, starting from 1 for the first row in each partition. It’s commonly used for tasks like pagination, ranking, or identifying duplicates.

```sql
ROW_NUMBER() OVER ([PARTITION BY partition_expression] ORDER BY order_expression)
```

##### Components of row_number():

1. **`PARTITION BY`** (Optional): Divides the result set into partitions. `ROW_NUMBER()` is applied separately to each partition.
2. **`ORDER BY`** (Required): Specifies the order in which the rows are assigned their row number within the partition.
   * Means we  must always use `ORDER BY`

```sql
select BusinessEntityID,TerritoryID,
SalesQuota,Bonus,CommissionPct,salesytd,
sum(SalesYTD) over(partition by businessentityid) as Total_YTD,
max(SalesYTD) over() as Max_YTD,
SalesYTD*100/max(SalesYTD) over() as '% of best performer',
ROW_NUMBER() over (ORDER by salesytd DESC ) as sales_rank
from Sales.SalesPerson
order by '% of best performer' DESC
```

![Advance Analysis with windows function](https://github.com/HBISHT233/Advance_SQL/blob/main/Images/2024-10-13-21-53-20-image.png)

#### RANK() vs DENSE_RANK()

Here’s a comparison between **`RANK()`** and **`DENSE_RANK()`** in table form:

| **Aspect**              | **`RANK()`**                                             | **`DENSE_RANK()`**                                       |
| ----------------------- | -------------------------------------------------------- | -------------------------------------------------------- |
| **Ranking Order**       | Assigns a rank to each row in the result set.            | Assigns a rank to each row in the result set.            |
| **Ties Handling**       | Tied rows get the same rank.                             | Tied rows get the same rank.                             |
| **Gaps in Ranking**     | Leaves gaps in ranks when there are ties.                | No gaps; consecutive ranking even with ties.             |
| **Next Rank After Tie** | The next rank skips by the number of ties.               | The next rank is sequential without skipping numbers.    |
| **Example (Ties)**      | If two rows are ranked 2, the next row will be ranked 4. | If two rows are ranked 2, the next row will be ranked 3. |
| **Use Case**            | Use when gaps in ranking are acceptable or desired.      | Use when consecutive ranking without gaps is preferred.  |

### Example Data:

| EmployeeID | SalesAmount |
| ---------- | ----------- |
| 1          | 5000        |
| 2          | 4000        |
| 3          | 4000        |
| 4          | 3000        |

### Result with `RANK()`:

```sql
SELECT
  EmployeeID,
  SalesAmount,
  RANK() OVER (ORDER BY SalesAmount DESC) AS Rank
FROM Sales;
```

| EmployeeID | SalesAmount | Rank |
| ---------- | ----------- | ---- |
| 1          | 5000        | 1    |
| 2          | 4000        | 2    |
| 3          | 4000        | 2    |
| 4          | 3000        | 4    |

### Result with `DENSE_RANK()`:

```sql
SELECT
  EmployeeID,
  SalesAmount,
  DENSE_RANK() OVER (ORDER BY SalesAmount DESC) AS DenseRank
FROM Sales;
```

| EmployeeID | SalesAmount | DenseRank |
| ---------- | ----------- | --------- |
| 1          | 5000        | 1         |
| 2          | 4000        | 2         |
| 3          | 4000        | 2         |
| 4          | 3000        | 3         |

#### Example: Rank, Dense_rank

```sql
select BusinessEntityID,
SalesQuota,salesytd,
sum(SalesYTD) over(partition by businessentityid) as Total_YTD,
max(SalesYTD) over() as Max_YTD,
SalesYTD*100/max(SalesYTD) over() as '% of best performer',
ROW_NUMBER() over (ORDER by SalesQuota DESC ) as sales_rank,
RANK() OVER (ORDER BY SalesQuota DESC) as 'rank()',
DENSE_RANK() OVER (ORDER by SalesQuota DESC) as 'DenseRank()'
from Sales.SalesPerson
order by SalesQuota DESC
```

![Advance Analysis with windows function](https://github.com/HBISHT233/Advance_SQL/blob/main/Images/2024-10-13-22-10-55-image.png)

#### LEAD() and LAG()

In SQL, **`LEAD()`** and **`LAG()`** are window functions that allow you to access data from subsequent (next) or preceding (previous) rows within the result set without needing to join the table to itself. These functions are commonly used in time series or sequential data analysis.

* **`LEAD()`** gives access to the value of a subsequent `(next)` row from the current row. It allows you to "look forward."

* **`LAG()`** gives access to the value of a preceding `(previous)` row from the current row. It allows you to "look back."

```sql
LEAD(column_name, offset, default_value) OVER (PARTITION BY partition_column ORDER BY order_column)
```

```sql
SELECT 
    EmployeeID, 
    SalesAmount,
    LAG(SalesAmount, 1) OVER (ORDER BY SalesAmount DESC) AS Previous_SalesAmount,
    LEAD(SalesAmount, 1) OVER (ORDER BY SalesAmount DESC) AS Next_SalesAmount
FROM Sales;
```

### Example with Data:

| EmployeeID | SalesAmount |
| ---------- | ----------- |
| 1          | 5000        |
| 2          | 4000        |
| 3          | 3000        |
| 4          | 2000        |

#### Result:

| EmployeeID | SalesAmount | Previous_SalesAmount | Next_SalesAmount |
| ---------- | ----------- | -------------------- | ---------------- |
| 1          | 5000        | NULL                 | 4000             |
| 2          | 4000        | 5000                 | 3000             |
| 3          | 3000        | 4000                 | 2000             |
| 4          | 2000        | 3000                 | NULL             |

* Note: we can use dafault value to fill the NULL

```sql
select SalesOrderID,OrderDate,
CustomerID,TotalDue,
lead(TotalDue,1) OVER (partition by customerid order by salesorderid) as Next_Due,
lag (TotalDue,1) OVER (partition by customerid order by salesorderid) as Prev_Due
from sales.salesorderheader
order by CustomerID,SalesOrderID
```

![Advance Analysis with windows function](https://github.com/HBISHT233/Advance_SQL/blob/main/Images/2024-10-13-22-36-38-image.png)

#### FIRST_VALUE()

In SQL, **`FIRST_VALUE()`** is a window function that returns the first value in an ordered partition of a result set. It allows you to retrieve the first value within a group of rows, which is useful for comparisons or when you need to fetch the initial record in a sorted group.

```sql
FIRST_VALUE(column_name) OVER (PARTITION BY partition_column ORDER BY order_column)
```

#### Example: FIRST_VALUE()

```sql
select SalesOrderID,OrderDate,
CustomerID,TotalDue,
row_number() over(partition by customerid order by salesorderid) as rank,
first_value(TotalDue) over (partition by customerid order by salesorderid) as first from sales.salesorderheader
order by CustomerID,SalesOrderID
```

![Advance Analysis with windows function](https://github.com/HBISHT233/Advance_SQL/blob/main/Images/2024-10-13-23-03-47-image.png)

The Query display the first value of each group [CustomerID]

#### ROWS_BETWEEN()

In SQL, the `ROWS BETWEEN` clause is often used in conjunction with window functions to define a specific range of rows to consider for calculations. It is part of the `OVER()` clause and allows you to specify a frame of rows relative to the current row.

```sql
<window_function> OVER (
    [PARTITION BY partition_column]
    ORDER BY order_column
    ROWS BETWEEN <start> AND <end>
)
```

### Frame Specifications:

- **`UNBOUNDED PRECEDING`**: Refers to the start of the partition.
- **`N PRECEDING`**: Refers to the Nth row before the current row.
- **`CURRENT ROW`**: Refers to the current row.
- **`N FOLLOWING`**: Refers to the Nth row after the current row.
- **`UNBOUNDED FOLLOWING`**: Refers to the end of the partition.

```sql
SELECT
    OrderDate,
    SumTotalDue,
    AVG(SumTotalDue) OVER (ORDER BY OrderDate ROWS BETWEEN 2 PRECEDING AND CURRENT ROW) AS day_rolling
FROM (
    SELECT 
        OrderDate,
        SUM(TotalDue) AS SumTotalDue
    FROM Sales.SalesOrderHeader
    GROUP BY OrderDate
) temp
ORDER BY OrderDate;
-- 3 day rolling average including current day 
```

![Advance Analysis with windows function](https://github.com/HBISHT233/Advance_SQL/blob/main/Images/2024-10-13-23-56-21-image.png)
