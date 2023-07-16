+++
title = "2. Use AWS Credentials"
chapter = false
weight = 11
+++

{{% notice warning %}}
<p style='text-align: left;'>
Notice that using static credentials for authentication is not recommended for production setups. In a real situation you should use proper IAM roles and policies.
</p>
{{% /notice %}}

Once you get access to your temporary AWS account, you will see the following credentials in your screen. Copy them to notes:

![AWS credentials](/images/prerequisites/aws-creds.png)

Later for a Codefresh pipeline that interacts with AWS you will need to enter the credentials as pipeline variables. On the right sidebar when editing your pipeline click **Add variable button** and enter the credentials as

* `AWS_ACCESS_KEY_ID`
* `AWS_SECRET_ACCESS_KEY`

![GitOps Integration](/images/prerequisites/codefresh-aws-auth.png)

This will allow Codefresh to authenticate with your AWS account.


