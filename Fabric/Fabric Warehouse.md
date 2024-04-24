# Data Warehouse in Fabric

- Powered up by Synapse Analytics
- Relational layer built over the lakehouse and its delta tables (Which already has data warehouse functionalities).
- Full T-SQL support (Allowing to modify data unlike the lakehouse SQL eindpoint).
- Managed, scalable and highly available.
- Data can be queried with the built-in **SQL query editor** (Intellisense, code completion, parsing, etc.), or SSMS or other client.
- **Visual query editor** similar to the diagram view in PQO.
  
Tables in the lakehouse do not need to be copied to the warehouse, if it is only for viewing and not editing. They can be queried from the DWH using **cross-database querying**.

Creating a DWH creates a default semantic models, and additional ones can be created. THese are in sync with the DWH so no need to mantain them.


## Security

### Permissions
Workspace and individual item permission. 
Access to the DWH item can be given via T-SQL or the Fabric Portal.
Permission types are:
- Read: Can connect using the SQL connection string.
- ReadData: Read data from any table or view within the DWH.
- ReadAll: Read data from the raw parquet files in OneLake.

### Monitoring
Acces to **dynamic management views (DMV)** to monitor usage within the DWH: Active queries, duraion, etc. Some are:
- sys.dm_exec_connections: Connection between the warehouse and the engine.
- sys.dm_exec_sessions: Authenticated sessions between the item and the engine.
- sys.dm_exec_requests: Active request in a session.

Views allow workspace admin to see every user, other roles (Member, contributor, viewer) can only see their own results.

%KILL% allows to terminate active requests by a workspace admin.

#### Check long-running queries

```sql
-- see longest requests
SELECT request_id, session_id, start_time, total_elapsed_time
    FROM sys.dm_exec_requests
    WHERE status = 'running'
    ORDER BY total_elapsed_time DESC;

-- check session of the request
SELECT login_name
    FROM sys.dm_exec_sessions
    WHERE 'session_id' = 'SESSION_ID WITH LONG-RUNNING QUERY';

-- kill the request
KILL 'SESSION_ID WITH LONG-RUNNING QUERY';
```


### Read data from outside of Fabric

%COPY% allows to retrieve data from Azure Data Lake Storage Gen2 and Azure Blob Storage. Some functionalities:
- Identification with Shared Access Signature or Storage Account Key.
- Allows to specifiy input file extension: PARQUET and CSV for now.
- Allows specifying a location for storing rejected rows: %REJECTED_ROW_LOCATION%.
- Skip header rows
- Loading multiple files if they have the same structure, with the * wildcard.

```sql
COPY my_table
-- Multiple file locations can only be specified from the same storage account and container
FROM 'https://myaccount.blob.core.windows.net/myblobcontainer/folder0/*.csv, 
    https://myaccount.blob.core.windows.net/myblobcontainer/folder1/'
WITH (
    FILE_TYPE = 'CSV',
    CREDENTIAL=(IDENTITY= 'Shared Access Signature', SECRET='<Your_SAS_Token>') -- SAS used, can be used SAK as well
    FIELDTERMINATOR = '|'
)
```
```sql
COPY INTO test_parquet
FROM 'https://myaccount.blob.core.windows.net/myblobcontainer/folder1/*.parquet'
WITH (
    CREDENTIAL=(IDENTITY= 'Shared Access Signature', SECRET='<Your_SAS_Token>')
)
```
```sql
COPY INTO dbo.Region 
FROM 'https://mystorageaccountxxx.blob.core.windows.net/private/Region.csv' WITH ( 
            FILE_TYPE = 'CSV'
            ,CREDENTIAL = ( 
                IDENTITY = 'Shared Access Signature'
                , SECRET = 'xxx'
                )
            ,FIRSTROW = 2
            )
GO
```

### Read data from other lakehouses or data warehouses

```sql
CREATE TABLE [analysis_warehouse].[dbo].[combined_data]
AS
SELECT 
FROM [sales_warehouse].[dbo].[sales_data] sales
INNER JOIN [social_lakehouse].[dbo].[social_data] social
ON sales.[product_id] = social.[product_id];
```


