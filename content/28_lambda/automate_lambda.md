+++
title = "Automating Lambda deployments"
chapter = false
weight = 23
+++

In the previous 2 sections we have seen how to manually

* create the source code for a lambda function
* deploy the lambda function with Terraform

We will fully automate the lambda deployment with the following Codefresh pipeline:

![Lambda deploy pipeline](/images/lambda/lambda-deploy-pipeline.png)

This pipeline does the following:

1. Clones the Git repository that contains all the resources
2. Runs Terraform to authenticate against AWS and deploy the method

Here is the full YAML of the pipeline:

```yaml
# More examples of Codefresh YAML can be found at
# https://codefresh.io/docs/docs/yaml-examples/examples/

version: "1.0"
# Stages can help you organize your steps in stages
stages:
  - "prepare"
  - "infra"

steps:
  clone:
    title: "Cloning repository"
    type: "git-clone"
    repo: "codefresh-contrib/aws-workshop-demos"
    # CF_BRANCH value is auto set when pipeline is triggered
    # Learn more at codefresh.io/docs/docs/codefresh-yaml/variables/
    revision: "main"
    git: "github-1"
    stage: "prepare"


  DeployWithTerraform:
    image: hashicorp/terraform:1.3.7
    title: "Deploying Lambda method"
    working_directory: "${{clone}}" 
    stage: infra
    commands:
      - cp -r /codefresh/volume/sensitive/.kube/ ~/.kube 
      - cd lambda
      - terraform init
      - terraform apply -auto-approve



```

Once you run the pipeline and the Lambda function is deployed, the logs will also
show you the URL. We have made the endpoint public so you can call it with your browser


![Call method](/images/lambda/lambda-test-url.png)

This concludes the CD section for Lambda. You have now setup a CD pipeline that deploys a lambda method with your source code. If you change the source code of the application the pipeline will run again and a new version will be deployed.

We have also included a second pipeline at [https://github.com/codefresh-contrib/aws-workshop-demos/blob/main/lambda/pipelines/codefresh-destroy.yml](https://github.com/codefresh-contrib/aws-workshop-demos/blob/main/lambda/pipelines/codefresh-destroy.yml) that allows you to undeploy the lambda method when you are finished with the workshop. 










