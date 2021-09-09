+++
title = "3.  Continuous Delivery"
chapter = true
weight = 40
+++

# Continuous Delivery

In the previous sections we have seen how to set up separate workflows for creating a container (CI) and for deploying a container (CD).

While you can keep the two workflows separate (and in a big organization even have [approval steps](https://codefresh.io/docs/docs/codefresh-yaml/steps/approval/) in between) it is also possible to combine the two in a single workflow.

This way you can have an end-to-end deployment pipeline that starts from a single commit in the source code of the application and ends with a deployment on your Amazon Kubernetes cluster.

We will also see how to perform rollbacks as they are an integral part of any deployment strategy.

{{% children showhidden="false" %}}


