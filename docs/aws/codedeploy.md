# AWS CodeDeploy

* This is an automated deployment service which allows you to deploy your app code automatically to
EC2 instances, on-premise systems and Lambda functions.
* Allows you to quickly release new features, avoid downtime during application deployments, and avoid
the risks associated with manual processes
* Automatically scales with your infra and integrates with various CD/CD tools e.g. Jenkins, GitHub,
Atlassian, AWS CodePipeline as well as config management tools like Ansible, Puppet and Chef
* Two deployment approaches available:
    * In-Place and Blue/Green

## In Place

* The application is stopped on each instance and the latest revision installed
* The instance is out of service during this time and your capacity will be reduced
* If the instances are behind a load balancer, you can configure the load balancer to stop sending
requests to the instances which are being upgraded.
* Also known as a 'Rolling Update'
* It can only be used for EC2 and on-premise systems - not supported for Lambda
* If you need to roll back changes, the previous version of the App will need to be re-deployed

## Blue / Green Deployment

* New instances are provisioned at the latest revision is installed on the new instances.
Blue represents the active deployment, green is the new release
* The new instances are registered with an ELB, traffic is then routed to the new instances and the original instances
eventually terminated
* Advantages of Blue/Green are that the new instances are created ahead of time and the code released to production by
simply switching all traffic to the new servers
* Switching back to the original environment is faster and more reliable and is just a case of routing the traffic back to
the original servers (as long as you haven't already terminated them)

## Terminology

* Deployment Group - A set of EC2 instances or Lambda functions to which a new revision of the software is to be deployed
* Deployment - The process and components used to apply a new revision
* Deployment Configuration - A set of deployment rules as well as success / failure conditions used during a deployment
* AppSpec File - Defines the deployment actions you want AWS CodeDeploy to execute
* Revision - Everything needed to deploy the new version: AppSpec file, application files, executables, config files
* Application - Unique identifier for the application you want to deploy. To ensure the correct combination of
revision, deployment configuration and deployment group are referenced during a deployment

## Exam Tips

* AWS CodeDeploy is a fully managed automated deployment service and can be used as part of a Continuous Delivery/Deployment process
* Remember types of deployment approach:
    * In-Place/Rolling:
        * Stop app on each host & deploy latest code
        * EC2 and on premise systems only
        * Roll back requires redeploy of previous version of app
    * Blue/Green:
        * New instances provisioned and new app is deployed to those new instances
        * Traffic routed to new instances on your own schedule
        * Supported for EC2, on-premise & lambda functions
        * Roll back is a traffic route back to the original instances
        * Blue is active deployment, green is new release


## Practical Notes

Setup of CodeDeploy on EC2:

```bash
#Install CodeDeploy agent on your EC2 instance:

sudo yum update
sudo yum install ruby
sudo yum install wget
cd /home/ec2-user
wget https://aws-codedeploy-eu-central-1.s3.amazonaws.com/latest/install
chmod +x ./install
sudo ./install auto
sudo service codedeploy-agent status


#Create your application.zip and load it into CodeDeploy:

aws deploy create-application --application-name mywebapp
aws deploy push --application-name mywebapp --s3-location s3://<MY_BUCKET_NAME>/webapp.zip --ignore-hidden-files
```

Sample AppSpec file

```yaml
version: 0.0
os: linux
files:
  - source: /index.html
    destination: /var/www/html/
hooks:
  BeforeInstall:
    - location: scripts/install_dependencies.sh # yum install -y httpd
      timeout: 300
      runas: root
    - location: scripts/start_server.sh # service httpd start
      timeout: 300
      runas: root
  ApplicationStop:
    - location: scripts/stop_server.sh # service httpd stop
      timeout: 300
      runas: root

```

# Advanced CodeDeploy (AppSpec File)

The AppSpec File is used to define the parameters that will be used for a CodeDeploy deployment.
The file structure depends on whether you are deploying to Lambda or ECS / On Premises.

For Lambda deployments, the AppSpec File may be written in YAML or JSON and contain the following fields:

* version: Reserved for future use - currently the only allowed value is 0.0
* resources: The name and properties of the Lambda function to deploy
* hooks: Specifies Lambda functions to run at set points in the deployment lifecycle to validate the deployment
e.g. validation tests to run before allowing traffic to be sent to your newly deployed instances

## Lambda Example

```yaml
version: 0.0
resources:
  - myLambdaFunction:
      Type: AWS::Lambda:Function
      Properties:
        Name: "myLambdaFunction"
        Alias: "myLambdaFunctionAlias"
        CurrentVersion: "1"
        TargetVersion: "2"

hooks:
  - BeforeAllowTraffic: "LambdaFunctionToValidateBeforeTrafficShift"
  - AfterAllowTraffic: "LambdaFunctionToValidateAfterTrafficShift"
```

## BeforeAllowTraffic

Used to specify the tasks or functions you want to run before traffic is routed to the newly deployed Lambda function
e.g. test to validate that the function has been deployed correctly.

## AfterAllowTraffic

Used to specify the tasks or functions you want to run after the traffic has been routed to the newly deployed Lambda function.
e.g. test to validate that the function is accepting traffic correctly and behaving as expected.

## EC2 / On Premises Example

* version: Reserved for future use - currently the only allowed value is 0.0
* os: The Operating System version you are using e.g. Linux, Windows
* files: The location of any application files that need to be copies and where they should be copied to (source and destination folders)
* hooks: Lifecycle event hooks allow you to specify scripts that need to run as set points in the deployment lifecycle e.g.
to unzip application files prior to deployment, run functional tests on the newly deployed application, and to
de-register and re-0register instances with a load balancer.

For EC2 and On Premises deployments, the appspec.yml must be placed in the root of the directory of your revision - this is the
directory containing your application source code, otherwise the deployment will fail.

Typical setup looks like this:

mywebapp folder:
appspec.yml
/Scripts
/Config
/Source

## Example AppSpec File - EC2

```yaml
version: 0.0
os: linux
files:
  - source: Config/config.txt
    destination: /webapps/Config
  - source: Source
    destination: /webapps/myApp
hooks:
  BeforeInstall:
    - location: Scripts/UnzipResourceBundle.sh
    - location: Scripts/UnzipResourceTests.sh
  AfterInstall:
    - location: Scripts/RunResourceTests.sh
      timeout: 180
  ApplicationStart:
    - location: Scripts/RunFunctionalTests.sh
      timeout: 3600
  ValidateService:
    - location: Scripts/MonitorService.sh
      timeout: 3600
      runas: codedeployuser
```

## Supported Hooks for EC2 and On Premises

The 'run-order' or hooks in a CodeDeploy deployment

When starting application:

* BeforeBlockTraffic - Run tasks on instances before they are deregistered from a load balancer
* BlockTraffic - Deregister instances from a load balancer
* AfterBlockTraffic - Run tasks on instances after they are Deregistered from a load balancer

When upgrading any application:

* ApplicationStop - Gracefully stop the application in prep for deploying the new revision
* DownloadBundle - The CodeDeploy agent copies the app revision files to a temporary location
* BeforeInstall - Details of any pre-installation scripts e.g. backing up current version, decrypting files
* Install - The CodeDeploy agent copies the app revision files from their temporary location to their current location
* AfterInstall - Details of any post-installation scripts e.g. configuration tasks, change file permissions
* ApplicaitonStart - Restarts any services that were stopped during ApplicationStop
* ValidateService - Details of any tests to validate the service

When re-registering your instances with load balancer:

* BeforeAllowTraffic - Run tasks on instances before they are registered with a load balancer
* AllowTraffic - Register instances with a load balancer
* AfterAllowTraffic - Run tasks on instances after they are registered with a load balancer

## Exam Tips - Advanced CodeDeploy

* The AppSpec file defines all the params needed for the deployment e.g. location of app files and pre/post deployment validation tests to run
* For EC2/On Premises systems, the appspec.yml file must be placed in root directory of revision (the same folder that contains app code). Written in YAML
* Lambda supports YAML or JSON
* The run order for hooks in a CodeDeploy deployment:
    * BeforeBlockTraffic -> BlockTraffic -> AfterBlockTraffic
    * ApplicationStop, BeforeInstall, Install, AfterInstall, ApplicationStart, ValidateService
    * BeforeAllowTraffic -> AllowTraffic -> AfterAllowTraffic

## Docker & CodeBuild Lab

* Docker is an Open Source technology which allows you to create applications based on either Linux or Windows containers
* A Container is a lightweight standalone executable software package which includes everything the software needs to run -
code, runtime environments, libraries, environment settings etc.
* AWS provides Elastic Container Service as a fully managed clustered platform which allows you to run your Docker images in the cloud
* AWS CodeBuild is a fully managed build service which runs a set of commands that you define e.g. compiles code, runs tests and produces 
artifacts that are ready to deploy
