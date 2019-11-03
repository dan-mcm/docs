# AWS SAM (Serverless Application Model)

* SAM is an extension to CloudFormation used to define serverless applications
* Simplified syntax for defining serverless roesources:
    * APIs
    * Lambda Functions
    * DynamoDB Tables etc.
* Use the SAM CLI to package your deployment code, upload it to S3 and deploy your serverless application

## SAM CLI Commands

```bash
sam package \
  --template-file ./mytemplate.yml \
  --output-template-fil sam-template.yml \
  --s3-bucket s3-bucket-name


sam deploy \
  --template-file sam-template.yml \
  --stack-name mystack \
  --capabilities CAPABILITY_IAM
```

## Exam Tips

* SAM is the Serverless Application Model
* Allows you to define and provision serverless applications using CloudFormation
* Uses the SAM CLI commands to package and deploy:
    * sam package - packages your applicaiton and uploads to S3
    * sam deploy - deploys your serverless app using CloudFormation

# Lab Notes

```bash
# 1) Pre-requisties: After installing Python 3.x, install pip and the SAC CLI:
easy_install pip
pip install aws-sam-cli

# validate the IAM user your using -> ensure has S3FullAccess Policy
aws iam get-user

# 2) Create an S3 bucket:
aws s3 mb s3://cfsambucket --region eu-west-2  #use the bucket name and region name of your choice

# 3) Package your deployment:
sam package \
--template-file ./lambda.yml \
--output-template-file sam-template.yml \
--s3-bucket cfsambucket

#use the bucket name you used in the previous step
#Windows users: replace \ with ^ (use Shift + 6) for line continuation

#4) Deploy your package:
sam deploy \
--template-file sam-template.yml \
--stack-name mystack \
--capabilities CAPABILITY_IAM

#Windows users: replace \ with ^ (use Shift + 6) for line continuation
```

index.js Lambda function
```javascript
exports.handler = (event, context, callback) => {
    // TODO implement
    const response = {
        statusCode: 200,
        body: JSON.stringify('Hello Cloud Gurus, This Lambda Function was deployed using SAM!')
    };
    callback(null, response);
};
```

CloudFormation Lambda (Transform clarifies its SAM)

```yaml
AWSTemplateFormatVersion: '2010-09-09'
Transform: AWS::Serverless-2016-10-31
Resources:
  TestFunction:
    Type: AWS::Serverless::Function
    Properties:
      Handler: index.handler
      Runtime: nodejs8.10
      Environment:
        Variables:
          S3_BUCKET: cfsambucket
```
