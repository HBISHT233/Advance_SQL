## Assignment 1
#### Exercise: Windows function and partition by

#### Ques 1

Create a query with the following columns:

- FirstName and LastName, from the Person.Person table**

- JobTitle, from the HumanResources.Employee table**

- Rate, from the HumanResources.EmployeePayHistory table**

- A derived column called "AverageRate" that returns the average of all values in the "Rate" column, in each row

- Note: ***All the above tables can be joined on BusinessEntityID***

```sql
select FirstName,LastName,jobtitle,rate,avg(rate) over() as AverageRate
from person.Person pp
inner join HumanResources.Employee hre
on hre.BusinessEntityID = pp.BusinessEntityID
inner join HumanResources.EmployeePayHistory hrep
on hrep.BusinessEntityID = pp.BusinessEntityID
```

#### Ques 2

Enhance your query from Exercise 1 by adding a derived column called "MaximumRate" that returns the largest of all values in the "Rate" column, in each row.

```sql
select FirstName,LastName,jobtitle,rate,avg(rate) over() as AverageRate,
max(rate) over() as MaximumRate -- maximum rate column added
from person.Person pp
inner join person.businessentity pb
on pp.BusinessEntityID = pb.BusinessEntityID
inner join HumanResources.Employee hre
on hre.BusinessEntityID = pb.BusinessEntityID
inner join HumanResources.EmployeePayHistory hrep
on hrep.BusinessEntityID=pb.BusinessEntityID
```

#### ![Assignment 1](https://github.com/HBISHT233/Advance_SQL/blob/main/Images/2024-10-14-11-27-21-image.png)

#### Ques 3

Enhance your query from ques 2 by adding a derived column called "DiffFromAvgRate" that returns the result of the following calculation: An employees's pay rate, MINUS the average of all values in the "Rate" column.

```sql
Select *,(rate-averagerate) as DiffFromAvgRate
from
(
select FirstName,LastName,jobtitle,rate,avg(rate) over() as AverageRate,
max(rate) over() as MaximumRate -- maximum rate column added
from person.Person pp
inner join HumanResources.Employee hre
on hre.BusinessEntityID = pp.BusinessEntityID
inner join HumanResources.EmployeePayHistory hrep
on hrep.BusinessEntityID = pp.BusinessEntityID
)temp
```

![Assignment 1](https://github.com/HBISHT233/Advance_SQL/blob/main/Images/2024-10-14-11-13-33-image.png)

#### Ques 4

Enhance your query from Ques 3 by adding a derived column called "PercentofMaxRate" that returns the result of the following calculation: An employees's pay rate, DIVIDED BY the maximum of all values in the "Rate" column, times 100.

```sql
Select *,(rate-averagerate) as DiffFromAvgRate,
(rate*100/MaximumRate)as PercentofMaxRate  -- % of maximum column added
from
(
select FirstName,LastName,jobtitle,rate,avg(rate) over() as AverageRate,
max(rate) over() as MaximumRate -- maximum rate column added
from person.Person pp
inner join HumanResources.Employee hre
on hre.BusinessEntityID = pp.BusinessEntityID
inner join HumanResources.EmployeePayHistory hrep
on hrep.BusinessEntityID = pp.BusinessEntityID
)temp
```

![Assignment 1](https://github.com/HBISHT233/Advance_SQL/blob/main/Images/2024-10-14-11-16-11-image.png)

#### Ques 5

Create a query with the following columns:

- “Name” from the Production.Product table, which can be alised as “ProductName”

- “ListPrice” from the Production.Product table

- “Name” from the Production. ProductSubcategory table, which can be alised as “ProductSubcategory”*

- “Name” from the Production.ProductCategory table, which can be alised as “ProductCategory”**

**Join Production.ProductSubcategory to Production.Product on “ProductSubcategoryID”***

***Join Production.ProductCategory to ProductSubcategory on “ProductCategoryID”***

All the tables can be inner joined, and you do not need to apply any criteria.

```sql
select pp.name as ProductName,ListPrice,
pps.Name as ProductSubcategory,
ppc.name as ProductCategory
from Production.Product as pp
inner join Production.ProductSubcategory as pps
on pp.ProductSubcategoryID = pps.ProductSubcategoryID
inner join Production.ProductCategory ppc
on ppc.ProductCategoryID = pps.ProductCategoryID
```

#### Ques 6

Enhance your query from ques 5 by adding a derived column called "AvgPriceByCategory " that returns the average ListPrice *for the product category in each given row*.

```sql
select pp.name as ProductName,ListPrice,
pps.Name as ProductSubcategory,
ppc.name as ProductCategory,
avg(ListPrice) over(partition by ppc.name) as AvgPriceByCategory
from Production.Product as pp
inner join Production.ProductSubcategory as pps
on pp.ProductSubcategoryID = pps.ProductSubcategoryID
inner join Production.ProductCategory ppc
on ppc.ProductCategoryID = pps.ProductCategoryID
```

![Assignment 1](https://github.com/HBISHT233/Advance_SQL/blob/main/Images/2024-10-14-11-42-55-image.png)

#### Ques 7

Enhance your query from Ques 6 by adding a derived column called "AvgPriceByCategoryAndSubcategory" that returns the average ListPrice *for the product category AND subcategory in each given row.*

```sql
select pp.name as ProductName,ListPrice,
pps.Name as ProductSubcategory,
ppc.name as ProductCategory,
avg(ListPrice) over(partition by ppc.name) as AvgPriceByCategory,
avg(listprice) over(partition by ppc.name,pps.name)as AvgPriceByCategoryAndSubcategory
from Production.Product as pp
inner join Production.ProductSubcategory as pps
on pp.ProductSubcategoryID = pps.ProductSubcategoryID
inner join Production.ProductCategory ppc
on ppc.ProductCategoryID = pps.ProductCategoryID
```

![Assignment 1](https://github.com/HBISHT233/Advance_SQL/blob/main/Images/2024-10-14-11-44-13-image.png)

#### Ques 8

Enhance your query from ques 7 by adding a derived column called "ProductVsCategoryDelta" that returns the result of the following calculation: A product's list price, MINUS the average ListPrice for that product’s category.

```sql
select pp.name as ProductName,ListPrice,
pps.Name as ProductSubcategory,
ppc.name as ProductCategory,
avg(ListPrice) over(partition by ppc.name) as AvgPriceByCategory,
avg(listprice) over(partition by ppc.name,pps.name)as AvgPriceByCategoryAndSubcategory,
(listprice-avg(ListPrice) over(partition by ppc.name)) as ProductVsCategoryDelta
from Production.Product as pp
inner join Production.ProductSubcategory as pps
on pp.ProductSubcategoryID = pps.ProductSubcategoryID
inner join Production.ProductCategory ppc
on ppc.ProductCategoryID = pps.ProductCategoryID
```

![Assignment 1](https://github.com/HBISHT233/Advance_SQL/blob/main/Images/2024-10-14-11-50-30-image.png)

### Exercise: ROW_NUMBER()

#### Ques 1

Create a query with the following columns (feel free to borrow your code from Exercise 1 of the PARTITION BY exercises):

- “Name” from the Production.Product table, which can be alised as “ProductName”

- “ListPrice” from the Production.Product table

- “Name” from the Production. ProductSubcategory table, which can be alised as “ProductSubcategory”

- “Name” from the Production.ProductCategory table, which can be alised as “ProductCategory”

**Join Production.ProductSubcategory to Production.Product on “ProductSubcategoryID”***

***Join Production.ProductCategory to ProductSubcategory on “ProductCategoryID”***

All the tables can be inner joined, and you do not need to apply any criteria.

```sql
select pp.name as ProductName,pp.ListPrice,
pps.name as ProductSubcategory,
ppc.name as ProductCategory
from Production.Product pp
inner join Production.ProductSubcategory pps
on pp.ProductSubcategoryID = pps.ProductSubcategoryID
inner join Production.ProductCategory ppc
on ppc.ProductCategoryID = pps.ProductCategoryID
```

#### Ques 2

Enhance your query from Exercise 1 by adding a derived column called

"Price Rank " that ranks all records in the dataset by ListPrice, in descending order. That is to say, the product with the most expensive price should have a rank of 1, and the product with the least expensive price should have a rank equal to the number of records in the dataset.

```sql
select pp.name as ProductName,pp.ListPrice,
pps.name as ProductSubcategory,
ppc.name as ProductCategory,
row_number() over(order by listprice DESC) as 'Price Rank'
from Production.Product pp
inner join Production.ProductSubcategory pps
on pp.ProductSubcategoryID = pps.ProductSubcategoryID
inner join Production.ProductCategory ppc
on ppc.ProductCategoryID = pps.ProductCategoryID
```

![Assignment 1](https://github.com/HBISHT233/Advance_SQL/blob/main/Images/2024-10-14-12-06-23-image.png)

#### Ques 3

Enhance your query from Exercise 2 by adding a derived column called

"Category Price Rank" that ranks all products by ListPrice – *within each category -* in descending order. In other words, every product within a given category should be ranked relative to other products in the same category.

```sql
select pp.name as ProductName,pp.ListPrice,
pps.name as ProductSubcategory,
ppc.name as ProductCategory,
row_number() over(order by listprice DESC) as 'Price Rank',
row_number() over (partition by ppc.name order by listprice DESC)as 'Category Price Rank'
from Production.Product pp
inner join Production.ProductSubcategory pps
on pp.ProductSubcategoryID = pps.ProductSubcategoryID
inner join Production.ProductCategory ppc
on ppc.ProductCategoryID = pps.ProductCategoryID
```

![Assignment 1](https://github.com/HBISHT233/Advance_SQL/blob/main/Images/2024-10-14-12-08-38-image.png)

#### Ques 4

Enhance your query from Exercise 3 by adding a derived column called

"Top 5 Price In Category" that returns the string “Yes” if a product has one of the top 5 list prices in its product category, and “No” if it does not. You can try incorporating your logic from Exercise 3 into a CASE statement to make this work.

```sql
select pp.name as ProductName,pp.ListPrice,
pps.name as ProductSubcategory,
ppc.name as ProductCategory,
row_number() over(order by listprice DESC) as 'Price Rank',
row_number() over (partition by ppc.name order by listprice DESC)as 'Category Price Rank',
case when row_number() over (partition by ppc.name order by listprice DESC) <6 
then 'Yes' else 'No' end as  'Top 5 Price In Category'
from Production.Product pp
inner join Production.ProductSubcategory pps
on pp.ProductSubcategoryID = pps.ProductSubcategoryID
inner join Production.ProductCategory ppc
on ppc.ProductCategoryID = pps.ProductCategoryID
```

![Assignment 1](https://github.com/HBISHT233/Advance_SQL/blob/main/Images/2024-10-14-12-14-35-image.png)

### Exercise: Rank and Dense_rank

#### Ques 1

Using your solution query to Exercise 4 from the ROW_NUMBER exercises as a staring point, add a derived column called “Category Price Rank With Rank” that uses the RANK function to rank all products by ListPrice – *within each category -* in descending order. Observe the differences between the “Category Price Rank” and “Category Price Rank With Rank” fields.

```sql
select pp.name as ProductName,pp.ListPrice,
pps.name as ProductSubcategory,
ppc.name as ProductCategory,
row_number() over(order by listprice DESC) as 'Price Rank',
row_number() over (partition by ppc.name order by listprice DESC)as 'Category Price Rank',
case when row_number() over (partition by ppc.name order by listprice DESC) <6 
then 'Yes' else 'No' end as  'Top 5 Price In Category',
rank() over (partition by ppc.name order by listprice DESC) as 'Category Price Rank With Rank'
from Production.Product pp
inner join Production.ProductSubcategory pps
on pp.ProductSubcategoryID = pps.ProductSubcategoryID
inner join Production.ProductCategory ppc
on ppc.ProductCategoryID = pps.ProductCategoryID
```

![Assignment 1](https://github.com/HBISHT233/Advance_SQL/blob/main/Images/2024-10-14-12-22-47-image.png)

#### Ques 2

Modify your query from Exercise 2 by adding a derived column called "Category Price Rank With Dense Rank" that that uses the DENSE_RANK function to rank all products by ListPrice – *within each category -* in descending order. Observe the differences among the “Category Price Rank”, “Category Price Rank With Rank”, and “Category Price Rank With Dense Rank” fields.

```sql
select pp.name as ProductName,pp.ListPrice,
pps.name as ProductSubcategory,
ppc.name as ProductCategory,
row_number() over(order by listprice DESC) as 'Price Rank',
row_number() over (partition by ppc.name order by listprice DESC)as 'Category Price Rank',
case when row_number() over (partition by ppc.name order by listprice DESC) <6 
then 'Yes' else 'No' end as  'Top 5 Price In Category',
rank() over (partition by ppc.name order by listprice DESC) as 'Category Price Rank With Rank',
dense_rank() over (partition by ppc.name order by listprice DESC) as 'Category Price Rank With Dense Rank'

from Production.Product pp
inner join Production.ProductSubcategory pps
on pp.ProductSubcategoryID = pps.ProductSubcategoryID
inner join Production.ProductCategory ppc
on ppc.ProductCategoryID = pps.ProductCategoryID
```

![Assignment 1](https://github.com/HBISHT233/Advance_SQL/blob/main/Images/2024-10-14-12-25-02-image.png)

#### Ques 3

Examine the code you wrote to define the “Top 5 Price In Category” field back in the ROW_NUMBER exercises. Now that you understand the differences among ROW_NUMBER, RANK, and DENSE_RANK, consider which of these functions would be most appropriate to return a true top 5 products by price, assuming we want to see the top 5 **distinct** prices AND we want “ties” (by price) to all share the same rank.

```sql
select pp.name as ProductName,pp.ListPrice,
pps.name as ProductSubcategory,
ppc.name as ProductCategory,
row_number() over(order by listprice DESC) as 'Price Rank',
row_number() over (partition by ppc.name order by listprice DESC)as 'Category Price Rank',
case when dense_rank() over (partition by ppc.name order by listprice DESC) <6 
then 'Yes' else 'No' end as  'Top 5 Price In Category',
rank() over (partition by ppc.name order by listprice DESC) as 'Category Price Rank With Rank',
dense_rank() over (partition by ppc.name order by listprice DESC) as 'Category Price Rank With Dense Rank'

from Production.Product pp
inner join Production.ProductSubcategory pps
on pp.ProductSubcategoryID = pps.ProductSubcategoryID
inner join Production.ProductCategory ppc
on ppc.ProductCategoryID = pps.ProductCategoryID
```

![Assignment 1](https://github.com/HBISHT233/Advance_SQL/blob/main/Images/2024-10-14-12-28-16-image.png)

### Exercise: lead and lag

#### Ques 1

Create a query with the following columns:

- “PurchaseOrderID” from the Purchasing.PurchaseOrderHeader table

- “OrderDate” from the Purchasing.PurchaseOrderHeader table

- “TotalDue” from the Purchasing.PurchaseOrderHeader table

- “Name” from the Purchasing.Vendor table, which can be aliased as “VendorName”*****

**Join Purchasing.Vendor to Purchasing.PurchaseOrderHeader on BusinessEntityID = VendorID*

Apply the following criteria to the query:

- Order must have taken place on or after 2013

- TotalDue must be greater than $500

```sql
select 
    PurchaseOrderID,
    OrderDate,TotalDue,
    pv.Name as VendorName
from
    Purchasing.PurchaseOrderHeader pph
inner join
    Purchasing.Vendor pv
    on pv.BusinessEntityID = pph.VendorID
where
    TotalDue > 500 and OrderDate >= '2013-01-01'
```

#### Ques 2

Modify your query from Exercise 1 by adding a derived column called

"PrevOrderFromVendorAmt", that returns the “previous” TotalDue value (relative to the current row) *within the group of all orders with the same vendor ID.* We are defining “previous” based on order date.

```sql
select 
    PurchaseOrderID,
    OrderDate,
    TotalDue,
    pv.Name as VendorName,
    lag(TotalDue,1) over(partition by pv.name order by orderdate ASC) as PrevOrderFromVendorAmt
from
    Purchasing.PurchaseOrderHeader pph
inner join
    Purchasing.Vendor pv
    on pv.BusinessEntityID = pph.VendorID
where
    TotalDue > 500 and OrderDate >= '2013-01-01'
order by
    pv.name,orderdate ASC
```

![Assignment 1](https://github.com/HBISHT233/Advance_SQL/blob/main/Images/2024-10-14-22-14-09-image.png)

#### Ques 3

Modify your query from Exercise 2 by adding a derived column called

"NextOrderByEmployeeVendor", that returns the “next” vendor name (the “name” field from Purchasing.Vendor) *within the group of all orders that have the same EmployeeID value in* Purchasing.PurchaseOrderHeader*.* Similar to the last exercise, we are defining “next” based on order date.

```sql
select 
    PurchaseOrderID,
    OrderDate,
    TotalDue,
    pv.Name as VendorName,
    lag(TotalDue,1) over(partition by pv.name order by orderdate ASC) as PrevOrderFromVendorAmt,
    EmployeeID,
    lead(pv.name,1) over(partition by employeeid order by orderdate ASC)as NextOrderByEmployeeVendor
from
    Purchasing.PurchaseOrderHeader pph
inner join
    Purchasing.Vendor pv
    on pv.BusinessEntityID = pph.VendorID
where
    TotalDue > 500 and year(OrderDate) >= 2013
order by
    EmployeeID,orderdate ASC
```

![Assignment 1](https://github.com/HBISHT233/Advance_SQL/blob/main/Images/2024-10-14-22-30-23-image.png)

#### Ques 4

Modify your query from Exercise 3 by adding a derived column called "Next2OrderByEmployeeVendor" that returns, within the group of all orders that have the same EmployeeID*, the vendor name offset TWO orders into the “future” relative to the order in the current row.* The code should be very similar to Exercise 3, but with an extra argument passed to the Window Function used.

```sql
select 
    PurchaseOrderID,
    OrderDate,
    TotalDue,
    pv.Name as VendorName,
    lag(TotalDue,1) over(partition by pv.name order by orderdate ASC) as PrevOrderFromVendorAmt,
    EmployeeID,
    lead(pv.name,1) over(partition by employeeid order by orderdate ASC)as NextOrderByEmployeeVendor,
    lead(pv.name,2) over(partition by employeeid order by orderdate ASC)as Next2OrderByEmployeeVendor
from
    Purchasing.PurchaseOrderHeader pph
inner join
    Purchasing.Vendor pv
    on pv.BusinessEntityID = pph.VendorID
where
    TotalDue > 500 and year(OrderDate) >= 2013
order by
    EmployeeID,orderdate ASC
```

![Assignment 1](https://github.com/HBISHT233/Advance_SQL/blob/main/Images/2024-10-14-22-33-00-image.png)

### Exercise: First_Value()

#### Ques 1

* Create a query that returns all records - and the following columns - from the **HumanResources.Employee** table:
  
  * To make the effect of subsequent steps clearer, also sort the query output by "JobTitle" and HireDate, both in ascending order.

* Now add a derived column called “FirstHireVacationHours” that displays – for a given job title – the amount of vacation hours possessed by the *first* employee hired who has that same job title. For example, if 5 employees have the title “Data Guru”, and the one of those 5 with the oldest hire date has 99 vacation hours, “FirstHireVacationHours” should display “99” for all 5 of those employees’ corresponding records in the query.

```sql
-- I am not selecting everthing just for the sake of Screenshot
select 
    NationalIDNumber,
    loginid,
    hiredate,
    jobtitle,
    vacationhours,
    first_value(VacationHours) over(partition by jobtitle order by hiredate ASC)as FirstHireVacationHours
from HumanResources.Employee
order by JobTitle,HireDate ASC
```

![Assignment 1](https://github.com/HBISHT233/Advance_SQL/blob/main/Images/2024-10-14-22-51-53-image.png)

#### Ques 2

* Create a query with the following columns:

        a. “ProductID” from the **Production.Product** table

        b. “Name” from the **Production.Product** table (alias this as “ProductName”)

        c. “ListPrice” from the **Production.ProductListPriceHistory** table

        d. “ModifiedDate” from the **Production.ProductListPriceHistory**

You can join the **Production.Product** and **Production.ProductListPriceHistory** tables on "ProductID".

* 1. Now add a derived column called “HighestPrice” that displays – for a given product – the highest price that product has been listed at. So even if there are 4 records for a given product, this column should only display the all-time highest list price for that product in each of those 4 rows.

```sql
select 
    pp.ProductID,
    pp.name as ProductName,
    pplph.ListPrice,
    pplph.ModifiedDate,
    max(pplph.ListPrice) over(partition by pp.name)as HighestPrice
from Production.Product pp
inner join Production.ProductListPriceHistory pplph
on pp.ProductID = pplph.ProductID
order by pp.ProductID,ModifiedDate ASC
```

* Similarly, create another derived column called “LowestCost” that displays the all-time lowest price for a given product.

* Finally, create a third derived column called “PriceRange” that reflects, for a given product, the difference between its highest and lowest ever list prices.

```sql
select 
    pp.ProductID,
    pp.name as ProductName,
    pplph.ModifiedDate,
    pplph.ListPrice,
    max(pplph.ListPrice) over(partition by pp.name)as HighestPrice,
    min(pplph.ListPrice) over(partition by pp.name)as LowestCost,
    MAX(pplph.ListPrice) OVER (PARTITION BY pp.ProductID) - MIN(pplph.ListPrice) OVER (PARTITION BY pp.ProductID) AS PriceRange
from Production.Product pp
inner join Production.ProductListPriceHistory pplph
on pp.ProductID = pplph.ProductID
order by pp.ProductID,ModifiedDate ASC
```

![Assignment 1](https://github.com/HBISHT233/Advance_SQL/blob/main/Images/2024-10-14-23-22-33-image.png)

### Exercise: Subqueries

#### Ques 1

Write a query that displays the three most expensive orders, **per vendor ID**, from the Purchasing.PurchaseOrderHeader table. There should ONLY be three records per Vendor ID, even if some of the total amounts due are identical. "Most expensive" is defined by the amount in the "TotalDue" field.

Include the following fields in your output:

- PurchaseOrderID

- VendorID

- OrderDate

- TaxAmt

- Freight

- TotalDue

```sql
Select * 
from
(
select 
    PurchaseOrderID,
    VendorID,
    OrderDate,
    TaxAmt,
    Freight,
    TotalDue,
    ROW_NUMBER() over(partition by vendorid order by totaldue DESC) as Price_rank
from 
    Purchasing.PurchaseOrderHeader ppoh 
)temp
where Price_rank <4
order by vendorid ASC,TotalDue DESC
```

![Assignment 1](https://github.com/HBISHT233/Advance_SQL/blob/main/Images/2024-10-14-23-31-32-image.png)

#### Ques 2

Modify your query from the first problem, such that the top three purchase order **amounts** are returned, regardless of how many records are returned per Vendor Id.

In other words, if there are multiple orders with the same total due amount, all should be returned as long as the total due amount for these orders is one of the top three.

Ultimately, you should see three distinct total due amounts (i.e., the top three) for each group of like Vendor Ids. However, there could be multiple records for each of these amounts.

```sql
Select * 
from
(
select 
    PurchaseOrderID,
    VendorID,
    OrderDate,
    TaxAmt,
    Freight,
    TotalDue,
    dense_rank() over(partition by vendorid order by totaldue DESC) as Price_rank
from 
    Purchasing.PurchaseOrderHeader ppoh 
)temp
where Price_rank <4
order by vendorid ASC,TotalDue DESC
```

![Assignment 1](https://github.com/HBISHT233/Advance_SQL/blob/main/Images/2024-10-14-23-33-45-image.png)

### ROW_BETWEEN

#### Ques 1

Create a query with the following columns:

- “OrderMonth”, a *derived* column (you’ll have to create this one yourself) featuring the month **number** corresponding with the Order *Date* in a given row

- “OrderYear”, a derived column featuring the **year** corresponding with the Order Date in a given row

- “SubTotal” from the **Purchasing.PurchaseOrderHeader** table

Your query should be an *aggregate* query – specifically, it should **sum** “SubTotal”, and **group by** the remaining fields

```sql
select
	orderdate,
	YEAR(orderdate) as OrderYear,
	MONTH(OrderDate) as OrderMonth,
	SubTotal,
	SUM(SubTotal) OVER (PARTITION BY YEAR(OrderDate), MONTH(OrderDate)) AS TotalSubTotal
from 
	Purchasing.PurchaseOrderHeader ppoh
order by
	YEAR(orderdate),MONTH(OrderDate) ASC
```

#### Ques 2

Modify your query from Exercise 1 by adding a derived column called "Rolling3MonthTotal", that displays  - for a given row - a running total of “SubTotal” for the prior three months (including the current row).

#### **Ques 3**

Modify your query from Exercise 3 by adding another derived column called "MovingAvg6Month", that calculates a rolling average of “SubTotal” for the previous 6 months, relative to the month in the “current” row. Note that this average should NOT include the current row.

#### **Ques 4**

Modify your query from Exercise 3 by adding (yet) another derived column called “MovingAvgNext2Months” , that calculates a rolling average of “SubTotal” for the month in the current row **and** the next two months after that. This moving average will provide a kind of "forecast" for Subtotal by month.

```sql
SELECT
	OrderMonth,
	OrderYear,
	SubTotal,
	Rolling3MonthTotal = SUM(SubTotal) OVER(ORDER BY OrderYear, OrderMonth ROWS BETWEEN 2 PRECEDING AND CURRENT ROW),
	MovingAvg6Month = AVG(SubTotal) OVER(ORDER BY OrderYear, OrderMonth ROWS BETWEEN 6 PRECEDING AND 1 PRECEDING),
	MovingAvgNext2Months = AVG(SubTotal) OVER(ORDER BY OrderYear, OrderMonth ROWS BETWEEN CURRENT ROW AND 2 FOLLOWING)

FROM (
	SELECT
		OrderMonth = MONTH(OrderDate),
		OrderYear = YEAR(OrderDate),
		SubTotal = SUM(SubTotal)

	FROM Purchasing.PurchaseOrderHeader

	GROUP BY
		MONTH(OrderDate),
		YEAR(OrderDate)
) X
```

![Assignment 1](https://github.com/HBISHT233/Advance_SQL/blob/main/Images/2024-10-15-00-01-48-image.png)