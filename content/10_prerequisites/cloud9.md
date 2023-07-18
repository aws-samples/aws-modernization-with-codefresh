+++
title = "3. Configure your Cloud IDE"
chapter = false
weight = 12
+++

{{% notice note%}}
Cloud9 is Cloud IDE. If you prefer to work with local one, please ensure AWS credentials configuration and all pre-requisites at place.
{{% /notice %}}

AWS Cloud9 is a cloud-based integrated development environment (IDE) that lets you write, run, and debug your code with just a browser. It includes a code editor, debugger, and terminal. Cloud9 comes pre-packaged with essential tools for popular programming languages and the AWS Command Line Interface (CLI) pre-installed so you don’t need to install files or configure your laptop for this workshop. 

Your Cloud9 environment will have access to the same AWS resources as the user with which you logged into the AWS Management Console. We strongly recommend using Cloud9 to complete this workshop.

{{% notice info%}}
Cloud9 works best with Chrome or Firefox, not Safari. It cannot be used from a tablet.
{{% /notice %}}

**:white_check_mark: Step-by-step Instructions**

1. From the AWS Management Console, Select **Services** then select **Cloud9** under Developer Tools. 

![Step 4](/images/cloud9/c9-step4.png)

2. Select **Create environment**

3. Enter `gitops-workshop` into **Name**, other fields can be left as default

4. Review the environment settings and select **Create**. It will take a couple of minutes for your Cloud9 environment to be provisioned and prepared.

### Configure Cloud9 IDE environment

When the environment comes up, click **Open** and customize the environment by:

1. Close the **welcome page** tab

2. Close the **lower work area** tab

3. Open a new **terminal** tab in the main work area (*Window - New Terminal*)
    - You can run AWS CLI commands in here just like you would on your local computer. Remember for this workshop to run all commands within the Cloud9 terminal window rather than on your local computer

4. Verify that your user is logged in by running the command `aws sts get-caller-identity`. Copy and paste the command into the Cloud9 terminal window. 
```console
aws sts get-caller-identity
```
You'll see output indicating your account and user information:

```json
{
    "Account": "123456789012",
    "UserId": "AKIAIOSFODNN7EXAMPLE",
    "Arn": "arn:aws:iam::123456789012:assumed-role/TeamRole/MasterKey"
}
```

{{% notice tip %}}
If you don't like this dark theme, you can change it from the **View / Themes** Cloud9 workspace menu.
{{% /notice %}}

{{% notice tip %}}
Cloud9 requires third-party-cookies. You can whitelist the [specific domains](https://docs.aws.amazon.com/cloud9/latest/user-guide/troubleshooting.html#troubleshooting-env-loading).  You are having issues with this, Ad blockers, javascript disablers, and tracking blockers should be disabled for the cloud9 domain, or connecting to the workspace might be impacted.
{{% /notice %}}