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

## X-Ray Configuration

* You need both the X-Ray SDK & Daemon on your systems
* You use the SDK to instrument your application to send the required data e.g.
data about incoming and outgoing HTTP requests that are being made to your Java application

### High Level Configuration Steps

* On premises & EC2 System
    * Install the X-Ray daemon on your EC2 instance or on-premises server.
* Elastic Beanstalk
    * Install the X-Ray daemon on the EC2 instances inside your Elastic Beanstalk environment.
* Elastic Container Service
    * Install the X-Ray daemon in its own Docker container on your ECS cluster alongside your app.

## Annotations & Indexing

* Annotations: when instrumenting your application, you can record additional information
about requests by using annotations.
* Key-value pairs: Annotations are simple key-value pairs that are indexed for use with filter
expressions, so that you can search for traces that contain specific data and group related
traces together in the console - e.g. game_name=TicTacToe,game_id=1350868349

## Configuration - Exam Tips

* X-Ray integrates with many AWS services like DynamoDB, Lambda, API Gateway etc.
* You can also instrument your own applications to send data to X-Ray
* Applications can be run on EC2, Elastic Beanstalk environments, on-premises systems and ECS
* For ECS, run the X-Ray daemon in it's own Docker image, running alongside your application
* Need three things to get started:
    * X-Ray SDK
    * X-Ray Daemon
    * Instrument your application using the SDK to send the required data to X-Ray e.g.
    data about incoming HTTP requests to your application
* If you want to also record application specific information in the form of key-value
pairs, use annotations to add user defined key-value pairs to your X-Ray data e.g.
game_name=TicTacToe,game_id=1350868349  
