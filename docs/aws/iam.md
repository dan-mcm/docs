# AWS IAM

IAM consists of:

* Users
* Groups [Group users and apply policies collectively]
* Roles
* Policy Documents [JSON]


## Basics:

* IAM is universal - does not apply to regions at this time
* The ‘root account’ is account created when first setup your AWS account (complete admin access)
* New Users have no permissions when first created
* New Users are assigned Access Key ID & Secret Access Keys when first created
* These are not same as a password, you cannot use the key ID and secret access key to login to the AWS management console
* You can use this to access AWS via the APIs and CLI


## Account Setup:

* You only get to view Access Key ID & Secrete Access Key once. If lost, you have to regenerate them.
* Always setup Multifactor Authentication (MFA) on your root account
* You can create and customise your own password rotation policies

## Practical:


_Applying IAM role to EC2_:

Go to EC2 in UI
Select Actions/Instance-Settings/
Attach/Replace IAM role

_Modifying S3 within EC2_:

```bash
cd ~/.aws [contains config credentials]
aws s3 ls
aws s3 ls s3://experimenting-aws
echo "Hello v2 from EC2" > hello2.txt
aws s3 cp hello2.txt s3://experimenting-aws
```

## Exam Tips

* Roles allow you to not use Access Key ID’s and Secret Access Keys
* Roles are preferred from a security perspective
* Roles are controlled by policies
* You can change a policy on a role and it will take immediate affect
* You can attach and detach roles to running EC2 instances without having to stop or terminate these instances
