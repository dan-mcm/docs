# AWS CLI

## SSH/Putty into EC2 instance

```bash
aws s3 ls
aws configure [copy paste details from programmatic IAM user]
aws s3 mb s3://experimenting-aws [make bucket]
echo "Hello world" > hello.txt
aws s3 cp ./hello.txt s3://experimenting-aws
aws s3 ls s3://experimenting-aws
```

AWS CLI reference: https://docs.aws.amazon.com/cli/latest/index.html

## Exam Tips:

* Least Privilege - always give your users the minimum amount of access required
* Create Groups - assign users to groups. Your users with auto inherit the permissions of the group - the groups permissions are assigned using policy documents.
* Secret Access Key - you will see this only once - if you do not save it you can delete the key pair (access key id and secret access key) and regenerate it. You will need to run aws configure again.
* Do not use just one access key - do not create just one access key and share that with all your developers. If someone leaves the company on bad terms, then you will need to delete the key and create a new one and every developer would then need to update their keys. Instead create on key pair per developer.
* You can use the CLI on your PC [Mac, Linux or Windows]
