# MongoDB

- Document database
- Natively distributed system, scalable and with fault tolerance
- Offers several versions and tools:
	- MongoDB Atlas is the cloud version.
	- MongoDB Shell allows interacting using a terminal
	- MongoDB Compass offers a graphical interface

- The organization on MongoDB is: Projects -> Clusters -> Databases

- The database can be split in several partitions called **sharded clusters**:
	- A sharded cluster has the following components:
		- **Shard:** Contains a partition of the data
		- **Mongos:** Interface between applications using MongoDB and the sharded cluster
		- **Config servers:** Stores metadata and configuration settings
	- A **shard key** distributes all the documents through the sharded clusters:
		- Distributes documents depending on the values of their keys
		- Data can be moved close where it is going to be used (ex: European users data to an European server)
	- In each sharded clusters there are different **replica sets**, a group of typically three instances or nodes, which have a full copy of the same data

- Inside a database, the database is structured in: Collections -> Documents -> Fields

  - Documents are saved in BSON format (Binary JSON)
  - The defaultt maximum size of a document is 16 MB
  - A collection can be a **capped collection**, which overwrites its oldest documents when the allocated space is filled
  - Each document can have different fields
  - A field is a key-value pair

  - A field can contain:
  	- A single value from a group of [data types](https://www.mongodb.com/docs/manual/reference/bson-types/) like date or string
  	- An array of values
  	- A nested document

  - A field in different documents don't need to have the same structure
  - A field can be a unique field:
  	- By default, the field `_id`
  	- It has to be populated in every document
  	- There can not be two document with the same field value
  	- If not specified, randomly assigned

- Use of **MQL** for simple queries in across single collections and **MongoDB Aggregation Pipeline** for complex ones

> Example of a document with an array

```json
{
   "id": 1120,
    "name": "Sergi Joan Sastre",
    "city": "Valencia",
    "hobby": ["Movies", "Music"]
}
```

> Example of a document with a nested document

```json
{
   "id": 1120,
   "name": "Sergi Joan Sastre",
   "adress": {
      "country": "Spain",
      "city": "Valencia",
      "postal code": 46024
   }
}
```

## Setting Up MongoDB Atlas

### Creating the cluster

1. Go to [mongodb.com/try](https://www.mongodb.com/try)
2. Select Atlas and fill the form on the right
3. Create a Shared database
4. Select a cloud provider and a free region
5. Select the free cluster tier
6. Add a name to the cluster
7. Go to Database access to add a user
8. Select password as authentication method
9. Give admin privileges and add the user
10. Go to Network Access to add IPs the the access list
11. Select: ALLOW ACCESS FROM ANYWHERE (For practicing but not recommended in production)

### Installing MongoDB shell

1. Open [mongodb.com/try/download/shell](mongodb.com/try/download/shell) and install the zip package
2. Find mongosh.exe inside the bin folder
3. Right click on the file and copy the location path
4. Open the program: Edit the system environment variables
5. Highlight PATH in the user variables and click EDIT.
6. Add the copied location
7. To check if it worked, open a terminal and write `mongosh --help`

### Install database tools

1. Open [mongodb.com/try/download/database-tools](mongodb.com/try/download/database-tools) and install the zip package
2. Copy the location of one the exe files in the bin folder
3. Add the location to the path as done with the shell location
4. To check if worked, open a terminal and write `mongoexport --help`

### Install Compass

1. Open [mongodb.com/try/download/compass](mongodb.com/try/download/compass) and download the exe
2. Execute the to install the program, which is called MongoDBCompass
3. Open [mongodb.com/cloud/atlas](mongodb.com/cloud/atlas)
4. On the right of the cluster name, click on Connect and select Compass
5. Copy the connection string
6. On Compass, paste the link when adding a connection, but substituting the user password on the string

### Add JSON data to a database collection

1. On Compass, select CREATE DATABASE
2. Choose a name for the database and the initial collection of the database
3. Enter on the collection and select ADD DATA
4. Select the JSON file with the data
5. On the left panel, using the + in the database name, a new collection can be added

## Managing databases

### Connecting to a cluster

1. Open [mongodb.com/cloud/atlas](mongodb.com/cloud/atlas)
2. On the right of the cluster name, click on Connect and select MongoDB Shell
3. Copy the connection string
4. Paste the connection string, which will ask for the user password

### Export a collection to a file

```
mongoexport --uri="mongodb+srv://<user>:<password>@<cluster>.mongodb.net/<database>" --collection=<collection> --out=<file_path>
```

- Can replace `mongoexport` with `mongodump` to use BSON instead of JSON, which is not human readable but offers better perfromance.

### Import a collection to a file

```
mongoimport --uri="mongodb+srv://<user>:<password>@<cluster>.mongodb.net/<database>" --collection=<collection> --file=<file_path>
```

- If the collection does not exist, it is created automatically.

- Can replace `mongoimport` with `mongorestore` to use BSON instead of JSON, which is not human readable but offers better perfromance

### Import a database

```
mongodump --uri="mongodb+srv://<user>:<password>@<cluster>.mongodb.net/<database>" 
```

### Restore a database

```
mongorestore --uri="mongodb+srv://<user>:<password>@<cluster>.mongodb.net/<database>" <path>
```

- `<path>` is the path of the folder inside the dump folder which has the database we want to be restored

### Switch database

```bash
use <name>
```

- When using any functions that refers to an specific database, this must be used before the use the desired one

### Show databases

```bash
show dbs
```

### Show collections from a database

```bash
show collections
```

### Create a database

```bash
use <collection>
```

- When switching to a non-existing database, it is automatically created
- It won't appear until a collection is created inside the database

### [Drop a database](https://www.mongodb.com/docs/manual/reference/method/db.dropDatabase/)

```bash
db.dropDatabase()
```

### [Create a collection](https://www.mongodb.com/docs/manual/reference/method/db.createCollection/)

```
db.createCollection("<name>", {options})
```

- In `options` we can create a schema validation
	- `validator`: Defines the structure of the document
	- `validationLevel`: Defines when to apply the validation
		- `strict` to apply validations to all insert and updates
		- `moderate` to avoid validations when updating documents not fulfilling the validation
	- `validationAction`: Define the action when an insert or update does not follow the validation
		- `error` reject the action
		- `warn` allows the action but saves it in a log

> Creates a collection named coll2 whose documents must have field1 and field1, being the first a string and the last an int between 0 and 110

```
db.createCollection("coll2", {
	validator:{ $jsonSchema: {
		required:["name", "age"],
		properties: {
			name: {
				bsonType: "string",
				description: "must be a string and is required"
			age:
				bsonType: "int",
				minimum: 0,
				maximum: 110,
				description: "must be an integer in [ 0, 110] and is required"
}}}}})
```

### [Drop a collection](https://www.mongodb.com/docs/manual/reference/method/db.collection.drop/)

```bash
db.<name>.drop()
```

## Find documents from a collection

### [Return specified documents](https://www.mongodb.com/docs/manual/reference/method/db.collection.find/)

```bash
db.<collection>.find(<query>, <projection>)
```

- A query allows to filter the documents from the collection
- A projection allows to choose what will be displayed from the documents filtered

> Returns all documents which satisfy a query and don't show the student_id field

```
db.<collection>.find({"student_id":1036, "class_id":419}, {"student_id":0})
```

> Returns documents which satisfy a query

```bash
db.<collection>.find({$and:[{$and:{"student_id":{$lt:20}},{"class_id":{$eq:19}}]})
```

### [Return a single document](https://www.mongodb.com/docs/manual/reference/method/db.collection.findOne/)

```bash
db.<collection>.findOne(<query>, <projection>)
```

- Works like `find()` but if several documents satisfy the query, only the first result is shown

## Query

**To filter documents returned.**

- To be used inside `db.<collection>.find()`

```
db.<collection>.find(<query>, <projection>)
```

### [Comparison](https://www.mongodb.com/docs/manual/reference/operator/query-comparison/)

| Operator | Description                             |
| -------- | --------------------------------------- |
| `$eq`    | Equal                                   |
| `$ne`    | Different                               |
| `$gt`    | Greater                                 |
| `$gte`   | Greater or equal                        |
| `$lt`    | Less                                    |
| `$lte`   | Less or equal                           |
| `$in`    | Equal to any value of an array          |
| `$nin`   | Equal to none of the values of an array |

```
{<field>: {<operator> : <value>}}
```

> Field greather than 10

```
{"field" : {$gt : 10}}
```

> Field either 1, 3, or 5

```
{"field" : {$in : [1,3,5]}}
```

### [Logical](https://www.mongodb.com/docs/manual/reference/operator/query-logical/)

| Operator | Description                                              |
| -------- | -------------------------------------------------------- |
| `$and`   | Returns documents that match all clauses                 |
| `$not`   | Returns documents that do not match the query expression |
| `$nor`   | Returns documents that does not match either clauses     |
| `$or`    | Returns documents that match either clauses              |

```
{<operator> : [{<condition1>, {<condition2>}}]}
```

- Without using operators, and is the default behavior. However, if the same field is used more than once, the last use overwrites the others, so `$and` must be used
- When using`$not`, documents which don't have the corresponding field are also returned

> field1 greather than 10 or field2 different than "Spain"

```
{$or : [{"field1" : {$gt : 10}},{"field2" : {$ne : "Spain"}}]}
```

> field not greater than 10 or field not exists

```
db.inventory.find( { "field": { $not: { $gt: 10 } } } )
```

### [`$expr`](https://www.mongodb.com/docs/manual/reference/operator/query/expr/)

**Allows to make comparations between fields of the same document.**

```
{$expr: {<operator> : [<$field>, <field_to_compare>]}}
```

> field1 bigger than field2

```
{$expr: {$gt : ["$field1", "$field2"]}}
```

### [`$exists`](https://www.mongodb.com/docs/manual/reference/operator/query/exists/)

**Returns documents that have the specific field.**

```
{<field> : {$exists: <boolean>}}
```

> field1 exists but field2 does not

```
{"field1" : {$exists: true}, "field2" : {$exists: false}}
```

### [`$type`](https://www.mongodb.com/docs/manual/reference/operator/query/type/)

**Returns documents if a field is of the specified type.**

-  It must be used the [number associated to each data type ](https://www.mongodb.com/docs/manual/reference/bson-types/)

```
{<field> : {$type: <BSON_type>}}
{<field> : {$type: [<BSON_type1>, <BSON_type2>, ...]}}	#for a list of types
```

> field1 is an string or null and field2 is a date

```
{"field1" : {$type: [2, 10]}, "field2" : {$type : 9}}
```

### [Query arrays](https://www.mongodb.com/docs/manual/tutorial/query-arrays/)

> Find documents which have in at least one of the field1 array the value Valencia

```
db.<collection>.find({"field1" : "Valencia"})
```

> Find documents which have in the field1 array [1, 2, 3]

```
db.<collection>.find({"field1" : [1, 2, 3]})
```

### [`$all`](https://www.mongodb.com/docs/manual/reference/operator/query/all/)

**Returns all documents where the value of a field is an array that contains all the specified elements, regardless the order.**

```
{"<field>": {$all: [<value1>, <value2>, ...]}}
```

###  [`$size`](https://www.mongodb.com/docs/manual/reference/method/cursor.size/)

**Returns document whose array field has a specific size.**

- If the value is not an array but a single value, the size is 1

```
{<field>: {$size : <number>}}
```

### [Query embedded documents](https://www.mongodb.com/docs/manual/tutorial/query-embedded-documents/)

**To reference a field inside a field, inside of using just `"<field>"`, use `"<field>.<subfield>"`.** 

> Find documents whose subfield1 inside the field1 is Valencia

```
db.<collection>.find({"field1.subfield1" : "Valencia"})

```

### [Query arrays of embedded documents](https://www.mongodb.com/docs/manual/tutorial/query-array-of-documents/)

> Find documents which have inside field1 (an array of embedded documents), one embedded document with a subfield1 equal to Valencia

```
{"field1.subfield1":"Valencia"}
```

### [`$elemmatch`](https://www.mongodb.com/docs/manual/reference/operator/query/elemMatch/)

**Finds documents with an array field that has at least one element matching the specified criteria.**

```
{ <field>: { $elemMatch: { <query1>, <query2>, ... } } }
```

> Find documents which have inside field1 (an array of embedded documents), one embedded document with a subfield1 equal to Valencia. And in the same embedded document, a subfield2 greater than 5

```
{ field1: { $elemMatch: { "subfield1": "Valencia", "subfield2": {$gt: 5} } } }
```

## Cursor methods

### [`count()`](https://www.mongodb.com/docs/manual/reference/method/db.collection.count/)

**Returns the total of documents**

```
db.<collection>.find({<query>}).count()
```

> Total of documents with field1 greater than 1

```
db.<collection>.find({"field1" : {$gt : 1}}).count()
```

### [`sort()`](https://www.mongodb.com/docs/manual/reference/method/cursor.sort/)

**Orders the documents based on specified fields**

```
db.<collection>.find({<query>}).sort({<parameters>})
```

> Order the documents by field1 descending and then field2 ascending

```
db.collection1.find().sort({"field1" : -1, "field2" : 1})
```

### [`limit()`](https://www.mongodb.com/docs/manual/reference/method/cursor.limit/)

**Limits the number of documents returned.**

```
db.<collection>.find({<query>}).limit(<number>)
```

### [`skip()`](https://www.mongodb.com/docs/manual/reference/method/cursor.skip/)

**Skips the first documents.**

```
db.<collection>.find({<query>}).skip(<number>)
```

### [`pretty()`](https://www.mongodb.com/docs/manual/reference/method/cursor.skip/)

**Returns the documents in a format easier to read.**

```
db.<collection>.find().pretty()
```

### [`explain()`](https://www.mongodb.com/docs/manual/reference/method/cursor.explain/)

**Shows information about the process performed.**

- Using this method, it is used the verbosity mode for any action performed, so these will not modify the database content

```
db.<collection>.find().explain()
```

The type of explanation can be chosen passing a string to the function:

- `queryPlanner`: (By default) Using a query optimizer, show info about the best plan to execute the query and also the alternatives contemplated
- `executionStats`: Additionally, runs the query showing info about the results
- `allPlansExecution`: Additionally, runs the query with the others plans as well showing info about the result

## Projection

**To return only specified fields from the documents.**

- Specified after the query
- To be used inside `db.<collection>.find()`
- If no query is going to be specified, put `{}`
- The `_id` field has to specifically specified to not be used or it will be shown

```
db.<collection>.find(<query>, <projection>)
```

> Return only field2 and field3 from each document

```
db.collection1.find({}, {"field2":1, "field2":1, "_id":0})
```

## [Insert documents](https://www.mongodb.com/docs/manual/reference/method/db.collection.insertMany/)

- By default, if a document has an error, only the previous documents are inserted. The use of the option `ordered` allows to insert also the following documents

```
db.collection.insertOne(
   <document>
)
```

```
db.collection.insertMany(
   [ <document 1> , <document 2>, ... ]
)
```

## [Delete documents](https://www.mongodb.com/docs/mongodb-shell/crud/delete/)

```
db.<collection>.deleteOne({<query>})
```

```
db.<collection>.deleteMany({<query>})
```

## [Update documents](https://www.mongodb.com/docs/manual/reference/method/db.collection.updateMany/)

```
db.<collection>.updateOne({<query>}, {<update>})
```

```
db.<collection>.updateMany({<query>}, {<update>})
```

### [Update operators](https://www.mongodb.com/docs/manual/reference/operator/update/)

| Operator                                                     | Description                              |
| ------------------------------------------------------------ | ---------------------------------------- |
| [`$set`](https://www.mongodb.com/docs/manual/reference/operator/update/set/#mongodb-update-up.-set) | Creates or replaces fields values        |
| [`$unset`](https://www.mongodb.com/docs/manual/reference/operator/update/unset/#mongodb-update-up.-unset) | Deletes specified fields                 |
| [`$inc`](https://www.mongodb.com/docs/manual/reference/operator/update/inc/#mongodb-update-up.-inc) | Increments a field by an specific amount |
| [`$rename`](https://www.mongodb.com/docs/manual/reference/operator/update/rename/#mongodb-update-up.-rename) | Renames a field                          |
| [`$currentDate`](https://www.mongodb.com/docs/manual/reference/operator/update/currentDate/#mongodb-update-up.-currentDate) | Sets the current date as a field value   |
| [`$push`](https://www.mongodb.com/docs/manual/reference/operator/update/push/#mongodb-update-up.-push) | Adds a single item to an array           |

> Change field2 and field3 from documents with field1 greater or equal than 5

```
db.collection.updateMany({"field1": {$gte: 5}}, {$set: {"field2": true, "field3": "Passed" }})
```

> Removes field1 and field2 and renames field3 from all documents

```
db.collection.updateMany(
	{},
	{
	$unset: {"field1": "", "field2": ""},
	$rename: {"field3": "field3_newname"},
	}
)
```

> In all documents, increments field1 by 1 and field2 by 10, and also adds two values to the field3 array

```
db.customers.updateMany(
   {},
   {
     $inc: { "field1": 1, "field2": 10 },
     $push: {"field3": 3}
   }
)
```

> In all documents, adds three values to the field1 array

```
db.customers.updateMany(
   {},
   {
     $push: { ¨field1: { $each: [ 6, 4, 7] } }
   }
)
```

> In one document, sets field1 to the current date, field2 to the current timestamp and sets field3 to 5

```
db.customers.updateOne(
   { _id: 1 },
   {
     $currentDate: {
        field1: true,
        "field2": { $type: "timestamp" }
     },
     $set: {
        "field3": 6
     }
   }
)
```

## [Aggregation framework](https://www.mongodb.com/docs/manual/reference/method/db.collection.aggregate/)

**Pipeline of operations performed to the documents of a collection.**

- In MongoDB versions prior to 6.0, the maximum space is 100 MB to be used in each stage, but it can be used the function [`allowDiskUse()`](https://www.mongodb.com/docs/manual/reference/method/cursor.allowDiskUse/)

```
db.<collection>.aggregate(
[
{<stage1>},
{<stage2>},
{<stageN>}
]
)
```

### [`$match`](https://www.mongodb.com/docs/manual/reference/operator/aggregation/match/)

**Stage where the documents are filtered with a query.**

```
{$match: {<query>}}
```

### [`$project`](https://www.mongodb.com/docs/manual/reference/operator/aggregation/project/)

**Stage where are chosen which fields will be displayed.**

- Can also display custom fields

```
{ $project: { <specification(s)> } }
```

> Shows only field1 and a custom field1_b which shows the field1 value

```
db.collection.aggregate(
[{$project: {"_id": 0, "field1": 1, "field1_b": "$field1"}}]
)
```

### [`$addfields`](https://www.mongodb.com/docs/manual/reference/operator/aggregation/addFields/)

**Stage where fields are added to the documents.**

- Similar to `$project`, but it keeps showing all the other fields

```
{ $addFields: { <newField>: <expression>, ... } }
```

> Add a field1_b which is field1 rounded

```
db.collection.aggregate(
[{$addFields: {"field1_b": {$round:["$field1",1] }}}]
)
```

### [`$sort`](https://www.mongodb.com/docs/manual/reference/operator/aggregation/sort/)

**Stage where the documents are filtered according the specified fields.**

> Sorts the documents by field1 descending

```
db.collection.aggregate(
[{$sort: {"$field1": -1}}]
)
```

### [`$count`](https://www.mongodb.com/docs/manual/reference/operator/aggregation/count/)

**Stage where a single document is passed indicating the number of documents that were in the stage input.**

> Shows the total of documents

```
db.collection.aggregate(
[{$count: "total_doc"}]
)
```

### [`$limit`](https://www.mongodb.com/docs/manual/reference/operator/aggregation/limit/)

**Stage where the documents passed to the next stage are limited.**

> Show only the first 10 documents

```
db.collection.aggregate(
[{$limit: 10}]
)
```

### [`$skip`](https://www.mongodb.com/docs/manual/reference/operator/aggregation/skip/)

**Stage where the n first documents are not passed into the next stage.**

> First 5 documents are not showed

```
db.collection.aggregate(
[{$skip: 5}]
)
```

### [`$group`](https://www.mongodb.com/docs/manual/reference/operator/aggregation/group/)

**Stage where documents are grouped according the a group key, which is an expression.**

```
{
  $group:
    {
      _id: <expression>, // Group key
      <field1>: { <accumulator1> : <expression1> },
      ...
    }
 }
```

> Group the documents by field1 and in the resulting documents shows for each group the total of field2 and the total of documents

```
db.collection.aggregate(
[
{
  $group:
    {
      _id: "$field1",
      "field2_tot": { $sum : "$field2" },
      "group_tot": { $sum : 1 }
    }
 }
 ])
```

### [`$bucket`](https://www.mongodb.com/docs/manual/reference/operator/aggregation/bucket/)

**Stage where the documents are grouped based on defined ranges from an expression.**

- If no `output` is defined, a count is returned

```
{
  $bucket: {
      groupBy: <expression>,
      boundaries: [ <lowerbound1>, <lowerbound2>, ... ],
      default: <literal>,
      output: {
         <output1>: { <$accumulator expression> },
         ...
         <outputN>: { <$accumulator expression> }
      }
   }
}
```

> With field1 as the expression, groups the documents in three groups: [0, 9], [10, 49] and [50, 99]. Those documents with a field1 out all the previous ranges, receives a default value of "other". Also, a field2_total is computed as the sum of field2 for each group is performed

```
db.collection.aggregate(
[
{
  $bucket: {
      groupBy: "$field1",
      boundaries: [ 0, 10, 50, 100],
      default: "other",
      output: {
         "field2_total": { $sum: "$field2"}
      }
   }
}
])
```

### [`$bucketAuto`](https://www.mongodb.com/docs/manual/reference/operator/aggregation/bucketAuto/)

**Stage where the documents are grouped based on a defined number or ranges from an expression.**

- Boundaries are determined to evenly distribute the documents into the number of buckets

- If no `output` is defined, a count is returned
- `granularity` allows to define how the boundaries are calculated

```
{
  $bucketAuto: {
      groupBy: <expression>,
      buckets: <number>,
      output: {
         <output1>: { <$accumulator expression> },
         ...
      }
      granularity: <string>
  }
}
```

> With field1 as the expression, groups the documents in four groups. Also, a field2_total is computed as the sum of field2 for each group is performed

```
db.collection.aggregate(
[
{
  $bucketAuto: {
      groupBy: "$field1",
      buckets: 3,
      output: {
         "field2_total": { $sum: "$field2"}
      }
   }
}
])
```

### [`$facet`](https://www.mongodb.com/docs/manual/reference/operator/aggregation/facet/)

**Stage where different sub pipelines are processed at the same time.**

- In the ouput, an array of the documents generated by each subPipeline

```
{ $facet:
   {
      <outputField1>: [ <stage1>, <stage2>, ... ],
      <outputField2>: [ <stage1>, <stage2>, ... ],
      ...

   }
}
```

### [`$sortByCount`](https://www.mongodb.com/docs/manual/reference/operator/aggregation/sortByCount/)

**Stage where the documents are grouped based on an expression and then shows the groups in descending order of count**

- Same result using `$group` and then `$sort`

```
{ $sortByCount:  <expression> }
```

### [`$unwind`](https://www.mongodb.com/docs/manual/reference/operator/aggregation/unwind/)

**Stage where documents with an array are converted to separated documents for each array element.**

- Documents with array empty are not even returned a single time by default

```
{ $unwind: <field path> }
```

> Returns one document for each value of the array field1

```
db.collection.aggregate(
[
	{$unwind: {path: "$field1"}}
])
```

### [`$lookup`](https://www.mongodb.com/docs/manual/reference/operator/aggregation/lookup/)

**Performs a left join to another collection.**

- Matching documents are returned as an array of documents

```
{
   $lookup:
     {
       from: <collection to join>,
       localField: <field from the input documents>,
       foreignField: <field from the documents of the "from" collection>,
       as: <output array field>
     }
}
```

> For each document from coll1, find all documents from coll2 which are related by field1 of each collection and return them in an array called coll2_all_doc

```
db.coll1.aggregate(
[
{
   $lookup:
     {
       from: "coll2",
       localField: "field1",
       foreignField: "field1",
       as: "coll2_all_doc"
     }
}
])
```

### [`$out`](https://www.mongodb.com/docs/manual/reference/operator/aggregation/out/)

**Stage to save the documents in an new or existing collection.**

- If the collection is in the same database than the current, `db` can be omitted

```
{ $out: { db: "<output-db>", coll: "<output-collection>" } }
```

### [Aggregation operators](https://www.mongodb.com/docs/manual/reference/operator/aggregation/)

**Operators to do arithmetic, string, etc. operations.**

- `$expr` needs to be used when in the `$match` stage, since it does not accept raw aggregation operators

> Show field2 as two times field1

```
db.collection.aggregate(
[{$project: {"_id": 0, "field2": {$multiply: ["$field1", "$field1"]}}}]
)
```

> Filter documents whose field2 is greater than field1 + 2

```
db.collection.aggregate(
[{$match: {$expr: {$gt: ["$field2", {$add: ["$field1", 2]}}}]
)
```

> Shows field1 in uppercase

```
db.collection.aggregate(
[{$project: {"field1": {$toUpper: "$field1"}}}]
)
```

> Shows field1 and field1_start, which indicates if field1 contains the string "No"

```
db.collection.aggregate(
[{$project: {"field1": 1, "field1_start": {$regexMatch: {input: "$field1", regex: "No"}}}}]
)
```

> Shows field12, which is field1 and field2 concatenated

```
db.collection.aggregate(
[{$project: {"field12": {$concat: ["$field1", " and " ,"$field2"]}}}]
)
```

> Shows to month from the date type field1

```
db.collection.aggregate(
[{$project: {"field1_month": {$month: "$field1"}}}]
)
```

> Returns field1 in uppercase

```
db.collection.aggregate(
[{$project: {"field1": {$toUpper: "$field1"}}}]
)
```

> Returns a field1_cond with Yes when field1 is greater or equal than 5 or No if not

```
db.collection.aggregate(
[{$project: 
	{"field1_cond": {$cond: {if: {$gte: ["$field1",5]}, then: "Yes", else: "No"}}}]
)
```

> Returns field1 but with 0 if null

```
db.collection.aggregate(
[{$project: 
	{"field1": {$ifNull: ["$field1",0]}}]
)
```

> Show a field1_count with the size of the field1 array. Show only those with field1_count greater than 10

```
db.collection.aggregate(
[
{$project: 
	{"field1_count": {$size: "$field1"}},
{$match: 
	{"field1_count": {$gt:10}}}

]
)
```

## Variables

### User defined variables

```
<variable_name> = <value>
```

> Define a stage in a variable

```
var1 = "$field1"
stage1 = {$project: {var1: 1}}
db.collection.aggregate([stage1])
```

### [`$map`](https://www.mongodb.com/docs/manual/reference/operator/aggregation/map/)

**Applies an expression to each element of an array.**

- Creates a variable which iterates all the elements of the array 

```
{ $map: { input: <expression>, as: <array_variable>, in: <expression> } }
```

> Returns all elements of field1 array in uppercase

```
db.collection.aggregate(
[{$project: {"field1": {$map: {input: "$field1", as: "i", in: {$toUpper: "$$i"}}}}}]
)
```

### [`$let`](https://www.mongodb.com/docs/manual/reference/operator/aggregation/let/)

**Binds variables to use in a specified expression.**

- Returns the value of the expression

```
{
  $let:
     {
       vars: { <var1>: <expression>, ... },
       in: <expression>
     }
}
```

> Return a field12 which is field1 plus field2

```
db.collection.aggregate(
[
{$addFields: 
    {
      $let:
         {
           vars: { "var1": "$field1", "var2": "$field2"},
           in: {$multiply: ["$$var1", "$$var2"]}
         }
    }
}
])
```

### [System variables](https://www.mongodb.com/docs/manual/reference/aggregation-variables/)

```
"$$<system_variable>"
```

> Group the documents by field1 and show for each group an array field1_tod with all the documents

```
db.collection.aggregate([
{$group: {"_id": "$field1", "field1_tod": {$push: "$$ROOT"}}}
])
```

## [Indexes](https://www.mongodb.com/docs/manual/reference/method/db.collection.createIndex/)

**Increases performance when finding documents by the indexed field.** 

- There is always an index on `_id`
- They take storage space
- Instead of a COLLSCAN (Scan all the documents), does a IXSCAN (Scan with the index)
- If using an indexed field, no sort function is needed to apply since the index stores and returns the values sorted already (In case the sort order is in the same direction)
- Some stages in pipelines don't allow indexes, and the following stages won't either, so mongoDB tries to rearrange the stages
- An index using field1 and field2 would we used for actions using field1 (or field1 and then field2 of course) but not field2 (or field2 and then field1 of course), since in the index field2 is only indexed for each field1 value
- Also, sorting field 1 and field2 with an index of those two fields would only work  if the sort order of both field is the same in all or different in all the fields
- Can also be indexed subfields inside arrays or nested documents
- For single indexes, sorting order does not matter, but compound indexes can not be used if both fields are sorted in different directions
- Can be used [`hint()`](https://www.mongodb.com/docs/manual/reference/method/cursor.hint/) to force the index to be used. Better to use an indexes with the same fields than the ones used in the action
- [`dropIndex()`](https://www.mongodb.com/docs/manual/reference/method/db.collection.dropIndex/) allows to delete indexes

```
db.collection.createIndex(<keys>, <options>)
```

- `keys` is a document specifying the fields to use in the index and how will be ordered
- `options` allows some index configuration, like:
	- `background` to create the index without blocking the collection for other purposes
	- `name` to give a custom name to the index
	-  `partialFilterExpression` allows to create partial indexes, which applies to a part of the collection only. It will be used when an action used documents in the index

> Create and index with field1 ascending and field2 descending

```
db.collection.createIndex(
{
"field1": 1,
"field2": -1
},
{
background: true,
name: "index_field_1_2"
}
)
```

## [Python Drivers](https://pymongo.readthedocs.io/en/stable/)

There are two drivers, synchronous PyMongo and asynchronous Motor. For the first one:

Install the pymongo package

```bash
pip install pymongo
```

### Connect to a server

```python
from pymongo import MongoClient
import pprint # to show documents in a nicer format

# MongoDB deployment's connection string.
conn_str = "<mongoDb uri string>"

# prepare connection with a 5-second connection timeout
client = pymongo.MongoClient(conn_str, serverSelectionTimeoutMS=5000)

# try the connection
try:
    print(client.server_info())
except Exception:
    print("Unable to connect to the server.")
    
# list databases from the server
client.list_database_names()

# use a database
db = client.<database> # or also client.['database']

# list collections from the database
db.list_collection_names()

# use a collection
coll = db.<collection> # or also db['collection']

# find a document in a collection
db.<collection>.find_one()

# find all docuemnts in a collection (but viweing only 20 first)
for i in coll.find().limit(5):
  print(i)

# to show documents in a nicer format, use the module pprint
for i in coll.find().limit(5):
  pprint.pprint(i)

# instead of loop, other way
pprint.pprint(list(coll.find().limit(5)))

# create a database
client.<database> # if it does not exist, is created

# insert documents in a collection
# inserting a document in a non existent collection creates it
documents = [
  {"name": "Vishwash", "Roll No": "1001"},
  {"name": "Vishesh", "Roll No": "1002"}
]
db.coll.insert_many(documents)

# aggregation pipeline

db.coll.aggregate()
pipeline = [
    {<stage1>},
    {<stage2>},
    ...
]

result = db.coll.aggregate(pipeline)
pprint.pprint(list(result)) # can also be used for loop
```
