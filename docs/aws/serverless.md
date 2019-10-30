# Serverless 101

EC2 Launches 2006 (provision machines from CLI -> IAAS born!)
Data Centre -> IAAS (EC2) -> PAAS (Azure/ElasticBeanstalk) -> Containers (Docker) -> Serverless (Lambda)

## Lab Example

* Setup S3 Bucket with Public Site Hosting Setup
    * Upload index.html & error.html
* Register Route53 (need bucket & domain name to be same)
* Create Lambda Function - multiple options (select author from scratch)
    * Author from Scratch
    * Blueprints
    * Serverless App Repository (CF Templates)
* Provide Name, Create Role for Lambda (policy template: simple microservice permissions) -> Create Function.
* Scroll to Function code (used to be 'Cloud 9' IDE) copy pasta in code sample below
* Select 'Add Trigger' -> [popular exam - which of the following is not a trigger] -> select API Gateway
    * Configure triggers -> pick an existing API, or create a new one (create new one -> filling in UI)
* Need to configure GET endpoint in Amazon API Gateway & then deploy API

Sample Lambda Function (python)

```python
def lambda_handler(event, context):
    print("In lambda handler")

    resp = {
        "statusCode": 200,
        "headers": {
            "Access-Control-Allow-Origin": "*",
        },
        "body": "Dan McM"
    }

    return resp
```
