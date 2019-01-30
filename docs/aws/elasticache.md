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
