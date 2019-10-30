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

# Step Functions
