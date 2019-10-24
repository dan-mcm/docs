# AWS Elasticache

Elasticache is a web services that makes it easy to deploy, operate and scale an in-memory cache in cloud. Improve performance of web apps by allowing retrieval of info from fast managed in memory caches instead of relying on slower disk-based DBs.

Can be used to significantly improve latency for read-heavy app workloads (social networking/gaming) or compute-intensive workloads (recommendation engine)

Caching improves app performance by storing critical piece of data in memory for low-latency access. Cached information may include the results of I/O-intensive DB queries or the results of computation intense calculations.

## Types

### Memcached

Widely adopted memory object caching system. Protocol compliant with Memcached, so popular tools with existing Memcached environment will work with the services

Pure caching solution with no persistence, ElastiCache manages Memcached nodes as a pool that can grow and shrink similar to an EC2 auto scaling group. individual nodes are expendable, and ElastiCache provides additional capabilities here, such as automatic node replacement and auto discovery.

Use Cases:

* Object caching your goal - to offland DB.
* Simple caching model as possible
* Running large cache nodes & multithreaded performance with use of multiple cores
* Grow & scale cache horizontally

### Redis

Popular open source in-memory key-value store supports data structs such as sorted sets and lists. Supports master/slave replication and Multi-AZ which can be used to achieve cross AZ redundancy.

ElastiCache manages Redis more as a relational DB. Redis ElastiCache clusters are managed as stateful entities that include failover, similar to how Amazon RDS manages DB failover.

Use Cases:

* More adv data types such as lists hashes & sets
* Sorting and ranking datasets in memory i.e. leaderboards
* Persistence of key store important
* Run multiple AZs with failover

## Exam Tips

Typically scenario where DB is under stress/load -> may be asked which service you should use to alleviate this.

Elasticache is a good choice if DB is read-heavy and not prone to frequent changing

Redshift good answer if reason the DB is feeling stress is because management keep running OLAP transactions on it etc.

# Elasticache - contd.

In memory cache in the cloud

* Improves performance of web apps, allowing retrieval of info from fast in-memory caches rather than slower disk based DBs.
* Sits between your app and the DB e.g. application frequently requesting specific product information for your best selling products
* Takes the load off your DBs
* Good if DB is particularly read-heavy and the data is not changing frequently

### Benefits & Use Cases

* Improves performance for read-heavy workloads e.g. Social Networking, gaming, Q&A Portals
* Frequently-accessed dat is store din memory for low-latency access, improving overall performance of your app.
* Also good for compute heavy workloads e.g. recommendation engines
* Can be used to store results of I/O intensive DB queries or output of compute-intensive calculations.

### Types

#### Memcached

* Wiedly adopted memory object cachign system
* Multi threaded
* No Multi-AZ capability

#### Redis

* Open-source in-memory key-value store
* Supports more complex data structures: sorted sets and lists
* Supports Master / SLave replicatoin and Multi-AZ for cross AZ redundancy

### Caching Strategies

2 strategies available:

#### Lazy Loading

* Loads data into the cache only when necessary
* If requested data is in the cache, Elasticache returns the data to the application
* If the data is not in the cache or has expired, Elasticache returns a null
* Your application then fetches the data from the DB and writes the data received into the cache so that it is available next time.

| Advantages     | Disadvantages     |
| :------------- | :------------- |
| Only requested data cached: Avoids filling up cache with useless data      | Cache miss penalty: initial request, query to DB, writing of data to the cache       |
| Node failures are not fatal a new empty node will just have a lot of cache misses initially      | Stale data - if data is only updated when there is a cache miss, it can become stale. Doesn't automatically update if the data in the database changes       |

##### Lazy Loading TTL (Time To Live)

* Specifies number of seconds until the key (data) expires to avoid keeping stale data in the cache
* Lazy loading treats an expired key as a cache miss and causes the application to retrieve the data from the DB and subsequently write the data into the cache with a new TTL
* Does not eliminate stale data - but helps to avoid it

#### Write-Through

Adds or updates data to the cache whenever data is written to the database

| Advantages     | Disadvantages     |
| :------------- | :------------- |
| Data in the cache never stale       | Write Penalty: every write involves a write to the cache as well as a write to the DB       |
| Users are generally more tolerant of additional latency when updating data than when retrieving it       | If a node fails and a new one is spun up, data is missing until added or updated in the DB (mitigate by implementing Lazy Loading in conjunction with write-through)       |
|        | Wasted resources if most of the data is never read       |

### Exam Tips - Elasticache

* In-memory cache sites between your application and DB
* 2 different caching strats - Lazy Loading & Write Through
* Lazy loading only caches data when it is requested
* Elasticache Node failures not fatal, just lots of cache misses
* Cache miss penalty: Initial request, query DB, writing to cache
* Avoid stale data by implementing a TTL
* Write through strategy writes data into the cache whenever there is a change to the DB
* Data is never Stale
* Write penalty: each write involves a write to the cache
* Elasticache node failure means that data is missing until added or updated in the DB
* Wasted resources if most of the data is never used
