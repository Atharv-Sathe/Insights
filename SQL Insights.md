
#### **SQL Query Writing Order (Logical Order)**

When writing a SQL query, the common order is:

1. **SELECT** – Specify the columns to retrieve.
2. **FROM** – Define the table(s) to fetch data from.
3. **JOIN** – Combine data from multiple tables (if needed).
4. **WHERE** – Apply row filtering conditions.
5. **GROUP BY** – Group rows based on specified columns.
6. **HAVING** – Filter grouped results (works on aggregated values).
7. **ORDER BY** – Sort results.
8. **LIMIT/OFFSET** – Limit number of rows returned.

#### **SQL Query Execution Order (Internal Processing)**

The database engine follows this execution sequence:

1. **FROM** – Identify the source table(s).
2. **JOIN** – Apply joins and merge data from different tables.
3. **WHERE** – Filter rows **before aggregation**.
4. **GROUP BY** – Group rows (if applicable).
5. **HAVING** – Filter grouped data **after aggregation**.
6. **SELECT** – Process columns and apply functions.
7. **ORDER BY** – Sort the final result.
8. **LIMIT/OFFSET** – Apply row limiting.

#### **LIMIT vs OFFSET**
**Key Differences**

1. **`LIMIT` (Restricting the Number of Rows)**

- Specifies **how many rows** to return.
- Example:

```mysql
SELECT * FROM Orders LIMIT 5;
```

- This returns **only the first 5 rows**.

7. **`OFFSET` (Skipping Rows)**

- Specifies **how many rows to skip** before selecting the remaining rows.
- Example:

```mysql
SELECT * FROM Orders LIMIT 5 OFFSET 10;
```

- This **skips the first 10 rows** and then returns the **next 5 rows**

#### Date
- To add a day to column of type *date* use -
```mysql
DATE_ADD(date_col, INTERVAL 1 DAY)
```
- If suppose a column has type date, and I need to extract just year and month from it then I can use this - 
```mysql
date_format(trans_date, "%Y-%m") as month
```

#### Miscellaneous
- Aggregate functions like `count` are used in `having` clause instead of `where`. This is because `where` filters the rows before aggregation and therefore any aggregate function is meaningless, while `having` filters rows after aggregation.
- Count only counts the number of rows, it doesn't evaluate a boolean expression i.e count will take both true and false as 1 hence count(c.action = 'confirmed') ends up counting all the rows where c.action is not null. Instead we have used sum(c.action = 'confirmed'), sum considers true as 1 and false as 0. [1934. Confirmation Rate](https://leetcode.com/problems/confirmation-rate/)
- The above three queries are slower not because they are using **derived tables** or **Common Table Expression(CTE)** but they are slower due to the existence of correlated sub queries which cause a time complexity of O(N^2) while the below query uses Window Function and no correlation exists between subquery and parent query. Partition by is similar to group by but it does not collapse the related rows. Row Number() assigns a number to each row within a partition. [1174. Immediate Food Delivery II](https://leetcode.com/problems/immediate-food-delivery-ii/)

