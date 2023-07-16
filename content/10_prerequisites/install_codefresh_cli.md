+++
title = "5. Install Codefresh CLI"
chapter = false
weight = 14
+++

The Codefresh command-line tool, [codefresh](https://github.com/codefresh-io/cli-v2), allows you to manage your Codefresh deployments from your terminal

### Installing the Codefresh CLI

Find your version by looking at the [latest releases](https://github.com/codefresh-io/cli-v2/releases).

- At the terminal command prompt, enter the following commands to download the CLI locally:
```
cd /tmp
wget https://github.com/codefresh-io/cli-v2/releases/latest/download/cf-linux-amd64.tar.gz
tar zxvf cf-linux-amd64.tar.gz
```
- To will install `cf` in your **Cloud9** environment
```
chmod +x ./cf-linux-amd64
sudo mv ./cf-linux-amd64 /usr/local/bin/cf
```

- To validate the command is installed properly:
```
cf version 
```

You should see the `codefresh` version message.

### Connecting your CLI with your Codefresh account

To connect your Codefresh CLi with your own account you need to [setup 
authentication](https://codefresh.io/docs/docs/installation/gitops/upgrade-gitops-cli/#download-the-gitops-cli).

- Sign in Codefresh and visit your user settings at [https://g.codefresh.io/user/settings](https://g.codefresh.io/user/settings)

- Scroll down until the *API Keys* section and click the **Generate button** 
![Codefresh API settings](/images/prerequisites/api-token.png)

- Enter an arbitrary key name (for example `aws-workshop`) and enable the first checkbox next to *Scopes*.

- Finally click the **Create** button in the dialog and make sure to copy the API Token that was generated (it will not be visible again after you leave this screen)
![Codefresh API Token](/images/prerequisites/api-token-generated.png)

- In your terminal enter the following:
```
cf config create-context codefresh --api-key <your_key_here>
```

- Now the CLI is authenticated against your Codefresh account. You can verify the authentication with the following command:
```
cf get context
```


