# <img src="https://play-lh.googleusercontent.com/RyoQTmHnxsxPYabsETmWVXHtLorVh_yOO48hsdv2VmI-Uki4qt5c5vV1cicJODV56A4" style="zoom:6%;" /> Google Cloud

[TOC]

Cloud SDK

SSH access

API Key

By default, when you load data, BigQuery expects UTF-8 encoded data

For many tables with same schema, instead of many UNION, a wildcard table can be used.

```sql
SELECT * FROM `ecommerce.sales_by_sku_2017*`
```

```sql
SELECT * FROM `ecommerce.sales_by_sku_2017*`
WHERE _TABLE_SUFFIX = '0802' # to filter tables
```

Legacy SQL

--------------------------------------------------------------------------------------------

## Introduction

### Data engineer

Responsibilities:

- Productionize data processes
- Add new value to the data
- Get the data where it can be useful
- Get data into a usable condition
- Manage the data

Challenges:

- Difficult access to data
- Bad quality data
- High computational resources needed to process the data
- Bad query performance

### Data lake

Platform to store to store all types of data in bi gquantity. They serve data pipelines, analytical workloads

Commonly used for backup and archival storage

Before choosing:

- Can it store all types of data needed?
- Can it scale to meet the demand?
- Does it support high high-throughput ingestion?
- Does it allow fine-grained access to objects?
- Is it needed to seek within files?
- Can it be connected easily to other tools?

### Data transformation

EL: Process of extracting data from the source and loading it directly to the destination ,without transforming it. Can be done if both the source and the destination have the same schema.

ELT: Data is extracted to and loaded to the system. Once it is loaded, it is transformed whenever it is going to be used. Done when the transformation amount to be done is not high.

ETL: Used when transformation is essential, which is done before storing the data. The transformations could reduce the data amount, having to use less storage.

### Data warehouse

Where data is stored ordered and for analysis.

Storage is not the purpose of the data warehouse but the data lake. All the data in the DWH should be prepared for querying.

A modern data warehouse:

- Scalable to terabytes or even petabytes
- Serverless, with no mantaining needed
- Seamlessly connection to processing or visualization tools
- Capacity to receive batch but also streaming data
- Support for machine learning
- Varied options to secure and share data

Before choosing:

- Can it receive both batch and streaming data from pipelines?
- Can it scale?
- How is data organized and its access controlled?
- Is it designed for performance?
- How much maintenance is needed?

Differences with a relational database:

- Scales to higher storages of data
- Easily connects to external data sources for ingestion
- Column-based storage
- Optimized for analytical queries workloads

### Data management and governance

Access policies and overall governance must be used to control who has access to which data and how.

Questions before applying data management:

- Who should have access to the data?
- How is personally identifable information (Addresses or emails) handled?
- How can end-users discover the data available for analysis?

For data governance, it is offered solutions like Cloud Data Catalog (To show the user the data he has available) or Cloud Data Loss Prevention (To manage sensitive data).

### Fully Managed vs Serverless

A fully managed service needs no setup or maintaining the instance. The next step is a serverless service (Which can be treated simply as an API you are using), which no server has to be managed and only is needed to pay for the use.

### Data pipelines in production

For that, tools for workflow orchestration like Apache Airflow are used. Based on that tool, a fully managed version is available in Google Cloud, called Cloud Composer. It orchestrates all Google Cloud products using their API endpoints.

Questions to make:

- How can be ensured the pipeline health and data cleanliness?
- How can be the pipeline put into production minimizing maintenance and maximizing uptime?
-   How does the pipeline adapt to changing schemas and business needs?
- Are being used the latest tools and best preactices?

### Batch Data Pipelines

Process amounts of data at specific times.

Three types:

- EL: Data loaded into the destination as it comes from the source. Used for correct and clean data.

- ELT: Data loaded into the destination as it comes from the source and then transformed when needed. The transformations are carried in the destination service (ej. Using a view instead of the raw table). Used when is not yet know the transformations needed.

- ETL. Data is processed in an intermediate process before being loaded to the destination. For higher transformation needs.

	It must be used (Due to an ELT being not enough) in cases like:

	- Using APIs
	- Complex functions in SQL.
	- Necessity to control data quality, or enrich data.
	- Data comes in streaming.
	- When CI/CD systems must be integrated and unit tests performed.

	ETL options in Google Cloud are Dataflow, Dataproc or Cloud Data Fusion. The recommended option is Dataflow to load into BigQuery. However, these can have specific limitations:

	- Latency or throughput: Load into Bigtable instead.
	- Reusing Spark pipelines: Process with Dataproc.
	- Need for visual pipelines building: Use Cloud Data Fusion.

Attributes of data that processing can solve:

- Valid: Data conforms to business rules.
- Accurate: Data has objective true values.
- Complete: Data is located in the whole dataset.
- Consistent: Data computations are correct.
- Uniform: Data exploration in not misleading.

Is important to **tracking data lineage** (Metadata about the data) to know where it cam from, the processes it passed and where is currently.

### Streaming data processing

Processing of unbounded data, which has no defined limits.

Data has some challenges: Volume, velocity and variety. For this, it can be used, respectively: Autoscaling, streaming and artificial intelligence.

The typical arquitecture starts with Pub/Sub, then to Dataflow and either BigQuery for storing aggregates or Bigtable for individual records.

### Machine Learning

AI are problems that can be solved with computers thinking or acting as humans. Within, ML uses data to train the AI, and within, deep learning can use even unstructured data.

Several options in Google Cloud:

- Build a custom ML model using libraries like TensorFlow in Vertex AI.

- Use AutoML.

- Called pretrained models with APIs. Work good if data passed is similar to the data used to train the model. It allows to enrich unustructured data by applying labels to it. Version for natural lenguage, vision, audio or video.

	curl (With an API key) can be use to send a request.

Services:

- Dialogflow: Natural language understanding platform to design and integrate conversational user interfaces.
- Vision API.

#### AI Hub

Repository of ML components. To find something already done and optimized instead of doing it. It can be found:

- Kubeflow (ML on Kubernetes) pipelines of components.
- Jupyter notebooks.
- TensorFlow modules.
- Trained models.
- VM images.
- Services.

Both public and restricted assets (e.g. Restricted to an organization or team).

### Billing

It can be added labels (Key-value pairs) to objects of many services. With this, it can be filter the costs on specified categories.

### Labels

Cloud Storage buckets

BigQuery datasets, tables, views

Dataproc jobs or clusters

## <img src="https://help.sap.com/doc/8b8d6fffe113457094a17701f63e3d6a/GIGYA/en-US/loio41289de270b21014bbc5a10ce4041860_LowRes.png" style="zoom:3%;"/> Google Cloud Storage

Used for data lake, stored all kinds of data, which persists in time.

Cheaper than the cost of compute, so it is a good idea save the state of compute applications if these are not being used, to restore them later.

Object store, so it stores binary objects regardless its content. However, has some functionality to use objects is if they were files.

Blob storage.

### Characteristics

- Available more than 99,99% of the time
- Data is persistent, instantly available, encrypted.
- Objects are available globally, but can be restricted to a single location
- High throughput and moderate latency. Resumable loads.
- Can return object uncompressed if specified.
- It can be specified for users to pay for accessing objects. There are network egress charges when accessing data from a different region, so it can be used to make the user pay for the access.
- Objects can be shared using signed URLs.
- Objects can be uploaded in pieces and then automatically create a composite object.

### Internal arquitecture

Objects are stored inside buckets. Buckets are identified by a global unique namespace. Cant use a name used elsewhere in the world until that bucket deleted.

When an object is stored, replicas are made which are monitored to replace them if lost or corrupted.

If the bucket is multi-regional, replicas are distributed across regions, and if it is single-region, across zones. When an object is retrieved, it comes form the closest replica to the requester, allowing lower latency. With multiple requesters at the same time, using the replicas allows high throughput.

Objects are stored with metadata which Cloud Storage use for several purposes (ej. Knows when an object is created so it can delete it after a given period)

Objects in a bucket are not actually stored in a file system with directories but it is simulated as if they were. Actually, when an object is stored inside a folder, it just adds the total path to the object name. So, an object called valencia_data.csv inside spain_data folder, is internally called spain_data/valencia_data.csv.

To move objects from one directory to other, it should be scanned all objects with the initial directory as the first part of the name to then be renamed that part with the new directory. So, when there a lot of objects in the bucket, this operation can take longer. 

The bucket name, an / and then the bucket name is the object URI.

It can be applied management features like a retention policy (To delete objects after a period), versioning (To keep track of different versions of an object) or lifecycle management (To automatically move objects to lower-cost storage classes).

### Versions

Different storage classes, depending of how frequent data will be retrieved.

As lower the class, lower the costs, lower the availability and higher the costs for data access.

- Standard storage: For data accessed frequently (Hot data) or for data stored briefly.
- Nearline storage: When availability is not that critical, for data accessed about once a month. Data must be stored at least 30 days.
- Coldline storage: When data will be rarely used, about once a quarter. Data must be stored at least 90 days.
- Archive storage: For data accessed about once a year. Data must be stored at least one year.

### Security

Two overlapping ways of controlling access to objects:

- IAM policy:
	- Standard across GCP.
	- Set at bucket level, with uniform access rules to all the objects within the bucket.
	- Include project and bucket roles:
		- Project and bucket roles of owner, writer or reader.
		- Bucket rol to control access control lists.
		- Project rol to create and delete buckets and to set IAM policy.
		- Custom roles.
- Access control lists
	- Can be applied to buckets or individual objects.
	- Can be disabled when creating the bucket.

Encryption:

- Data is encrypted both at rest and in transit.
- Can't be disabled.
- Data is encrypted using encryption keys which are also encrypted using  key encryption keys.
- There are three ways to control how the encryption works:
	- Cloud KMS automatically creates Google-managed encryption keys (GMEK) which are encrypted using key encryption keys (KEK) which automatically rotate on a schedule.
	- The user controls on Cloud KMS the creation and existence of the ecvryption keys, called Customer-managed ecryption keys (CMEK).
	- The user provides the encryption keys, called Customer-supplied encryption keys (CSEK) without using Cloud KMS.

- Additionally, data could be client-side encrypted before being uploaded to Cloud Storage. Still, one of the three server-side encryption methods is still performed.
- It can be created immutable logs of data access.
- A hold can be applied to an object or a bucket, and operations that change or delete the object are suspended until the hold is released.

## <img src="https://codelabs.developers.google.com/static/codelabs/cloud-dataflow-starter/img/62b0919755804bea.png" style="zoom:13%;"/> Dataflow

Serverless data processing service for both batch and streaming data. It creates an efficient execution mechanism to run Apache Beam.

### Characteristics

- Continuous auto scaling of storage and compute.
- Same code for both batch and streaming pipelines.
- Integration with Cloud Logging and Cloud Monitoring.
- Automatically disabled when the job is done.
- Optimization of the pipeline graph created.
- Rebalances load between workers to optimize performance.
- Great integration with other Google Cloud services.
- Existing templates and ability to create new.
- Optimizations with Pub/Sub if using the Dataflow runner: Improved watermark latency and accuracy, efficient deduplication of messages and automatic acknowledge of messages.
- Dataflow Shuffle Service: In batch pipelines, moves shuffle operations from the worker VMs to the Dataflow service backend. This allows for better execution times VM consumption, autoscaling and fault tolerance.
- Dataflow Streaming Engine: In streaming pipelines, moves the window stage storage from the VM persistent disks to the service backend. This allows for better VM consumption, response to data variations and supportability.
- Flexible Resource Scheduling:  Reduces the cost of batch pipelines using both preemptible and normal VM through advanced scheduling techniques. Job will be done within 6 hours after creation. For not time-critical workloads. Just after submitting the job, it is scanned to check failures and report immediately.

### Architecture

A pipeline is formed by PTransforms, where each process PCollections (The data). PCollections are immutable, so each step PTransforms creates a new one. Pipeline runners use a service called backend system, that could a local computer, a data center VM or the Dataflow service. This last one offers auto scaling. The pipeline is not just a single line but it has brunches and aggregations.

A PCollection can be batch or streaming data, without size limit. The data is distributed to the different workers. Streaming data is an unbounded PCollection (No fixed size of the data). All data types are stored in a serialized state as byte strings.

### Apache Beam

Core of the service, Dataflow is one of the runners Beam has. It offers a portability framework to define a pipeline in a chosen language and run it in a chosen runner. A pipeline can even be multi language (Create custom code in a different language than the one running the job). Inside the code, a transform of another language SDK can be called.

The runtime environment can be containerized with Docker.

### Streaming features

Doing any kind of aggregation is a challenge, since it is not predefined a limit group of data as in batch. So, time must be divided into windows to group data using a timestamp.

For Pub/Sub messages, it is usually used the ingest time metadata. However, there may latency between this value and the timestamp created in the publisher, so the origin field can be used instead. For this, Dataflow can change the timestamp metadata of the data to use the value generated in the origin.

If custom message IDs are used in Pub/Sub, Dataflow de-duplicates messages if the same ID has already appeared in the last 10 minutes.

To create the windows, there are several options:

- Fixed or tumbling: Windows divided into constant time slices.

	<img src="https://cloud.google.com/static/dataflow/images/fixed-time-windows.png" style="zoom:70%;"/>

- Sliding or hopping: Windows that are computed every fixed timed but using data within a different range (e.g. Having the last 30 minutes of data every 5 minutes). Defined by a minimum gap duration and timing is triggered by another element.

	<img src="https://cloud.google.com/static/dataflow/images/sliding-time-windows.png" style="zoom:70%;"/>

- Sessions: Windows defined by all that with a time difference with the previous element lower than the gap duration. For cases where communication is in bursts (e.g. A web session). Different windows are assigned to each data key.

	<img src="https://cloud.google.com/static/dataflow/images/session-windows.png" style="zoom:70%;"/>

It has to be considered that a window can close and later a message which corresponded to that windows can arrive (e.g. A windows from 8:00 to 8:05 closed at 8:05, but a message from 8:04 arrives to Dataflow at 8:07). The time between the expected time for a message to arrive and the actual time it arrives is called the lag time.

The concept of Watermark is a time after the window would close  in which late messages arriving are included in the window. So, it a late message has a lag time within the watermark, it is included, and otherwise, it is not.

Then, there is the triggers, which determine when to emit aggregated results in a window. By default, these happen after the watermark. Several options to choose based on (A combination can be defined):

- Event time triggers (e.g. Using only data arriving before the watermark).
- Processing time triggers (e.g. Using only data processed before a certain in one stage of the pipeline).
- Data-driven triggers (e.g. Using only the first certain amount of elements to have arrived).

Each trigger in a window fires a pane, and there can be many panes within a window. Data affected by a trigger become a pane, and the aggregation is done at pane level. A pane can be defined between two modes: Accumulate mode to use also the data from previous panes in the window or discard mode to only use the data from that pane.

### Dataflow prime

Dataflow Prime version with upgraded capacities:

- Vertical autoscaling of the workers memory
- Right fitting: Different workers pools created with different capacities, the most appropriate being used in each transformation. Only needed pools have workers with GPU. Combined with horizontal and vertical autoscaling within each pole, best performance and adjusted cost.

New pricing model. In regular Dataflow, independent charges for resources use like CPU, memory or data processed. Now, all compute resources are grouped in DCU (Data Compute Units), and the charge is based on the amount of DCU used. One DCU is equivalent to a job running one hour with 1 vCPU and 4 GB. Other resources, like snapshots, GPUs or persistent disk are still charges the same way, individually.

Same code used than in regular Dataflow and compatible with Streaming Engine and Dataflow Shuffle.

### Set up

There are input and output process for different outside connections, like BigQuery or Cloud Storage. 

Then, there are several intermediate processed like:

- ParDo to filter, cast compute, or extract parts of data.
- GroupByKey to shuffle key-values pairs and group them.
- CoGroupByKey to join two or more key-value pairs.
- Combine to aggregate values with different options like doing it with all the values or all within each key. The aggregation method can be personalized creating a subclass. Using Combine is more effective than GroupByKey since no need for data shuffling when scanning through the data partitions.
- Flatten merges identical PCollections like a union.
- Partition divides a PCollection in smaller ones.

Apart from a PCollection, other inputs can be used called side inputs. This data can be accessed by a function each time it is processing an element. These input values can be determined by the input data or by a different pipeline branch.

Many transforms have two parts, one occurring every time an item is processed and the other occurring after the last item processed. This last step, used on GroupByKey or Combine per example, can not be done on unbounded PCollections (Streaming data). However, time based windows can be used to process data in specified groups. This can be used for batch data also, windowing the data with a timestamp.

### Security

IAM to give access to the Dataflow resources. When a Beam code is submitted, it is sent to Cloud Storage and the Dataflow service.

In the Dataflow service, several steps are performed: Validate and optimize the code, create the VMs, deploy the code to the VMs, start monitoring and finally start running the code. In all these stages, IAM plays a role to continue the process.

Three credentials are needed to determine if a job can be launched:

- User role. The user can be a viewer (Read-only access to resources), developer (To update or cancel jobs also) or admin (To create and manage jobs also).

	If a user has only the developer role, new jobs can not be deployed, since permission for Compute Engine (To create the VM) and Cloud Storage (To stage the code files) are needed as well.

- Dataflow service account to interact between the project and Dataflow. Automatically created with the needed permissions when enabling the API.
- Controller or worker service account, assigned to the VMs running the pipeline. Automatically created with the needed permissions when enabling the API. For production, recommended to create a new account with the only the roles and permissions needed. When launching a Dataflow pipeline, the account to be used can be passed, which must have at least the Dataflow worker role.

An streaming Dataflow pipeline can be updated while running or it can be set when started to be auto-updated.

### Quotas

Dataflow consumes several quotas: CPU quota, IP addresses quota or persistent disk quota. When launching a job, some configurations can be done regarding quotas:

- Use internal IP for the VMs created.

- Choose between HDD or SSD.

- By default, each VM has one persistent disk. For batch pipelines, the default size of each disk is 250 GB but using Shuffle Service reduces the size to 25 GB.

	Streaming pipelines are deployed with fixed pool of persistent disks. Each worker must have at least one PD and 15 at the maximum. Using Streaming Engine, a default 400 GB disk size is reduced to 400 GB. Also, the PD allocates is equal to the `maxNumWorkers` flag (Required if Streaming Engine not used) so, a job having three initial workers but set with a maximum of 25 will cause 25 disks to count against the quota.

	These values can be overwritten with the flag `disk_size_gb`.

### Security

Ways to improve security:

- **Data locality** ensures all data and metadata to stay in one region. When launching a job, a backend is created in a Google-managed project that controls the pipeline. The Dataflow Service Account communicates between the user project and the backend. Region of both can be different, and choosing a regional endpoint allows to comply regulations, reduce latency and reduce network transport costs (Network egress not charged if between the same region).

	When launching a job, use only the flag `region` (This flag is mandatory is newer versions) if no preferences within the region of the regional endpoint or use also the flag `worker_zone` to specify the zone of the region also.

	Also, the worker region can be specified with the flag `worker_region` if one specifically is needed where there is no regional endpoint.

- **Shared VPC**. Dataflow can run in networks in the same project or in a different one, called the host project. When the network exists in a host project, the setup is called shared VPC (Virtual Private Cloud). This allows organization admins to centralize control over network resources like firewalls or subnets but nor administrative responsibilities like creating and managing instances.

	The Dataflow service account needs the Compute Network user role in the host project.

	Use the flag `network` or `subnetwork` to deploy the job in a custom network or subnetwork.

- **Disable external IP** blocks workers from accessing the Internet by not using public IPs. Also, IP quota is not affected. Still, administrative and monitoring can be performed. When enabled, the pipeline can access resources if are located in the same VPC networks, a shared VPC network or a network with VPC networks peering enabled (Which allows connectivity between internal IP of two different VPC networks even if these are independent).

	Create private IP using the flags `network` or `subnetwork` to specify where to run the job and then `no_use_public_ips` to use only internal IPs. Private Google Access needs to be activated.

- **CMEK** (Customer Managed Encryption Key) use. During the job, data is stored in several places, and is always encrypted using GMEK. However, a customer key, located in Google Cloud Key Management System, can be used instead. Metadata is also encrypted. Cloud KMS role in needed. Use of ``

## <img src="https://miro.medium.com/max/397/1*A2GhICVux__ox2jcfGXfrg.png" style="zoom:10%;"/> BigQuery

Query cache does not work if:

- Query has non-deterministic behaviour (e.g. CURRENT_TIMESTAMP or RAND)
- table of view has changed, even if does parts are not used in the query
- If the table is associated with a streamign buffer, even if no new rows.
- If the query uses DML statements
- If the query uses external sources.

If a WITH clause, view or subquery is often used, one way to increase performance is to store the results into a table or materialized view. It must be decide if it the increase of performance is better than keeping the new table or view up to date.



Modern and serverless data warehouse.

### Characteristics

- Scalable to petabytes
- Can ingest up to 100000 rows per second
- Real-time insights
- Optimized for high-read data

- Geospatial data analytics
- Compatible with ANSI SQL 2011
- More cost-effective than Cloud Bigtable
- Use of denormalized data, no relational data models. More disk space but better processing (With better distribution along slots).

### Features

- Use of array and struct data types.
- Allows for data partitioning:
  - Only need of accessing one or few partitions of the data in specific queries.
  - Data keeps correctly grouped in partitions regardless the operations the table can suffer.
  - It can be done by the ingestion timestamp, date, date time or range of an integer column.
  - Partitioning by date or timestamp puts in each partition a day of data.
  - It can be set an expiration time for data in partitions.
  - Partitioned tables need to store more metadata than regular tables (And more when more partitions).
  - The partition filter must be isolated on the left side of the filter of a query for the partition to work properly.
  - Created during creation time.
  - After many operations on the table, the degree to which the data is sorted begins to weaken, and the tables becomes only partially sorted. In this case, more blocks may need to be scanned than when fully sorted. However, BigQuery automatically re-clusters the data to be fully sorted again.
- Allows for clustering:
  - Stores together rows with similar values in the chosen clustering columns.
  - Works only on partitioned tables and the order is made within each partition (However, a fake date column with null values could be created to do partition and then use clustering, even though is not ideal).
  - The order of the column specified because it goes in order of priority.
  - It can be chosen up to four columns.
  - Improves queries such as aggregating or filtering.
  - Created at table creation time.
- Can be used as a query engine, allowing federated queries with data that can be, per example, in Cloud SQL (MySQL or PostgreSQL) or in Cloud Storage (Avro, ORC and Parquet files). Less performant though.
- Tables are organized in datasets are referenced using: `<project>.<dataset>.<table>`. Datasets useful for organizing tables and for access control.
- It can be loaded data from files:
  - It supports CSV, Avro, Parquet, ORC or JSON (If newline delimited).
  - Accepts gzip compressed files, but is slower.
  - Asynchronous job, so no need to keep connection while the job is being executed.
  - Schema can be directly determined from Avro files. For JSON or CSV or formats, it can auto detect but manual checking is recommended.
  - There are limits to the size of individual files.
- Some common metadata is stored (Like row count) so querying it does not processed any data.
- Queries can be scheduled to run on a recurring basis. These can be parameterized (To use current date or time per example).
- Keeps a seven-day history of changes in the tables, so it can be queried an snapshot of the data. Also used to restore previous state.
- Support for user-defined functions (UDF) using SQL preferably or JavaScript. These take a list of values and return a single value. Stored as objects in the database.
- Materialized views:
  - Automatically refreshed and up to date.
  - Periodically cache the results for increased performance, which are usually used with the delta changes in the main table.
  - Queries using them tend to be faster and less consuming than queries retrieving data directly from the source table. Fit for common queries.
- A table expiration flag can be add to program the expiration of a table.
- Support for analytical functions. Three types:
	- Standards aggregations.
	- Navigation functions.
	- Ranking and numbering functions.

- Support for geographical data. Can create objects which are spatial points, line or areas, which can be used for computing or visualizing.
- To track the use on the service, it can be used the built-in cloud monitoring for Google Cloud resources.

### Pricing

Different costs:

- Storage
	- Based on the amount of data in the tables when it is uncompressed.
	- Pricing is calculated per megabyte per second.
	- A table not edited for 90 days is considered long-term storage, dropping the price by 50% but keeping the same functionality. After modifying again by any mean, it returns to the regular price and the timer is set to 0. However, other actions as querying or creating a view don't affect the state. 
- Processing, with two options:
	- On-demand pricing
		- 5$ per TB processed
	- Flat-rate pricing
		- Slots are rented and can be used as much as wanted.
		- 10k$ per slots per month.
		- The slots are active 24h.
		- It must be defined the location of the slots.
		- Starting at 500 slots, and with increments of 500.
		- Recommended 200 slots for each 50 medium queries.
		- The slots are evenly distributed within the active queries. If one query needs less, the others can have the rest.
		- A hierarchical reservation can be defined to prioritize slots to users or projects per example.
		- 25$ for at least 1 year lengths.
		- Monthly commitments must be kept at least 30 days.
		- Yearly commitments must be kept at least 1 year.
		- When canceled, costs are prorated per second.
		- For short periods, Flex Slots can be used:
			- Periods of at least 1 minute.
			- 0.04$ per slot per hour.
			- Available in increments of 100.
			- Just few minutes to deploy the slots.
			- Combines with the current regular rented slots.
			- Used for peaks of use.
			- Subject to availability, but once acquired, these are guaranteed.

### Optimization

Some optimizations should be done (BigQuery may do some of them anyway internally):

- Use only necessary columns (e.g. SELECT * EXCEPT useful if almost all columns are needed).
- For large datasets, consider using approximate aggregation functions (e.g. APPROX_COUNT_DISTINCT instead of COUNT(DISTINCT)). Good option if about a 1% error is tolerable and the dataset is large.
- Filter early and often.
- Order only at the end result, not in intermediate subqueries.
- For joins, put the largest table on the left.
- Avoid self-joins of large tables. Try to use instead aggregation or window functions.
- Use of wildcards to query multiple tables.
- Group results only by fields with not many different values.
- Use partitioning.
- Ordering and some other operations have to be done in a single worker, whose memory could be overwhelmed. So, instead of using ROW_NUMBER through the whole dataset, doing it for each day could prevent an error if the dataset was too large.
- Other way to overwhelm a worker's memory could be using ARRAY_AGG or GROUYP BY if one of the kays is much more common than the others. To avoid an error, more granular keys can be used and then the results can be aggregated.

### Architecture

Tables are column oriented, compared to row-oriented traditional RDBM tables. Optimized to read and append data (Not for updating though). It only needs to read columns involved in the query.

Implemented in two parts (With fast communication thanks to Google's network Jupiter):

- Storage engine (Data is stored on Colossus (Google's distributed file system), which reorders and encodes data to optimize storage).
- Analytics engine

Both resources are dynamically allocated depending on use. No need of commitment of minimum usage.

Data is stored using two encoding systems: Run-length encoding and dictionary-encoding.

Queries use slots, units of computation with a combination of CPU, RAM and network resources:

- Not all have the same specs.
- The number of slots for each query is determined by BigQuery.
- The default maximum available is 2000.
- A fixed amount can be reserved.

When a query is executed, a job is submit to the BigQuery query service, which puts the result in a temporary table stored in the cache for 24 hours. If made the same query and the results have not changed, the same table will be directly returned. Results served from the cache are free of charge.

### Security

Use of IAM to control permissions:

- Roles can be granted at dataset, table, view or column level.
- Can be used Gmail addresses or Google Workspace accounts too.

- Admins or data owners can apply row level security to filter a table depending on the user.
- Authorized views allow to share query results without giving them access to the underlying data. Must be in a different dataset than the data, both in the same region.

Datasets can be of two types:

- Regional: Replicated across multiple zones in the region
- Multi-regional: Replicated among multiple regions

Data is encrypted both at rest and in transit. Like Cloud Storage, it can be used GMEK, CMEK or CSEK.

### Services

BigQuery offers multiple services:

- Cloud Audit Logs to see in detail information about data accessed and queries executed.

- BigQuery query validator to estimate size of data processed by a query. Used with the Google Cloud Pricing Calculator
- BigQuery Data Transfer Service to manage automatic data movement into BigQuery, offering connectors and pre-built load jobs with basic transformations.
- BigQuery geo Viz to visualize geospatial data
- BigQuery BI Engine: In-memory analysis service to speed up BI applications. No need to use OLAP cubes or separate servers. An amount of reserved memory can be defined.

### Streaming insert

Streaming data is not added to BigQuery with a regular load job, but Streaming inserts are used, which allow to insert into a table one item at a a time:

- Until the data is inserted into the table, it is held in a streaming buffer.

- These inserts have a cost, unlike load jobs.

- There is both a daily limit and a concurrent rate limit.

- It can be disabled best effort de-duplication by not populating the insert ID field, getting much higher streaming ingest quotas
- Used instead of batch loads when immediate availability is needed. If not, loading batch data is not charged.
- Concurrent entries have a limit, which is 1 million in the best of cases (Certain regions and some things configured).

### Machine Learning

BigQuery ML to create custom models using SQL.

Several model options for doing classification, regression, clustering or matrix factorization for a recommendation model. Use of different techniques like Boosted tree or Deep neural networks.

Steps:

1. Do feature engineering to choose the features and label.
2. Query to extract training data. Some transformations could be applied to the data. To automatically do the same transformations for data at prediction time, `TRANSFORM` can be used.
3. Specify model type and other hyperparameters. Use of `CREATE OR REPLACE MODEL ...`. Training and test dat is split automatically.

1. Evaluate the model. Use of `ML.EVALUATE(MODEL <model_name>)`.
2. Use the model to make predictions. Model is automatically available to serve predictions. Use of `ML.PREDICT(MODEL <model_name>)`

Also, a model trained externally can be brought to BigQuery and use it to make predictions.

## Cloud Functions

## <img src="https://miro.medium.com/max/512/1*ya5rb97H-xxwFK3-jd8Ujw.png" style="zoom:15%;"/> [Cloud Pub/Sub](https://cloud.google.com/pubsub/docs/overview)

Asynchronous messaging service, which allows tools to send, receive or filter events or data streams from publishers to subscriptors.

xxxxxxxxxxxxxxxxxxxxxxxxxxxxxx

Publishers or subscribers don't need to be online all the time

Stores messages for 7 days by default. Can be set to a minimum of 10 minutes and maximum of 7 days.

Messages encrypted in transit and in rest

Only messages created after the subscription are available by default

Filtered messages are automatically acknowledged and don't incur egress fees

One topic can have many publishers

Recommended to acknowledge the messages after properly processing them.

In pull: 1 is making the pull from subscriber to the topic, 2 is to send the messages to the subscriber and third the subscriber to acknowledge the messages.

In push: 1 is sending the message with an http call and 2 is the ack.

In push, the topic use the rate of success responses to self-limit itself so the subscriber is not overload.

Replay mechanism to to replay messages.

ack time can be changed by subcription.

Topic message retention gives flexibility to replay previous ack messages. Also allows to get messages before a subscription was done.

Snapshot??

Multiple subscribers can share a subscription, with each being distributed to only one of the subscribers.

Messages are sent as raw bites, so no only text can be sent. Also, images or audio.

The maximum size for a message is 10 Mb.

By default, Pub/Sub batches messages to prevent overhead. It can be disabled for lower latency.

If messages are in the same region and have the same ordering key, it can enabled message ordering in a subscription to receive messages in the same order that Pub/Sub receives them. Latency might increase.

In general, Pub/Sub does not guarantee messages to be sent in order. This is because a mesh network is used (Several devices offering a unified single service), so depending on the device the message goes, this can go at different speeds. To grant correct ordering, other techniques must be used.

Pub/Sub guarantees at least once delivery for every subscription. But the ack signal may be sent late or not sent due to network issues, so the message could be sent again, having the subscriber a message duplicates. Dataflow can handle this, since both messages will have the same messageID.

Some messages may not be able to be processed by a subscriber. For this:

1. By default, the message will keep being sent over and over if it is not ack.
2. An exponential backoff policy can be used to progressively add longer delay between tries to send the message.
3. After a specified number of tries, the message can be sent to a dead-letter topic (aka dead-letter queue).

Uses Cloud Logging, Cloud and IAM.

xxxxxxxxxxx

### Features

- Durable message storage until received by all subscribers
- Data storage is synchronously replicated in three zones (Two assured and best-effort for a third)
- Ensures that no message is lost and that all are sent to the subscriber in order
- Consistent, scalable and high available performance
- Scales global data delivery automatically
- It assures data producers not to change if data consumers do since it works as an intermediary which manages distribution
- Runs in any Google Cloud region and can be accessed by publishers and subscriptions within any region

### Process

1. A **topic** is created, which receives and stores **messages** (Combination of data and optional attributes in form of key-value pair, following a predefined schema) from one or more publishers
2. **subscriptions** allow subscribers to receive the messages from the topics.
3. It has to be chosen how the two messages delivery methods: Pub/Sub **pushing** them to the subscriber or the subscriber **pulling** them from Pub/Sub.
4. When a publisher sends a message, it is stored in the topic and the subscribers can access it.
5. When a subscriber receives a message, it sends an **acknowledgment** (aka ack) signal to Pub/Sub. If the message is not acknowledged before a deadline (10s default, 10 mins max), the message is sent again.
6. Messages can be filtered in a subscription for not delivering them to the subscriber. If that is the case, the ack signal is sent automatically. Message attributes can be used for defining filters.
7. When all subscribers of a topic send the ack signal, the message is deleted (By default) from the message queue.

Messages published before a subscription is created will not be delivered to that subscription.

There can be one publisher and several subscribers (one-to-many or fan-out),  several publishers and one subscriber (many-to-one or fan-in) or several publishers and subscribers (many-to-many).

> Pub/Sub diagram
>
> <img src="https://cloud.google.com/static/pubsub/images/many-to-many.svg" style="zoom: 67%;" />

It is offered **Pub/Sub Lite** as well, a lower cost version with a few limitations:

- Lower reliability
- It offers zonal (Data replicated in only one zone) or regional (Data replicated to a second zone asynchronously) topic storage
- Storage and throughput capacity must be reserved and managed, is not automatic
- Storage in a topic is limited
- Routes messages within a region or zone, so publishers and subscribers must do a connection to the region where the topic is located, adding latency

A publisher can be any application able to make HTTPS requests. The same for a pull subscriber, but a push subscriber ................................... Publishers are apps, webs, databases, iot devices and many other event producers. Subscribers are Dataflow or other similar data processing services.

The **cost** is usage based for regular Pub/Sub but based on provisioned capacity on Pub/Sub Lite.

Pub/Sub is intended for service-to-service communication and not with end-user or IoT clients.

Pub/Sub offers **integrations** with other GCP products:

- Dataflow and also Spark (particulary within Dataproc) for stream processing and data integration
- OAuth and IAM for authentication and security
- Monitoring and logging products
- Cloud Composer for orchestration

### Implementation

1. Create the topic
2. Publish to topic 

## <img src="https://www.iri.com/blog/wp-content/uploads/2021/11/google-bigtable-logo.png" style="zoom:3%;" /> Cloud Bigtable

noSQL key-value database for high performance applications.

### Characteristics

- Best used for big data loads, at least 300 GB, with very fast access needed. Up to billions of rows and thousands of columns
- Offers high-throughput of inserts, more than millions per second.
- Latency on the order of milliseconds.
- Great for time-series data
- Integration with the Apache ecosystem
- Increase of cluster size for a specific time of larger loads
- Optimal for storing time-series, graph or iot data
- Tables are grouped in instances, with a maximum of 1000 tables each
- Tables in development can be upgraded to production

### Use

Data is stored in a table with rows and columns. However, there is only one index column, the row key. However, since most of the SQL can not be used, it is called a noSQL database.

Columns can be grouped on columns families.

To retrieve data:

1. Scan the row key through all the table to get only the desired rows.
2. Resulting rows are sorted through the row key. Speed depends of the number row and the orderliness of the original data.
3. Not, it must be searched through the data columns to find the actual rows wanted.

Deleting and updating:

- When deleting a data, it is not done immediately, but it is marked for deletion, skipping it on next processings.
- When updating a row, the new version is appended to the end of the table and the old marked for deletion.
- Periodically, Bigtable compacts the table removing marked rows and reordering the new records. This is called the garbage collection.

### Optimization

- Allow Bigtable to learn about your patterns for automatic optimizations, using at least 300 GB and using it over a long enough period of time.
- Design a proper table schema to evenly distribute writes and reads through the cluster:
- Add nodes if necessary to see a linear performance increase.
- When adding or removing nodes, performance will decrease temporarily.
- Making requests to a table that has had a period of no usage has slower performance until the connection is warm again. To avoid this, some artificial traffic can be sent when there is no real use.
- Use SSD instead of HHD.
- Optimize latency keeping a cluster CPU load under 70% and under 50% if possible. Also, recommended below 60% of storage utilization per node.
- Use smaller rows for higher throughput and general better performance.
- Rows should not be more than 100 MB and a cell not more than 10 MB.
- Use a single table instead of several smaller with similar data
- Rows are sorted lexicographically with the row key. Take into account to distribute writes. With this, 3 > 20. However, putting a 0 first makes 20 > 03.
- Write the timestamp in reverse to keep the most recent data at the start of the table instead of the end.
- Bigtable does automatic data compression. It can not be configured but some keys makes it more efficient:
	- Patterned data instead of random data
	- Put identical values near, in the same or adjoining rows
	- Compress values larger than MiB before entering in Bigtable


- Try to have Bigtable and the clients in the same zone.

- Group related data for more efficient reads.

- To avoid slower performance, reading a lot of not-contiguous rows in a single read should be avoided if possible.

- Use column families for related.

- Create no more than a hundred column families.

- Choose short column names if possible.

- Use row keys to organize similar data.

- Put identical records in the same row or in adjacent rows.

- Replication allows to copy the data across different regions or zones within a region. Use it to increase availability and durability.

- With replication, a good practice is to use one cluster for reading only and other for writing. In case of failover, traffic automatically goes the other.

- Using replication increase read throughput. However, write throughput does not improve and can even worsen since new data must be replicated to all the other clusters.

- Replication is eventually consistent and faster the closer the zones are.

- Use Key Visualizer, a tool to analyze Bigtable usage patterns with visual reports. It shows different usage metrics per row key (Grouping them by delimiters) and over time has a heatmap.

- When doing a data update, it should be though for a single row needed to be updated.

- A table can have millions of columns but a single row should not have more than a hundred with values. Tables are sparse, so empty values in columns don't affect storage or performance.

- Columns should be treated as data. So, to store a table of relationships, the most normal way would be to create a row per relationship. Instead, it is better creating one row per person and then creating a columns for each friend. This method makes the table not to grow really long. However, many columns are created in the table, but it is not a table, since the problem is how many columns a single row has (About a hundred). Since data is sparse, columns having no value for a row won't affect storage or performance. The only limit is the case that is expected for people to have a lot of friends (And a lot of columns for that row then).

  | Jose  | Fred:book-club | Gabriel:work    | Hiroshi:tennis   |
  | ----- | -------------- | --------------- | ---------------- |
  | Sofia | Hiroshi:work   | Seo Yoon:school | Jakob:chess-club |

- If not sure about the most frequent future queries will be, one option is to store all the data for a row in one column, in a single protobuf (Serialized structured data, like a JSON, but smaller and faster), instead of several columns. Some advantages like storage saving or more flexibility, but messages always have to be deserialize and some natives features are disables.
- In case that several types of queries will be frequent, it may be better to store the same data in more in more than a single table, with different schemas. Each table should be optimized to carry out specific queries. 

### Row key design

- A row key should not follow a predictable order so it can be distributed across nodes
- Consider which are the most concurrent queries.
- Putting multiple identifiers can be useful, usually separating them with a delimiter.
- Usually, the prefix of the row key should be more common values and then more granular.
- Using a timestamp in the row key, latest records will appear at the bottom of the table. However, a reverse timestamp (Maximum value of the long integer in the programming language used minus timestamp) can be used to put the most recent records at the top of the table. With the RTS, only getting the first n rows can give the n most recent records, without having to search the whole table.
- Timestamp should not be at the start of the row key, since it would create a hotspot in a node. Put before a high-cardinality value like user ID.
- It should not be used a sequential numeric ID, mainly because new IDs tend to be more active, funneling requests in a few nodes. An alternative is reversing is reversing the ID, since the least significant bit is random so active users are distributed across the nodes.
- The shorter the row key the better, so using abbreviations for each identifier is recommended for better performance.
- Row key should make data to be grouped
- Row key should not identify a value that is frequently updated. For example, if reading some metrics from some devices every second, if having a row key like `<device_id>#<metric>`, each row would be updating continuously. This overloads the tablet storing the row. Also, since updating a row adds the new value and the old one is not instantly removed, the row could exceed its limit size. For this case, the recommended way is storing each new read in a new row, with a row key like `<device_id>#<metric>#<timestamp>`. Better approach since creating a new row isn't slower than creating a cell (A new cell with the new value inside the existing row). However, the best practice for this case will always be keeping data in memory in the application and write new rows periodically.
- It should not contain hashed values, since the Bigtable natural sorting order ability is lost. Also, key visualizer is not intuitive. Human-readable values should be used. Same applies for values expressed as raw byes, which should also be omitted.
- For a multi-tenancy use case (Similar data with the same data model for multiple clients stored in the same table), row key prefixes are used. It is actually the best way, instead of several smaller tables. Also, avoided the risk of passing the limit of 1000 tables per instance.
- Using PII (Personally identifiable info) should be avoided, also in column family names. This is because these are metadata, and it can be more exposed than actual data in actions like logging or encryption.
- Using domain names, save the reverse domain name is a good idea: `com.company.google`.

### Table design for time-series data

Storing this can be done in two main ways:

- Time buckets, where one row represents a specific period of time, like a full hour or day. If data inside will be more than 100 MB, make smaller buckets. If 100 measurements are being read, faster to retrieve them within a single row than from 100 rows. Also, data is better compressed. However, this schema pattern has a higher development effort.

	Two types of time buckets:

	- Adding new event data in a new column (Knowing the limit of 100 columns), while in the cell itself the timestamp is stored.

		| Row key                                             | <value_1>     | <value_2>     | <value_3>     |
		| :-------------------------------------------------- | :------------ | :------------ | :------------ |
		| <region_id>#<sensor_id>#<measure_id>#<period_range> | <timestamp_1> | <timestamp_2> | <timestamp_3> |

		Not ideal if need for measuring changes in the time series data. Also, storage saving by using column qualifiers as data.

	- Writing new event data in a new cell, storing different timestamped values in several cells within a row.

		| Row key                                | pressure                        | temp                           |
		| :------------------------------------- | :------------------------------ | :----------------------------- |
		| <region_id>#<sensor_id>#<period_range> | <value_press_1> (<timestamp_1>) | <value_temp_1> (<timestamp_1>) |
		|                                        | <value_press_2> (<timestamp_2>) | <value_temp_2> (<timestamp_2>) |
		|                                        | <value_press_3> (<timestamp_3>) | <value_temp_3> (<timestamp_3>) |

		Best fit for measuring changes in values over time.

- Single-timestamp rows, where each new event is stored in a new row. The timestamp value is stored in the row key suffix (Not prefix, in order to avoid hotspots).

	Two types of single-timestamp rows patterns:

	- Single-timestamp serialized, where all row data is stored in a single column using a serialized format like a protobuf (Protocol buffer)

		| Row key                               | measurements_blob |
		| :------------------------------------ | :---------------- |
		| <region_id>#<sensor_id>#<timestamp_1> | <protobuf_1>      |
		| <region_id>#<sensor_id>#<timestamp_2> | <protobuf_2>      |
		| <region_id>#<sensor_id>#<timestamp_3> | <protobuf_3>      |

		The storage efficiency and speed are advantages. However, inability to retrieve only certain columns of a row and need for deserializing.

	- Single-timestamp unserialized, where each data value is stored in its own column.

		| Row key                               | pressure        | temperature    |
		| :------------------------------------ | :-------------- | :------------- |
		| <region_id>#<sensor_id>#<timestamp_1> | <value_press_1> | <value_temp_1> |
		| <region_id>#<sensor_id>#<timestamp_2> | <value_press_2> | <value_temp_2> |
		| <region_id>#<sensor_id>#<timestamp_3> | <value_press_3> | <value_temp_3> |

### Architecture

- Data stored in the Google file system called Colossus.
- When using replication, data is stored in other Colossus location
- Colossus contains data structures called tablets used to identify and manage the data.
- Metadata about the tablets is what is actually stored on the Bigtable cluster (In the VMs).
- Writes are stored in Colossus's shared log
- With that scheme, three levels of operations:
	- Manipulate actual data.
	- Manipulate the tablets, which point the data.
	- Manipulate the metadata of the tablets.
- Rebalancing tablets is very fast since only pointers are updated.
- Automatic optimization, detecting tablets with a lot of use to divide it in two or moving tablet pointers between the cluster VMs.
- If a node is lost, fast replacement since only metadata must be copied to the new VM.

<img src="https://cloud.google.com/bigtable/img/bigtable-architecture.svg" style="zoom:65%;" />

### Table structure

- Columns grouped in column families
- One row key-column can have several cells, each with a unique timestamp
- If a column is not used in a row, it does not take space

<img src="https://cloud.google.com/bigtable/img/storage-model.svg" style="zoom:80%;" />

### Security

- Access control with IAM at project, instance or table levels

- Use of customer-managed encrypted keys if wanted

- Creation of backups of a table's schema and data

- It can be defined which cluster the requests will use: A specific one or the nearest one within a group or total

## <img src="https://www.zdnet.com/a/img/2017/11/14/a0641c5a-1404-4ed6-a564-43931e35cb2f/spanner-logo.png" style="zoom:15%;" /> Cloud Spanner

Relational database available globally.

### Features

- Fully managed deployment, replication or maintenance.
- Data reads and writes up-to-date globally.
- Multi-region instance option.
- Use of schemas, SQL, ACID transactions.
- Backups stored up to a year (Export to keep them longer).
- Point-in-time recovery function to see the state of the database in any moment of the last 7 days.
- 99.999% availability for multi-regional instances and 99.99% for regional ones.
- Up to about 10000 read queries or 2000 write queries per node if the instance is in good condition. A bit less for multi-region instances.
- No need for maintenance windows.
- Use of IAM (At database level), Cloud Logging, Monitoring and Cloud KMS.
- Integration with BigQuery and Vertex AI.
- Key Visualizer and Query Insights to analyze performance.
- Automatic data sharding (Varying key ranges for each partition) for performance increase.
- Can be accessed through the PostgreSQL ecosystem to use many of its features with PGAdapter.
- Costs for storage, compute, backups and potential costs for network egress. 20% or 40% saving committing to a certain use for at least one or three years.
- 3 months free trial with up to 10 GB.

### Deployment

- Instance creation, where is defined:
  - Regional or multi-region (Can be later moved if some conditions are met):
  	- If the clients are within a region, the regional option is better.
  	- A multi-region offers lower read latency but higher write latency.
  	- For multi-region, these can be within one or more continents.
  - Compute capacity (Resources available to the databases) as a number of nodes or processing units (1 node = 1000 processing units) in batches of 100 pu.
    - At least 1 node recommended. Each node is assigned a task, and having multiple increases performance and allows for database splits.
    - Limits storage capacities:
    	- 1 node or more: 4 TB per node
    	- Less than a node: 409.6 GB per 100 pu.
    - Resources can be later increased and if possible, decreased.

- Creation of a database, which contains tables, views and indexes within schemas. It is chosen
  - Dialect: Either PostgreSQL or Google Standard SQL.
  - Permissions with IAM and encryption.
  - Default leader region.
  - Other configurations like retention policy or query optimizer.

- Creation of tables:

  - Defining the data type of each column.

  - Defining the primary key. It can be one more columns or a specified value. Using a monotonically increasing is not recommended, since data is distributed in shards by the PK range, so all new created data would be written in the same shard, creating a hotspot. So, techniques to avoid this:

  	- Hash the key and use it alone or combined with the key.
  	- Put the key after other column.
  	- Use a Universally Unique Identifier (Version 4 recommended) instead. However, locality of related rows is lost.
  	- Bit-reverse the key.

  	For timestamp-based keys, the key column should use a descending order to have closer the most recent data.

  - Add secondary indexes (Besides the primary that is the primary key). Useful if queries accessing the PK in reverse order are frequent.

- Creation of views:
	- Read only.
	- The view query can't have query parameters.
	- Can't be indexed.


### Schema

Group of tables with parent-child relationships between them. Two ways of defining them:

- Table interleaving: Child rows are physically stored co-located with the parent rows:

	- The relation can only be done with their primary key columns.
	- It can be created a hierarchy of up to seven tables.
	- Created with DDL and by including the parent table primary key as the prefix of the child table primary key.

	For example, if there is a table of customers and other of payments, and payments are frequently queried by customer, payments can be defined as an interleaved child table of customers. So, one or more rows of payments will be with one row of customers.

	<img src="https://cloud.google.com/static/spanner/docs/images/singers_albums_interleaved_physical.svg" style="zoom:60%;" />

- Foreign keys in a child table referring the primary key of a parent table. Spanner ensures the integrity of the relationships are maintained, so transactions no fulfilling them fail.

### Best practices

- Keep CPU usage under 65% regional instances and 65% for multi-regions's.
- Design a schema to avoid hotspots
- The region with the highest write load is where the leader shards should be.

### Arquitecture

Instances are a collection of databases and compute resources (Storage and compute are de-coupled).

The storage is divided into shards or splits, being each one a range of rows after being ordered by the primary key.

For each group of identical splits distributed into the different instances, one of those is the leader and the rest the followers. The write operations are first done in the leader shard, so its optimal to put the leader where the most write operations come from.

When a write request is generated:

- The request always goes first to the leader.
- The leader shares the request with other replicas forming a write quorum. The quorum votes if the write should be committed.
- If the result is yes, the write is done and replicated through the replicas.

For a read-only request, it is done on the closest replica. If the request is heavy, the replica must first communicate it to the leader.

In the case of a regional configuration:

- 3 read-write replicas in different zones within the region.
- Additional read-only replicas in other zones can be added.
- Between the same shards of all the replicas, one of the them is the leader and then the rest are the followers.
- The operations are carried on the leader first and then propagated to the followers.
- If a operation affects different shards whose leaders are in different zones, one of the zones is assigned as the zone coordinator.
- In case of a leader failure, a follower becomes the new leader.

In the case of a multi-region configuration:

- Two read-write regions with two read-write replicas each. One of the two regions is the default leader region (For each data split or shard). Also, a witness region with an additional replica, mainly used for voting in writes.
- Additional read-only replicas in other regions can be added.
- Write operations are first performed in the leader region and then propagated.
- In case of of failing, a the other shard in the same region is the new leader and if both legion leader shards fail, a shard in the other read-write region is the new leader. Usually when the original leader goes back to its initial state, it is the leader again.

Use of TrueTime, a protocol to use a unified timestamp for every operation through any region using atomic clocks.

## <img src="https://interpolados.files.wordpress.com/2018/10/google-cloud-sql.png?w=640" style="zoom:12%;" /> Cloud SQL

Fully managed service for third party RDBMSs: SQL Server, MySQL and PostgreSQL.

99,95% availability.

Vertically scalable, upgrading the machine size. Up to 64 processor cores and more than 100 GB of RAM.

Horizontally scalable with read replicas, up to 3. For horizontal read-write scaling, Cloud Spanner must be considered.

Accessible by Google Cloud and external services, like App Engine, Compute Engine.

Default choice from OLTP workloads.

Optimized for high-throughput writes

Row-based storage

### Security

- Data is encrypted.
- Securely stored up to 7 backups, included in the cost of the instance.
- Easy to restore from a backup and recover to a specific state of an instance.
- Instances include a network firewall, allowing to control network access to the instance by granting access.
- Support for failover: Instances can be have a failover replica in different zone in the same region. Data is replicated in both zones. If a datacenter outage happened where the main instance is, the replica automatically becomes available in the zone affected as well. Considerations:
	- Failover replicas are charges as a separate instance.
	- When failover occurs, existing connections to the instance are closed, but reconnecting can be done with the same connections string or IP address.
	- After the failover, the replica becomes the primary, and a new replica is created in a third zone.
	- If the instance was located intentionally in a zone to be close to other resources, the new primary instance can be relocated there again when the affected zone is available. If it is not the case, no need to relocate the new primary instance.
	- The failover replica can also be used a read replica to offload read operations from the primary.

### Setting up



## Cloud Datastore

## Cloud Composer

Serverless orchestration tool to coordinate multiple services. Based on the open source orchestration engine Apache Airflow. Use of DAGs to create the flow instructions.

The service is ordered as: Environments -> Airflow instances -> DAGs (Each being a Python file). The environments are created on Cloud Composer and then the Airflow web server is used.

It can be created environment variables, but it is more common to create them at the instance level. Parameters can be made dynamic using macros.

The code of the airflows (Written in Python) are stored in a Cloud Storage Bucket.

Inside a DAG, there a series of user created tasks that invoke predefined operators (e.g. A task using the Dataflow Python operator). Usually, there is one operator per task. There are operators for a lot of services, in Google Cloud, other Cloud or on-premise solutions. These depend of the Apache Airflow project.

Once the DAG is in the folder, in the Airflow web server it appears and can be seen graphically (With nodes and edges).

A DAG can take several paths (Or workflows) depending on results of previous nodes. However, all this possible workflows have common operators used, which are executed anyway.

Each operator in the DAG has parameters to define how and where will work.

After defining all the tasks, it must be specified the which affect which.

Workflows can be automatized in two ways:

- Scheduled, or pull mode, on periodic times
- Trigger based, or push mode (Cloud Functions is used for this).

In a case of a transient bug occurred, it can be set a number of attempts to try to execute the DAG.

In the UI, it can be monitored the runs of the DAGs. Three different states can appear: Success, running or failure. It can be also be seen the task that failed in the run and it's logs. Other way to see logs is with Cloud Logging, where the logs of the other executed Google Cloud services go.



Only the workflows in the /dags folder of the bucket are scheduled.

In each run, each task is a task instance, which can be in state running, success, failed, skipped, etc.

## Cloud Data Fusion

Fully managed no-code data integration service for quick data pipelines.

### Characteristics

- Integrate with any data.
- API for scripts to automate execution.
- Extensible (Ability to templatize pipelines or create triggers)
- Templates available.
- Hub with plugins or prebuilt pipelines.
- For batch pipelines.

### Components

- Wrangler UI for exploring data visually. Transformations can be done which will create a pipeline.
- Control Center: Section in the UI for seeing the applications, artifacts and datasets.

- Data pipeline UI for drawing pipelines.
- Rules Engine tool where users can program all their checks or transformations to be used by data engineers later as a rulebook.
- Metadata aggregation tool to access the lineage of each field and other metadata.
- Microservice framework to build specialized logic for processing data.
- Event Condition Action application to parse events, trigger conditions or executions consequent actions.

### Building a pipeline

Pipelines are visually represented as a series of stages (Nodes) arranged in a graph The graphs are the DAGs (Directed Acyclic Graphs).

Before putting them in production, use the preview mode, where can be seen in detail what is happening in each node.

The pipelines are transformed into an Spark or MapReduce program in an ephemeral Cloud DataProc parallel cluster.

## Dataproc

Managed service for batch processing, streaming, querying and machine learning. Service for the Hadoop ecosystem.

### Against on-premise

Compared to on-premise, using Hadoop on Dataproc gives some advantages:

- Managed hardware and configuration.
- Simplified version management.
- Flexible job configuration.
- Automatic cluster turning off for saving.
- Built-in integration with services as Cloud Storage BigQuery or Bigtable.
- Integration with Cloud Logging or Cloud Monitoring for a complete data platform.

Also, it is very direct to move works on-premise to the cloud, with minimal adpatation:

- No need to learn new tools or APIs.
- Can use existing libraries or documentation.

### Hadoop Ecosystem

The idea of Hadoop was to distribute processing among a cluster. HDFS (File system) stores data on machines in the cluster and MapReduce provided distributed processing of the data.

Software grew around, like Hive (SQL DW), Pig (Scripting) or Spark (data processing). Ecosystem used for on-premises workloads.

Apache Hadoop is an open source project that maintains the framework for distributes processing of big data.

### Characteristics

- Fast and scalable (Clusters can be quickly scaled with many options)
- Open source escosystem
- Fully managed
- Versioning (Switch between versions of the tools)
- Integrated with other services.
- Highly available (Jobs that can restart on failure).
- Cost effective (And only paid when used).
- Developer tools to manage the service: Cloud Console, Cloud SDK, APIs or SSH access.
- Manual control of the server if desired.

### Arquitecture

A Dataproc cluster has:

- Primary nodes (VMs in Compute Engine)
- Primary workers (VMs in Compute Engine)
- Optional secondary workers (Preemptable VMs in Compute Engine, which can be abruptly disconnected).
- Storage disks (HDFS)

Worker nodes can be part of a managed instance group, to ensure that the all the VMs are from the same template.

HDFS storage goes down with the cluster, so is a best practice to use external storage. Instead of using the native HDFS, a cluster of buckets on Cloud Storage could be used using the HDFS connector. The code adoption for that is pretty easy (Changing the prefix HDFS// to GS//).

Other storage options are Bigtable, an alternative to HBase for sparse data, or BigQuery for analytical workloads.

### Configuration

Five steps:

1. Setup: Creating a cluster, from several places:

  - Cloud Console or command line using the gcloud command.
  - YAML file new or from an existing project.
  - Terraform configuration
  - REST API with Cloud SDK

2. Configuration:

  - Single VM (Usually for development), standard with one primary node or high availability with three nodes.
  - Selection of a region and zone (Increased isolation in some cases and latency) or select global (Default).
  - Selection of worker nodes (Default two) and preemptible nodes with their VM options like VCPU or memory.
  - Choose optional components like Anaconda, Hive, notebooks, etc.
  - Initialization actions to customize the cluster with executables or scripts to run on the nodes after the set up.
  - Defining cluster properties (Runtime values used by configuration files) or user labels.

3. Optimization:

	- Data and cluster in close locations. Increase of latency between regions, and maybe even performance.
	- Check that network traffic is not funneled creating bottlenecks.
	- Try not to deal with more than 10000 input files, combining or appending them if that is the case (If that is done, but the setting fs.gs.block.size to a larger value).
	- Check that a persistent disk is not limiting the thorughput.
	- Choose enough virtual machines for the cluster (Prototypes with real data recommended).

4. Utilization:

  Jobs can be submitted through the console, the gcloud command, the API or orchestration services like Cloud Composer or the Dataproc Workflow Templates. Hadoop's direct interfaces is disabled by default and not recommended.

  Jobs are not restartable by default but can be created jobs that do are.

5. Monitoring:

  Cloud Logging to check all the driver and executioners output (Warning: Can not be obtained if a job is submitted connecting directly to the primary node using SSH). Logs can also be found in a Cloud Storage bucket or in files inside the cluster (Available with the Spark Web UI).

  Cloud Monitoring to monitor the use of CPU, disk or network.

### Cloud Storage instead of HDFS

Low network speeds caused data to be kept close, but not needed anymore. On-premise Hadoop clusters need disk, since they not only compute data, but store it as well. This storage can be directly put on HDFS on the cloud, but it is not an ideal solution, since storage is tied to compute, not elastic or not automatically rescaled.

With Google Jupyter, a network of one petabit per second, the storage needs of Dataproc clusters can be separated in Google Cloud storage services. This allows to clusters to be treated as ephemeral resources, which don't need to cost any money if a job is not running.

Cloud Storage, compared to HDFS:

- Tends to be faster, cheaper
- Less maintenance
- Better connected to other services on the cloud
- Charges only for the storage used at the moment (With HDFS, it must be over-provisioned disk space for the future).

However, using HDFS is still a good option if:

- Jobs doing a lot of metadata operations (Usually with small files).
- Frequently changing directories (Cloud Storage does not have a file system and objects are immutable, hence these must be copied with new name and deleted) or modifying data.
- Append operations frequently used on HDFS files.
- Workloads with heavy IO.
- Latency is crucial.

In these last cases, Cloud Storage is still recommended to be the initial and final source of data, but with HDFS being the source of data between jobs.

Even if all data is moved to Cloud Storage, the cluster still needs HDFS for certain operations (Like recovery files or logs). Also, non-HDFS local disk space is needed for shuffling.

Resizing the local HDFS total size:

- To reduce size, reduce the size of the primary persistent disks for the primary and workers (Keep at least 100 GB since it has the boot volume and system libraries).
- To increase size:
	- Increase the size of the primary persistent disk for workers.
	- Attach up to 8 SSDs to each worker (For critical latency and IO workloads), first checking if the worker machine can support them.
	- Use SSD persistent disks for the primary or workers as primary disk.

For migrating the data, the tool DistCp can be useful. After changing HDFS to Cloud Storage, all jobs will still work without problem.

The change of storage can also be done on non-cloud Hadoop clusters.

Decoupling storage and having ephemeral clusters, allows to create many of them focused in certain jobs. This is called the ephemeral model. Even clusters after a certain idle time can be auto deleted.

### Dataflow Workflow template

YAML file processed through a DAG (Directed acyclic graph) which makes it active. Can be sent several time with new paramters. It can do thinks like creating, choosing or deleting a cluster or submitting jobs.

Available through the gcloud command or the API (Not with the Cloud Console).

In the file, it is defined:

- Things to be pip-installed in the cluster.
- Create the cluster, adding parameters like the template to use, the architecture desired or the images version wanted.
- Steps for a job in the cluster.

The file can be stored in Cloud Storage per example, and submitted as a workflow template.

### Autoscaling workflow

Dataproc can autoscale the size of clusters:

- Based on Hadoop YARN metrics
- Works for clusters with decoupled data.
- It does nor support Spark Streaming.
- Can not scale to 0, so not for sparse or idle clusters (For this case, the cluster can just be taken down and then created again, using a orchestration tool if needed).
- Important to set the initial workers to avoid it to a several autoscales at start.

Benefits:

- No need to monitor the resource usage to adapt the size.
- More fine-grained control.
- Autoscaling decisions available on Cloud Logging.

## Compute Engine

Creation of snapshots to directly recreate the instance. No need to export the snapshots.

### Quotas

Quotas restrict how much resources can be used in Compute Engine.

- The limits are assigned to the project based on the project zone and the user account history with Google.

- Each region has independent quotas.

- Prevents spikes in usage.

- When a quota is exceeded, access to the resource is blocked, causing the task to fail.

- Quotas are shared by all services in most cases. Different types of quotas:

	- Number of IP addresses available. Limits the number of VMs that can be launched using an external IP address. If a VM does not need access to external APIs or services, an internal IP can be used, if not, an external IP is needed to have an internet access to APIs or services outside Google Cloud.

	- Number of CPU. Limits the total of CPU nodes within all the VMs. Using VMs with more nodes will cause the quota to trigger with less instances.
	- Persistent storage for both HDD and SSD. Each type has an independent capacity limit.

	- Other quotas for GPUs, HDDs or SSDs.

- 

## Other services

### Datastream

Reads and delivers changes (Insert, update and delete) from a database origin (MySQL, PostgreSQL, AlloyDB or Oracle) to an output GCP database (BigQuery, Cloud SQL, Cloud Storage and Cloud Spanner)

## Cloud Composer

## Kubernetes Engine

## Notebooks

## Vertex AI

Fully managed service for custom machine learning models. For both training and serving. Can build ML pipelines for the different steps.

### Characteristics

- Scales to production.
- Can use libraries as TensorFlow or XGBoost.
- Can transform input data.
- Includes parameter tuning.
- Include ML pipelines:
	- Can be built with Python SDK.
	- Scalable.
	- Store metadata and lineage.
	- Tools for monitoring.
	- Secure.
	- Cost-effective.

### Notebooks

Jupyter notebooks can be created, which allow for custom CPU and GPU. Notebook instances use Compute Engine. These run the latest version of JupyerLab. Integrated with Git by default and provide connectors with BigQuery.

There notebooks include a magic function %bigquery to directly apply queries to the service and save the results. Pulling only a sampling subset recommended, or memory errors can happen.

```
%%bigquery df
SELECT...
```

```
df.head()
```

## AutoML

Creation of ML models with no code.

Follows a standard procedure of three phases:

- Train after preparing a proper dataset. AutoML can receive a dataset from the web UI or a dataset using some programming language. Can also be used a CAV in GCS where the files to be used are specified (These can be labeled to use for train, validation or test, or AutoML can do it randomly. AutoML checks if the data passed is enough.
- Deploy. Every version train with new data creates a new model. These can not be exported.
- Serve it in a server. It can be used in the web UI, through the command line with CURL or using the API.

AutoML makes almost everything in the deploy and serve phases as well.

Models not used are deleted after a period. Even those used are removed eventually. This is due to the service making constant changes which could be incompatible to old models. Best practice is to constantly retrain the model with new data.

Different versions depending on data: AutoML Vision, AutoML Natural Language and AutoML Tables.

Suited for making several specific models instead of a single one.

Both model creation and service requests apply for quota.

## Looker

## Data Studio

To visualize data.

When adding a source to a report, other people who can view the report can potentially see all data in that data source and anyone who can edit the report can also use all the fields from any of the sources to create new charts.

## <img src="https://wursta.com/wp-content/uploads/2021/03/cloud-dlp-solution-d26cda4b-39ea-3586-ba91-bcf79110892f-1554235897478.png" style="zoom:17%;" /> Cloud Data Catalog

Fully managed and scalable system to make metadata about data from different services and projects available to search for the users. It can be added information like tags, flag sensitive columns, etc. Each user can have a overall view of the data he has access to.

Use of the UI or API to cataloging data.

Provides tags of different types, not only strings.

Usually used with the Cloud Data Loss Prevention API to classify sensible data.

Foundation for data governance.

## <img src="https://www.jhanley.com/wp-content/uploads/2019/01/Cloud-IAM.png" style="zoom:17%;" /> IAM

Access control for cloud resources.

###  Characteristics

- Granular access within services
- Recommender can detect and alert about unwanted permissions to resources
- Full audit trail history of permissions given and removed
- Dynamic access based on contextual attributes like IP, data or security status
- No charge
- Support for standard accounts

### Parts

- Principal: Who access a cloud resource. An email address to identity it. It can be:
	- Google Account (Of end users)
	- Service account (For applications and compute workloads). Associated with a public/private RSA key pair. Two types:
		- Google-managed Service Account or Service agent. Created automatically by Google (If Pub/Sub writes to BG, a service agent with BG roles are given to Pub/Sub, per example). Not recommended to change their roles
		- User-managed service account. Explicitly created or when specific services are created like Computer Engine or App Engine
	- Google group (Collection of Google accounts and service accounts)
	- Google workspace account
	- Cloud Identity domain (Using third party identity providers)
	- All users or all authenticated users
	- Being GC structured as Organization, Folders, Projects and Services, IAM can be configured at all levels
- Role: Collection of permissions (Specified in the form service.resource.verb and usually correspond to one API method), which allow to do specific actions. There are basic, predefined (For specific functions like Pub/Sub subscriber) and custom roles.
- Allow Policy: Collections of bindings between principals and roles.

## Cloud Data Loss Prevention

Service to understand and manage personally identifiable information (PII). Provides fast and scalable classification and reduction for sensitive data like addresses or credit card numbers.

The API detect PII when an input is passed. Findings are classified in different categories depending of how confident the API is that are actually PII.

An of how the API works can be found [here](https://github.com/GoogleCloudPlatform/training-data-analyst/blob/master/courses/data-engineering/demos/cloud_dlp.md).

Use of the API to redact.

## <img src="https://miro.medium.com/max/440/1*gN2yiS3EtraUpVD_E41Ebw.png" style="zoom:9%;" /> Cloud Logging

Collects data from Google Cloud services automatically or from any custom log source.

Sinks control where the logs are routed. These can be configured to send logs to:

- Cloud Logging buckets to store them inside the application for a customizable period

- Cloud Storage to store them
- BigQuery to do analytics
- Pub/Sub to send them to third party platforms

In Cloud Logging buckets there are by default the buckets `_Default` and `_Required`. The first is customizable but the last isn't, which receives specific types of logs which can not be routed elsewhere. Additional buckets can be created.

The Logs Explorer interface allows interact with the logs. It also allows to create metrics based on the logs which can be then sent to Cloud Monitoring.

The error logs are automatically analyzed by Error Reporting service for a centralized error management interface.

>  Cloud Logging infrastructure
>
> ![](https://cloud.google.com/static/logging/docs/images/routing-overview-17-09-21.png)

## Data migration

### Storage Transfer Service

Used to move data between place, once or periodically (As part of a pipeline or workflow):

- To GCS from a local or other cloud provider origin.
- Between GCS buckets.
- From GCS to a local or cloud file system.
- Between file systems.

### HDFS to GCS

Use of Hadoop DistCp to copy data from HDFS to Cloud Storage. Two methods:

- Push model, where files are directly uploaded from the source cluster to GCS.
- Pull model, where ephemeral Dataproc clusters pull the files from the source clusters and copies them to GCS. More complex but recommended if the source cluster will be used while migrating. Also, no need to install the Cloud Storage connector on the source.

### Transfer Appliance Service

Offline process for large amounts of data. Offline process. Google Cloud ships an appliance where data is upload to a GCS bucket, then it is sent to a Google Cloud facility where it is uploaded.

The appliance is about the size of a suitcase, resistant and sealed and comes in two capacities, 40 TB and 300 TB. A rack format is also available.

Useful for data collection, replication and migration.

## Best practices

### Use hot pats and cold paths

Divide input data if it needs to be accessed in real time (hot) or it can have a delay (cold):

- Log integrity: Can see complete logs, none is lost due to streaming quota limits
- Cost reduction: Streaming inserts (To a database) are more expensive than inserts from batch jobs (From Cloud Storage)
- Reserved query resources: Moving logs with lower priority to batch assures these don't have an impact on reserved query resources

In the messaging component (Pub/Sub in this case) is recommendable to use a single topic to send both the hot and cold data. It is easier to adapt a dataflow job than deploying a new version of the ingestion system which affects the clients.

> Architecture example
>
> ![](https://cloud.google.com/static/architecture/images/large-scale-general-ingestion-paths.svg)

## BigQuery practice

### View metadata

There are read-only views providing metadata about the datasets like:

-  `__TABLES__` has metadata about all the tables of the dataset. Info like size, creation time or row count.
-  `INFORMATION_SCHEMA_COLUMNS` has metadata about all the columns of the dataset. Info like data type, default value or if the table is partitioned by that column.

### Arrays

- List of items in brackets
- All elements must be of the same group of data types (Not string with int, but int with float ok)
- The only data type that can not include are arrays.
- When looking at a table schema, if the Mode attribute says REPEATED, it means that the column is an array

Arrays are shown by default flattened (In different rows, even though all arrays element are actually in the same row). In the example below, the table has only two rows, but fruits is an array:

| row  | person    | fruits     | total_cost |
| ---- | --------- | ---------- | ---------- |
| 1    | sally     | raspberry  | 10.99      |
|      |           | blackberry |            |
|      |           | strawberry |            |
|      |           | cherry     |            |
| 2    | frederick | orange     | 5.55       |
|      |           | apple      |            |

That table can also be interpreted like:

| row  | person    | fruits                                      | total_cost |
| ---- | --------- | ------------------------------------------- | ---------- |
| 1    | sally     | [raspberry, blackberry, strawberry, cherry] | 10.99      |
| 2    | frederick | [orange, apple]                             | 5.55       |

So, using

```sql
SELECT
['raspberry', 'blackberry', 'strawberry', 'cherry'] AS fruit_array
```

returns visually 4 rows is actually a single one.

### Creating arrays

Regular fields can be shown as arrays using `ARRAY_AG(<column>)` allows to show regular fields as arrays (It can also be used in window functions).

Other functions:

- `ARRAY_LENGTH(<array>)` allows to se the size of the array.
-  `ARRAY_AG(DSITINCT <column>)` creates an array with only the distinct values.
- `ARRAY_AGG(<column> ORDER BY <column>)` orders the elements in the array.
- `ARRAY_AG(<column> LIMIT n)` creates the array with only the n first elements.

Using

```sql
SELECT
  col1,
  col2,
  ARRAY_AGG(col3 ORDER BY col3) AS col3_all,
  ARRAY_LENGTH(ARRAY_AG(col3)) AS col3_len,
  ARRAY_AGG(DISTINCT col3) AS col3_dist,
  ARRAY_LENGTH(ARRAY_AG(DISTINCT col3)) AS col4_dist_len,
  
  FROM `<dataset>.<table>`
GROUP BY col1, col2
```

will return a row per column1 and column2 values, column3 and column4 shown as arrays and also the length of this arrays for each row. Something like this:

| col1  | col2  | col3_all | col3_len | col3_dist | col3_dist_len |
| ----- | ----- | -------- | -------- | --------- | ------------- |
| value | value | value    | 4        | value     | 3             |
|       |       | value    |          | value     |               |
|       |       | value    |          | value     |               |
|       |       | value    |          |           |               |
| value | value | value    | 2        | value     | 1             |
|       |       | value    |          |           |               |

Query above will return a single row for each pair of column1 and column2 values. Different values of column3 and column4 will appear flattened vertically.

### Querying arrays

If trying to query an array, it must be broken first into rows. In the following example, col2.col1 is an array, so it will give an error

```sql
SELECT
  col1,
  col2.col1
FROM `<dataset>.<table>`
```

because the col2 is an array and has all its values in a single row. Values must be broken into one row per value. That is the use of `UNNEST(<column>)`

```sql
SELECT DISTINCT
  col1,
  st.col1
FROM `<dataset>.<table>`,
	UNNEST(col2) AS st
```

It will return the table in a traditional way, with col1 repeated for each col2.col1 value.

### Struct

- Allows to group similar fields together.
- These can be though as a table inside a main table.
- Fields can have the same or different data types for each field.
- In the schema view, the type is RECORD.
- There can be Structs inside Structs.

An example of a struct, where device.type and device.price are the fields of the device struct.

| order | date       | device.type | device.price |
| ----- | ---------- | ----------- | ------------ |
| 342   | 04/10/2022 | tablet      | 499          |
| 343   | 04/10/2022 | tv          | 699          |

To query all fields of an struct, like col2 or col3 could be, use *:

```sql
SELECT
  col1,
  col2.*,
  col3.*
FROM `<dataset>.<table>`
```

It can be created an struct manually

```sql
SELECT STRUCT("tablet" as type, 499 as price) as device
```

which would give

| row  | device.type | device.price |
| ---- | ----------- | ------------ |
| 1    | tablet      | 499          |

### Arrays and Structs combined

Several splits can be stored in an array. It significantly increases performance avoiding doing joins between tables. Example of a table with arrays of structs (Nested and repeated columns):

| OrderID | Date       | cart.Product | cart.Quantity |
| ------- | ---------- | ------------ | ------------- |
| 3493    | 04/10/2022 | Table        | 1             |
|         |            | Chair        | 4             |
|         |            | Carpet       | 1             |
| 2322    | 04/10/2022 | Shelf        | 3             |
|         |            | Desktop      | 1             |

An array can be one of the values of a struct:

```sql
SELECT STRUCT("tablet" as type, [499, 349, 299, 199] as prices) AS device
```

which gives something like

| row  | device.type | device.prices |
| ---- | ----------- | ------------- |
| 1    | tablet      | 499           |
|      |             | 349           |
|      |             | 299           |
|      |             | 199           |

Which can be intrerpreted as

| row  | device.type | device.prices        |
| ---- | ----------- | -------------------- |
| 1    | tablet      | [499, 349, 299, 199] |

Suppose we have the following table

| row  | race | participants.name | participants.splits |
| ---- | ---- | ----------------- | ------------------- |
| 1    | 800M | Rudisha           | 23.4                |
|      |      |                   | 26.3                |
|      |      |                   | 26.4                |
|      |      | Murphy            | 23.9                |
|      |      |                   | 26.0                |

If we want to list the name of all the runners and the type of race, we could have an error if doing it directly

```sql
SELECT race, participants.name
FROM `<dataset>.<table>`
```

because the columns have different granularity. Race would return one row and participants.name would return two. What we actually want are two rows with both names and the field race to be repeated. For this, a CROSS JOIN should be done with the main tables with the races and the internal table (The struct) with the participants of each race:

```sql
SELECT race, participants.name
FROM `<dataset>.<table>` as tab
CROSS JOIN
tab.participants
```

Instead of putting CROSS JOIN, simply putting a comma works the same way:

```sql
SELECT race, participants.name
FROM `<dataset>.<table>` as tab
	, tab.participants
```

This would return one row per participant and the race of the participant.

| **Row** | **race** | **name** |
| ------- | -------- | -------- |
| 1       | 800M     | Rudisha  |
| 3       | 800M     | Murphy   |

In case of having more than one race, it may look like a CROSS JOIN would associate every racer name with every possible race. However, it does not, since it is a correlated cross join which only unpacks the elements associated to each row.

How many racers were there in total in the races and participants table?

```sql
SELECT COUNT(par.name) AS total_participants
FROM `<dataset>.<table>` as tab
	, UNNEST(tab.participants) as par
```

Which gives:

| row  | total_participants |
| ---- | ------------------ |
| 1    | 2                  |

To get the participants of each races, the result could be grouped by race.

In the same table, to list the total race time of racers, ordered by fastest total time, UNNEST() must be used:

```sql
SELECT
  par.name,
  SUM(split_times) as total_race_time
FROM `<dataset>.<table>` AS tab
	, UNNEST(tab.participants) AS par
	, UNNEST(par.splits) AS split_times
GROUP BY par.name
ORDER BY total_race_time ASC
```

This gives

| row  | name    | total_race_time |
| ---- | ------- | --------------- |
| 1    | Rudisha | 76,1            |
| 2    | Murphy  | 49,9            |

In the same table, knowing that the second fastest lap is 23.9, check who is the runner of that lap.

```sql
SELECT
  par.name,
  split_time
FROM `<dataset>.<table>` AS tab
	, UNNEST(tab.participants) AS par
	, UNNEST(par.splits) AS split_time
WHERE split_time = 23.9
```

This returns:

| row  | name   | split_time |
| ---- | ------ | ---------- |
| 1    | Murphy | 23.9       |

### Create partitioning and clustering in a table

In the next example a partitioned and clustering based on the data of other table. The new table is partitioned by col4 and clustered by col2. The partitions will be deleted after three days.

```sql
CREATE TABLE <dataset>.<table2>
(
col1 NUMERIC,
col2 STRING,
col3 STRING,
col4 TIMESTAMP,
col5 GEOGRAPHY
)
PARTITION BY DATE(col4)
CLUSTER BY col2
OPTIONS
(
	partition_expiration_days = 3,
    description = 'cluster'
)
AS SELECT * FROM <dataset>.<table2>
```

## Pub/Sub practice

### Create a topic and send messages

```python
import os
from google.cloud import pubsub_v1

publisher = pubsub_v1.PublisherClient() # publisher client

topic_name = 'projects/{project_id}/topics/{topic_name}'.format(
	project_id = os.getenv('GOOGLE_CLOUD_PROJECT'),
    topic = 'MY_TOPIC_NAME'
)

publisher.create_topic(topic_name)
publisher.publish(topic_name, b'Hola', autor = 'Sergi') # message sent in bytes. author is an attribute
```

### Create a pull subscription

First, the subscription is defined. It is also defined what to do with incoming messages.

```python
import os
from google.cloud import pubsub_v1

subcriber = pubsub_v1.SubscriberClient() # subcriber client

topic_name = 'projects/{project_id}/topics/{topic_name}'.format(
	project_id = os.getenv('GOOGLE_CLOUD_PROJECT'),
    topic = 'MY_TOPIC_NAME'
)

subcription_name = 'projects/{project_id}/subcriptions/{sub}'.format(
	project_id = os.getenv('GOOGLE_CLOUD_PROJECT'),
    sub = 'MY_SUBCRIPTION_NAME'
)

subcriber.create_subcription(
    name = subcription_name, topic = topic_name)

# actions to be done when a message is received
def callback(message):
    print(message.data) # print the content of the message
    message.ack() # acknowledge to the topic that the message has been obtained

future = subscriber.subcribe(subcription_name, callback)
```

Then, it has to be done the pull of the messages (Only the last one is read by default).

```python
import time
from google.cloud import pubsub_v1

subcriber = pubsub_v1.SubscriberClient() # subcriber client

subcription_path = subcriber.subcription_path(project_id, subcription_name)

NUM_MESSAGES = 2
ACK_DEADLINE = 10
SLEEP_TIME = 10

# The last 2 messages are pulled
response = subsriber.pull(subscription_path, max_messages = NUM_MESSAGES)
```

## Vocabulary

Data sync: The ending point for data in architecture diagrams (ej, A data warehouse)

Federated queries: Queries done on data in a service, without the need of copying or moving it to the where the query is performed.

Network Egress charges: Charges for using services in different locations ???

Signed URL: URL that gives limited permissions in a specific time to make actions over specified objects. It includes authentication information in the string so anyone with it can directly manipulate the resource as specified.

Parsing data: Converting data from one format to another.

Padded string: String with a certain character added at the start and end of the string, which is not part of the value and should be removed.

Backfilling: Automatic process of detecting data drops and requesting data items to fill in the gasps.

Delta changes: Changes in data done between a particular moment and the current version.

Network firewall: Security system to prevent access to private networks connected to the Internet, like a Google Cloud service. The only traffic allowed is defined with firewall policies.

YARN: In a Hadoop ecosystem, a resource manager to distribute loads between parts.

Reinforcement learning: Technique where a machine tries actions without previous training training to reach the highest rewards. Wrong actions are punished and correct are rewarded so it learns with trial and error. Examples are training an agent to play a maze game or a racing game.

Multi tenancy: Mode of operation where multiple independent instances operate in a shared environment.
