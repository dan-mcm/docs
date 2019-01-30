# AWS Relational Database Service (RDS)

Think traditional spreadsheet:

* Database
* Tables
* Row
* Fields (Columns)

## Relational Database Types (with RDS)

* SQL Server
* Oracle
* MySQL Server
* PostgresSQL
* Aurora (amazons DB)
* MariaDB

## Non-Relational Databases

* Database
  * Collection - Table
    * Document - Row
    * Key Value Pairs - Fields

## Data Warehousing?
* Used for business intelligence, tools like Cognos, Jaspersoft, SQL Server, Reporting Services, Oracle Hyperion, and SAP NetWeaver
* Used to pull in very large and complex data sets. Usually used by Mgmt to do queries on data (such as current performance v targets, etc.)

__OLTP -v- OLAP__
Online Transaction Porcessing (OLTP) differs from Online Analytical Processing in terms of the types of queries you will run

_OLTP Example_:

Order number 2120121 - pulls up a row of data such as name, date, address to devlier to, delivery status etc.

_OLAP Example_:

Net profit for EMEA and Pacific for the Digital Radio Product.
Pulls in large number of records

Data warehousing DBs use different types of architecture both from a DB perspective and infrastructure layer.

## Elasticache
* A web service that makes it easy to deploy, operate and scale an in-memory cache in the cloud
* Service improves the performance of web apps by allowign you to retrieve information from fast, managed, in-memory caches, instead of relying entirely on slower disk-based DBs
* Elasticache supports two open-source in-memory caching engines:
    * Memcached
    * Redis

## Database Types - Summary:

1. RDS - OLTP
  * SQL
  * MySQL
  * PostgreSQL
  * Oracle
  * Aurora
  * MariaDB
2. DynamoDB - No SQL
3. RedShift - OLAP
4. Elasticache - In memory Caching
  * Memcached
  * Redis

## RDS Lab

Setup RDS through UI (MySQL - free tier)
Create EC2 instance - Step 3 - Configure Instance Details - Include shell bootstrap script

```bash
#!/bin/bash
yum install httpd php php-mysql -y
yum update -y
chkconfig httpd on
service httpd start
echo "<?php phpinfo();?>" > /var/www/html/index.php
cd var/www/html
wget https://s3.eu-west-1.amazonaws.com/experimenting-aws/connect.php
```

In s3 instance file will look like this (copy pasta the relevant value in here beforehand):

```php
<?php
$username = "sampledb";
$password = "sampledb";
$hostname = ""; // extracted from RDS dashboard connect name
$dbname = "sampledb";

//connection to the database
$dbhandle = mysql_connect($hostname, $username, $password) or die("Unable to connect to MySQL");
echo "Connected to MySQL using username - $username, password - $password, host - $hostname<br>";
$selected = mysql_select_db("$dbname", $dbhandle) or die("Unable to connect to MySQL DB - check the database name and try again.");
?>
```

Always given a DNS address (not given a public IPv4 Address)

Note once EC2 starts running you can go to the public IP address and should see the PHP server running.

SSH into EC2 instance (can use public ip address)

```bash
sudo su
cd /var/www/html
ls
```

(should see 2 files from bootstrap in here)

Go to http://34.247.73.41/connect.php

Next - need to allow RDS access…. Go to RDS via console
Go to security groups (click first option)
Click Inbound -> Add Rule
MySQL/Aurora, TCP, 3306 - use security group of your EC2 instance (type sg it will auto complete available options)

Go back to http://34.247.73.41/connect.php and you should see a ‘connected to mysql…….’ message - success!

Common Troubleshooting - RDS & EC2 in different security groups.

Open port 3306 to the security group the RDS instance is in, open up mysql and allow to the security group your EC2 instance is in so the two can talk.

## Automated Backups

* Automated will allow you to recover any point within a 'retention period'
  * Recover will choose most recent daily backup and apply those transaction logs (point in time recovery down to a second within the retention period (1-365 days))
* Enabled by default - stored in S3 & free storage space equal to size of DB.
* Taken within a defined window, during window storage I/O may be suspended while data is being backup -> may experience some low-latency

## Snapshots

* User initiated -> stored even after deletion of original RDS instance.
* Whenever restored, there will be a new RDS instance with a new DNS endpoint.

## Encryption

Encryption at rest is supported for MySQL, Oracle, SQL Server, PostgreSQL, MariaDB & Aurora. Done using AWS KMS service. Once RDS is encrypted data stored arest in underlying storage is encrypted as well as its automated backups, read replicas & snapshots. Currently encrypting existing DB instance isn't supported.

## Multi-AZ (disaster recovery)

_synchronous_

Allows exact copy of prod DB in another availability zone. AWS handles replication, so when your prod DB is written to, the write will auto sync to the stand by DB.

In event of planned DB maintenance, DB failure or Availability one failure, RDS will automatically failover to the standby so DB operations can resume without admin intervention.

Used only for disaster recovery -> not used for improving performance.

Available on:

* SQL Server
* Oracle
* MySQL Server
* PostgreSQL
* MariaDB
* Aurora (enabled by default)

## Read Replicas (performance improvements)

_asynchronous_

* Used for scaling not disaster recovery.
* Must have auto backups turned on in order to deploy a read replicas
* You can have up to 5 read replica copies of any DB
* You can have read replicas of replicas (watch latency)
* Each read replica will have its own DNS endpoint
* You can have read replicas that have Multi-AZ
* You can create read replicas of Multi-AZ source DBs
* Read replicas can be promoted to be their own DBs, this breaks the replication.
* You can have a read replica in a second region.

Available on:

* MySQL
* PostgreSQL
* MariaDB
* Aurora
