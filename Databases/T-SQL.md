# TRANSACT SQL

## `SELECT`

[SELECT (Transact-SQL) - SQL Server | Microsoft Docs](https://docs.microsoft.com/es-es/sql/t-sql/queries/select-transact-sql?view=sql-server-ver16)

Show rows

```sql
SELECT column1, column2 FROM name_table
```

```sql
SELECT * FROM name_table -- returns all columns
```

```sql
SELECT column1*column2 as temp_name, column2 FROM name_table -- returns a custom column
```

### `DISTINCT`

Show distinct rows

```SQL
SELECT DISTINCT * FROM name_table
```

### `ORDER BY`

Order rows

[Cláusula ORDER BY (Transact-SQL) - SQL Server | Microsoft Docs](https://docs.microsoft.com/es-es/sql/t-sql/queries/select-order-by-clause-transact-sql?view=sql-server-ver16)

Descending order by default

```sql
SELECT * FROM name_table
ORDER BY column1 DESC
```

```sql
SELECT * FROM name_table
ORDER BY column1 DESC, column2 ASC
```

To order randomly:

```sql
SELECT * FROM name_table
ORDER BY NEWID()
```

### `TOP`

Show top rows, used with ORDER BY

```sql
SELECT TOP n * FROM name_table -- show top n rows
```

```SQL
SELECT TOP n PERCENT * FROM name_table -- show top n% rows
```

```SQL
SELECT TOP n WITH TIES * FROM name_table -- return rows tied in the order by with the nth column
ORDER BY column1
```

### `OFFSET` - `FETCH`

Show top rows ignoring n first

```sql
SELECT * FROM name_table
ORDER BY column1
OFFSET n ROWS -- not showing n top prows
FETCH NEXT N ROWS ONLY -- show n rows only
```

## `WHERE`

[WHERE (Transact-SQL) - SQL Server | Microsoft Docs](https://docs.microsoft.com/es-es/sql/t-sql/queries/where-transact-sql?view=sql-server-ver16)

Filter rows, can be used several conditions with logical operators

```SQL
SELECT * FROM name_table
WHERE column1 NOT IN {1,3,4,5} OR (column2 BETWEEN n1 AND n2 and column1 <> 1)
```

## `GROUP BY`

[GROUP BY (Transact-SQL) - SQL Server | Microsoft Docs](https://docs.microsoft.com/en-us/sql/t-sql/queries/select-group-by-transact-sql?view=sql-server-ver16)

Groups rows same values of the defined columns.

Used with aggregate functions

```sql
SELECT column1, column1 + column2, sum(column3), count(column3)
FROM name_table
GROUP BY column1, column1 + column2
```

Using count(*) or the equivalent count(1) includes null values but puting count(column) instead does not count rows for which the column is null.

To generate grand totals and sub totals, the next statements must be used.

### `ROLLUP`

Creates group from the first to the last column and then each time removing the last column until there is only the first column. 

Used for hierarchical data mainly. 

All columns used in the rollup must be included in the select.

For four columns:

- col1, col2, col3, col4 
- col1, col2, col3
- col1, col2
- col1
- Grand total

```sql
SELECT column1, column2, column3, sum(column4)
FROM name_table
GROUP BY ROLLUP(column1, column2, column3)
```

It can also be done a partial ROLLUP.

```sql
SELECT column1, column2, column3 sum(column4)
FROM name_table
GROUP BY column1
ROLLUP(column2, column3)
```

In this case, the combinations would be:

- col1, col2, col3, col4 
- col1, col2, col3
- col1, col2
- col1

So the same, except the grand total, because we are forcing col1 to be always.

### `CUBE`

Group for each posible combination of the columns. 

All columns used in the cube must be included in the select.

For three columns:

- col1, col2, col3
- col1, col2
- col2, col3
- col1, col3
- col1
- col2
- col3
- Grand total

```sql
SELECT column1, column2, column3, sum(column4)
FROM name_table
GROUP BY CUBE(column1, column2, column3)
```

### COMPOSITE COLUMNS

Inside the lists of columns of ROLLUP or CUBE, columns can be comosited working as a whole.

So, combinations for a CUBE with three columns, compositing column1 and column2 ( `GROUP BY ROLLUP((column1, column2), column3)`):

- column1, column2, column3
- column1, column2
- Grand total

And if the same for CUBE ( `GROUP BY CUBE((column1, column2), column3)`):

- col1, col2, col3
- col1, col2
- col3
- Grand total

### `GROUPING SET`

It is specified which are the only exact groupings done.

```sql
SELECT column1, column2, column3, sum(column4)
FROM name_table
GROUP BY GROUPING SET((column1, column3), column2, ())
```

In the case above, there will be three different aggrupations:

- column1, column3
- column3
- Grand total

### `GROUPING_ID`

[GROUPING_ID (Transact-SQL) - SQL Server | Microsoft Docs](https://docs.microsoft.com/en-us/sql/t-sql/functions/grouping-id-transact-sql?view=sql-server-ver16)

**Indicates the combination of columns to group that is being done in each row.**

- Used for `ROLLUP` and `CUBE`

- The number returned is the addition of 2^(position - 1) for the active column. The first column in the group has the position 1 and so on

> For this case, if it is being grouped by column1 and column3, it would be 2^(1 - 1) + 2^(3 - 1) = 5. Or for the grand total, it would be 0.

```sql
SELECT column1, column2, column3 sum(column4), GROUPING_ID(column1, column2, column3)
FROM name_table
GROUP BY CUBE(column1, column2, column3)
```

### `GROUPING`

**Column which indicates whether a column is being part of the group by (1) or not (0) in that row.**

```sql
SELECT column1, column2, column3 sum(column4), GROUPING(column1)
FROM name_table
GROUP BY CUBE(column1, column2, column3)
```

## ALIAS

**Define a temporary name for a column or table**

 Uses `AS` which can be ommited

If a name is a reserved word, it can be put between `""`.

### COLUMN

```sql
SELECT <name_column> as <alias_column>, <name_column> as "<alias_column>" FROM <name_table>
```

### TABLE

```sql
SELECT * FROM <name_table> AS <alias_table>
```

## CONTROL OF FLOW

[Control-of-Flow - SQL Server | Microsoft Docs](https://docs.microsoft.com/en-us/sql/t-sql/language-elements/control-of-flow?view=sql-server-ver16)

### `IF`

[IF...ELSE (Transact-SQL) - SQL Server | Microsoft Docs](https://docs.microsoft.com/es-es/sql/t-sql/language-elements/if-else-transact-sql?view=sql-server-ver16)

**Executes one or other statement depending on a expression.**

ELSE is not mandatory

```sql
IF Boolean_expression   
     { sql_statement | statement_block }    
[ ELSE   
     { sql_statement | statement_block } ]   
```

### `WHILE`

[WHILE (Transact-SQL) - SQL Server | Microsoft Docs](https://docs.microsoft.com/es-es/sql/t-sql/language-elements/while-transact-sql?view=sql-server-ver16)

**Executes one or other statement depending on a expression.**

```sql
WHILE Boolean_expression   
     { sql_statement | statement_block | BREAK | CONTINUE }  
```

- `BREAK`: Exits the whole loop
- `CONTINUE`: Goes to the next iteration, ignoring the rest of the code in the iteration

### `TRY` - `CATCH`

[TRY...CATCH (Transact-SQL) - SQL Server | Microsoft Docs](https://docs.microsoft.com/es-es/sql/t-sql/language-elements/try-catch-transact-sql?view=sql-server-ver16)

**Does `TRY`, but if it fails, does `CATCH` instead.**

```SQL
BEGIN TRY  
     { sql_statement | statement_block }  
END TRY  
BEGIN CATCH  
     [ { sql_statement | statement_block } ]  
END CATCH  
[ ; ]
```

## `BULK INSERT`

[BULK INSERT (Transact-SQL) - SQL Server | Microsoft Docs](https://docs.microsoft.com/en-us/sql/t-sql/statements/bulk-insert-transact-sql?view=sql-server-ver16)

**Loads a data file into a table or view**

- The table or view must exist with the same layout than the file

```sql
BULK INSERT tabla
FROM 'ruta\archivo.csv'
WITH
(
    FIRSTROW = 2,
    FIELDTERMINATOR = ',',
    ROWTERMINATOR = '\n',
	CODEPAGE = '65001', -- for UTF-8
)
```

## `CREATE`

### `CREATE DATABASE`

[CREATE DATABASE (Transact-SQL) - SQL Server | Microsoft Docs](https://docs.microsoft.com/en-us/sql/t-sql/statements/create-database-transact-sql?view=sql-server-ver16&tabs=sqlpool#select-a-product)

**Creates a database.**

```sql
CREATE DATABASE database_name
```

### `CREATE SCHEMA`

**Creates a schema.**

[CREATE SCHEMA (Transact-SQL) - SQL Server | Microsoft Docs](https://docs.microsoft.com/en-us/sql/t-sql/statements/create-schema-transact-sql?view=sql-server-ver16)

```sql
CREATE SCHEMA name_schema
```

### `CREATE TABLE`

**Creates a table.**

It must be defined:

- Table name
- Columns name
- Datatype of each column
- Optional constraints to each column

Some of the datatypes that can be used:

- `INT`: Can be unisgned.
- `FLOAT(M, D)`: Must be defined the display length (M) and the number of decimals(D).
- `DATE`: YYYY-MM-DD format.
- `DATETIME `: YYYY-MM-DD HH:MM:SS format.
- `TIME`: HH:MM:SS format.
- `VARCHAR(M)`: String. Must be defined the length (M).
- `CHAR(M)`: String. Must be defined the length (M). Regardless the actual length, uses all the memory. Better if all values will have the same fixed length.
- `BLOB`: For storing large amount of binary data, like images.

It can also be defined constraints to the columns:

- `PRIMARY KEY`: To define a column as the primary key.

- `NOT NULl`: To define that a column can't have NULL values.
- `UNIQUE`: To define that a column can't have repeated values.
- `DEFAULT`: Define default value in a column if no other value is passed.
- `CHECK`: Define range of values which can be placed in a column. Can be named.
- `AUTO INCREMENT`: Allows to put as default value of a new element of a column an increment of the last value in the column. Useful for the primary key columns.

```sql
CREATE TABLE name_table
(
column_name1 int IDENTITY(1,1) PRIMARY KEY, -- the column is autoincremental, adding 1 starting from 1
column_name2 varchar(30) NOT NULL,
column_name3 datetime UNIQUE,
column_name4 int DEFAULT 0 UNIQUE,
CHECK(column_name1 >= 0)
CONSTRAINT check_name CHECK(column_name2 IN ('Valencia', 'Barcelona', 'Madrid'))
PRIMARY KEY(column_name1)
)
```

## `DROP`

[DROP DATABASE (Transact-SQL) - SQL Server | Microsoft Docs](https://docs.microsoft.com/en-us/sql/t-sql/statements/drop-database-transact-sql?view=sql-server-ver16)

[DROP SCHEMA (Transact-SQL) - SQL Server | Microsoft Docs](https://docs.microsoft.com/en-us/sql/t-sql/statements/drop-schema-transact-sql?view=sql-server-ver16)

[DROP TABLE (Transact-SQL) - SQL Server | Microsoft Docs](https://docs.microsoft.com/en-us/sql/t-sql/statements/drop-table-transact-sql?view=sql-server-ver16)

**Deletes either a database, a schema, or a table.**

Optional `IF EXISTS` to avoid errors if there is no table.

```SQL
DROP DATABASE IF EXISTS <name_database>
```

```SQL
DROP SCHEMA <name_schema>
```

```sql
DROP DATABASE <name_table1>, <name_table2>
```

## `TRUNCATE`

[TRUNCATE TABLE (Transact-SQL) - SQL Server | Microsoft Docs](https://docs.microsoft.com/en-us/sql/t-sql/statements/truncate-table-transact-sql?view=sql-server-ver16)

**Removes all rows from a table**

- Similar to `DELETE` without `WHERE` but faster.

```sql
TRUNCATE <name_table>
```

## `ALTER`

[ALTER TABLE (Transact-SQL) - SQL Server | Microsoft Docs](https://docs.microsoft.com/en-us/sql/t-sql/statements/alter-table-transact-sql?view=sql-server-ver16)

**Adds, delete or modify columns in an existing table. It can also add or drop constraints.**

```sql
ALTER TABLE <name_table>
ADD <column_name> <data_type>
```

```sql
ALTER TABLE <name_table>
DROP COLUMN <column_name>
```

```sql
ALTER TABLE <name_table>
ALTER COLUMN <column_name> <data_type>
```

```sql
ALTER TABLE <name_table>
ADD CONSTRAINT <constraint_name> PRIMARY KEY (<column1>, <column2>)
```

## GENERAL DB STATEMENTS

### `USE`

Defines the database which will be referred by the following statements

```SQL
USE database_name
```

### TRANSACTIONS

[Transactions (Azure Synapse Analytics) - SQL Server | Microsoft Docs](https://docs.microsoft.com/en-us/sql/t-sql/language-elements/transactions-sql-data-warehouse?view=aps-pdw-2016-au7&viewFallbackFrom=sql-server-ver16)

### `GO`

### `EXECUTE`

## `SHOW`

### SHOW DATABASES

[View list of databases on SQL Server - SQL Server | Microsoft Docs](https://docs.microsoft.com/en-us/sql/relational-databases/databases/view-a-list-of-databases-on-an-instance-of-sql-server?view=sql-server-ver16)

```sql
SELECT name, database_id, create_date  
FROM sys.databases 
```

### SHOW TABLES AND VIEWS

[TABLES (Transact-SQL) - SQL Server | Microsoft Docs](https://docs.microsoft.com/es-es/sql/relational-databases/system-information-schema-views/tables-transact-sql?view=sql-server-ver16)

Table with all the tables and views of a database

```sql
SELECT * FROM INFORMATION_SCHEMA.TABLES
```

### SHOW COLUMNS

[COLUMNS (Transact-SQL) - SQL Server | Microsoft Docs](https://docs.microsoft.com/es-es/sql/relational-databases/system-information-schema-views/columns-transact-sql?view=sql-server-ver16)

Table with all the columns of a database

```SQL
SELECT * FROM INFORMATION_SCHEMA.COLUMNS
```

## LOGICAL FUNCTIONS AND OPERATORS

### `IIF`

Returns the true or false value depending on a boolean expression

```sql
IIF(4 > 2, 1, 0)
```

### `CHOOSE`

From a list, return the nth value defined in the first argument. If there is no such value, returns a null

```sql
SELECT CHOOSE ( 3, 'Monday', 'Tuesday', 'Wednesday')
```

### `IN`

[IN (Transact-SQL) - SQL Server | Microsoft Docs](https://docs.microsoft.com/en-us/sql/t-sql/language-elements/in-transact-sql?view=sql-server-ver16)

Returns true if a value is inside a specified list.

The list can be a subquery as well.

In the following queries, is shown only rows whose column1 either A, G or N:

```sql
SELECT * FROM name_table_1
WHERE column1 IN ('A', 'G', 'N')
```

### `BETWEEN - AND`

[BETWEEN (Transact-SQL) - SQL Server | Microsoft Docs](https://docs.microsoft.com/en-us/sql/t-sql/language-elements/between-transact-sql?view=sql-server-ver16)

Returns true if a value is inside a specified range.

In the following queries, is shown only rows whose column1 value is between 1 and 10, both included"

```sql
SELECT * FROM name_table_1
WHERE column1 BETWEEN 1 AND 10
```

### `ANY`

[SOME | ANY (Transact-SQL) - SQL Server | Microsoft Docs](https://docs.microsoft.com/en-us/sql/t-sql/language-elements/some-any-transact-sql?view=sql-server-ver16)

Returns true if the comparation is fulfilled for at least one of the values passed. The values passed can be as a static list, or a SELECT statement.

In the query below, is shown only rows from name_table_1 whose column1 is different to any value of the column2 from name_table_2:

```sql
SELECT * FROM name_table_1
WHERE column1 < >  
     ANY (SELECT column2 FROM name_table_2)   
```

Can be used `SOME` instead of `ANY` indistinctly.

### `ALL`

[ALL (Transact-SQL) - SQL Server | Microsoft Docs](https://docs.microsoft.com/en-us/sql/t-sql/language-elements/all-transact-sql?view=sql-server-ver16)

Returns true if the comparation is fulfilled for all the values passed. The values passed can be as a static list, or a SELECT statement.

In the query below, is shown only rows from name_table_1 whose column1 is lower to all the values of the column2 from name_table_2:

```sql
SELECT * FROM name_table_1
WHERE column1 <  
     ALL (SELECT column2 FROM name_table_2)
```

## Expression functions

### `ISNULL`

[ISNULL (Transact-SQL) - SQL Server | Microsoft Docs](https://docs.microsoft.com/es-es/sql/t-sql/functions/isnull-transact-sql?view=sql-server-ver16)

Returns an alternative value if null

```sql
ISNULL ( check_expression , replacement_value )
```

### `COALESCE`

[COALESCE (Transact-SQL) - SQL Server | Microsoft Docs](https://docs.microsoft.com/es-es/sql/t-sql/language-elements/coalesce-transact-sql?view=sql-server-ver16)

Returns the first non-null argument.

```SQL
COALESCE ( expression [ ,...n ] )
```

### `GREATEST` - `LEAST`

[GREATEST (Transact-SQL) - SQL Server | Microsoft Docs](https://docs.microsoft.com/en-us/sql/t-sql/functions/logical-functions-greatest-transact-sql?view=azure-sqldw-latest)

[LEAST (Transact-SQL) - SQL Server | Microsoft Docs](https://docs.microsoft.com/en-us/sql/t-sql/functions/logical-functions-least-transact-sql?view=azure-sqldw-latest)

Returns either the higher or lower expressions from all passed in the function.

All expressions must be of comparable data types.

If different data types passed, converts all to the same using the [Data type precedence (Transact-SQL) - SQL Server | Microsoft Docs](https://docs.microsoft.com/en-us/sql/t-sql/data-types/data-type-precedence-transact-sql?view=azure-sqldw-latest)

```sql
GREATEST ( expression1 [ ,...expressionN ] ) 
LEAST ( expression1 [ ,...expressionN ] ) 
```

### `PIVOT`

[Using PIVOT and UNPIVOT - SQL Server | Microsoft Docs](https://docs.microsoft.com/en-us/sql/t-sql/queries/from-using-pivot-and-unpivot?view=sql-server-ver16)

Pivots a table, adding all different values of a column as rows.

From the table, we must define:

- Pivot columns: Whose different values will be converted to columns
- Aggregate column(s): Column will have an aggregate function applied
- Values columns(s): Columns which will stay the same

Supposing we start with a table which has PurchaseOrderID, EmployeeID and VendorID.

We pivot the table:

```sql
SELECT *
FROM
-- query with the original table
(SELECT PurchaseOrderID, EmployeeID, VendorID FROM name_table)
PIVOT  
(  
COUNT (PurchaseOrderID)  -- aggregation applied for each column and row
FOR EmployeeID -- column to pivot
IN  
( select distinct EmployeeID FROM name_table)  -- obtain different values of the column to pivot
) AS pvt  -- alias for the new table
ORDER BY pvt.VendorID -- orders the new table by a value column
```

The result would we something like:

| VendorID | 234  | 453  | 533  |
| -------- | ---- | ---- | ---- |
| 1492     | 2    | 5    | 6    |
| 1494     | 8    | 5    | 4    |
| 1496     | 4    | 6    | 3    |
| 1498     | 3    | 5    | 2    |
| 1500     | 2    | 4    | 1    |

### `UNPIVOT`

[Using PIVOT and UNPIVOT - SQL Server | Microsoft Docs](https://docs.microsoft.com/en-us/sql/t-sql/queries/from-using-pivot-and-unpivot?view=sql-server-ver16)

Unpivots a pivoted table.

Supposing we have the table resulted when pivoting stored named pvt:

```sql
 SELECT * 
FROM 
	-- query with original pivoted table
   (SELECT * FROM pvt)
UNPIVOT  
	(Orders -- column to hold aggregated values
    FOR Employee -- column which will store values of pivoted columns
    IN (col1, col2, col3, col4) -- pivoted columns names 
)AS unpvt -- alias for new table
```

We get a table with three columns: VendorID, Employee and Orders.

To get the number of columns, we could obtain all the column names of the table using the  system table sys.columns and substract the value columns: [[sql server - Passing column names dynamically to UNPIVOT - Database Administrators Stack Exchange](https://dba.stackexchange.com/questions/48393/passing-column-names-dynamically-to-unpivot)](https://www.mssqltips.com/sqlservertip/3002/use-sql-servers-unpivot-operator-to-dynamically-normalize-output/)

If PIVOT and then UNPIVOT, the resulting table is not the same than the original, because when using PIVOT an aggregation is done and so info is lost.

## STRING FUNCTIONS

[String Functions (Transact-SQL) - SQL Server | Microsoft Docs](https://docs.microsoft.com/en-us/sql/t-sql/functions/string-functions-transact-sql?view=sql-server-ver16)

### `CONCAT`

Adds strings together

```SQL
SELECT CONCAT(column1, ' - ', column2) FROM name_table
```

### `UPPER`

Converts lowercase characters to uppercase

```sql
SELECT UPPER(column1) FROM name_table
```

### `LOWER`

Converts uppercase characters to lowercase

```sql
SELECT LOWER(column1) FROM name_table
```

### `RIGHT`

Returns n last characters of the expression

```sql
RIGHT(expression,n)
```

### `LEFT`

Returns n first characters of the expression

```sql
LEFT(expression,n)
```

### `SUBSTRING`

Returns n characters of the string starting from certain position

```sql
SUBSTRING ( string ,start , length ) 
```

### `TRIM`

[TRIM (Transact-SQL) - SQL Server | Microsoft Docs](https://docs.microsoft.com/en-us/sql/t-sql/functions/trim-transact-sql?view=sql-server-ver16)

Removes specified characters from the start or end of an string. If no character is specified, it is by default the whitespace.

```SQL
TRIM ( '.,! ' FROM '!!Hola, que esta pasando!... ') -- Hola, que esta pasando
```

### `LTRIM` - `RTRIM`

[LTRIM (Transact-SQL) - SQL Server | Microsoft Docs](https://docs.microsoft.com/en-us/sql/t-sql/functions/ltrim-transact-sql?view=sql-server-ver16)

[RTRIM (Transact-SQL) - SQL Server | Microsoft Docs](https://docs.microsoft.com/en-us/sql/t-sql/functions/rtrim-transact-sql?view=sql-server-ver16)

Removes either leading or ending white spaces.

```sql
LTRIM('    Hola') -- Hola
RTRIM('Hola    ') -- Hola
```



## Date & Time Functions

[Date and Time Data Types and Functions - SQL Server (Transact-SQL) | Microsoft Docs](https://docs.microsoft.com/en-us/sql/t-sql/functions/date-and-time-data-types-and-functions-transact-sql?view=sql-server-ver16)

### `DATEPART`

Returns specified info about a datetime as integer

Equivalent to specific functions like YEAR(), MONTH(), DAY().

```SQL
DATEPART ( datepart , datetime_expression )
```

Most common values for datepart:

| *datepart*    | Abbr.            |
| :------------ | :--------------- |
| **year**      | **yy**, **yyyy** |
| **quarter**   | **qq**, **q**    |
| **month**     | **mm**, **m**    |
| **dayofyear** | **dy**, **y**    |
| **day**       | **dd**, **d**    |
| **week**      | **wk**, **ww**   |
| **weekday**   | **dw**           |
| **hour**      | **hh**           |
| **minute**    | **mi, n**        |
| **second**    | **ss**, **s**    |

### `YEAR`

Returns the year of the date as an integer

```sql
YEAR(date_expression)
```

### `MONTH`

Returns the month of the date as an integer

```sql
MONTH(date_expression)
```

### `DAY`

Returns the month day of the date as an integer

```sql
DAY(date_expression)
```

### `DATEADD`

Adds or substract a certain times an date or time element to a datetime

```sql
DATEADD (datepart , n , date_expression )
```

Most common values for datepart:

| *datepart*    | Abbr.            |
| :------------ | :--------------- |
| **year**      | **yy**, **yyyy** |
| **quarter**   | **qq**, **q**    |
| **month**     | **mm**, **m**    |
| **dayofyear** | **dy**, **y**    |
| **day**       | **dd**, **d**    |
| **week**      | **wk**, **ww**   |
| **weekday**   | **dw**, **w**    |
| **hour**      | **hh**           |
| **minute**    | **mi**, **n**    |
| **second**    | **ss**, **s**    |

### `DATEDIFF`

## Correlated subqueries

Contains a reference to a table in the outer query. 

The subquery is executed once for each row of the outer query, unlike normal subqueries, which run just once.

Can be used with logical operators, IN, ANY or ALL operators.

Can be used in SELECT, UPDATE or DELTE.

An example to return only the records from the table whose column2 is bigger than the average of column2 with the same column3:

```sql
SELECT column1, column2, column3
 FROM name_table a
 WHERE column2 >
                (SELECT AVG(column2)
                 FROM name_table
                 WHERE column3 =
                        a.column3);
```

Other example to update column2 from the table a with column2 from table b for the records which has the same value in column3 than the outer record has in column2:

```sql
UPDATE name_table_1 a
 SET column2 = (SELECT column2
               FROM name_table_2 b
               WHERE a.column2 =
                     b.column3);
```

### `EXIST`

To check whether there is a record in the subquery.

Can also be used NON EXISTS to return true if no records in the subquery.

For example, return only the records from table a whose a.column1 value is in any record of the table b.

```sql
SELECT a.* FROM name_table_1 a
WHERE EXISTS
	(SELECT b.column1 FROM name_table_2 b
     WHERE b.column1 = a.column1)
```

## Window Functions

[Cláusula OVER (Transact-SQL) - SQL Server | Microsoft Docs](https://docs.microsoft.com/es-es/sql/t-sql/queries/select-over-clause-transact-sql?view=sql-server-ver16)

[SQL Window Functions Cheat Sheet | LearnSQL.com](https://learnsql.com/blog/sql-window-functions-cheat-sheet/)

- A column in which an analytical function is applied to all the rows of the table
- Keeps the same number of rows than the original table, unlike GROUP BY
- For the calculation in each row, it is only used a selection of rows from the table

### `OVER`

To define that is going to be applied a window function in the column

```sql
SELECT
column1,
SUM(column2) OVER() as window_column
```

In this case, for every row window_column is the sum of the column2 of the whole table, since no filtering has been defined.

### `PARTITION BY`

We can filter the sub-table for each so it only uses rows with the same value in certain column(s)

```SQL
SELECT
column1,
SUM(column2) OVER(PARTITION BY column1, column3) as window_column
```

In this case, window_column is the sum of the column2 but only the rows which have the same value of column1 and column3.

### `ORDER BY`

We can order the sub-table used in each row by certain column(s)

```SQL
SELECT
column1,
RANK() OVER (ORDER BY column1 ASC, column3 DESC) as window_column
```

In this case, window_column is the ranking of all the rows depending on the ascending order of column1 and then the descending order of column3.

There is one ranking for all the table, but it can be done a ranking for each group of values using PARTITION BY as well:

```SQL
SELECT
column1,
RANK() OVER (PARTITION BY column2 
             ORDER BY column1 ASC, column3 DESC) as window_column
```

Now, there is a ranking for each value of column2.

### `ROW` - `RANGE`

From the sub-table, we can additionally filter to keep only rows which are under or/and above the current row.

- If ORDER BY is not used, the effect will be random

- For each above and under rows, we can keep all the rows, a n number of rows or the rows which fulfill a logical conditional with respect to the current row using the following syntaxis:

	`ROWS|RANGE BETWEEN (choose rows above) AND (choose rows under)`

	To use with either `ROWS` or `RANGE`

	- `UNBOUNDED PRECEDING`: Keeps all rows over the current (for above rows)
	- `UNBOUNDED FOLLOWING`: Keeps all rows under the current (for under rows)
	- `CURRENT ROW`: Only the current row

	To use with `ROWS`:

	- `n PRECEDING`: Keeps n rows above the current (for above rows)
	- `n FOLLOWING`: Keeps n rows under the current (for under rows)

	To use with `RANGE`:

	- `n PRECEDING`: Keeps rows whose value of the order by column is not more than n bigger compared to the value of the column of the current row (for above rows)
	- `n FOLLOWING`: Keeps rows whose value of the order by column is not more than n smaller compared to the value of the column of the current row (for under rows)

- If one of the bounds is `CURRENT ROW`, it can be omitted. So, `ROWS n PRECEDING` is the same than `ROWS BETWEEN n PRECEDING AND CURRENT ROW`
- If not defined, default is:
	- If there is `ORDER BY`: `RANGE UNBOUNDED PRECEDING AND CURRENT ROW`
	- If there is not `ORDER BY`: `RANGE UNBOUNDED PRECEDING AND UNBOUNDED FOLLOWING`

```sql
SELECT
column1,
SUM(column2) OVER
			 (ORDER BY column3 DESC
             ROWS BETWEEN UNBOUNDED PRECEDING AND 3 FOLLOWING
             ) as window_column
```

From the sub-table we only keep the current row, those which are ordered before and the 3 rows ordered after.

### Named Window Definition

[WINDOW (Transact-SQL) - SQL Server | Microsoft Docs](https://docs.microsoft.com/en-us/sql/t-sql/queries/select-window-transact-sql?view=sql-server-ver16)

The window functions can be defined with another syntaxis. These can be defined at the end of the query a name, and when creating the columns, instead of defining the whole window function there, we can just reference the one created with the name.

The main advantage is to define it once and use it in several columns.

For performance, is it better to use the same window function twice, than two different window functions. For that, using this way, the syntax is more clear.

```sql
SELECT column1,
    ,SUM(column2) OVER win AS col_a
    ,MIN(column3) OVER win AS "Min"
FROM name_table
WINDOW win AS (PARTITION BY column2 ORDER BY column3)
```

If used `WHERE`, `GROUP BY` or `HAVING` are used, they must go before the window definition, but `ORDER BY` goes after.

### Functions

It can be used regular analytical expressions like `SUM`, `MAX` or `COUNT`, but there are also specific functions:

- `ROW_NUMBER()`: Ranks the sub-table in the format: 1, 2, 3, 4, 5...
- `RANK()`: Ranks the sub-table in the format: 1, 2, 2, 4, 5...
- `DENSE_RANK()`: Ranks the sub-table in the format: 1, 2, 2, 3, 4...
- `LAG(exp[, n] [,def])`: Expression from a row n positions preceding (1 by default) in the sub-table. If no value, shows def (null by default)
- `LEAD(exp[, n] [,def])`: Expression from a row n positions following (1 by default) in the sub-table. If no value, shows def (null by default)
- `FIRST_VALUE(exp)`: Expression from the first row of the sub-table
- `LAST_VALUE(exp)`: Expression from the last row of the sub-table
- `N_TILE(n)`: Divides the rows of the sub-query in n groups depending on the value of the ORDER BY columns
- `PERCENT_RANK()`: (rank - 1) / (rows in the sub-table - 1)
- [Another function in SQL but not in T-SQL is `NTH-VALUE`, to retrieve an expression of an specified nth row ]

Other functions that can be used are included in the following links:

[Aggregate Functions (Transact-SQL) - SQL Server | Microsoft Docs](https://docs.microsoft.com/en-us/sql/t-sql/functions/aggregate-functions-transact-sql?view=sql-server-ver16)

[Analytic Functions (Transact-SQL) - SQL Server | Microsoft Docs](https://docs.microsoft.com/en-us/sql/t-sql/functions/analytic-functions-transact-sql?view=sql-server-ver16)

[Ranking Functions (Transact-SQL) - SQL Server | Microsoft Docs](https://docs.microsoft.com/en-us/sql/t-sql/functions/ranking-functions-transact-sql?view=sql-server-ver16)

## COMMON TABLE EXPRESSION

[WITH common_table_expression (Transact-SQL) - SQL Server | Microsoft Docs](https://docs.microsoft.com/en-us/sql/t-sql/queries/with-common-table-expression-transact-sql?view=sql-server-ver16)

Temporary result that can be referenced in a query.

Efficient when the result is used more than once because it is stored in the temporary tablespace.

More than one statement can be used to create the query (Used in recursive CTE), but if so, they must be joined by one of `UNION`, `UNION ALL`, `EXCEPT`, `INTERSECT`.

The following query creates two cte named cte_table_1 and cte_table_2 (Columns names of each cte must be defined) and the the cte are joined:

```sql
WITH cte_table_1 (cte_column1) AS
(
	SELECT DISTINCT column1 FROM name_table
),
cte_table_2 (cte_column1, cte_column2) AS
(
	SELECT column2, column3 FROM name_table WHERE column1 = 1
)
SELECT cte_table_1.cte_column1, cte_table_2.cte_column2
FROM cte_table_1
LEFT JOIN 
cte_table_2
ON cte_table_1.cte_column1 = cte_table_2.cte_column3
```

### Recursive CTE

A CTE which has a subquery that references the CTE itself.

Alternative to hierarchical queries.

First, an anchor clause is performed which creates an initial result table.

Then, the recursive table uses the result of the anchor clause to create another result table.

Then, the recursive table will repeat more times using each time the last result table to create a new result table. It stops when the recursive table does not generate any result, so the loop ends.

At the end, all the rows from all the result tables are append in a table, which is the result that the cte gives.

The syntax is:

```sql
WITH <cte_name> (<column_names>)
AS
(
<anchor_clause> -- creates the first rows (can't reference the cte)
UNION ALL
<recursive_clause> -- creates the next rows (must reference the cte)
)
SELECT ...
```

An example where we first get a list of employees from an employees table in the anchor clause. Then, in the recursive clause we reference the CTE (Which has the rows from the anchor clause) and we inner join those rows with the table of employees again. In this join, we add to the cte the rows with the employees whose manager is in the rows obtained in the anchor clause:

```sql
WITH cte_name (emp_id, emp_name, emp_manager_id)
AS
(
	SELECT emp_id, emp_name, emp_manager_id 
    FROM employees 
    WHERE emp_name IN ('Blake', 'Clark')
UNION ALL
	SELECT employees.emp_id, employees.emp_name, employees.emp_manager_id
    FROM cte_name
    INNER JOIN employees
    ON cte_name.emp_id = employees.emp_manager_id -- to obtain employees whose manager_id is in the list of emp_id obtained in the anchor clause
)
SELECT * FROM cte_name -- we obtain a list of employees defined in the anchor clause and also the employees whose manager is one of those
```

It the employees under Blake and Clark are managers as well and so on, we also get these people, with the recursive clause doing one interaction for each hierarchy of people. It will stop when the employees obtained in the last result are managers of no employee (Since their emp_id does not match any value of the emp_manager_id column)

To know in which rank each employee is, we can add a column to the cte which will be 1 in the anchor SELECT and cte_name.n+1 in the recursive SELECT. We then could easily filter this filter to obtain employees of certain ranges.



Another example, where we create a list from 1 to 20. The recursive clause starts to do iterations over the last returned table(First time against the result of the anchor clause and then over the result of the previous recursive clause) until the WHERE clause is satisfied. At the end, all the returned tables are unioned.

```sql
WITH cte_1 (date)
AS
(
	SELECT 5
UNION ALL
	SELECT n+1 FROM cte_1
	WHERE n+1<= 20
)
SELECT * FROM cte_1
```

It is important to assure that it is not created an infinite loop. In the first example, putting in the inner join `cte_name.emp_id = employees.emp_id` would create a loop since every time the recursive clause would return a table which would activate a new iteration on the recursive clause. For the second example, avoiding the `WHERE` would create the loop, since the result of the recursive query would always activate the next iteration of the recursive query.

## Order of Operations

`FROM`

`JOIN`

`WHERE`

`GROUP BY`

`HAVING`

`WINDOW`

`SELECT`

`DISTINCT`

`ORDER BY`

`LIMIT` / `FETCH` / `TOP`

## SQLCMD

### Execute T-SQL files

[Ejecutar archivos de scripts Transact-SQL mediante sqlcmd - SQL Server Management Studio (SSMS) | Microsoft Docs](https://docs.microsoft.com/es-es/sql/ssms/scripting/sqlcmd-run-transact-sql-script-files?view=sql-server-ver16)

```powershell
sqlcmd -S myServer\instanceName -i path\file.sql
```
