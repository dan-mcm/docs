# AWS X-Ray

AWS X-Ray is a service that collects data about requests that your application serves,
and provides tools you can use to view, filter, and gain insights into that data to identify
issues and opportunities for optimization.

For any traced request to your application, you can see detailed information not only about
the request and response, but also about calls that your application makes to downstream AWS
resources, microservices, databases and HTTP web APIs.

1. X-Ray SDK installed within your application
2. SDK sends JSON to X-Ray Daemon (installed on linux/win/osx) - listening on UDP -> sends to X-Ray API
3. X-Ray Console stores visualization

AWS SDK/CLI communicate with Daemon or API directly.

## X-Ray SDK

Provides:

* Interceptors to add to your code to trace incoming HTTP requests
* Client handlers to instrument AWS SDK clients that your application uses to call other AWS services
* An HTTP client to use to instrument calls to other internal and external HTTP web services

## X-Ray Integrations

X-Ray integrates with the following AWS services:

* Elastic Load Balancing
* AWS Lambda
* AWS API Gateway
* Amazon EC2
* AWS Elastic Beanstalk

## X-Ray Languages

X-Ray Supported Languages:

* Java
* Go
* Node.js
* Python
* Ruby
* .Net

## Exam Tips

* What X-Ray SDK Provides
* What X-Ray Integrated with
* What X-Ray languages are supported
