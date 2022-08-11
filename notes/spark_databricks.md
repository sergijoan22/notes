## Spark

- Spark does not stores data, it just computes it from a source to a destination.

- There is a driver that distributes data to be computed by the executors, who actually perform the work.
- More executors does not always mean faster processing, an equilibrium must be found.

- Using distributed computing is called parallelism.
- Depending of the percentage of a task that can be parallelized, the number of processors that can be used to improve performance varies (Amdahl's law).
- Spark has linear scalability, so it can take advantage of thousands of resources.

- Spark can be used for scaling data amount or for better speeds.

More info: [The Internals of Apache Spark](https://books.japila.pl/apache-spark-internals/)

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

Using the DataFrame API, you specify what you want to do, not how. The catalyst, an internal optimizer will decide the best way to achieve the task. The catalyst can change your code to be more efficient. This is a difference with the RDD API, which is imperative, while the DataFrame's is declarative.

The catalyst generates several plans which gives the same result than the user input. Then, depending of the cost of each plan, the most optimal is chosen.

## Databricks trial

Databricks is a unified platform with several tools including deployed instances of Spark with some benefits and optimizations.

It allows also to do other tasks reporting or machine learning in the same environment.

Databricks Community Edition is a free version to try Databricks. When logging and having to choose a cloud provider, select "Get started with Community Edition" at the bottom.

Create a cluster using the highest runtime version with both ML and LTS. The cluster has a timeout but for that case it has a Restart button.

A notebook, in which it is chosen the language and cluster used, can be created to interact with a cluster using the language. In case of using SQL, so we can start writing code.

```SQL
SHOW TABLES
```

No results given since no table has ben defined yet.

Even though we have defined the language as SQL, we can use other language.

```python
%python
x = 10
print(x)
```

## Databricks Practice

In workspace -> Home there is the home folder.  Using an URL (https://files.training.databricks.com/courses/ucdavis/Lessons.dbc), it is imported a collection of notebooks for practice.

### 1.4-SQL-Notebook

To access data, we run a script to that mounts (points to the data origin, does not upload it) a dataset, specifically an S3 bucket in AWS. It is mounted in the path /mnt/davis

```
%run ../Includes/Classroom-Setup
```

We can see a preview of a file. It can be see that the file has a header

```
%fs head /mnt/davis/fire-calls/fire-calls-truncated-comma.csv
```

Now a database is created to be used and then a table inside the database to store the file content.

```sql
CREATE DATABASE IF NOT EXISTS Databricks
```

```sql
USE Databricks
```

```sql
DROP TABLE IF EXISTS fireCalls;

CREATE TABLE IF NOT EXISTS fireCalls
USING csv
OPTIONS (
  header "true", -- file has a header
  path "/mnt/davis/fire-calls/fire-calls-truncated-comma.csv",
  inferSchema "true" -- to find data type of each column
)
```

Now, in the left-hand menu , in the Data tab we can find the database and the table. Also, in the Recents tab we can directly go to what we recently accessed.

We can apply queries to the table. Under the output, there is an option to show insted a table as the result a graph of different kinds.

```sql
SELECT `Neighborhooods - Analysis Boundaries` as neighborhood, 
  COUNT(`Neighborhooods - Analysis Boundaries`) as count 
FROM FireCalls 
GROUP BY `Neighborhooods - Analysis Boundaries`
ORDER BY count DESC
```

### 1.5-Import-Data

First, download a csv file (https://s3-us-west-2.amazonaws.com/davis-dsv1071/data/fire-calls/fire-calls-truncated-comma-no-spaces.csv). Now on the Dat tab, we can create a table and then use the previous file. It can be selected the 'Create Table with UI' option and it must be chosen the cluster. Then, with an UI it can be configured the table attributes. Then, we can use the new table.

In this case, dat is not mounted, but actually stored in Databricks.

