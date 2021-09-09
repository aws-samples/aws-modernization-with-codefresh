+++
title = "Install Codefresh CLI"
chapter = false
weight = 13
+++

The Codefresh command-line tool, [codefresh](https://codefresh-io.github.io/cli/), allows you to manage your Codefresh deployments from your terminal

## Installing the Codefresh CLI

Find your version by looking at the [latest releases](https://codefresh-io.github.io/cli/installation/download/).


At the terminal command prompt, enter the following commands:

```
cd /tmp
wget https://github.com/codefresh-io/cli/releases/download/v0.75.26/codefresh-v0.75.26-linux-x64.tar.gz
tar zxvf codefresh-v0.75.26-linux-x64.tar.gz
```

This download the CLI locally. To install and use it:

```
chmod +x ./codefresh
sudo mv ./codefresh /usr/local/bin/codefresh
```

This will install `codefresh` in your **Cloud9** environment. To test to make sure the command is installed properly, execute the command:

```
codefresh version 
```

You should see the `codefresh` version message.

## Connecting your CLI with your Codefresh account

To connect your Codefresh CLi with your own account you need to [setup 
authentication](https://codefresh.io/docs/docs/integrations/codefresh-api/#authentication-instructions).

Sign in Codefresh and visit your user settings at [https://g.codefresh.io/user/settings](https://g.codefresh.io/user/settings)

Scroll down until the *API Keys* section and click the *Generate button*

![Codefresh API settings](/images/prerequisites/api-token.png)

Enter an arbitrary key name (for example `aws-workshop`) and enable
the first checkbox next to *Scopes*.

Finally click the *Create* button in the dialog and make sure to copy the API Token that was generated (it will not be visible again after you leave this screen)

Then in your terminal enter the following:

```
codefresh auth create-context --api-key <your_key_here>
```

Now the CLI is authenticated against your Codefresh account. 

Type 

```
codefresh get context
```

to verify the authentication.


