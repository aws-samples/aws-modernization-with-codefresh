+++
title = "1. Continuous Integration"
chapter = true
weight = 20
+++

# Create a basic CI pipeline

Continuous Integration is the process where for each commit, the source code
is tested, compiled and packaged in an artifact (a container in the case of Kubernetes).

As a minimum the code should be compiled/minified but you may have other optional stages such as unit tests, linters, security scanning, integration tests and so on.

In this section we will setup Continuous Integration with Codefresh for the example application at [https://github.com/codefresh-contrib/aws-gitops-app](https://github.com/codefresh-contrib/aws-gitops-app)

{{% notice info %}}
For this workshop we will use the SAAS version of Codefresh where all pipelines will run on the Codefresh cloud. You can also use the [Hybrid version](https://codefresh.io/docs/docs/administration/behind-the-firewall/) of Codefresh where the pipelines [run in your own cluster](https://codefresh.io/docs/docs/administration/codefresh-runner/) for maximum flexibility and extra security.
{{% /notice %}}

{{% children showhidden="false" %}}


