### Temp Tables

#### Limitation of CTE

* CTE can only be used in the current query scope, meaning they cannot be referred after the final select -> Thus issue with reusability of the table

* Virtual table cannot be referred individually making debugging more difficult
  
  * You can't directly query a view or CTE for debugging like a real table.

#### Temp Table

**Temporary tables** in SQL are special tables that exist temporarily and are used for intermediate data storage during session-specific operations. They help you store results of complex queries, perform transformations, or manipulate data before producing the final result. Once the session is closed, the temporary table is automatically dropped.

```sql
SELECT column1, column2, ...
INTO #TempTable
FROM ExistingTable
WHERE condition;
```

* **Local Temporary Table**: Defined with a single `#` (e.g., `#TempTable`)

* **Global Temporary Table**: Defined with two `##` (e.g., `##GlobalTempTable`)

Note: In order to call a Temp Table use # or ## along with the name

* In order to delete the Temp table use the command
  
  * `DROP TABLE #TempTable`

#### Create and Insert

If you want to **create a temporary table** first and then **insert data** into it using SQL, you can do it in two separate steps: one for creating the temp table, and another for inserting data. Here's how you can achieve this:

```sql
-- Step 1: Create temporary table
CREATE TABLE #TempTable (
    ID INT,
    Name VARCHAR(50),
    Salary DECIMAL(10, 2)
);

-- Step 2: Insert data into temporary table
INSERT INTO #TempTable (ID, Name, Salary)
VALUES (1, 'Alice', 55000.00),
       (2, 'Bob', 65000.00),
       (3, 'Charlie', 70000.00);

-- Step 3: Query the temporary table
SELECT * FROM #TempTable;

```

```sql
-- Step 1: Create temporary table

CREATE TABLE #TempTable (
    ID INT,
    Name VARCHAR(50),
    Salary DECIMAL(10, 2)
);

-- Step 2: Insert data from another table (Employees) into the temporary table

INSERT INTO #TempTable (ID, Name, Salary)
SELECT EmployeeID, EmployeeName, Salary
FROM Employees
WHERE Department = 'IT';

-- Step 3: Query the temporary table

SELECT * FROM #TempTable;
```

* Note: Keep the sequence in Select query same as the sequence in the Create table query

#### Truncate

In SQL, the `TRUNCATE` statement is used to **remove all rows** from a table quickly, while preserving the table structure for future use. It is often faster than the `DELETE` statement, especially for large tables, because it bypasses certain checks and logging.

| Feature                   | `TRUNCATE`                           | `DELETE`                              |
| ------------------------- | ------------------------------------ | ------------------------------------- |
| **Action**                | Removes all rows                     | Removes specific rows (can use WHERE) |
| **Logging**               | Minimal logging                      | Logs each row deletion                |
| **Speed**                 | Faster                               | Slower for large tables               |
| **Resets Auto-Increment** | Yes                                  | No                                    |
| **WHERE Clause**          | Not allowed                          | Allowed                               |
| **Trigger Execution**     | Does not fire triggers               | Triggers are executed                 |
| **Rollback**              | Not always allowed (depending on DB) | Can be rolled back if in transaction  |

```sql
-- Remove all data from the Employees table
TRUNCATE TABLE Employees;
```

### Update

The `UPDATE` statement in SQL is used to **modify existing records** in a table. You can update one or more rows based on a condition using the `WHERE` clause. If the `WHERE` clause is omitted, all rows in the table will be updated.

```sql
UPDATE table_name
SET column1 = value1, column2 = value2, ...
WHERE condition;
```

* What if we want to update one field with the values in another field that we have updated [Order of update is very important here]
  
  * In such case you need to use multiple Update

#### Delete

The `DELETE` statement in SQL is used to **remove rows** from a table. You can delete specific rows by specifying a condition with the `WHERE` clause, or delete all rows if the condition is omitted

```sql
DELETE FROM table_name
WHERE condition;
```
