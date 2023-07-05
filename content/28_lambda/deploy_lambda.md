+++
title = "Deploying Lambda functions"
chapter = false
weight = 22
+++

Now that we have an VM image we can launch it with the [aws cli](https://aws.amazon.com/cli/).

To launch the image we need

* the AMI ID that we created
* the region 
* the instance type
* the security group
* an instance name
* a user data script

The user data script is critical because it tells the VM what to do after it is launched. In our case we want to run our application which is already contained in the image.

In our case it is [very simple](https://github.com/codefresh-contrib/aws-workshop-demos/blob/main/ec2-deploy/scripts/startup.sh). It runs the app


```bash
#! /bin/bash
/home/ubuntu/sample &
```


The AWS CLI already accepts as arguments all the required options

```shell
IMAGE_ID=$(aws ec2 describe-images --filters Name=name,Values=my-own-ami | jq -r .Images[0].ImageId)
aws ec2 run-instances --image-id $IMAGE_ID --count 1 --no-cli-pager --instance-type t2.micro --security-group-ids sg-077847d2f63340b3f --user-data file://scripts/startup.sh --tag-specifications 'ResourceType=instance,Tags=[{Key=Name,Value=from-packer}]'
```

The first command find the AMI id of any image by name (`my-own-ami` in the example).
The second command launches a VM using that AMI ID

If you visit the AWS Console you will now see your instance running:

![Running instance](/images/ec2/running-instance.png)

While you can run [manually these commands in your terminal](
  https://github.com/codefresh-contrib/aws-workshop-demos/blob/main/ec2-deploy/launch-it.sh
) we will also automate this process using Codefresh.



