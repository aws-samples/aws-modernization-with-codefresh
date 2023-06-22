+++
title = "Automating EC2 deployments"
chapter = false
weight = 28
+++

In the previous 2 sections we have seen how to manually

* create AMIs with Packer
* launch EC2 instances with the AWS CLI

We will fully automate both steps with the following Codefresh pipeline:

![Push image](/images/ec2/baking-pipeline.png)

This pipeline does the following:

1. Clones the Git repository that contains all the resources
2. Compiles a minimal Go application to create a binary
3. Runs Packer to create a new AMI that also includes the Go artifact
4. Finds the ID of the AMI that was just created
5. Runs an EC2 instance with this AMI (also launching the application)

Here is the full YAML of the pipeline:

```yaml
# More examples of Codefresh YAML can be found at
# https://codefresh.io/docs/docs/yaml-examples/examples/

version: "1.0"
# Stages can help you organize your steps in stages
stages:
  - prepare
  - build
  - deploy

steps:
  clone:
    title: "Cloning repository"
    type: "git-clone"
    repo: "codefresh-contrib/aws-workshop-demos"
    revision: "main"
    git: "github-1"
    stage: "prepare"

  BuildMyApp:
    title: Compiling App code
    stage: build
    image: 'golang:1.12'
    working_directory: "${{clone}}/ec2-deploy"
    commands:
      - go build -o sample src/sample/trivial-web-server.go   
  CreatePackerImage:
    title: Baking VM image
    stage: build
    image: 'hashicorp/packer:1.9'
    working_directory: "${{clone}}/ec2-deploy"
    commands:
      - packer init .
      - packer validate aws-packer-example.pkr.hcl
      - packer build -force aws-packer-example.pkr.hcl
  GetImageDetails:
    title: Get Image Details
    stage: build
    image: 'amazon/aws-cli:2.11.23'
    working_directory: "${{clone}}/ec2-deploy"
    commands:
      - aws ec2 describe-images --region=us-east-1 --filters Name=name,Values=kostis-demo > image-details.json
      - cat image-details.json    
  FindAMI:
     title: Choose AMI ID
     stage: deploy
     image: 'stedolan/jq'
     working_directory: "${{clone}}/ec2-deploy"
     commands:
       - export IMAGE_ID=$(cat image-details.json | jq -r .Images[0].ImageId)
       - echo $IMAGE_ID
       - cf_export IMAGE_ID
       
  DeployToVM:
    title: Deploying to EC2
    stage: deploy
    image: 'amazon/aws-cli:2.11.23'
    working_directory: "${{clone}}/ec2-deploy"
    commands:
      - aws ec2 run-instances --no-cli-pager --region=us-east-1 --image-id $IMAGE_ID --count 1 --instance-type t2.micro --security-group-ids sg-077847d2f63340b3f --user-data file://scripts/startup.sh --tag-specifications 'ResourceType=instance,Tags=[{Key=Name,Value=from-codefresh}]'
```

Once you run the pipeline and the EC2 Image is launched, you can connect to it with your browser by using the public IP you will get.


![Preview app](/images/ec2/running-app.png)

This concludes the CD section for EC2. You have now setup a CD pipeline that creates an EC2 instance with your application. If you change the source code of the application the pipeline will run again and a new version will be deployed.

You can of course expand the pipeline to deploy to other regions, run security scans first, perform load testing etc.








