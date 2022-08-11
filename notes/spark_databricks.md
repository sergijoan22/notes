# Spark

- Spark does not stores data, it just computes it from a source to a destination.

- There is a driver that distributes data to be computed by the executors, who actually perform the work.
- More executors does not always mean faster processing, an equilibrium must be found.

- Using distributed computing is called parallelism.
- Depending of the percentage of a task that can be parallelized, the number of processors that can be used to improve performance varies (Amdahl's law).
- Spark has linear scalability, so it can take advantage of thousands of resources.

- Spark can be used for scaling data amount or for better speeds.

## DataFrame

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

In workspace -> Home there is the home folder.  Using an URL (https://files.training.databricks.com/courses/ucdavis/Lessons.dbc), it is imported a collection of notebooks for practice. Then, create a notebook, in which it is chosen the language and cluster used. With the notebook we can use the language to connect to the cluster. We have selected SQL, so we can start writting code

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

