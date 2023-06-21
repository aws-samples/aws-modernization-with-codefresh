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

![Push image](/images/ec2/ami-created.png)



![Push image](/images/ec2/running-app.png)

![Push image](/images/ec2/running-instance.png)






