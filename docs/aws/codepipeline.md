# AWS CodePipeline

AWS CodePipeline is a fully managed Continuous Integration & Continuous Delivery service.

It can orchestrate the Build, Test & Deployment of your app every time there is a change to your code - all
based on a user defined software release process.

Traditional manual approaches to code delivery can be slow and prone to errors, whereas an automated process
allows developers to frequently release new features and bug fixes in a fast and consistent way

CodePipeline allows you to model your release process as a workflow or pipeline made up of different tasks,
such as the simple workflow represented below.

Code Update -> Build -> Test -> Deploy

You define what happens and where for each of the different stages of the workflow, and this can
be modelled using the CodePipeline GUI or CLI

Integrates with CodeCommit, CodeBuild, CodeDeploy, Lambda, Elastic Beanstalk, CloudFormation, Elastic Container Service
as well as third party tools, like GitHub and Jenkins.

Every code change pushed to your code repo automatically enters the workflow and triggers the set of
actions defined for each stage of the pipeline

The pipeline automatically stops if one of the stage fails, For example, if one of your automated
unit tests fails. This means that bugs are caught before the code is deployed while they are still
easy to fix.

## Exam Tips

* CI/CD service
* Automates your end-to-end software release process based on a user defined workflow
* Can be configured to automatically trigger your pipeline as soon as a change is detected in your source code repo (S3/CodeCommit)
* Integrates with other services from AWS like CodeBuild and CodeDeploy, as awell as third party and custom plug-ins

# Practical Notes

```bash
# 1) To create your EC2 instance using CloudFormation, first save CF_Template.json to your own S3 bucket,
# then update the command below to reference your bucket as well as the name of a Key pair that you own in the
# region that you are working in.
#
# WINDOWS users will need to use ^ (Shift + 6) instead of \ for line continuation.

aws cloudformation create-stack --stack-name CodeDeployDemoStack \
--template-url http://s3-eu-west-1.amazonaws.com/cftemplates-faye/CF_Template.json \
--parameters ParameterKey=InstanceCount,ParameterValue=1 \
ParameterKey=InstanceType,ParameterValue=t2.micro \
ParameterKey=KeyPairName,ParameterValue=irkp \
ParameterKey=OperatingSystem,ParameterValue=Linux \
ParameterKey=SSHLocation,ParameterValue=0.0.0.0/0 \
ParameterKey=TagKey,ParameterValue=Name \
ParameterKey=TagValue,ParameterValue=CodeDeployDemo \
--capabilities CAPABILITY_IAM

# 2) Verify that the Cloud Formation stack has completed using:
aws cloudformation describe-stacks --stack-name CodeDeployDemoStack --query "Stacks[0].StackStatus" --output text

# 3) Log in to your instance and check that the CodeDeploy agent has correctly installed:  
sudo service codedeploy-agent status
```

CF-Template.json

```JavaScript
{
  "Description": "Create instances ready for CodeDeploy: Create up to 3 Amazon EC2 instances with an associated instance profile and install the AWS CodeDeploy Agent. **WARNING** This template creates one or more Amazon EC2 instances. You will be billed for the AWS resources used if you create a stack from this template. Copyright [2012-2014] Amazon.com, Inc. or its affiliates. All Rights Reserved. Licensed under the Apache License, Version 2.0 (the \"License\"). You may not use this file except in compliance with the License. A copy of the License is located at http://aws.amazon.com/apache2.0/ or in the \"license\" file accompanying this file. This file is distributed on an \"AS IS\" BASIS, WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied. See the License for the specific language governing permissions and limitations under the License.  (Updated May 2019 to use HVM t2.micro Linux(1) or Windows2019-Full-Base )",
  "AWSTemplateFormatVersion": "2010-09-09",
  "Parameters": {
    "TagKey": {
      "Description": "The EC2 tag key that identifies this as a target for deployments.",
      "Type": "String",
	  "Default": "Name",
      "AllowedPattern": "[\\x20-\\x7E]*",
      "ConstraintDescription": "Can contain only ASCII characters."
    },
    "TagValue": {
      "Description": "The EC2 tag value that identifies this as a target for deployments.",
      "Type": "String",
	  "Default": "CodeDeployDemo",
      "AllowedPattern": "[\\x20-\\x7E]*",
      "ConstraintDescription": "Can contain only ASCII characters."
    },
    "KeyPairName": {
      "Description": "Name of an existing Amazon EC2 key pair to enable SSH or RDP access to the instances.",
      "Type": "String",
      "MinLength": "1",
      "MaxLength": "255",
      "AllowedPattern": "[\\x20-\\x7E]*",
      "ConstraintDescription": "KeyPairName is a required Field and can contain only ASCII characters."
    },
    "InstanceType": {
      "Description": "Amazon EC2 instance type.",
      "Type": "String",
      "Default": "t1.micro",
      "ConstraintDescription": "Must be a valid Amazon EC2 instance type."
    },
    "InstanceCount": {
      "Description": "Number of Amazon EC2 instances (Must be a number between 1 and 3).",
      "Type": "Number",
      "Default": "1",
      "ConstraintDescription": "Must be a number between 1 and 3.",
      "MinValue": "1",
      "MaxValue": "3"
    },
    "OperatingSystem": {
      "Description": "Amazon EC2 operating system type (Linux or Windows).",
      "Type": "String",
      "Default": "Linux",
      "ConstraintDescription": "Must be Windows or Linux.",
      "AllowedValues": [
        "Linux",
        "Windows"
      ]
    },
    "SSHLocation": {
      "Description": "The IP address range that can be used to connect using SSH or RDP to the Amazon EC2 instances.",
      "Type": "String",
      "MinLength": "9",
      "MaxLength": "18",
      "Default": "0.0.0.0/0",
      "AllowedPattern": "(\\d{1,3})\\.(\\d{1,3})\\.(\\d{1,3})\\.(\\d{1,3})/(\\d{1,2})",
      "ConstraintDescription": "Must be a valid IP CIDR range of the form x.x.x.x/x."
    }
  },
  "Mappings": {
    "RegionOS2AMI": {
      "us-east-1": {
        "Linux": "ami-0080e4c5bc078760e",
        "Windows": "ami-0204606704df03e7e"
      },
      "us-west-2": {
        "Linux": "ami-01e24be29428c15b2",
        "Windows": "ami-08f938baed1592ad4"
      },
      "eu-west-1" : {
        "Linux" : "ami-08935252a36e25f85",
        "Windows" : "ami-02f0a4701f9b8a91e"
      },
      "ap-southeast-2" : {
        "Linux" : "ami-02fd0b06f06d93dfc",
        "Windows" : "ami-04c715c762e2de351"
      }
    },

    "OS2SSHPort": {
      "Linux": {
        "SSHPort": "22"
      },
      "Windows": {
        "SSHPort": "3389"
      }
    }
  },
  "Conditions": {
    "LaunchInstance2": {
      "Fn::Or": [
        {
          "Fn::Equals": [
            "2",
            {
              "Ref": "InstanceCount"
            }
          ]
        },
        {
          "Fn::Equals": [
            "3",
            {
              "Ref": "InstanceCount"
            }
          ]
        }
      ]
    },
    "LaunchInstance3": {
      "Fn::Equals": [
        "3",
        {
          "Ref": "InstanceCount"
        }
      ]
    },
    "LaunchLinuxEC2Instance1": {
      "Fn::Equals": [
        {
          "Ref": "OperatingSystem"
        },
        "Linux"
      ]
    },
    "LaunchLinuxEC2Instance2": {
      "Fn::And": [
        {
          "Condition": "LaunchLinuxEC2Instance1"
        },
        {
          "Condition": "LaunchInstance2"
        }
      ]
    },
    "LaunchLinuxEC2Instance3": {
      "Fn::And": [
        {
          "Condition": "LaunchLinuxEC2Instance1"
        },
        {
          "Condition": "LaunchInstance3"
        }
      ]
    },
    "LaunchWindowsEC2Instance1": {
      "Fn::Equals": [
        {
          "Ref": "OperatingSystem"
        },
        "Windows"
      ]
    },
    "LaunchWindowsEC2Instance2": {
      "Fn::And": [
        {
          "Condition": "LaunchWindowsEC2Instance1"
        },
        {
          "Condition": "LaunchInstance2"
        }
      ]
    },
    "LaunchWindowsEC2Instance3": {
      "Fn::And": [
        {
          "Condition": "LaunchWindowsEC2Instance1"
        },
        {
          "Condition": "LaunchInstance3"
        }
      ]
    }
  },
  "Resources": {
    "LinuxEC2Instance": {
      "Type": "AWS::EC2::Instance",
      "Condition": "LaunchLinuxEC2Instance1",
      "Metadata": {
        "AWS::CloudFormation::Init": {
          "services": {
            "sysvint": {
              "codedeploy-agent": {
                "enabled": "true",
                "ensureRunning": "true"
              }
            }
          }
        }
      },
      "Properties": {
        "ImageId": {
          "Fn::FindInMap": [
            "RegionOS2AMI",
            {
              "Ref": "AWS::Region"
            },
            {
              "Ref": "OperatingSystem"
            }
          ]
        },
        "InstanceType": {
          "Ref": "InstanceType"
        },
        "SecurityGroups": [
          {
            "Ref": "SecurityGroup"
          }
        ],
        "UserData": {
          "Fn::Base64": {
            "Fn::Join": [
              "",
              [
                "#!/bin/bash -ex\n",
                "yum update -y aws-cfn-bootstrap\n",
                "yum install -y aws-cli\n",
                "# Helper function.\n",
                "function error_exit\n",
                "{\n",
                "  /opt/aws/bin/cfn-signal -e 1 -r \"$1\" '",
                {
                  "Ref": "WaitHandle"
                },
                "'\n",
                "  exit 1\n",
                "}\n",
                "# Install the AWS CodeDeploy Agent.\n",
                "cd /home/ec2-user/\n",
                "aws s3 cp 's3://aws-codedeploy-us-east-1/latest/codedeploy-agent.noarch.rpm' . || error_exit 'Failed to download AWS CodeDeploy Agent.'\n",
                "yum -y install codedeploy-agent.noarch.rpm || error_exit 'Failed to install AWS CodeDeploy Agent.' \n",
                "/opt/aws/bin/cfn-init -s ",
                {
                  "Ref": "AWS::StackId"
                },
                " -r LinuxEC2Instance --region ",
                {
                  "Ref": "AWS::Region"
                },
                " || error_exit 'Failed to run cfn-init.'\n",
                "# All is well, so signal success.\n",
                "/opt/aws/bin/cfn-signal -e 0 -r \"AWS CodeDeploy Agent setup complete.\" '",
                {
                  "Ref": "WaitHandle"
                },
                "'\n"
              ]
            ]
          }
        },
        "KeyName": {
          "Ref": "KeyPairName"
        },
        "Tags": [
          {
            "Key": {
              "Ref": "TagKey"
            },
            "Value": {
              "Ref": "TagValue"
            }
          }
        ],
        "IamInstanceProfile": {
          "Ref": "InstanceRoleInstanceProfile"
        }
      }
    },
    "WindowsEC2Instance": {
      "Type": "AWS::EC2::Instance",
      "Condition": "LaunchWindowsEC2Instance1",
      "Metadata": {
        "AWS::CloudFormation::Init": {
          "config": {
            "commands": {
              "00-download-host-agent": {
                "command": {
                  "Fn::Join": [
                    "",
                    [
                      "powershell.exe -Command \"Read-S3Object ",
                      "-BucketName aws-codedeploy-us-east-1 ",
                      "-Key latest/codedeploy-agent.msi ",
                      "-File codedeploy-agent.msi\""
                    ]
                  ]
                },
                "cwd": "C:/cfn",
                "waitAfterCompletion": 0
              },
              "01-install-host-agent": {
                "command": "C:\\cfn\\codedeploy-agent.msi /quiet /l C:\\cfn\\host-agent-install-log.txt",
                "ignoreErrors": "true",
                "waitAfterCompletion": 0
              },
              "02-signal-ready": {
                "command": {
                  "Fn::Join": [
                    "",
                    [
                      "\"C:\\Program Files\\Amazon\\cfn-bootstrap\\cfn-signal\"",
                      " -e 0 \"",
                      {
                        "Ref": "WaitHandle"
                      },
                      "\""
                    ]
                  ]
                },
                "waitAfterCompletion": 0
              }
            },
            "services": {
              "windows": {
                "codedeploy-agent": {
                  "enabled": "true",
                  "ensureRunning": "true",
                  "commands": [
                    "01-install-host-agent"
                  ]
                }
              }
            }
          }
        }
      },
      "Properties": {
        "ImageId": {
          "Fn::FindInMap": [
            "RegionOS2AMI",
            {
              "Ref": "AWS::Region"
            },
            {
              "Ref": "OperatingSystem"
            }
          ]
        },
        "InstanceType": {
          "Ref": "InstanceType"
        },
        "SecurityGroups": [
          {
            "Ref": "SecurityGroup"
          }
        ],
        "UserData": {
          "Fn::Base64": {
            "Fn::Join": [
              "",
              [
                "<script>\n",
                "mkdir c:\\cfn\n",
                "mkdir c:\\cfn\\log\n",
                "c:\\\"Program Files\"\\Amazon\\cfn-bootstrap\\cfn-init.exe -s ",
                {
                  "Ref": "AWS::StackName"
                },
                " --region ",
                {
                  "Ref": "AWS::Region"
                },
                " -r WindowsEC2Instance > c:\\cfn\\log\\cfn-call-log 2>&1",
                "</script>"
              ]
            ]
          }
        },
        "KeyName": {
          "Ref": "KeyPairName"
        },
        "Tags": [
          {
            "Key": {
              "Ref": "TagKey"
            },
            "Value": {
              "Ref": "TagValue"
            }
          }
        ],
        "IamInstanceProfile": {
          "Ref": "InstanceRoleInstanceProfile"
        }
      }
    },
    "WaitHandle": {
      "Type": "AWS::CloudFormation::WaitConditionHandle"
    },
    "WaitCondition": {
      "Type": "AWS::CloudFormation::WaitCondition",
      "Properties": {
        "Count": {
          "Ref": "InstanceCount"
        },
        "Handle": {
          "Ref": "WaitHandle"
        },
        "Timeout": "900"
      }
    },
    "SecurityGroup": {
      "Type": "AWS::EC2::SecurityGroup",
      "Properties": {
        "GroupDescription": "Enable HTTP access via port 80 and SSH access.",
        "SecurityGroupIngress": [
          {
            "IpProtocol": "tcp",
            "FromPort": "80",
            "ToPort": "80",
            "CidrIp": "0.0.0.0/0"
          },
          {
            "IpProtocol": "tcp",
            "FromPort": {
              "Fn::FindInMap": [
                "OS2SSHPort",
                {
                  "Ref": "OperatingSystem"
                },
                "SSHPort"
              ]
            },
            "ToPort": {
              "Fn::FindInMap": [
                "OS2SSHPort",
                {
                  "Ref": "OperatingSystem"
                },
                "SSHPort"
              ]
            },
            "CidrIp": {
              "Ref": "SSHLocation"
            }
          }
        ]
      }
    },
    "CodeDeployTrustRole": {
      "Type": "AWS::IAM::Role",
      "Properties": {
        "AssumeRolePolicyDocument": {
          "Statement": [
            {
              "Sid": "1",
              "Effect": "Allow",
              "Principal": {
                "Service": [
                  "codedeploy.us-east-1.amazonaws.com",
                  "codedeploy.us-west-2.amazonaws.com",
                  "codedeploy.eu-west-1.amazonaws.com",
                  "codedeploy.ap-southeast-2.amazonaws.com"
                ]
              },
              "Action": "sts:AssumeRole"
            }
          ]
        },
        "Path": "/"
      }
    },
    "CodeDeployRolePolicies": {
      "Type": "AWS::IAM::Policy",
      "Properties": {
        "PolicyName": "CodeDeployPolicy",
        "PolicyDocument": {
          "Statement": [
            {
              "Effect": "Allow",
              "Resource": [
                "*"
              ],
              "Action": [
                "ec2:Describe*"
              ]
            },
            {
              "Effect": "Allow",
              "Resource": [
                "*"
              ],
              "Action": [
                "autoscaling:CompleteLifecycleAction",
                "autoscaling:DeleteLifecycleHook",
                "autoscaling:DescribeLifecycleHooks",
                "autoscaling:DescribeAutoScalingGroups",
                "autoscaling:PutLifecycleHook",
                "autoscaling:RecordLifecycleActionHeartbeat"
              ]
            }
          ]
        },
        "Roles": [
          {
            "Ref": "CodeDeployTrustRole"
          }
        ]
      }
    },
    "InstanceRole": {
      "Type": "AWS::IAM::Role",
      "Properties": {
        "AssumeRolePolicyDocument": {
          "Statement": [
            {
              "Effect": "Allow",
              "Principal": {
                "Service": [
                  "ec2.amazonaws.com"
                ]
              },
              "Action": [
                "sts:AssumeRole"
              ]
            }
          ]
        },
        "Path": "/"
      }
    },
    "InstanceRolePolicies": {
      "Type": "AWS::IAM::Policy",
      "Properties": {
        "PolicyName": "InstanceRole",
        "PolicyDocument": {
          "Statement": [
            {
              "Effect": "Allow",
              "Action": [
                "autoscaling:Describe*",
                "cloudformation:Describe*",
                "cloudformation:GetTemplate",
                "s3:Get*"
              ],
              "Resource": "*"
            }
          ]
        },
        "Roles": [
          {
            "Ref": "InstanceRole"
          }
        ]
      }
    },
    "InstanceRoleInstanceProfile": {
      "Type": "AWS::IAM::InstanceProfile",
      "Properties": {
        "Path": "/",
        "Roles": [
          {
            "Ref": "InstanceRole"
          }
        ]
      }
    },
    "LinuxEC2Instance2": {
      "Type": "AWS::EC2::Instance",
      "Condition": "LaunchLinuxEC2Instance2",
      "Metadata": {
        "AWS::CloudFormation::Init": {
          "services": {
            "sysvint": {
              "codedeploy-agent": {
                "enabled": "true",
                "ensureRunning": "true"
              }
            }
          }
        }
      },
      "Properties": {
        "ImageId": {
          "Fn::FindInMap": [
            "RegionOS2AMI",
            {
              "Ref": "AWS::Region"
            },
            {
              "Ref": "OperatingSystem"
            }
          ]
        },
        "InstanceType": {
          "Ref": "InstanceType"
        },
        "SecurityGroups": [
          {
            "Ref": "SecurityGroup"
          }
        ],
        "UserData": {
          "Fn::Base64": {
            "Fn::Join": [
              "",
              [
                "#!/bin/bash -ex\n",
                "yum update -y aws-cfn-bootstrap\n",
                "yum install -y aws-cli\n",
                "# Helper function.\n",
                "function error_exit\n",
                "{\n",
                "  /opt/aws/bin/cfn-signal -e 1 -r \"$1\" '",
                {
                  "Ref": "WaitHandle"
                },
                "'\n",
                "  exit 1\n",
                "}\n",
                "# Install the AWS CodeDeploy Agent.\n",
                "cd /home/ec2-user/\n",
                "aws s3 cp 's3://aws-codedeploy-us-east-1/latest/codedeploy-agent.noarch.rpm' . || error_exit 'Failed to download AWS CodeDeploy Agent.'\n",
                "yum -y install codedeploy-agent.noarch.rpm || error_exit 'Failed to install AWS CodeDeploy Agent.' \n",
                "/opt/aws/bin/cfn-init -s ",
                {
                  "Ref": "AWS::StackId"
                },
                " -r LinuxEC2Instance --region ",
                {
                  "Ref": "AWS::Region"
                },
                " || error_exit 'Failed to run cfn-init.'\n",
                "# All is well, so signal success.\n",
                "/opt/aws/bin/cfn-signal -e 0 -r \"AWS CodeDeploy Agent setup complete.\" '",
                {
                  "Ref": "WaitHandle"
                },
                "'\n"
              ]
            ]
          }
        },
        "KeyName": {
          "Ref": "KeyPairName"
        },
        "Tags": [
          {
            "Key": {
              "Ref": "TagKey"
            },
            "Value": {
              "Ref": "TagValue"
            }
          }
        ],
        "IamInstanceProfile": {
          "Ref": "InstanceRoleInstanceProfile"
        }
      }
    },
    "WindowsEC2Instance2": {
      "Type": "AWS::EC2::Instance",
      "Condition": "LaunchWindowsEC2Instance2",
      "Metadata": {
        "AWS::CloudFormation::Init": {
          "config": {
            "commands": {
              "00-download-host-agent": {
                "command": {
                  "Fn::Join": [
                    "",
                    [
                      "powershell.exe -Command \"Read-S3Object ",
                      "-BucketName aws-codedeploy-us-east-1 ",
                      "-Key latest/codedeploy-agent.msi ",
                      "-File codedeploy-agent.msi\""
                    ]
                  ]
                },
                "cwd": "C:/cfn",
                "waitAfterCompletion": 0
              },
              "01-install-host-agent": {
                "command": "C:\\cfn\\codedeploy-agent.msi /quiet /l C:\\cfn\\host-agent-install-log.txt",
                "ignoreErrors": "true",
                "waitAfterCompletion": 0
              },
              "02-signal-ready": {
                "command": {
                  "Fn::Join": [
                    "",
                    [
                      "\"C:\\Program Files\\Amazon\\cfn-bootstrap\\cfn-signal\"",
                      " -e 0 \"",
                      {
                        "Ref": "WaitHandle"
                      },
                      "\""
                    ]
                  ]
                },
                "waitAfterCompletion": 0
              }
            },
            "services": {
              "windows": {
                "codedeploy-agent": {
                  "enabled": "true",
                  "ensureRunning": "true",
                  "commands": [
                    "01-install-host-agent"
                  ]
                }
              }
            }
          }
        }
      },
      "Properties": {
        "ImageId": {
          "Fn::FindInMap": [
            "RegionOS2AMI",
            {
              "Ref": "AWS::Region"
            },
            {
              "Ref": "OperatingSystem"
            }
          ]
        },
        "InstanceType": {
          "Ref": "InstanceType"
        },
        "SecurityGroups": [
          {
            "Ref": "SecurityGroup"
          }
        ],
        "UserData": {
          "Fn::Base64": {
            "Fn::Join": [
              "",
              [
                "<script>\n",
                "mkdir c:\\cfn\n",
                "mkdir c:\\cfn\\log\n",
                "c:\\\"Program Files\"\\Amazon\\cfn-bootstrap\\cfn-init.exe -s ",
                {
                  "Ref": "AWS::StackName"
                },
                " --region ",
                {
                  "Ref": "AWS::Region"
                },
                " -r WindowsEC2Instance > c:\\cfn\\log\\cfn-call-log 2>&1",
                "</script>"
              ]
            ]
          }
        },
        "KeyName": {
          "Ref": "KeyPairName"
        },
        "Tags": [
          {
            "Key": {
              "Ref": "TagKey"
            },
            "Value": {
              "Ref": "TagValue"
            }
          }
        ],
        "IamInstanceProfile": {
          "Ref": "InstanceRoleInstanceProfile"
        }
      }
    },
    "LinuxEC2Instance3": {
      "Type": "AWS::EC2::Instance",
      "Condition": "LaunchLinuxEC2Instance3",
      "Metadata": {
        "AWS::CloudFormation::Init": {
          "services": {
            "sysvint": {
              "codedeploy-agent": {
                "enabled": "true",
                "ensureRunning": "true"
              }
            }
          }
        }
      },
      "Properties": {
        "ImageId": {
          "Fn::FindInMap": [
            "RegionOS2AMI",
            {
              "Ref": "AWS::Region"
            },
            {
              "Ref": "OperatingSystem"
            }
          ]
        },
        "InstanceType": {
          "Ref": "InstanceType"
        },
        "SecurityGroups": [
          {
            "Ref": "SecurityGroup"
          }
        ],
        "UserData": {
          "Fn::Base64": {
            "Fn::Join": [
              "",
              [
                "#!/bin/bash -ex\n",
                "yum update -y aws-cfn-bootstrap\n",
                "yum install -y aws-cli\n",
                "# Helper function.\n",
                "function error_exit\n",
                "{\n",
                "  /opt/aws/bin/cfn-signal -e 1 -r \"$1\" '",
                {
                  "Ref": "WaitHandle"
                },
                "'\n",
                "  exit 1\n",
                "}\n",
                "# Install the AWS CodeDeploy Agent.\n",
                "cd /home/ec2-user/\n",
                "aws s3 cp 's3://aws-codedeploy-us-east-1/latest/codedeploy-agent.noarch.rpm' . || error_exit 'Failed to download AWS CodeDeploy Agent.'\n",
                "yum -y install codedeploy-agent.noarch.rpm || error_exit 'Failed to install AWS CodeDeploy Agent.' \n",
                "/opt/aws/bin/cfn-init -s ",
                {
                  "Ref": "AWS::StackId"
                },
                " -r LinuxEC2Instance --region ",
                {
                  "Ref": "AWS::Region"
                },
                " || error_exit 'Failed to run cfn-init.'\n",
                "# All is well, so signal success.\n",
                "/opt/aws/bin/cfn-signal -e 0 -r \"AWS CodeDeploy Agent setup complete.\" '",
                {
                  "Ref": "WaitHandle"
                },
                "'\n"
              ]
            ]
          }
        },
        "KeyName": {
          "Ref": "KeyPairName"
        },
        "Tags": [
          {
            "Key": {
              "Ref": "TagKey"
            },
            "Value": {
              "Ref": "TagValue"
            }
          }
        ],
        "IamInstanceProfile": {
          "Ref": "InstanceRoleInstanceProfile"
        }
      }
    },
    "WindowsEC2Instance3": {
      "Type": "AWS::EC2::Instance",
      "Condition": "LaunchWindowsEC2Instance3",
      "Metadata": {
        "AWS::CloudFormation::Init": {
          "config": {
            "commands": {
              "00-download-host-agent": {
                "command": {
                  "Fn::Join": [
                    "",
                    [
                      "powershell.exe -Command \"Read-S3Object ",
                      "-BucketName aws-codedeploy-us-east-1 ",
                      "-Key latest/codedeploy-agent.msi ",
                      "-File codedeploy-agent.msi\""
                    ]
                  ]
                },
                "cwd": "C:/cfn",
                "waitAfterCompletion": 0
              },
              "01-install-host-agent": {
                "command": "C:\\cfn\\codedeploy-agent.msi /quiet /l C:\\cfn\\host-agent-install-log.txt",
                "ignoreErrors": "true",
                "waitAfterCompletion": 0
              },
              "02-signal-ready": {
                "command": {
                  "Fn::Join": [
                    "",
                    [
                      "\"C:\\Program Files\\Amazon\\cfn-bootstrap\\cfn-signal\"",
                      " -e 0 \"",
                      {
                        "Ref": "WaitHandle"
                      },
                      "\""
                    ]
                  ]
                },
                "waitAfterCompletion": 0
              }
            },
            "services": {
              "windows": {
                "codedeploy-agent": {
                  "enabled": "true",
                  "ensureRunning": "true",
                  "commands": [
                    "01-install-host-agent"
                  ]
                }
              }
            }
          }
        }
      },
      "Properties": {
        "ImageId": {
          "Fn::FindInMap": [
            "RegionOS2AMI",
            {
              "Ref": "AWS::Region"
            },
            {
              "Ref": "OperatingSystem"
            }
          ]
        },
        "InstanceType": {
          "Ref": "InstanceType"
        },
        "SecurityGroups": [
          {
            "Ref": "SecurityGroup"
          }
        ],
        "UserData": {
          "Fn::Base64": {
            "Fn::Join": [
              "",
              [
                "<script>\n",
                "mkdir c:\\cfn\n",
                "mkdir c:\\cfn\\log\n",
                "c:\\\"Program Files\"\\Amazon\\cfn-bootstrap\\cfn-init.exe -s ",
                {
                  "Ref": "AWS::StackName"
                },
                " --region ",
                {
                  "Ref": "AWS::Region"
                },
                " -r WindowsEC2Instance > c:\\cfn\\log\\cfn-call-log 2>&1",
                "</script>"
              ]
            ]
          }
        },
        "KeyName": {
          "Ref": "KeyPairName"
        },
        "Tags": [
          {
            "Key": {
              "Ref": "TagKey"
            },
            "Value": {
              "Ref": "TagValue"
            }
          }
        ],
        "IamInstanceProfile": {
          "Ref": "InstanceRoleInstanceProfile"
        }
      }
    }
  },
  "Outputs": {
    "CodeDeployTrustRoleARN": {
      "Value": {
        "Fn::GetAtt": [
          "CodeDeployTrustRole",
          "Arn"
        ]
      }
    }
  }
}
```

Cloudformation Acceess Policy

```yaml
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Sid": "VisualEditor0",
            "Effect": "Allow",
            "Action": "cloudformation:*",
            "Resource": "*"
        },
        {
            "Effect": "Allow",
            "Action": "iam:*",
            "Resource": "*"
        },
        {
            "Action": "ec2:*",
            "Effect": "Allow",
            "Resource": "*"
        }
    ]
}
```

Useful AWS commands

```bash
aws iam get-user # shows configured user for CLI
aws configure list # lists region you are configured to be using

aws configure # you can use aws configure to setup an IAM account using key pair from a specific region
```
