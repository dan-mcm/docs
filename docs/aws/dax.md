# DAX (Dynamo DB Accelerator)

A fully managed, clustered in-memory cache for DynamoDB
* Delivers up to 10x read performance improvement
* Microsecond performance for millions of requests per second
* Ideal for Read-Heavy and bursty workloads e.g. auction applications, gaming and retail sites during black Friday promotions

### How DAX Works

* Dax is a write-through caching service - data is written to the Cache as well as the back end store at the same time
* Allows you to point your DynamoDB API calls at the DAX cluster
* If the item you are querying is in the cache (cache hit), DAX returns the result to the application
* If the item is not available (cache miss) then DAX performs an Eventually Consistent GetItem operation against DynamoDB
* Retrieval of data from DAX reduces the read load on DynamoDB tables
* May be able to reduce Provisioned Read Capacity

### What DAX is not suitable for

* Caters for Eventually Consistent reads only - no not suitable for applications that require Strongly Consistent Reads
* Write intensive applications
* Applications that do not perform many read operations
* Applications that do not require microsecond response times

### Exam Tips - DAX

* Provides in-memory caching for DynamoDB tables
* Improves response times for Eventually Consistent reads only
* You point your API calls to the DAX cluster instead of your table
* If item you are querying is on the cache, DAX with return it; otherwise it will perform an Eventually Consistent GetItem operation to your DynamoDB table
* Not suitable for write-intensive applications or applications that require Strongly Consistent Reads
