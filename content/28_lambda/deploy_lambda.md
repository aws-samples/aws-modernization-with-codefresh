+++
title = "Deploying Lambda functions"
chapter = false
weight = 22
+++

Now that we have the code for our lambda method we can deploy it using terraform.

You can find all the terraform files at [https://github.com/codefresh-contrib/aws-workshop-demos/tree/main/lambda](https://github.com/codefresh-contrib/aws-workshop-demos/tree/main/lambda)

Here is the main file

```hcl
terraform {
  required_providers {
    aws = {
      source  = "hashicorp/aws"
      version = "~> 4.0"
    }
  }

    backend "s3" {
    bucket = "kostis-terraform-state"
    key    = "aws-demo-lambda"
    region = "us-east-1"
  }
}

provider "aws" {
  region = "us-east-1"
}

locals {
  function_name               = "simple"
  function_handler            = "demo.handler"
  function_runtime            = "nodejs18.x"
  function_timeout_in_seconds = 5

  function_source_dir = "${path.module}/aws_lambda_functions/${local.function_name}"
}

resource "aws_lambda_function" "function" {
  function_name = "${local.function_name}-${var.env_name}"
  handler       = local.function_handler
  runtime       = local.function_runtime
  timeout       = local.function_timeout_in_seconds

  filename         = "${local.function_source_dir}.zip"
  source_code_hash = data.archive_file.function_zip.output_base64sha256

  role = aws_iam_role.function_role.arn

  environment {
    variables = {
      ENVIRONMENT = var.env_name
    }
  }
}

data "archive_file" "function_zip" {
  source_dir  = local.function_source_dir
  type        = "zip"
  output_path = "${local.function_source_dir}.zip"
}

resource "aws_iam_role" "function_role" {
  name = "${local.function_name}-${var.env_name}"

  assume_role_policy = jsonencode({
    Statement = [
      {
        Action = "sts:AssumeRole"
        Effect = "Allow"
        Principal = {
          Service = "lambda.amazonaws.com"
        }
      },
    ]
  })
}

resource "aws_lambda_function_url" "function" {
    function_name      = aws_lambda_function.function.function_name
    authorization_type = "NONE"
}

```

This terraform file defines a lambda function with name "simple" using the `nodejs18.x` backend. It creates a ZIP file for the code (AWS lambda also supports container images).
We use the respective [Terraform provider for AWS](https://registry.terraform.io/modules/terraform-aws-modules/lambda/aws/latest) in this example.


Terraform needs a way to store its [state](https://developer.hashicorp.com/terraform/language/state), and we have chosen to use an S3 bucket. 

So only the first time we need to create the bucket for the state

```shell
aws s3api create-bucket --bucket kostis-terraform-state --region us-east-1
aws s3api put-bucket-versioning --bucket kostis-terraform-state --versioning-configuration Status=Enabled
```

Now that everything is ready we can run terraform

```shell
cd aws-workshop-demos/lambda
terraform init
terraform apply
```

Answer yes in the query and after a while your lambda function will be deployed.

If you visit the AWS Console you will now see your instance running:

![Lambda function](/images/lambda/lambda-inspect.png)

You can also test the method with the "test" button to verify that it works

![Lambda function testing](/images/lambda/lambda-test.png)

We now have a lambda function deployed in AWS. We will automated the deployment using Codefresh in the next section.





