# AWS Elastic Beanstalk

Elastic beanstalk is a service to deploying and scaling web applications developed in many languages:
Java, .NET, PHP, Node.js, Python, Ruby, Go, and Docker onto widely used application server platforms
like Apache Tomcat, Nginx, Passenger, and IIS.

Developers can focus on writing code and don't need to worry about any of the underlying infrastructure
needed to run the application.

You upload the code and Elastic Beanstalk will handle deployment, capacity provisioning, load balancing,
auto-scaling and application health.

You retain full control of the underlying AWS resources powering your application and you pay only for
the AWS resources required to store and run your applications e.g. EC2 instances & S3 buckets.

* Fastest and simplest way to deploy your application in AWS
* Automatically scales your application up and down
* You can select the EC2 instance type that is optimal for your application
* You can retain full admin control over the resources powering your app, or have EB do it for you
* Managed Platform Updates feature automatically applies & updates your OS, Java, PHP, Node.js, etc.
* Monitor and manage application health via dashboard
* Integrated with CloudWatch and X-Ray for performance data and metrics

## Exam Tips - EB

* Deploys and scales your web applications including the web application server platform where required
* Supports widely used programming tech - Java, PHP, Python, Ruby, Go, Docker, .NET, Node.js
* Support application server platforms - Tomcat, Passenger, Puma, IIS
* Provisions the underlying resources for you
* Can fully manage the EC2 instances for you or you can take full admin control
* Updates, monitoring, metrics and health checks all included.

# Updating Elastic Beanstalk

EBS (Elastic Beanstalk) Deployment Policies:

Elastic Beanstalk supports several options for processing deployments (deployment policies):

* All at once
* Rolling
* Rolling with additional batch
* Immutable

## All at once

* Deploys the new version to all instances simultaneously
* All of your instances are out of service while the deployment takes place
* You will experience an outage while the deployment is taking place - not ideal for mission-critical production systems
* If the update fails, you need to roll back the changes by re-deploying the original version to all your instances

## Rolling

* Deploys the new version in batches
* Each batch of instances is taken out of service while the deployment takes place
* Your environment capacity will be reduced by the number of instances in a batch while the deployment takes place
* Not ideal for performance sensitive systems
* If the update fails, you need to perform an additional rolling update to roll back the changes

## Rolling with Additional Batch

* Launches an additional batch of instances
* Deploy the new version in batches
* Maintains full capacity during the deployment process
* If the update fails, you need to perform an additional rolling update to roll back the changes

## Immutable

* Deploys the new version to a fresh group of instances in their own new auto-scaling group
* When the new instances pass their health checks they are moved to your existing auto scaling group; and finally, the old instances are terminated
* Maintains full capacity during the deployment process
* The impact of a failed update is far less, and the rollback process requires only terminating the new auto scaling group
* Preferred option for Mission Critical production systems.

## Exam Tips - Updating EB

Remember the 4 different deployment approaches:

* All at Once
    * Service interruption while you update the entire env. at once
    * To roll back, perform a further All at Once upgrade.
* Rolling
    * Reduced capacity during deployment
    * To roll back, perform a further rolling update
* Rolling with Additional Batch
    * Maintains full capacity
    * To roll back, perform a further rolling update
* Immutable
    * Preferred option for mission critical production systems
    * Maintains full capacity
    * To roll back, just delete the new instances and auto-scaling group

# Advanced Elastic Beanstalk

## Configuring Elastic Beanstalk

You can customize your Elastic Beanstalk environment using Elastic Beanstalk configuration files
e.g. you can define packages to install, create Linux users and groups, run shell commands, specify
services to enable or configure your load balancer, etc.)

These are written in YAML or JSON format. They can have a filename of your choice but must have a
.config extension and be saved inside a folder called .ebextensions.

## Location of Configuration Files

The .ebextensions folder must be included in the top-level directory of your application source code bundle.

This means that the configuration files can be placed under source control along with the rest of your application code.

### Example - myhealthcheckurl.config

This .config file configures an Application health-check URL which will be used by the Elastic Load Balancer.

```javascript  
{
  "option_settings":
  [
    {
      "namespace": "aws:elasticbeanstalk:application",
      "option_name": "My Application Healthcheck URL",
      "value": "/healthcheck"
    }
  ]
}
```

## Exam Tips - Advanced Elastic Beanstalk

* You can customize your Elastic Beanstalk environment by adding configuration files
* The files are written in YAML or JSON
* Files have a .config extension
* The .config files are saved to the .ebextensions folder
* Your .ebextensions folder must be located in the top level directory of your application source code bundle

# Elastic Beanstalk & RDS

Elastic Beanstalk supports two ways of integrating an RDS database with your Beanstalk environment.

You can launch the RDS instance from within the Elastic Beanstalk console, which means the RDS instance is created
within your Elastic Beanstalk environment - a good option for Dev and Test deployments.

However this may not be ideal for production environments because it means the lifecycle of your database is tied
to the lifecycle of your application environment. If your terminate the environment, the database instance will be terminated too.

For Production environments, the preferred option is to decouple the RDS instance from your EBS environment i.e. launch it
outside of Elastic Beanstalk, directly from the RDS section of the console.

This option gives you a lot more flexibility, allows you to connect multiple environments to the same database, provides a wider choice of
database types, and allows you to tear down your application environment without affecting the database instance.

## Access to RDS from Elastic Beanstalk

To allows the EC2 instances in your Elastic Beanstalk environment to connect to an outside database,
there are two additional configuration steps required:

* An additional Security Group must be added to your environment's Auto Scaling group
* You'll need to provide connection string configuration information to your application servers
(endpoint, password using Elastic Beanstalk environment properties)

## Exam Tips - RDS Elastic Beanstalk

* Two different options for launching your RDS instance:
* Launch Within Elastic Beanstalk
    * When your terminate the Beanstalk env, the DB will also be terminated
    * Quick and easy to add your DB and get started
    * Suitable for Dev and Test environments only
* Launch Outside Elastic Beanstalk
    * Additional config. steps required - security group & connection information
    * Suitable for Prod environments, more flexibility
    * Allows connection from multiple envs, you can tear down the app stack without impacting the DB
