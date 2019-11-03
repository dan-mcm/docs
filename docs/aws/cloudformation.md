# AWS CloudFormation

* CloudFormation is a service that allows you to manage configure and provision your AWS infrastructure as code.
* Resources are defined using a CloudFormation template
* CloudFormation interprets the template and makes the appropriate API calls to create the resources you have defined
* Supports YAML or JSON

## CloudFormation Benefits

* Infrastructure is provisioned consistently, with fewer mistakes
* Less time and effort than configuring things manually
* You can version control and peer review your templates
* Free to use (charged for what your create)
* Can be used to manage updates & dependencies
* Can be used to rollback and delete the entire stack as well

## CloudFormation Template

* YAML or JSON template used to describe the endstate of the infrastructure you are either provisioning or changing
* After creating the template, you upload it to CloudFormation using S3
* CloudFormation reads the template and makes the API calls on your behalf
* The resulting resources are called a Stack
* https://aws.amazon.com/cloudformation/resources/templates/

Example:

```yaml
AWSTemplateFormatVersion: "2010-09-09"
Description: "Template to create an EC2 instance"
Metadata:
  Instances:
    Description: "Web Server Instance"

Parameters: #input values
  EnvType:
    Description: "Environment type"
    Type: String
    AllowedValues:
      - prod
      - test

Conditions:
  CreateProdResources: !Equals [ !RefEnvType, prod]

Mappings: #e.g. set vlaues based on a region
  RegionMap:
    eu-west-1:
      "ami": "ami-0bdb1d6c15a40392c"

Transform: #include snippets of code outside the main template - https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/CHAP_TemplateQuickRef.html
  Name: 'AWS::Include'
  Parameters:
    Location: 's3://MyAmazonS3BucketName/MyFileName.yaml'

Resources: #the AWS resources you are deploying
  EC2Instance:
    Type: AWS::EC2::Instance
    Properties:
      InstanceType: t2.micro
      ImageId: ami-0bdb1d6c15a40392c

Outputs:
  InstanceID:
    Description: The Instance ID
    Value: !Ref EC2Instance
```

* Resources is the only mandatory section of the CloudFormation template
* Remember that the Transform section is used to reference additional code store in S3, allowing for code
re-use e.g. for Lambda code or template snippets / reusable pieces of CloudFormation code

## Exam Tips

* CloudFormation allows you to manage, configure and provision AWS infrastructure as code (YAML/JSON)
* Remember the main sections in the CF Template:
    * Parameters - input custom values
    * Conditions - e.g. provision resources based on env
    * Resources - Mandatory - the AWS resources to create
    * Mappings - create custom mappings like Region : AMI
    * Transforms - reference code located in S3 e.g. Lambda code or reusable snippets of CF code

## Lab

* CloudFormation -> Create Stack -> Upload a template to Amazon S3

Sample Template

```yaml
AWSTemplateFormatVersion: 2010-09-09

Description: Template to create an EC2 instance and enable SSH

Parameters:
  KeyName:
    Description: Name of SSH KeyPair
    Type: 'AWS::EC2::KeyPair::KeyName'
    ConstraintDescription: Provide the name of an existing SSH key pair

Resources:
  MyEC2Instance:
    Type: 'AWS::EC2::Instance'
    Properties:
      InstanceType: t2.micro
      ImageId: ami-0bdb1d6c15a40392c # note this is region specific (AWS Linux2 AMI in eu-west-1)
      KeyName: !Ref KeyName
      SecurityGroups:
       - Ref: InstanceSecurityGroup
      Tags:
        - Key: Name
          Value: My CF Instance
  InstanceSecurityGroup:
    Type: 'AWS::EC2::SecurityGroup'
    Properties:
      GroupDescription: Enable SSH access via port 22
      SecurityGroupIngress:
        IpProtocol: tcp
        FromPort: 22
        ToPort: 22
        CidrIp: 0.0.0.0/0

Outputs:
  InstanceID:
    Description: The Instance ID
    Value: !Ref MyEC2Instance
```

# CloudFormation Nested Stacks

* Nested Stacks allow re-use of CloudFormation code for common use cases
e.g. standard config for a load balancer, web server, application server etc.

* Instead of copying out the code each time, create a standard template for each common
use case and reference from within your CloudFormation template.

## CloudFormation Template Structure

```yaml
Resources:
  Type: AWS::CloudFormation::Stack
  Properties:
    NotificationARNs:
      - String
    Parameters:
      AWS CloudFormation Stack Parameters
    Tags:
      - Resource Tag
    TemplateURL: https://s3.amazonaws.com/.../template.yml # mandatory - must be file within s3 bucket
    TimeoutInMinutes: Integer
```

## Exam Tips - Nested Stacks

* Nested Stacks allow you to re-use your CloudFormation code so you don't need to copy/paste every time
* Really useful for frequently used configurations, e.g. load balancer, web or app servers
* Simply create a Cloud Formation template, store it in S3 and you can reference it in the Resources section of any CF template 
using the Stack resource type
