---
title: "1. At an AWS Event"
chapter: false
weight: 10
---

To complete this workshop, you are provided with an AWS account via the AWS Event Engine service. A 12-digit hash will be provided to you by event staff.

{{% notice note%}}
You are welcome to use your own AWS account. In this case you will need to provision EKS cluster
{{% /notice %}}

### Create AWS Account

1. Navigate to [https://dashboard.eventengine.run/](https://dashboard.eventengine.run/). The following screen shows up. Enter the provided hash in the text box. The button on the bottom right corner changes to **Accept Terms & Login**. Click on that button to continue.
![Event Engine](/images/event-engine/event-engine-initial-screen.png)

1. You will be asked to Sign in. Simply choose "Email One-Time Password (OTP)" option, enter your email, and click "Send Passcode". Check you email for OTP 6-digit code and enter it on the screen.
   ![Event Engine Sign In](/images/10_prerequisites/eventengine-signin.png)
   ![Event Engine OTP](/images/10_prerequisites/eventengine-otp.png)
{{% notice tip %}}
Leave the Event Engine tab open (A new tab will be used for the next step)
{{% /notice %}}

1. Choose **AWS Console**, then **Open AWS Console**.
This account will expire at the end of the workshop and the all the resources created will be automatically deprovisioned. You will not be able to access this account after today.
![Event Engine Dashboard](/images/event-engine/event-engine-dashboard.png)

1. Use a single region for the duration of this workshop. This workshop supports the following regions:

* us-east-1 (US East - N.Virginia)

Make sure **US East (N.Virginia)** region is selected in the top right corner.

![Event Engine Region](/images/event-engine/event-engine-region.png)