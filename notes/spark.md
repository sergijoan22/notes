# Spark

These notes have been done using Spark in Databricks and mainly SQL but also Python.

Databricks practice is in the [misc repository](https://github.com/sergijoan22/misc/tree/main/databricks_exercises)

More info: [The Internals of Apache Spark](https://books.japila.pl/apache-spark-internals/)

## Architecture

- Spark does not stores data (mounts it, which is point to where it is stored), it is a computing engine for data processing.

- There is a driver that distributes data to be computed by the executors, who actually perform the work. Each of these are in one machine. Using several machines for processing is called parallelism.
- Each machine forming the cluster can have multiple cores and these multiple threads. The minimal unit of each, which can be a core or a thread, is called a slot.
- Unit of parallelism is calculated considering the slots of the executor machines.
- Not all executors or slots are always used since not always means faster performance. There is always a communication cost between the machines and slots to consider. There must be a balance between computation and communication, and the catalyst (An internal optimizer) helps with that.
- When the data is parallelized, the divisions are called partitions. The optimal number to divide the dataset depends on the size, the original partition in the dataset origin (ex. data comes from a lot of small files) and the cluster configuration.
- Depending of the percentage of a task that can be parallelized, the number of processors that can be used to improve performance varies (Amdahl's law).
- Spark has linear scalability, so it can take advantage of thousands of resources. Using more resources assures better speeds and scaling the data amount.

- Spark local mode: Driver and executor in the same physical machine. Not the most suitable for production (specially for heavy loads) but works for prototyping.

## Spark DataFrame

When Spark was created there was only RDD, then DataFrames appeared, which are an improved version with metadata or schemas. There was also introduced later the Dataset, only for Java and Scala, with type safety but with lower performance.

An RDD is:

- Resilient to faults
- Distributed across multiple nodes
- Collection of partitioned data

A DataFrames inherit these properties and also:

- Includes metadata (Columns and data types)
- Highly optimized

It should always be used the API of the DataFrame, and in case of using SQL, it is the only option.

When using RDD API, Scala if faster than Python, but using the DataFrame API the performance is similar between these and the other languages.

Using the DataFrame API, you specify what you want to do, not how. The catalyst will decide the best way to achieve the task. The catalyst can change your code to be more efficient. This is a difference with the RDD API, which is imperative, while the DataFrame's is declarative.

The catalyst generates several plans which gives the same result than the user input. Then, depending of the cost of each plan, the most optimal is chosen.

## Caching

- To avoid workers to be loading there data partition every time it is used, data can be cached in their memory,

- Significant increase in queries performance.
- Going to the Spark UI -> Storage it indicates the tables that are being caches, indicating for each in how many partitions or the memory size. They appear under the RDD tab (Since a DataFrame is in the core a RDD).
- Memory of data gets smaller saved in cache compared to disk thanks to built-in optimizations called Tungsten.
- It can also be done lazy caching, so data is not immediately cached but when needed. If after defining a lazy caching of table, a query uses part of the table, only partitions of the table containing the rows used will be cached. Per example, if only used the first 100 rows in a query, only the partition(s) containing those 100 rows are cached, while the rest of the table isn't.
- When a job is done, using the View option and then the DAG visualization, if the stage has a green dot means that the data accessed was cached. It will also be indicated in the dot's tooltip.
- To remove everything cached, a command can be used or restarting the cluster also works.

## Shuffle partitions

- First, there are two types of transformations. Narrow transformations (Such as a SELECT, DROP or WHERE) are those for which the executors don't need to access to data from the other partitions. However, in wide transformations (Such as a DISTINCT, GROUP BY or ORDER BY) the executors need to access to the rest of the data in the other nodes in the cluster.

- After performing a query, it is shown jobs that have been done and the stages of each job as well (The stages and jobs numbers vary depending and are incrementally increasing through the session).
- For a narrow transformation like a simple COUNT from a table, two stages are needed since first the COUNT is done within each node  partition and then the results are aggregated. Second stage can't start until all nodes finish the first stage (So a faster single node won't make any difference but a single slow one will cause bottleneck. Also, when all nodes have performed the task to be all put together is called a stage boundary) and is performed by any of the nodes chosen by chance. In the second step, since nodes can't directly read memory from other nodes, each one has created in the first step a shuffle write which is this case is a single row containing the COUNT performed in the partition. Then, the node that performs the second task does a shuffle read to get the results from the previous stage of all the partitions.
- These process of two steps is done this way since Spark is a  bulk synchronous processing system. All this process can be seen using the Spark UI.
- Now, in case of a wide transformation being performed (COUNT but using a GROUP BY): The first stage has one task per node (The number of tasks can also be a multiple of the number of nodes as well) like the simple COUNT. In this stage, each node has made a shuffle write containing the GROUP BY of each partition. The next stage would be to aggregate these results. In this stage, and for a wide transformation, the number of tasks is by default 200. This value may not be optimal, and in the Spark UI can be seen that most tasks maybe haven't done any work. However, this number of task performed is not automatic but rigged by a parameter.
- That parameter is the Shuffle partitions parameter and its default value is 200. It controls how many tasks are done in stage when there is shuffle (A shuffle read after shuffle writes have been done in the previous stage) caused by a wide transformation. In the example of the GROUP BY, in the first stage the nodes create shuffle writes and it is in the second stage where this happens. For the case of being 200 the parameter value, there are too many tasks to read just a few rows that the shuffle writes have generated, so only a few tasks do a shuffle read, while the others don't.
- The parameter can be changed when creating the cluster or it can be changed within a notebook dynamically. Using a improper value for a query may lead to poor performance. The value should be proportional to the amount of data.

## Adaptive Query Execution

- Automatically updates query plans based on runtime statistics. The catalyst optimizer, which is used by default and changes the query plans as well, does not know how to perform when the queries include user defined functions. So, AQE allows the catalyst optimizer to use a different way to perform queries, which happens in the middle of a query using runtime statistics. It is between stages when the the query is reoptimized.
- The AQE can dynamically:
	- Coalesce shuffle partitions: There is not an universal optimal shuffle partition number, since data changes at different times of query execution. AQE puts first a high value of partitions, but automatically coalesce them to have at the end a n optimal number of partitions which will have a similar shuffle read load. So, no need to manually put a shuffle partition value.
	- Switch join strategies: When doing a join, data is randomly located in nodes. AQE does a broadcast join, which, in case of being one of the datasets significantly smaller than the other, puts a copy of the dataset in all the nodes. This way, each node can do internally the join and then simply append all the shuffle writes, having to shuffle less data.
	- Optimize skew joins: There is a data skew if data load is unevenly distributed through the partitions, and it can have a negative performance impact since a node with a significantly heavier load will be a bottleneck. AQE, after the data being initially partitioned, it is going to provide skew hints (Indicating the dissimilar loads) and then with a skew reader, read the data in an evenly distribution.

- When seeing the jobs used in a query that has used AQE, it will as skipped the staged that have been substituted, and the substitution stage after.

More info: [How to Speed up SQL Queries with Adaptive Query Execution (databricks.com)](https://www.databricks.com/blog/2020/05/29/adaptive-query-execution-speeding-up-spark-sql-at-runtime.html)

## Data Pipelines

- Process of moving data through applications in a automated way. Must be scalable to process a bigger data volume, velocity and variety.
- Input data usually comes from a Kafka-like technology, which is a messaging system. It puts data in a queue if needed to avoid overloading the pipeline.
- A design principle is to decouple storage and compute. No data is stored on the cluster, so this can be disactivated when it is not being used, allowing to save resoruces.
- There are two bottlenecks: First, there are IO-bound problems, since a big amount of data requires a heavy network transfer. It is important to decide the best way to store and access the data. Spark optimizes the clusters locations to use faster networks. Additionally, there are CPU-bound problems, since some operations on data can be very complex and demanding.
- Pipelines prepare data for both OLAP and OLTP workloads.

### Data input

- Data lakes like Amazon S3 or Azure Blob Storage can be mounted into Spark to access data within them. These both services have support for the Databricks File System or DBFS.

- It is recommended to use JDBC if possible since Spark is written in Scala (A language which runs on the Java Virtual Machine). It stands for Java Database Connectivity, an API to connect to databases. Spark allows to use with JDBC an optimization called Predicate push down, which allows to perform certain operations in the database before retrieving the data, like filtering it so the network transfer is lighter.
- There are serial reads (Only one database connection to read data) and parallel connection (Parallelize data reading using several database connections).

### File formats

- The ideal file format for distributed systems is delta, column-based and based on Apache Parquet. Delta has some additions such as database-like ACID operations.
- Using a column-bases file format like Delta and Parquet gives several benefits like better compression or being able to be read in parallel. Those both have metadata so is not needed infer the schema when reading the table, saving a lot of time.

### Data output

- When writing data from Spark, it is done in parallel, since data is stored in different partitions. If writing in a file, it would be actually written one file per partition.

## Tables and views

- When creating a table, it is not actually stored in a database but in the Databricks File System and its metadata in the Hive Metastore.
- A global view or table is available through all clusters while a temporary view is available only in the current notebook.
- A managed table is a table that manages both the data and the metadata. If it the removed, both components are removed. However, unmanaged tables only manage the metadata, while the data is in a different location, so dropping the table does not delete the data.
- Unmanaged (Or external) tables allow to persist data even after shutting down the cluster. When created, a location is defined where data is persisted.

## Databricks

- Databricks is a unified platform with several tools including deployed instances of Spark with some benefits and optimizations.

- It allows also to do other tasks reporting or machine learning in the same environment.
- It is available in Azure, AWS and GCP.

- Databricks Community Edition is a free version to try Databricks.

## Lakehouses

- Combines the scalability and low-cost storage of data lakes and the speed, ACID transactions (Atomicity, Consistency, Isolation, Durability) and reliability of data warehouses. No need to be copying data from lake to warehouse, a single solution is enough.
- Delta Lake is an open-source project highly compatible with Spark. Allows to do database-like operations in a data lake. Built on top of Parquet, saves also a transaction log tracking an historical of all changes, allowing to roll back to previous versions.
- They work with any kind of data and are suitable for different applications like BI or ML.

- Address some of the data lakes problems: Hard to append or modify data in files, job failing mid way can cause corrupt files, real-time operations are complex, costly to keep historical data versions, difficult to handle large metadata files, worse performance when data distributed in a lot of small files, hard to track who accesses to data and hard to ensure data is following am expected defined schema.

- To solve some of the problems, Delta Lake offers ACID transactions, Indexing, table ACLs (Access Control Lists) for data governance and schema enforcement.

- Incoming data goes to a bronze table, where it is put raw, as it comes from the source. A schema enforcement can be made to quarantine data not meeting the expectations and avoid propagating it to later stages.

- Then, data passes to a silver table, with filtered, cleaned or augmented data. Then, gold tables have the data aggregated to a business-level, since not less granularity can be enough. The use of bronze, silver and gold tables in order is a medallion architecture. All three kinds of tables are Delta tables, backed by S3 or an equivalent service.

- In Delta lake, schema validation is on write, so new writes to a table are checked for compatibility. If there is an exception, the write is not executed. A table schema can be dynamically updated.
