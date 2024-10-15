## Assignment -2

### Exercise: SubQueries

#### Ques 1

Create a query that displays all rows and the following columns from the AdventureWorks2019.HumanResources.Employee table:

- BusinessEntityID

- JobTitle

- VacationHours

Also include a derived column called "MaxVacationHours" that returns the maximum amount of vacation hours for any one employee, in any given row.

```sql
select
    BusinessEntityID,
    JobTitle,
    VacationHours,
    (select max(VacationHours) from HumanResources.Employee)as MaxVacationHours
from 
    HumanResources.Employee hre
```

#### Ques 2

Add a new derived field to your query from Exercise 1, which returns the percent an individual employees' vacation hours are, *of the* *maximum vacation hours for any employee*. For example, the record for the employee with the most vacation hours should have a value of 1.00, or 100%, in this column.

**Hints:**

- You can repurpose your logic from "MaxVacationHours" for the denominator.

- Make sure you multiply at least one side of your equation by 1.0, to ensure the output will be a decimal.

```sql
select
    BusinessEntityID,
    JobTitle,
    VacationHours,
    (select max(VacationHours) from HumanResources.Employee)as MaxVacationHours,
    CONCAT(CAST(CAST(VacationHours * 100.0 / (SELECT MAX(VacationHours) FROM HumanResources.Employee) AS DECIMAL(5, 2)) AS VARCHAR(10)), '%') AS Vacation_wrt_Max
from 
    HumanResources.Employee hre
```

![Assignment -2](https://github.com/HBISHT233/Advance_SQL/blob/main/Images/2024-10-15-12-30-00-image.png)

#### Ques 3

Refine your output with a criterion in the WHERE clause that filters out any employees whose vacation hours are less then 80% of the maximum amount of vacation hours for any one employee. In other words, return only employees who have at least 80% as much vacation time as the employee with the most vacation time.

**Hint:** The query should return 60 rows

```sql
select
    BusinessEntityID,
    JobTitle,
    VacationHours,
    (select max(VacationHours) from HumanResources.Employee)as MaxVacationHours,
    CONCAT(CAST(CAST(VacationHours * 100.0 / (SELECT MAX(VacationHours) FROM HumanResources.Employee) AS DECIMAL(5, 2)) AS VARCHAR(10)), '%') AS Vacation_wrt_Max
from 
    HumanResources.Employee hre
WHERE
    CAST(VacationHours * 100.0 / (SELECT MAX(VacationHours) FROM HumanResources.Employee) AS DECIMAL(5, 2)) < 80
ORDER BY
    VacationHours DESC
OFFSET 0 ROWS FETCH NEXT 60 ROWS ONLY;
```

* Note: SQL server dont have `LIMIT`
* When using `OFFSET` and `FETCH NEXT`, it’s essential to have an `ORDER BY` clause. Without it, the result set may be non-deterministic, meaning the rows returned could change each time you run the query. The `ORDER BY` clause determines which rows are returned and in what order.

![Assignment -2](https://github.com/HBISHT233/Advance_SQL/blob/main/Images/2024-10-15-12-34-17-image.png)

### Exercise: Correlated Subqueries

#### Ques 1

Write a query that outputs all records from the Purchasing.PurchaseOrderHeader table. Include the following columns from the table:

- PurchaseOrderID

- VendorID

- OrderDate

- TotalDue

Add a derived column called **NonRejectedItems** which returns, for each purchase order ID in the query output, the number of line items from the Purchasing.PurchaseOrderDetail table which did not have any rejections (i.e., RejectedQty = 0). Use a correlated subquery to do this.

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

![Assignment -2](https://github.com/HBISHT233/Advance_SQL/blob/main/Images/2024-10-15-13-23-09-image.png)

#### Ques 2

Modify your query to include a second derived field called **MostExpensiveItem**.

This field should return, for each purchase order ID, the UnitPrice of the most expensive item for that order in the Purchasing.PurchaseOrderDetail table.

Use a correlated subquery to do this as well.

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
    as NonRejectedItems,
    (
    select max(unitprice)
    from Purchasing.PurchaseOrderDetail ppod
    where ppod.PurchaseOrderID = ppoh.PurchaseOrderId
    )as MostExpensiveItem
from Purchasing.PurchaseOrderHeader ppoh

select *  from Purchasing.PurchaseOrderDetail 
```

![Assignment -2](https://github.com/HBISHT233/Advance_SQL/blob/main/Images/2024-10-15-13-26-28-image.png)

### Exercise: EXISTS()

#### Ques 1

Select all records from the Purchasing.PurchaseOrderHeader table such that there is at least one item in the order with an order quantity greater than 500. The individual items tied to an order can be found in the Purchasing.PurchaseOrderDetail table.

Select the following columns:

- PurchaseOrderID

- OrderDate

- SubTotal

- TaxAmt

Sort by purchase order ID.

```sql
select
    ppoh.PurchaseOrderID,
    ppoh.OrderDate,
    ppoh.SubTotal,
    ppoh.TaxAmt
from
    Purchasing.PurchaseOrderHeader ppoh
where exists( 
            select 1
            from Purchasing.PurchaseOrderDetail ppod
            where ppod.PurchaseOrderID = ppoh.PurchaseOrderID
            AND OrderQty >500
)
order by ppoh.PurchaseOrderID
```

![Assignment -2](https://github.com/HBISHT233/Advance_SQL/blob/main/Images/2024-10-15-20-33-20-image.png)

#### Ques 2

Modify your query from Exercise 1 as follows:

Select all records from the Purchasing.PurchaseOrderHeader table such that there is at least one item in the order with an order quantity greater than 500, AND a unit price greater than $50.00.

Select ALL columns from the Purchasing.PurchaseOrderHeader table for display in your output.

Even if you have aliased this table to enable the use of a JOIN or EXISTS, you can still use the SELECT * shortcut to do this. Assuming you have aliased your table "A", simply use "SELECT A.*" to select all columns from that table.

```sql
select
    ppoh.PurchaseOrderID,
    ppoh.OrderDate,
    ppoh.SubTotal,
    ppoh.TaxAmt
from
    Purchasing.PurchaseOrderHeader ppoh
where exists( 
            select 1
            from Purchasing.PurchaseOrderDetail ppod
            where ppod.PurchaseOrderID = ppoh.PurchaseOrderID
            AND ppod.OrderQty >500 and ppod.UnitPrice >50
)
order by ppoh.PurchaseOrderID
```

#### Ques 3

Select all records from the Purchasing.PurchaseOrderHeader table such that NONE of the items within the order have a rejected quantity greater than 0.

Select ALL columns from the Purchasing.PurchaseOrderHeader table using the "SELECT *" shortcut.

```sql
select
    ppoh.PurchaseOrderID,
    ppoh.OrderDate,
    ppoh.SubTotal,
    ppoh.TaxAmt
from
    Purchasing.PurchaseOrderHeader ppoh
where exists( 
            select 1
            from Purchasing.PurchaseOrderDetail ppod
            where ppod.PurchaseOrderID = ppoh.PurchaseOrderID
            AND ppod.RejectedQty > 0
)
order by ppoh.PurchaseOrderID
```

![Assignment -2](https://github.com/HBISHT233/Advance_SQL/blob/main/Images/2024-10-15-20-39-17-image.png)

### Exercise: Flattening Rows

#### Ques 1

Create a query that displays all rows from the Production.ProductSubcategory table, and includes the following fields:

- The "Name" field from Production.ProductSubcategory, which should be aliased as "SubcategoryName"

- A derived field called "Products" which displays, for each Subcategory in Production.ProductSubcategory, a semicolon-separated list of all products from Production.Product contained within the given subcategory

**Hint:** Production.ProductSubcategory and Production.Product are related by the "ProductSubcategoryID" field.

```sql
SELECT 
    pps.name as SubcategoryName,
    -- Concatenate product names
    STUFF(
        (
            SELECT ',' + pp.name
            FROM Production.Product pp
            WHERE pps.ProductsubCategoryID = pp.ProductSubcategoryID
            FOR XML PATH('')
        ), 1, 1, ''
    ) AS Products
FROM
    Production.ProductSubcategory pps
```

 ![Assignment -2](https://github.com/HBISHT233/Advance_SQL/blob/main/Images/2024-10-15-20-57-43-image.png)

#### Ques 2

Modify the query from Exercise 1 such that only products with a ListPrice value greater than $50 are listed in the "Products" field.

**Hint:** Assuming you used a correlated subquery in Exercise 1, keep in mind that you can apply additional criteria to it, just as with any other correlated subquery.

**NOTE:** Your query should still include ALL product subcategories, but only list associated products greater than 50. But since there are certain product subcategories that don't have *any* associated products greater than $50, some rows in your query output may have a NULL value in the product field.

```sql
SELECT 
    pps.name as SubcategoryName,
    -- Concatenate product names
    STUFF(
        (
            SELECT ',' + pp.name
            FROM Production.Product pp
            WHERE pps.ProductsubCategoryID = pp.ProductSubcategoryID
            AND pp.ListPrice > 50
            FOR XML PATH('')
        ), 1, 1, ''
    ) AS Products
FROM
    Production.ProductSubcategory pps
```

![Assignment -2](https://github.com/HBISHT233/Advance_SQL/blob/main/Images/2024-10-15-20-59-40-image.png)

### Exercise: Pivot()

#### Ques 1

that summarizes the average amount of vacation time for Sales Representatives, Buyers, and Janitors.

Your output should look like the image below.

![Assignment -2](https://github.com/HBISHT233/Advance_SQL/blob/main/Images/2024-10-15-21-19-46-image.png)

```sql
SELECT
*
FROM
(
SELECT 
JobTitle,
VacationHours 

FROM HumanResources.Employee
) A

PIVOT(
AVG(VacationHours)
FOR JobTitle IN([Sales Representative],[Buyer],[Janitor])
) B
```

#### Ques 2

Modify your query from Exercise 1 such that the results are broken out by Gender. Alias the Gender field as "Employee Gender" in your output.

Your output should look like the image below:

![Assignment -2](https://github.com/HBISHT233/Advance_SQL/blob/main/Images/2024-10-15-21-20-12-image.png)

```sql
SELECT
Gender as 'Employee Gender',
[Sales Representative],
[Buyer],
[Janitor]
FROM
(
SELECT 
JobTitle,
Gender, -- no pivot or aggregate is applied to it means it will be a row
VacationHours

FROM HumanResources.Employee
) A

PIVOT(
AVG(VacationHours)
FOR JobTitle IN([Sales Representative],[Buyer],[Janitor])
) B

```
