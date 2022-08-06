## SQRT / AVG / COUNT / SUM / MAX / MIN / LENGTH

`SQRT` retrieves the square root of the values of a column.

```sql
SELECT SQRT(column1) AS column1_sqrt FROM table1
```

`AVG` returns the average of a column values (Returns a single value, not a column)

```sql
SELECT AVG(column1) AS column1_avg FROM table1
```

`COUNT` returns the total of a column values (Returns a single value, not a column)

```sql
SELECT COUNT(column1) AS column1_count FROM table1
```

`SUM` returns the sum of a column values (Returns a single value, not a column)

```sql
SELECT SUM(column1) AS column1_sum FROM table1
```

`MAX` and `MIN` returns the maximum and minimum value of a column values (Returns a single value, not a column)

```sql
SELECT MIN(column1) AS column1_min FROM table1
```

`LENGTH` retrieves the length of the values of a column.

```sql
SELECT LENGTH(column2) AS column2_len FROM table1
```

It can be filtered wich values of the column use these previous functions:

```sql
SELECT AVG(DISTINCT column2) AS column2_avg FROM table1
```

```sql
SELECT COUNT(column1) AS column1_count FROM table1 WHERE column2 != 'Valencia' 
```

## Subqueries

In a condition inside a query, we can create a query.

In this example, is going to be retrieved only the values of column1 which are greater than the average of all the values of column1.

```sql
SELECT column1 FROM table1 WHERE column1 > (SELECT AVG(column1) FROM table1)
```

## LIKE

In WHERE, used to search for a pattern. The next wildcards are used:

- `_` to represent a single character.
- `%` to represent zero, one or multiple characters.
- `[]` to represent a character which can be any that we put inside the brackets.
- `[^]` to represent a character which can be any if it is not inside the brackets.
- `[-]` to represent a character which can be any inside the range specified inside the brackets.

If a wildcard is part of the string, put a `\` before it.

To use `[]`, the synataxis is different and may differ

```sql
SELECT column1 FROM table1 WHERE column1 LIKE '1%'
```

```sql
SELECT column2 FROM table1 WHERE column2 LIKE '%a_'
```

## JOIN 

Create a temporary table combining two or more tables. In WHERE we put the condition that the value of a column of one table is equal to the value of the column of other table, normally primary and foreign key.

In this case, *table1.column1* is a primary key and *table2.column2* is the foreign key from table1.

```sql
SELECT columns_to_show FROM table1, table2 WHERE table1.column1 = table2.column2
```

Can add nicknames to the names of the tables to refer to them in the query

```sql
SELECT columns_to_show FROM table1 AS t1, table2 AS t2 WHERE t1.column1 = t2.column2
```

Types of JOIN:

- `INNER JOIN`: Returns records matching the join condition.
- `LEFT OUTER JOIN`: Returns all rows from the left table, even if no match with the right table.
- `RIGHT OUTER JOIN`: Returns all rows from the right table, even if no match with the left table.

```sql
SELECT columns_to_show FROM table1 INNER JOIN table2 ON table1.column1 = table2.column2
```

```sql
SELECT columns_to_show FROM table1 LEFT OUTER JOIN table2 ON table1.column1 = table2.column2
```

```sql
SELECT columns_to_show FROM table1 RIGHT OUTER JOIN table2 ON table1.column1 = table2.column2
```

Can also use a `WHERE` to return only values of one of the values with no match:

```sql
SELECT columns_to_show FROM table1 LEFT OUTER JOIN table2 ON table1.column1 = table2.column2 WHERE table2_column IS NULL
```

Also, to show all records of both tables, doesn't matter if there is a match, using `UNION` with both `LEFT OUTER UNION` and `RIGHT OUTER UNION`:

```sql
SELECT columns_to_show FROM table1 LEFT OUTER JOIN table2 ON table1.column1 = table2.column2
UNION
SELECT columns_to_show FROM table1 RIGHT OUTER JOIN table2 ON table1.column1 = table2.column2
```

## UNION

Combine data from various SELECT statements, which must have the same number of column in the same order and with the same datatypes. The columns names, may be different, and will take the names of the first SELECT. Two types:

- `UNION`: Combines datasets in a single one, removing duplicates.
- `UNION ALL`: Combines datasets in a single one, without removing duplicates.

```sql
SELECT column1 FROM table1 UNION SELECT column1 FROM table2
```

If the columns don't match through all queries, we can put in a SELECT a column with all NULL or other values, like a number, a string using CONCAT or an operating with other column.

```sql
SELECT column1, column2, column3 FROM table1
UNION
SELECT column1 + 5, NULL, CONCAT('Column', table1.column3) FROM table2
```

## INSERT INTO

To introduce new records in a table. Values inserted must be in the same order than the table columns. Must be at least defined values for columns which have not default value and do not support a NULL value.

```SQL
INSERT INTO table1 VALUES (value1, value2, value3...)
```

It can also specify for each value in which column it is being added. It must be defined at least values for columns which have not default value and do not support a NULL value.

```sql
INSERT INTO table1 (column1, column3, column4) VALUES (value1, value3, value4)
```

## UPDATE

To change values of existing records. With WHERE can be defined which values are changed, and if there is no WHERE clause, all records all modified.

```sql
UPDATE table1 SET column1 = value1 WHERE column3 = 'Valencia'
```

```sql
UPDATE table1 SET column1 = value1, column2 = value2 WHERE column1 > 5
```

## DELETE

To remove records from a table which fulfill the condition under WHERE, and if there is no WHERE clause, all records all deleted, ending with an empty table. The removal is permanent.

```sql
DELETE FROM table1 WHERE column2 LIKE 'A%'
```

## ALTER TABLE

To add, delete or modify columns in a table. Also to add or drop constraints.

### Add a new column

```sql
ALTER TABLE table1 ADD column_name5 int DEFAULT 0 NO NULL
```

### Remove a column

```sql
ALTER TABLE table1 DROP COLUMN column_name5
```

### Remove a table

```sql
DROP TABLE table1
```

### Rename a column

```sql
ALTER TABLE table1 RENAME column_name4 TO column_name4_newname
```

### Rename a table

```sql
RENAME TABLE table1 TO table1_newname
```

## VARIABLES

Declare a variable

```sql
DECLARE @variableName INT;
DECLARE @variableName INT;
```

Set a value to the variable

```sql
SET @variableName = 0;
```

## IF 

Execute one or other statement depending on a condition

```sql
IF expression
	block1
    ELSE 
    block
```









































