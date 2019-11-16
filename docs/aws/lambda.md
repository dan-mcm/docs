# AWS Lambda

* Data Centres
* Hardware
* Assembly Code/Protocols
* High Level Languages
* Operating Systems
* Application Layer/AWS APIs
* AWS Lambda

AWS Lambda is a compute service where you can upload your code and create a Lambda function.
AWS Lambda takes care of provisioning and managing the servers that you use to run the code.
You don't have to worry about operating systems, patching, scaling etc. You can use lambda in the following ways:

* As an event-driven compute service where AWS Lambda runs your code in response to events.
These events could be changes to data in an Amazon S3 bucket or an Amazon DynamoDB table.

* As a compute service to run your code in response to HTTP requests using Amazon API Gateway
or API calls made using AWS SDKs

## What Languages?

* Node.js
* Java
* Python
* C#
* Go

## How Priced?

* Number of requests
    * First 1 million requests are free. $0.20 per 1 million requests thereafter.

* Duration
    * Duration is calculated from the time your code begins executing until it returns or otherwise terminates,
    rounded up to the nearest 100ms. The price depends on the amount of memory you allocate to your function.
    You are charged $0.00001667 for every GB-second used.,

## Why is Lambda Cool?

* No servers
* Continuous Scaling
* Super cheap
* Alexa (Amazon echo) uses Lambda

## Exam Tips

* Lambda scales out (not up) automatically
* Lambda functions are independent, 1 event = 1 function
* Lambda is serverless / a compute service
* Know what services are serverless (S3 / DynamoDB / API Gateway / Lambda)
    * RDS & EC2 are not serverless (except potentially Aurora)
* Lambada functions can trigger other lambda functions, 1 event can = x functions if functions trigger other functions
* Architectures can get extremely complicated, AWS X-ray allows you to debug what is happening.
* Lambda can do things globally, you can use it to back up S3 buckets to other S3 buckets etc.
* Know your triggers

# Version Control with Lambda

When you use versioning in AWS Lambda, you can publish one or more versions of your Lambda function.
As a result, you can work with different variations of your Lambda function in your dev workflow, such a dev, beta, prod.

Each Lambda function version has a unique Amazon Resource Name (ARN). After you publish a version, it is immutable.

AWS Lambda maintains your latest function code in the $LATEST version. When you update your function code, AWS Lambda
replaces the code in the $LATEST version of the Lambda function.

### Qualified/Unqualified ARNs

* You can refer to this function using its Amazon Resource Name. There are two ARNs associated with this initial version:
    * Qualified ARN - The function ARN with the version suffix.
        * arn:aws:lambda:aws-region:acct-id:function:helloworld:$LATEST
    * Unqualified ARN - The function ARN without the version suffix.
        * arn:aws:lambda:aws-region:acct-id:function:helloworld

### Alias

After initially creating a Lambda function (the $LATEST version), you can publish a version 1 of it.
By creating an alias named PROD that points to version 1, you can now use the PROD alias to invoke version 1 of the Lambda function.

Now, you can update the code (the $LATEST version) with all of your improvements, and then publish another stable and improved version (version 2).
You can promote version 2 to production by remapping the PROD alias so that it points to version 2. If you find something wrong, you can easily roll
back the production version to version 1 by remapping the PROD alias so that it points to version 1.

Note: you can shift traffic between Alias using UI and defining the % weights. (can't use this with $LATEST)

## Exam Tips

* Can have multiple versions of lambda functions
* Latest version will use $LATEST
* Qualified version will use $latest, unqualified will not have it
* Versions are immutable
* Can split traffic using aliases to different versions
    * Cannot split traffic with $latest, instead create an alias to latest

## Lambda Concurrent Executions Limit

* Not necessary to memorize lots of limits for the Exam
* Be aware that there is a concurrent limit for Lambda
* Safety feature to limit the number of concurrent executions across all functions
in a given region per account
* Default is 1,000 per region
* TooManyRequestsException
* HTTP Status Code: 429
* Request throughput limit exceeded

* If you have many Lambda functions running in the same region and you suddenly start
seeing new invocation requests being rejected, then you may have hit your limit
* At ACG, the daily usage is around 6.5m Lambda Invocations per day in us-east-1
* Request an increase on this limit by submitting a request to the AWS Support Center
* Reserved concurrency guarantees that a set number of executions which will always be
available for your critical function, however this also acts as a limit

## Lambda Concurrent Execution Limit - Exam Tips

* Know that a limit exists - 1,000 concurrent executions per second
* If you are running a serverless website like ACG, its likely you will hit the limit at some point
* If you hit the limit you will start to see invocations being rejected - 429 HTTP status code
* The remedy is to get the limit raised by AWS support
* Reserved Concurrency guarantees a set number of concurrent executions are always available to
a critical function

## Lambda Versions

* When you create a lambda function there is only on version: $LATEST
* When you upload a new version fo the code to Lambda, this version will become $LATEST
* You can create multiple versions of your function code and use aliases to reference the
version you want to use as part of the ARN e.g. in a development environment you might want
to maintain a few versions of the same function as you develop and test your code
* An alias is like a pointer to a specific version of the function code

## Lambda Version - Exam Tips

* $LATEST is always the last version of code you uploaded to Lambda
* Use Lambda Versioning and Aliases to point your application to a specific version if you
don't want to use $LATEST
    * arn:aws:lambda:eu-west-2:738450006354:function:mylambda:Prod
    * arn:aws:lambda:eu-west-2:738450006354:function:mylambda:$LATEST
* If your application uses an alias, instead of $LATEST remember that it will not
automatically use new code when you upload it

## Lambda & VPC Access

* Some use cases require Lambda to access resources which are inside a private VPC e.g.
read or write to an RDS DB, or shut down an EC2 in response to a security alert

## Enabling Lambda to Access VPC Resources

* To enable this, you need to allow the function to connect in the private subnet.
* Lambda needs the following VPC Configuration information so that it can connect to
the VPC:
    * Private subnet ID
    * Security group ID (with required access)
    * Lambda uses this information to set up ENIs using an available IP address from
    your private subnet.
* You add VPC information to your Lambda function config using the vpc-config parameter.
`aws lambda-function-configuration --function-name my-function --vpc-config
SubnetIds=subnet-1122aabb,SecurityGroupIds=51530134`


## Lambda & VPCs - Exam Tips

* It is possible to enable Lambda to access resources which are inside a private VPC
* Provide VPC config to the function - private subnet ID, security group ID
* Lambda uses the VPC information to set up ENIs using an IP from the private subnet CIDR range
* The security group then allows your function to access resources in VPC
