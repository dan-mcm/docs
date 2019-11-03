# AWS CI/CD

* Continuous Integration & Continuous Delivery/Deployment are Best Practices for software
development and deployment
* They enable frequent software changes to be applied whilst maintaining system and service stability.
* Companies like AWS, Netflix, Google, and Facebook have pioneered this approach to releasing code,
successfully applying thousands of changes per day.

## Continuous Integration Workflow

* Multiple devs working on different features or bug fixes
* All contributing to the same application
* Sharing the same code repository e.g. git
* Frequently pushing their updates into the shared repo - at least daily
* Code repository integrated with a build management system
* Code changes trigger an automated build
* We need a way to ensure that any code change does not break the build or introduce new bugs in the Application
* The test system runs automated tests on the newly built application
* Identifies any bugs, preventing issues from being introduced in the master code
* CI focuses on small code changes which frequently committed into the main repository once they have been successfully tested

## Continuous Delivery

* Continuous Delivery is a Dev practice where merged changes are automatically built, tested, and
prepared for released into staging and eventually production environments,
* There is usually a manual decision  process to initiate deployment of the new code.

## Continuous Deployment

* Continuous Deployment takes the idea of automation one step further and automatically deploys
the new code following successful testing, eliminating any manual steps.
* The new code is automatically released as soon as it passes through the stages of your release process (build, test, package)
* Small changes are released early and frequently
* Both practices require the build, test, and deployment processes to be fully automated but Continuous Deployment
also automates the release process as well

## AWS Tools

* CodeCommit - create private git repository within AWS
* CodeBuild - fully managed build service & can run basic tests
* CodeDeploy - automated application deployment service -> EC2 instances/On-premise systems/Lambda functions
* CodePipeline - continuous deployment service - allows you to model/visualize/automate entire release process

## Exam Tips

* Worth reading white paper: https://d0.awsstatic.com/whitepapers/DevOps/practicing-continuous-integration-continuous-delivery-on-AWS.pdf
* Continuous Integration is about integrating to merging the code changes frequently - at least once per day, enables multiple devs to work on the same Application
* Continuous Delivery is all about automating the build, test and deployment functions
* Continuous Deployment fully automates the entire release process, code is deployed into Production as soon as it has successfully passes through the release pipeline
* AWS CodeCommit - Source Control (git)
* AWS CodeBuild - compile source code, run tests and package code
* AWS CodeDeploy - Automated Deployment to EC2, on premises systems and Lambda
* AWS CodePipeline - CI/CD workflow tool, fully automates the entire release process (build, test, deployment)
