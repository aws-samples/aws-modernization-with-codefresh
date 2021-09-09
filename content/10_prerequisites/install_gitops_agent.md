+++
title = "Install GitOps Agent"
chapter = false
weight = 15
+++

To start working with GitOps you need to install an agent on your cluster.
This agent will monitor your Git repositories and automatically apply 
changes to your cluster after each Git action you perfom

```
codefresh install gitops codefresh
```

This command will start a wizard that will ask you several questions. Choose the default
answer in all questions.


After you answer them the installation of the agent will take place.

{{% notice warning %}}
The installation process will take several minutes depending on your cluster
resources and network speed. Please be patient and don't interrupt the process
by pressing CTRL-C or closing the terminal.
{{% /notice %}}

You can see the status of your agent at [https://g.codefresh.io/account-admin/account-conf/integration/gitops](https://g.codefresh.io/account-admin/account-conf/integration/gitops).


![GitOps Integration](/images/prerequisites/gitops-integration.png)

Now we are ready to start GitOps deployments.