+++
title = "3. Lambda deployments"
chapter = true
weight = 20
+++

# Using Lambda methods

EC2 is a great service for long running applications and servers. There are times however where you either want to execute an one-off task, or you simply don't want to deal with servers at all

Amazon offers the [Lambda service](https://aws.amazon.com/lambda/) that provides serverless computing. With Lambda you can run your code without provisioning any infrastructure at all.

You can write your code in any supported programming language and then pass it over to Amazon Lambda for deployment. Amazon will automatically optimize the running resources and will scale intelligently the underlying infrastructure without any human intervention.

You can upload your code as a ZIP file or a container image.


In theory you could visit the Amazon Console, create a lambda method by clicking buttons and drop-downs, and while this would work in a small scale, it should never be used in production.

We will instead automate the process of creating lambda functions and deploying them using the [Terraform open source project](https://www.terraform.io/).

{{% children showhidden="false" %}}


