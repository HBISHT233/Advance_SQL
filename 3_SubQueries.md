#### Subquery v/s CTE(with as)

In SQL, both **subqueries** and **Common Table Expressions (CTEs)** (using the `WITH` clause) allow you to define temporary result sets that can be referenced within a query. However, they have some differences in terms of syntax, readability, and usability. Here’s a comparison between the two:

| **Aspect**      | **Subquery**                                                                                  | **CTE (WITH AS)**                                                                                  |
| --------------- | --------------------------------------------------------------------------------------------- | -------------------------------------------------------------------------------------------------- |
| **Syntax**      | Nested inside another SQL statement.                                                          | Defined using the `WITH` clause before the main query.                                             |
| **Readability** | Can be less readable, especially with multiple levels of nesting.                             | Generally more readable and organized for complex queries.                                         |
| **Reusability** | Cannot be referenced multiple times within the same query.                                    | Can be referenced multiple times in the main query.                                                |
| **Performance** | May lead to performance issues if used extensively, as the subquery is executed for each row. | Generally optimized by the SQL engine; may improve performance when used with large datasets.      |
| **Scope**       | Limited to the statement in which it is defined.                                              | Visible to the entire query that follows the `WITH` clause.                                        |
| **Use Cases**   | Useful for simple queries where temporary results are needed in one place.                    | Useful for complex queries, especially when multiple references to the same result set are needed. |

### Scalar subqueries

* A **scalar subquery** in SQL is a subquery that returns a single value (i.e., a scalar).

* **Scalar Subquery** are used when you need to compute a single value that can be used anywhere in the query (in the `SELECT`, `WHERE`, `HAVING`, or `FROM` clauses). It is particularly useful when you need to **compare the current row with an aggregate value across the entire dataset** (e.g., finding records greater than the average).
  
  * We cannot use `where,having etc.` clause with `windows function`

#### Example: We want to find those list price which are greater than avg(listprice)

* lets try this with both windows function and scalar sunquery

* Original dataset Image `without the ListPrice > Avg(ListPrice) condition`

![Scalar subqueries](https://github.com/HBISHT233/Advance_SQL/blob/main/Images/2024-10-15-12-12-22-image.png)

```sql
select
    ProductID,
    pp.Name,
    StandardCost,
    ListPrice,
    avg(ListPrice) over() as 'Avg_over()',
    (select avg(ListPrice) from Production.Product) as 'Avg_subquery',

    ListPrice-avg(ListPrice) over() as 'Avg_ListDiff_over()',
    ListPrice-(select avg(ListPrice) from Production.Product) as 'Avg_ListDiff_Query'
from Production.Product pp
where ListPrice > (select avg(ListPrice) from Production.Product)
```

![Scalar subqueries](https://github.com/HBISHT233/Advance_SQL/blob/main/Images/2024-10-15-12-13-42-image.png)

* As we can see the Windows function is givng wrong output while the scalar subquery are correct

### Correlated Subqueries

**Correlated Subqueries** are subqueries that depend on the outer query for their values. Unlike regular subqueries (which are independent and executed once), correlated subqueries execute once for every row in the outer query. Each time a row in the outer query is processed, the correlated subquery is reevaluated with reference to that specific row.

* They are run once for each record in outer query

```sql
select
    PurchaseOrderID,
    VendorID,
    OrderDate,
    TotalDue,
    (
    select count(*)
    from Purchasing.PurchaseOrderDetail ppod
    where ppod.PurchaseOrderID = ppoh.PurchaseOrderId
    and RejectedQty = 0
    )
    as NonRejectedItems
from Purchasing.PurchaseOrderHeader ppoh
```

### Exists()

In SQL, the `EXISTS` keyword is used to test for the existence of any records in a subquery. It returns `TRUE` if the subquery returns one or more records and `FALSE` if the subquery returns no records. This is typically used in `WHERE` clauses to filter rows based on whether certain conditions are met in a related table or query.

* `Exist()` is suitable for one to many relation table

![Scalar subqueries](https://github.com/HBISHT233/Advance_SQL/blob/main/Images/2024-10-15-14-12-51-image.png)

#### Lets us see an example to understand the exit

* let us find salesorderId which has alteast one line tota > 10000$

```sql
select  
    ssoh.SalesOrderID,
    ssoh.OrderDate,
    ssoh.TotalDue,
    ssod.SalesOrderDetailID,
    ssod.LineTotal

from 
    sales.SalesOrderHeader ssoh 
INNER JOIN
    sales.SalesOrderDetail ssod
ON
    ssoh.SalesOrderID = ssod.SalesOrderID
where ssod.LineTotal>10000
```

![Scalar subqueries](https://github.com/HBISHT233/Advance_SQL/blob/main/Images/2024-10-15-14-18-21-image.png)

* Here we can see there are duplicate value ..although we wanted only salesorderid with atleast 1 order with linetotal > 10000 [single salesorderId]

* In situation like these we use `EXISTS()`

```sql
select  
    ssoh.SalesOrderID,
    ssoh.OrderDate,
    ssoh.TotalDue

from 
    sales.SalesOrderHeader ssoh 
where EXISTS(
    Select 1 
    FROM sales.SalesOrderDetail ssod
    where ssod.linetotal > 10000
    AND ssoh.SalesOrderID = ssod.SalesOrderID
    )
```

![Scalar subqueries](https://github.com/HBISHT233/Advance_SQL/blob/main/Images/2024-10-15-14-26-50-image.png)

* Here we can see only single salesorderID is returned

* **`SELECT 1`** is used when we only care about the existence of rows and not their contents.

#### Example: Find SalesOrderID where none of the order are greater than 1000

```sql
select  
    ssoh.SalesOrderID,
    ssoh.OrderDate,
    ssoh.TotalDue

from 
    sales.SalesOrderHeader ssoh 
where NOT EXISTS(
    Select 1 
    FROM sales.SalesOrderDetail ssod
    where ssod.linetotal > 10000
    AND ssoh.SalesOrderID = ssod.SalesOrderID
    )
```

![Scalar subqueries](https://github.com/HBISHT233/Advance_SQL/blob/main/Images/2024-10-15-14-34-46-image.png)

### Flatteing row

![Scalar subqueries](https://github.com/HBISHT233/Advance_SQL/blob/main/Images/2024-10-15-14-44-18-image.png)

![Scalar subqueries](https://github.com/HBISHT233/Advance_SQL/blob/main/Images/2024-10-15-14-59-40-image.png)

```sql
Select 
STUFF(

(
select  
    ','+cast(cast(LineTotal as money) as varchar)

from 
    sales.SalesOrderDetail ssod 
where 
    SalesOrderID=43659
for xml path('')
),
1,1,''
)
```

![Scalar subqueries](https://github.com/HBISHT233/Advance_SQL/blob/main/Images/2024-10-15-14-59-59-image.png)

### PIVOT

```sql
select
*
FROM
(
select 
    ppc.Name as ProductCategoryName,
    ssod.LineTotal

from sales.SalesOrderDetail ssod
inner join Production.Product pp
    on pp.ProductID=ssod.ProductID
inner join Production.ProductSubcategory pps
    on pps.ProductCategoryID = pp.ProductSubcategoryID
inner join Production.ProductCategory ppc
    on ppc.ProductCategoryID = pps.ProductCategoryID
)temp

Pivot(
    sum(linetotal)
    for ProductCategoryName IN(Bikes,Clothing,Accessories,Components)

) b
```

![Scalar subqueries](https://github.com/HBISHT233/Advance_SQL/blob/main/Images/2024-10-15-15-25-21-image.png)

#### Example

```sql
select
*
FROM
(
select 
    ppc.Name as ProductCategoryName,
    ssod.LineTotal,
    ssod.OrderQty 
-- The row which is neither pivoted nor aggregated will still remain a row

from sales.SalesOrderDetail ssod
inner join Production.Product pp
    on pp.ProductID=ssod.ProductID
inner join Production.ProductSubcategory pps
    on pps.ProductCategoryID = pp.ProductSubcategoryID
inner join Production.ProductCategory ppc
    on ppc.ProductCategoryID = pps.ProductCategoryID
)temp

Pivot(
    sum(linetotal)
    for ProductCategoryName IN(Bikes,Clothing,Accessories,Components)

) b
order by OrderQty
```

![Scalar subqueries](https://github.com/HBISHT233/Advance_SQL/blob/main/Images/2024-10-15-15-34-28-image.png)
