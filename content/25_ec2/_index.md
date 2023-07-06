+++
title = "2. EC2 deployments"
chapter = true
weight = 20
+++

# Deploying to EC2

We now have an application that is built, tested and scanned. The next step is to deploy it and see it running in Amazon Web services.

One of the most basic deployments targets it using a virtual machine. Amazon offers 
virtual machines in the form of [Amazon EC2](https://aws.amazon.com/ec2/). With EC2 you can create virtual machines on demand and deploy any kind of software on them (e.g. containers,binaries, scripts)

In theory you could visit the Amazon Console, create a virtual machine by clicking buttons and drop-downs, and while this would work in a small scale, it should never be used in production.

We will instead automate the process of creating virtual machines and deploying software on them using the [Packer open source project](https://www.packer.io/) and the [aws cli](https://aws.amazon.com/cli/).

{{% children showhidden="false" %}}


