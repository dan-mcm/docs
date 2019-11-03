# AWS CodeBuild

## Docker & CodeBuild Lab

* Docker is an Open Source technology which allows you to create applications based on either Linux or Windows containers
* A Container is a lightweight standalone executable software package which includes everything the software needs to run -
code, runtime environments, libraries, environment settings etc.
* AWS provides Elastic Container Service as a fully managed clustered platform which allows you to run your Docker images in the cloud
* AWS CodeBuild is a fully managed build service which runs a set of commands that you define e.g. compiles code, runs tests and produces
artifacts that are ready to deploy

### Create Elastic Container Service (ECS) Cluster

Done via UI -> select option to enable with EC2.
Under ECS UI -> Repositories -> Create Repository (this will show commands)

```bash
# retrieve login command to use to authenticate your Docker client to your ECS registry
# note requires permission AmazonEC2ContainerRegistryPowerUser on user
$(aws ecr get-login --no-include-email --region eu-central-1)

# Build docker image using following command
docker build -t mydockerrepo .

# after build completes, tag your image so you can push
docker tag mydockerrepo:latest numbers.dkr.ecr.eu-central-1.amazonaws..com/mydockerrepo:latest

# run following command to push this image to your newly created aws ecr registry
docker push numbers.dkr.ecr.eu-central-1.amazonaws.com/mydockerrepo:latest
```

Create new task definition.
Task size: 512 MiB
CPU (unit): 512

Add Container -> Image -> mydockerrepo URI (taken from ECS -> Repository)
Port Mappings -> (maps host & container ports) -> 80:80
Actions -> Create Service (should find task definition & cluster by default)

### Sample Dockerfile

```dockerfile
FROM ubuntu:12.04
# Install dependencies
RUN apt-get update -y
RUN apt-get install -y apache2

# Install apache and write hello world message
RUN echo "Hello Cloud Gurus!!!! This web page is running in a Docker container!" > /var/www/index.html

# Configure apache
RUN a2enmod rewrite
RUN chown -R www-data:www-data /var/www
ENV APACHE_RUN_USER www-data
ENV APACHE_RUN_GROUP www-data
ENV APACHE_LOG_DIR /var/log/apache2

EXPOSE 80

CMD ["/usr/sbin/apache2", "-D",  "FOREGROUND"]

```

### Connecting to CodeCommit and cloning locally

```bash
# Use the following commands to connect to your codecommit repo and clone it locally:

# 1) Set Up the Credential Helper
git config --global credential.helper '!aws codecommit credential-helper $@'
git config --global credential.UseHttpPath true

# 2) Clone the repository with the git clone command:
git clone https://git-codecommit.eu-central-1.amazonaws.com/v1/repos/mysourcecoderepo

# Make sure you replace my clone url with your own!
```

### Buildspec.yaml

This tells CodeBuild how to build Docker Image

Services -> CodeBuild -> Create Project -> Source provider: CodeCommit -> Repository (the one containing your buildspec.yml)
-> Managed Image -> OS Ubuntu: Image standard:2.0 -> Enable Privileged flag -> Use a buildspec file (this is default, otherwise you can insert build commands here)
-> Create Build Project

IAM role may be missing all policies necessary
- Needs access to AmazonEC2ContainerRegistryPowerUser

```yaml
version: 0.2

#env:
  #variables:
     # key: "value"
     # key: "value"
  #parameter-store:
     # key: "value"
     # key: "value"

phases:
  install:
    runtime-versions:
        docker: 18     
    commands:
      - nohup /usr/local/bin/dockerd --host=unix:///var/run/docker.sock --host=tcp://127.0.0.1:2375 --storage-driver=overlay2&
      - timeout 15 sh -c "until docker info; do echo .; sleep 1; done"
  pre_build:
    commands:
    - echo Logging in to Amazon ECR....
    - aws --version
    # update the following line with your own region
    - $(aws ecr get-login --no-include-email --region eu-central-1)
  build:
    commands:
    - echo Build started on `date`
    - echo Building the Docker image...
    # update the following line with the name of your own ECR repository
    - docker build -t mydockerrepo .
    # update the following line with the URI of your own ECR repository (view the Push Commands in the console)
    - docker tag mydockerrepo:latest 757250003982.dkr.ecr.eu-central-1.amazonaws.com/mydockerrepo:latest
  post_build:
    commands:
    - echo Build completed on `date`
    - echo pushing to repo
    # update the following line with the URI of your own ECR repository
    - docker push 757250003982.dkr.ecr.eu-central-1.amazonaws.com/mydockerrepo:latest
#artifacts:
    # - location
    # - location
  #discard-paths: yes
  #base-directory: location
#cache:
  #paths:
    # - paths

```

## Exam Tips

* Docker COmmands to build, tag (apply an alias) and push your Docker image to the ECR repository
    * docker build -d myimagerepo .
    * docker tag myimagerepo:latest number.dkr.ecr.eu-central-1.amazonaws.com/myimagerepo:latest
    * docker push myimagerepo:latest number.dkr.ecr.eu-central-1.amazonaws.com/myimagerepo:latest
* Use buildspec.yml to define the build commands and settings used by CodeBuild to run your build
* You can override the settings in buildspec.yml by adding your own commands in the console when you launch the build
* If your build fails, check the build logs in the CodeBuil console and you can also view the full CodeBuild log in CloudWatch
