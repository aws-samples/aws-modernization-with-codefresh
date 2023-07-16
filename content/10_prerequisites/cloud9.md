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

### Configure Workspace

{{% notice info %}}
Cloud9 normally manages IAM credentials dynamically. This isn't currently compatible with
the EKS IAM authentication, so we will disable it and rely on the IAM role instead.
{{% /notice %}}

1. Return to your workspace and click the gear icon (in top right corner), or click to open a new tab and choose "Open Preferences"

2. Select **AWS SETTINGS** and turn off **AWS managed temporary credentials**

3. Close the Preferences tab

4. Copy and run (paste with **Ctrl+P** or **CMD+P**) the commands below.

      Before running it, review what it does by reading through the comments.

      ```sh
      # Update awscli
      sudo pip install --upgrade awscli && hash -r
      
      # Install jq command-line tool for parsing JSON, and bash-completion
      sudo yum -y install jq gettext bash-completion moreutils nc
      
      # Install yq for yaml processing
      echo 'yq() {
      docker run --rm -i -v "${PWD}":/workdir mikefarah/yq yq "$@"
      }' | tee -a ~/.bashrc && source ~/.bashrc
      
      # Verify the binaries are in the path and executable
      for command in jq aws
      do
        which $command &>/dev/null && echo "$command in path" || echo "$command NOT FOUND"
      done
      
      # Remove existing credentials file.
      rm -vf ${HOME}/.aws/credentials
      
      # Set the ACCOUNT_ID and the region to work with our desired region
      export AWS_REGION=$(curl -s 169.254.169.254/latest/dynamic/instance-identity/document | jq -r '.region')
      test -n "$AWS_REGION" && echo AWS_REGION is "$AWS_REGION" || echo AWS_REGION is not set
      
      # Validate that our IAM role is valid.
      aws sts get-caller-identity --query Arn | grep BastionRole -q && echo "IAM role valid" || echo "IAM role NOT valid"
      ```

      {{% notice warning %}}
   If the IAM role is not valid, <span style="color: red;">**DO NOT PROCEED**</span>. Go back and confirm the steps on this page.
   {{% /notice %}}
   
5. Now we'll start installing kubectl and set up our Cloud9 instance to be to connect to the pre-provisioned EKS cluster 
   
      Copy and run (paste with **Ctrl+P** or **CMD+P**) the commands below.

      Before running it, review what it does by reading through the comments.

      ```sh
      # Verify there is an EKS cluster already provisioned, the EKS cluster's name is basic-eks
      aws eks list-clusters --region us-east-1
      
      # Install kubectl binary
      curl -o kubectl https://amazon-eks.s3.us-west-2.amazonaws.com/1.19.6/2021-01-05/bin/linux/amd64/kubectl
      
      # Apply permission to execute binary
      chmod +x ./kubectl
      
      # Copy binary to PATH, which allows you to write kubectl commands in any folder 
      mkdir -p $HOME/bin && cp ./kubectl $HOME/bin/kubectl && export PATH=$HOME/bin:$PATH
      
      #Verify kubectl is installed and can be executed
      kubectl version
      
      # Update the kubeconfig file and point to the Kube API server
      aws eks update-kubeconfig --name basic-eks --region us-east-1
      
      # Verify you are connected to EKS cluster
      kubectl get pods --all-namespaces
      ```
      {{% notice warning %}}
   If the pods cannot be listed, <span style="color: red;">**DO NOT PROCEED**</span>. Please reach out to AWS Event Staff or confirm the last few steps. 
   {{% /notice %}}