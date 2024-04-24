# Spark

https://spark.apache.org/docs/latest/sql-programming-guide.html

## Spark SQL

https://spark.apache.org/docs/2.2.0/sql-programming-guide.html

Lake API to make a dataframe available for querying it in the Spark Catalog, a simple way is tor create a temporary view.

```python
df.createOrReplaceTempView("products_view") # A view is created from the df dataframe
```
A temporary view is deleted at the end of the session.

Tables can also be created and persisted in the catalog. With this, a database can be created which allows being queried. Tables are metadata structures, with the data stored in the catalog storage location. 

Lake API to save a dataframe as a table:

```python
df.write.format("delta").saveAsTable("salderorders")
```

This created the table stored in the Tables folder of the datalake and in delta format.

![alt text](images/spark_table_datalake.png)

Also, an empty table can be created with `spark.catalog.createTable`. Or an external one with `spark.catalog.createExternalTable`, where the underlying data is in an external location.

Deleting a managed table deletes the underlying tables, but not for external tables.

Spark Catalog supports tables in various formats, being delta the preferred, since is the format used by Delta Lake (Relational data technology) in Spark. Delta table support transactions, versioning or streaming data.







#### Get table info

```python
spark.sql("DESCRIBE EXTENDED salesorders").show(truncate=False)
```

#### Read table

The Spark SQL API can be called using any language to query data in the catalog.

```python
bikes_df = spark.sql("SELECT ProductID, ProductName, ListPrice \
                      FROM [your_lakehouse].salesorders \
                      WHERE Category IN ('Mountain Bikes', 'Road Bikes') LIMIT 1000")
display(bikes_df)
```

This will convert the query to PySpark code (ord the code being used) that retrieves data from the salesorder table and load the result of the query in the df dataframe.

In notebooks, using the magic `%sql` allows to use directly SQL code:

```sql
%%sql

SELECT Category, COUNT(ProductID) AS ProductCount
FROM products
GROUP BY Category
ORDER BY Category
``` 

## pySpark

https://spark.apache.org/docs/latest/api/python/reference/pyspark.sql/dataframe.html

### Reading data
Supposing there is this .csv file in a folder of the lakehouse:

```
ProductID,ProductName,Category,ListPrice
771,"Mountain-100 Silver, 38",Mountain Bikes,3399.9900
772,"Mountain-100 Silver, 42",Mountain Bikes,3399.9900
773,"Mountain-100 Silver, 44",Mountain Bikes,3399.9900
```

Read the file in a notebook:

```python
%%pyspark
df = spark.read.load('folder/file.csv',
    format='csv',
    header=True
)
display(df.limit(10)) # display is like print but for Spark dataframes
```

The output is a 10-rows table with a header.

`%%pyspark` is a **magic**, to define the language used in the cell. It overrides the default notebook language. `%%spark` to use Scala.

And to do the same with a script and supposing there is no header row so the schema must be defined:

```python
from pyspark.sql.types import * # to use StructType
from pyspark.sql.functions import *

productSchema = StructType([
    StructField("ProductID", IntegerType()),
    StructField("ProductName", StringType()),
    StructField("Category", StringType()),
    StructField("ListPrice", FloatType())
    ])

df = spark.read.load('Files/data/product-data.csv',
    format='csv',
    schema=productSchema,
    header=False)
display(df.limit(10)) 
```

An explicit schema should be passed to improve perfomance.

It can be read data saved in partitioned files (If saved that way):

```python
road_bikes_df = spark.read.parquet('Files/bike_data/Category=Road Bikes')
display(road_bikes_df.limit(5))
```

That reads only the file with the Road Bikes as the Category Field. Partitioning columns specified in the fle path are nos included in the dataframe result.


Lake API to read several files and append them, a wildcard can be used:

```python
df = spark.read.load('folder/*.csv', format='csv', header=True) # supossing several files in folder with same struc.
```

A wildcard can also be used when using partitioned data files:

```python
orders_2021_df = spark.read.format("parquet").load("Files/partitioned_data/Year=2021/Month=*")
display(orders_2021_df)
```

As the partition columns, Month and Year are not included in orders_2021_df.

#### Read data in delta format using Spark SQL

```python
 # Load table into df
 delta_table_name = "yellow_taxi"
 table_df = spark.read.format("delta").table(delta_table_name)
    
 # Create temp SQL table
 table_df.createOrReplaceTempView("yellow_taxi_temp")
    
 # SQL Query
 table_df = spark.sql('SELECT * FROM yellow_taxi_temp')
    
 # Display 10 results
 display(table_df.limit(10))
```


### Modifying data

#### SELECT:

```python
pricelist_df = df.select("ProductID", "ListPrice") # select certain columns (Usually in a new dataframe)
pricelist_df2 = df["ProductID", "ListPrice"] # Other way
```
It can be used to remove columns and to change the order of the selected columns

#### WHERE:

```python
bikes_df = df.where((df["Category"]=="Mountain Bikes") | (df["Category"]=="Road Bikes"))
```

Functions can be chained, to a SELECT and WHERE per exaple:

```python
bikes_pricelist_df = df.select("ProductName", "Category", "ListPrice").where((df["Category"]=="Mountain Bikes") | (df["Category"]=="Road Bikes"))
```

#### GROUP BY:

```python
counts_df = df.select("ProductID", "Category").groupBy("Category").count()
```

Several columns can be aggregated and grouped by at the same time:

```python
dataframe.groupBy("DEPT").agg(max("FEE"), sum("FEE"), 
                              min("FEE"), mean("FEE"),  
                              count("FEE")).show() 
```

#### DISTINCT:

```python
df.distinct()
```

#### COUNT:

```python
print(df.count())
```

#### ORDER BY:

```python
df..orderBy("Category")
```

#### UPSERT

Update or insert if no match:

```python
# Update existing records and insert new ones based on a condition defined by the columns SalesOrderNumber, OrderDate, CustomerName, and Item.

from delta.tables import *
    
deltaTable = DeltaTable.forPath(spark, 'Tables/sales_silver')
    
dfUpdates = df
    
deltaTable.alias('silver') \
  .merge(
    dfUpdates.alias('updates'),
    'silver.SalesOrderNumber = updates.SalesOrderNumber and silver.OrderDate = updates.OrderDate and silver.CustomerName = updates.CustomerName and silver.Item = updates.Item'
  ) \
   .whenMatchedUpdate(set =
    {
          
    }
  ) \
 .whenNotMatchedInsert(values =
    {
      "SalesOrderNumber": "updates.SalesOrderNumber",
      "SalesOrderLineNumber": "updates.SalesOrderLineNumber",
      "OrderDate": "updates.OrderDate",
      "CustomerName": "updates.CustomerName",
      "Email": "updates.Email",
      "Item": "updates.Item",
      "Quantity": "updates.Quantity",
      "UnitPrice": "updates.UnitPrice",
      "Tax": "updates.Tax",
      "FileName": "updates.FileName",
      "IsFlagged": "updates.IsFlagged",
      "CreatedTS": "updates.CreatedTS",
      "ModifiedTS": "updates.ModifiedTS"
    }
  ) \
  .execute()
```



#### Lake API to convert a date to year:

```python
category_yearly = df.select(year(col("OrderDate")).alias("Year"), "Category") # two columns, year and Category
```

#### Add a column

```python
from pyspark.sql.functions import *

# new columns with the Order date month and year
df2 = df.df.withColumn("Year", year(col("OrderDate"))).withColumn("Month", month(col("OrderDate")))

# two new columns extracting the name and first and last name from the CustomerName
df2 = df2.withColumn("FirstName", split(col("CustomerName"), " ").getItem(0)).withColumn("LastName", split(col("CustomerName"), " ").getItem(1))
```

### Save data

Lake API to save in a file:

```python
bikes_df.write.mode("overwrite").parquet('Files/product_data/bikes.parquet')
```

Parquet is recommended for data later used in analytics systems.

The output can be partitioned in several files:

```python
bikes_df.write.partitionBy("Category").mode("overwrite").parquet("Files/bike_data")
```

This creates a bike_data folder with one subfolder per Category field value. Data can be partition by more field, creating a higher hierarchy of folders.

![alt text](images/partition_tables_datalake.png)

### Visualize data

In a notebook, cells results can be visualized with built-in charts or using custom libraries. A common one is matplotlib. However, this used pandas dataframe and not Spark's, so a conversion must be made:

```python
pd_df = df.toPandas()
```

### Configurations for delta tables

https://learn.microsoft.com/en-us/fabric/data-engineering/delta-optimization-and-v-order?tabs=sparksql

```python
# Enable V-Order: More optimized reads by different engines (PBI, SQL, spark, etc.) to the parquet files with configuration when writting them.
spark.conf.set("spark.sql.parquet.vorder.enabled", "true")

# Enable automatic Delta optimized write: Less and bigger files 
spark.conf.set("spark.microsoft.delta.optimizeWrite.enabled", "true")
```

```sql
%%sql

-- Enable V-Order but in spark SQL
CREATE TABLE person (id INT, name STRING, age INT) USING parquet TBLPROPERTIES("delta.parquet.vorder.enabled" = "true");

ALTER TABLE person SET TBLPROPERTIES("delta.parquet.vorder.enabled" = "true");
```

### Create a delta table from a df

Lake API to create a managed delta table:

```python
df.write.format("delta").saveAsTable("table1") # creates a managed delta table called table1 from the df dataframe
```
That has created a managed delta table called table1 from the df dataframe

And to create an external one:

```python
df.write.format("delta").saveAsTable("table2", path="abfs_path/subfolder") # creates a delta table called table1 from the df dataframe
```

If we wanted to sabe it in the Files folder, we need to get the ABFS path of the folder (In the folder menu) and add it to the url. IT will look like:

`abfss://workspace@tenant-onelake.dfs.fabric.microsoft.com/lakehousename.Lakehouse/Files/subfolder`

That has created an external table called table2 (That will appear in the Tables folder as a table) but with the data and log stored not in the delta table location but in a subfolder of the Files folder.

### Create a delta table

Tables does not need to be written from a dataframe, using the DeltaTableBuilder API.
This creates a managed table:

```python
from delta.tables import *

DeltaTable.create(spark) \
  .tableName("products") \
  .addColumn("Productid", "INT") \
  .addColumn("ProductName", "STRING") \
  .addColumn("Category", "STRING") \
  .addColumn("Price", "FLOAT") \
  .execute()
```

Can be created also with SQL:

```sql
%%sql

CREATE TABLE salesorders
(
    Orderid INT NOT NULL,
    OrderDate TIMESTAMP NOT NULL,
    CustomerName STRING,
    SalesTotal FLOAT NOT NULL
)
USING DELTA
```

When saving a dataframe in the Tables folder of the lakehouse, a table is automatically created.


And to create an external table:

```sql
%%sql

CREATE TABLE MyExternalTable
USING DELTA
LOCATION 'Files/mydata'
```
The schema of the created table is determined by the external Parquet files containing the data.

### View info of a delta table

Info like location:

```sql
%%sql

DESCRIBE FORMATTED table1;
```

### Update a delta table

Most commonly done in Spark SQL

```python
spark.sql("INSERT INTO products VALUES (1, 'Widget', 'Accessories', 2.99)")
```

Or again, directly with the %sql magic:

```sql
%%sql

UPDATE products
SET Price = 2.49 WHERE ProductId = 1;
```

### Delete a delta table

```sql
%%sql

DROP TABLE table1;
```

If the table is external, only the metadata is removed, not the underlying data (Parquet files and delta_log folder).

### Save data in delta format

Data can be saved in the delta format, with parquet data files and JSON logs folder, but without creating a table:

```python
delta_path = "Files/mydatatable"
df.write.format("delta").save(delta_path) # saves in the path data in delta format (But no table)
new_df.write.format("delta").mode("overwrite").save(delta_path) # alternative to ovewwrite
```

Since having it in this format, if the parquet is modified using the Delta Lake API or through an external delta table connected to it, the log is also modified.


### Update data in delta format

The content of a dataframe can be appended to data stored in delta format

```python
delta_path = "Files/mydatatable"
new_rows_df.write.format("delta").mode("append").save(delta_path) # appends the data stored in the path
```

### View table versioning

#### See changes in a table

```sql
%%sql

DESCRIBE HISTORY products
```
It will show the transactions made to the table, with a versionID, timestamp, type of operations and parameters.

In case of an external table, the folder location can be specified instead:

```sql
%%sql

DESCRIBE HISTORY 'Files/mytable'
```

#### Retrieve certain version of data

Done loading the data in a dataframe, and specifying the version:

```python
df = spark.read.format("delta").option("versionAsOf", 0).load(delta_path) # omitting option retrieves the current version
```

Or a timestamp:

```python
df = spark.read.format("delta").option("timestampAsOf", '2022-01-01').load(delta_path)
```


### Delta Lake API

It can directly be used the Delta Lake API to modifiy data stored in delta mode, instead of using PySpark or Spark SQL.

```python
from delta.tables import *
from pyspark.sql.functions import *

delta_path = "Files/mytable"
deltaTable = DeltaTable.forPath(spark, delta_path) # creates a DeltaTable object

# updates the table data
deltaTable.update(
    condition = "Category == 'Accessories'",
    set = { "Price": "Price * 0.9" })
```

### Streaming data in delta tables 

Using Spark Structured Streaming API, delta tables can be either a source or sink.
Here, streaming orders are readed from a delta table

```python
from pyspark.sql.types import *
from pyspark.sql.functions import *

# Load a streaming dataframe from the Delta Table
stream_df = spark.readStream.format("delta") \
    .option("ignoreChanges", "true") \
    .load("Files/delta/internetorders")

# Now you can process the streaming data in the dataframe
# for example, show it:
stream_df.show()
```


And here, a delta table is the sink for data coming from JSON files:

```python
from pyspark.sql.types import *
from pyspark.sql.functions import *

# Create a stream that reads JSON data from a folder
inputPath = 'Files/streamingdata/'
jsonSchema = StructType([
    StructField("device", StringType(), False),
    StructField("status", StringType(), False)
])
stream_df = spark.readStream.schema(jsonSchema).option("maxFilesPerTrigger", 1).json(inputPath)

# Write the stream to a delta table
table_path = 'Files/delta/devicetable'
checkpoint_path = 'Files/delta/checkpoint'
delta_stream = stream_df.writeStream.format("delta").option("checkpointLocation", checkpoint_path).start(table_path)
```
checkpointLocation is a file to track processing state.

Then, a table can be created from the output:

```sql
%%sql

CREATE TABLE DeviceTable
USING DELTA
LOCATION 'Files/delta/devicetable';

SELECT device, status
FROM DeviceTable;
```

To stop a stream:

```python
delta_stream.stop()
```

### Connect to different places

#### Azure blob storage

```python
# Azure Blob Storage access info
blob_account_name = "azureopendatastorage"
blob_container_name = "nyctlc"
blob_relative_path = "yellow"
blob_sas_token = "sv=2022-11-02&ss=bfqt&srt=c&sp=rwdlacupiytfx&se=2023-09-08T23:50:02Z&st=2023-09-08T15:50:02Z&spr=https&sig=abcdefg123456" 

# Construct the path for connection
wasbs_path = f'wasbs://{blob_container_name}@{blob_account_name}.blob.core.windows.net/{blob_relative_path}?{blob_sas_token}'

# Read parquet data from Azure Blob Storage path
blob_df = spark.read.parquet(wasbs_path)

# Show the Azure Blob DataFrame
blob_df.show()
```


#### Azure SQL Database

```python
# Placeholders for Azure SQL Database connection info
server_name = "your_server_name.database.windows.net"
port_number = 1433  # Default port number for SQL Server
database_name = "your_database_name"
table_name = "YourTableName" # Database table
client_id = "YOUR_CLIENT_ID"  # Service principal client ID
client_secret = "YOUR_CLIENT_SECRET"  # Service principal client secret
tenant_id = "YOUR_TENANT_ID"  # Azure Active Directory tenant ID


# Build the Azure SQL Database JDBC URL with Service Principal (Active Directory Integrated)
jdbc_url = f"jdbc:sqlserver://{server_name}:{port_number};database={database_name};encrypt=true;trustServerCertificate=false;hostNameInCertificate=*.database.windows.net;loginTimeout=30;Authentication=ActiveDirectoryIntegrated"

# Properties for the JDBC connection
properties = {
    "user": client_id, 
    "password": client_secret,  
    "driver": "com.microsoft.sqlserver.jdbc.SQLServerDriver",
    "tenantId": tenant_id  
}

# Read entire table from Azure SQL Database using AAD Integrated authentication
sql_df = spark.read.jdbc(url=jdbc_url, table=table_name, properties=properties)

# Show the Azure SQL DataFrame
sql_df.show()
```