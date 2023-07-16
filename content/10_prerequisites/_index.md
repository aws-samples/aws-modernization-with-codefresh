+++
title = "Prerequisites"
chapter = true
weight = 10
+++

# Prerequisites

Before you can start the workshop you need to setup free accounts with the following services:

- Create a [GitHub account](https://github.com/join)
- Create a [DockerHub account](https://hub.docker.com/)
- Create an [AWS account](https://aws.amazon.com/free/) if you running at self-paced mode, or follow [At AWS Event]({{< ref "aws_event.md" >}}) to get a free temporary one when attending an AWS event
- Create a [Codefresh account](https://codefresh.io/docs/docs/getting-started/create-a-codefresh-account/)

{{% notice warning %}}
When creating a Codefresh account, be sure to choose Github
as your authentication provider. This will automatically add an integration with Github that we will use later on in our pipelines.
{{% /notice %}}

Then follow the instructions to install the CLI tools
as explained in the next sections:

{{% children showhidden="false" %}}


