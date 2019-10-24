# CloudWatch

Amazon CloudWatch is a monitoring service to monitor your AWS resources, as well as the applications that you run on AWS.

It can monitor things like:

* Compute
    * Autoscaling Groups
    * Elastic Load Balancers
    * Route53 Health Checks


* Storage & Content Delivery:
    * EBS Volumes
    * Storage Gateways
    * CloudFront


* Database & Analytics
    * DynamoDB
    * Elasticache Nodes
    * RDS Instances
    * Elastic MapReduce Job Flows
    * Redshift


* Other
    * SNS Topics
    * SQS Queues
    * Opsworks
    * CloudWatch Logs
    * Estimated Charges on AWS Bill

## CloudWatch and EC2

Host Level Metrics Consist of:

* CPU
* Network
* Disk

* Status Check

Exam Tip: RAM Utilization is a custom metrics. By Default EC2 monitoring is 5 minute intervals,
unless you enable detailed monitoring which will then make it 1 minute intervals.

How long are the Metrics Stored?

You can retrieve data using the GetMetricStatics API or by using third party tools offered by AWS partners.

You can store your log data in CloudWatch Logs for as long as you want. By default, CloudWatch Logs will store
your log data indefinitely. You can change the retention for each Log Group at any time.

You can retrieve data from any terminated EC2 or ELB instance after its termination.

## Metric Granularity

It depends on the AWS service. Many default metrics for many default services are 1 minute, but it can be
3 or 5 minutes depending on the service.

Exam Tip: for custom metrics the minimum granularity that you can have is 1 minute.

## CloudWatch Alarms

You can create an alarm to monitor any Amazon CloudWatch metric in your account. This can include
EC2 CPU Utilization, Elastic Load Balancer Latency or even the charges on your AWS bill. You can set
the appropriate thresholds in which to trigger the alarms and also set what actions should be taken if an alarm state is reached.

Note: CloudWatch can be used on premise - Not restricted to just AWS resources. Can be on premise too.
Just need to download and install the SSM agent and Cloudwatch agent.

Note: Detailed Monitoring - 1 Minute; Standard Monitoring - 5 Minutes.

# CloudWatch Lab

## Monitoring EC2 with Custom Metrics

IAM -> Roles -> Create Role -> Service use Role: EC2 -> Apply 'CloudWatchFullAccess' Policy

EC2 -> Launch Instance -> Amazon Linux 2 AMI -> Defaults (Add custom IAM roll) -> Bootstrap Script:
(Generate Security Group & KeyPair as usual...)
```
#!/bin/bash
yum update -y
sudo yum install -y perl-Switch perl-DateTime perl-Sys-Syslog perl-LWP-Protocol-https perl-Digest-SHA.x86_64
cd /home/ec2-user/
curl https://aws-cloudwatch.s3.amazonaws.com/downloads/CloudWatchMonitoringScripts-1.2.2.zip -O
unzip CloudWatchMonitoringScripts-1.2.2.zip
rm -rf CloudWatchMonitoringScripts-1.2.2.zip
```

Get started with sshing into EC2

```
chmod 400 myKeyPair.pem
ssh ec2-user@public-ip -i myKeyPair.pem
yes

sudo su
ls (should have aws-scripts-mon)
cd aws-scripts-mon
ls
```

Access CloudWatch from AWS UI.
Click 'Browse Metrics' -> EC2 -> Per Instance Metrics (default metrics)

```
/home/ec2-user/aws-scripts-mon/mon-put-instance-data.pl --mem-util --verify --verbose
('verifciation completed successfully. Not actual metrics were sent to CloudWatch')

/home/ec2-user/aws-scripts-mon/mon-put-instance-data.pl --mem-util --mem-used --mem-avail
('Successfully reported metrics to CloudWatch.')
```

Can take up to 5 minutes... but back to AWS UI -> CloudWatch should see 'Customer Namespaces' -> Linux System.

```
cd /etc
nano crontab

# add the cron below & save
# pushing this memory data every 1 minute
*/1 * * * * root /home/ec2-user/aws-scripts-mon/mon-put-instance-data.pl --mem-util --mem-used --mem-avail
```

Wait up to 20 minutes... see data populate in CloudWatch.
As detailed monitoring not turned on -> only have data points every 5 minutes.

## Exam Tips

Host Level Metrics Consist of:
* CPU
* Network
* Disk (I/O - not storage)
* Status Check

Ram Utilization -is a custom metric
Customer Metrics - minimum granularity is 1 minute (will require detailed monitoring turned on)

# CloudWatch -v- CloudTrail -v- Config

* CloudWatch monitor performance
* CloudTrail monitors API calls in the AWS platform
* AWS Config records the state of your AWS environment and can notify you of changes ('CCTV')
