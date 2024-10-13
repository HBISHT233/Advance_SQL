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
