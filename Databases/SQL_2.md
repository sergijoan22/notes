### Variables

Variables are local to the batch (Delimited with `GO`)

```sql
DECLARE
@num1 int,
@num2 int = 6;

SET @num1 = 4;s

SELECT @num1 + @num2 AS totalnum;
```


### Conditional loops

```sql
DECLARE 
@i int = 8,
@result nvarchar(20);

IF @i < 5
    SET @result = N'Less than 5'
ELSE IF @i <= 10
    SET @result = N'Between 5 and 10'
ELSE if @i > 10
    SET @result = N'More than 10'
ELSE
    SET @result = N'Unknown';

SELECT @result AS result;
```


```sql
DECLARE @customerID AS INT = 1;
DECLARE @fname AS NVARCHAR(20);
DECLARE @lname AS NVARCHAR(30);

WHILE @customerID <=10
BEGIN    
    SELECT @fname = FirstName, @lname = LastName FROM SalesLT.Customer          WHERE CustomerID = @CustomerID;
    PRINT @fname + N' ' + @lname;
    SET @customerID += 1;
END;
```