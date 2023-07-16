+++
title = "Use AWS Credentials"
chapter = false
weight = 15
+++

Once you get access to your temporary AWS account, you will see the following credentials in your screen:

![AWS credentials](/images/prerequisites/aws-creds.png)

Note them down.

Then for every Codefresh pipeline that interacts with AWS you need to enter the credentials as pipeline variables. On the right sidebar when editing your pipeline
click the "Add variable button" and enter the credentials as

* `AWS_ACCESS_KEY_ID`
* `AWS_SECRET_ACCESS_KEY`

![GitOps Integration](/images/prerequisites/codefresh-aws-auth.png)

This will allow Codefresh to authenticate with your AWS account.

{{% notice warning %}}
<p style='text-align: left;'>
Notice that using static credentials for authentication is not recommended for production setups. In a real situation you should use proper IAM roles and policies.
</p>
{{% /notice %}}

