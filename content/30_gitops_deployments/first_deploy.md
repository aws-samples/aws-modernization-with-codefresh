+++
title = "Your first deployment"
chapter = false
weight = 32
+++


Fork the following repository in your GitHub account https://github.com/codefresh-contrib/aws-workshop-demos. The application we are going to deploy
is in the `simple-helm-chart` folder.

It is a [simple Helm chart](https://helm.sh/) (the package manager for Kubernetes) that contains two manifests, one for the application and one for the Kubernetes service.

After you fork the repository, edit the file `simple-java-app/values.yaml`
and change the `repository` line to your own `<dockerhub id>`.

```yaml
replicaCount: 1

image:
  repository: <your dockerhub id>/my-app-image
  pullPolicy: IfNotPresent
  # Overrides the image tag whose default is the chart appVersion.
  tag: latest
```  

Click on “Applications” from the left sidebar in the Codefresh UI and then select the “Add application” button on top right.

![Adding the application](/images/gitops_first_deploy/add-application.png)

Enter a name for your application and make sure to select `codefresh-hosted` as the runtime.

In the next screen you can define your application details, and in the “destination” section you can choose your target deployments clusters that are managed by the hosted runtime.

![Adding the application](/images/gitops_first_deploy/add-settings.png)


Fill in the details.

* Repository URL: `https://github.com/<your github>/aws-workshop-demos`
* Revision: HEAD
* Path: `./simple-java-app`
* Cluster: `<your eks cluster>`
* Namespace: "default"
* Directory recurse: false

Remember that everything in Codefresh is committed to Git and that behind the scenes the platform is powered by the Argo family of tools.

After you define your application settings, the UI will ask you to commit your changes and will also show the auto-generated ArgoCD application that will be added to your Git repository (as defined in the previous section).

![First deployment](/images/gitops_first_deploy/first-deployment.png)

And that’s it! A sync process will start and your application will be deployed to your EKS cluster.

## Inspecting your application

You can monitor your deployment back in the Application dashboard that shows a list of all your active applications from all Argo/Codefresh instances that you have. If you have a large number of Argo instances, you can filter for the hosted runtime by selecting it from the drop-down on the runtime filter (top left of the screen).

![Application dashboard](/images/gitops_first_deploy/applications.png)


Afterwards you can click on the application and get access to all the usual dashboards available to applications managed by Codefresh.



## Deploying a new version

The first deployment is now active. To see how GitOps works in practice, we will make some additional deployments.

The main concept of GitOps is that ALL operations are happening via Git. 

To create a new application version make a change in GitHub in the `aws-workshop-demos/simple-java-app/src/main/java/sample/actuator/HelloWorldService.java` file.

![Application change](/images/gitops_first_deploy/app-change.png)

The exact change is not really important. Feel free to change the hello world message to something else (but keep the word "Spring" as it is checked by a Unit test).

{{% notice info %}}
For simplicity reasons we make a change straight into the main branch. In a production application we would make the change in a feature branch and [create a pull request](https://codefresh.io/docs/docs/ci-cd-guides/preview-environments/)
before actually accepting the change.
{{% /notice %}}


Commit and push your change and then click on the *Builds* section on the left sidebar in the Codefresh UI. Codefresh will automatically launch a pipeline to create a docker image as was described in the previous section.

![Edit new version](/images/gitops_first_deploy/git-change-app.png)

After the pipeline has finished a new Docker image will be created. Click on *Images* on the left sidebar and click on "my-app-image".

In the Activity column note down the last tag of the container image.

![Create image](/images/gitops_first_deploy/image-tag.png)

In the example above it is `docker.io/kostiscodefresh/my-app-image:385643c`

Now in Github go to your second folder that contains the Kubernetes manifests (and not the application source code). Edit the file `aws-workshop-demos/blob/main/simple-java-app/values.yaml` and enter your new container tag.

![Edit new version](/images/gitops_first_deploy/new-version-manual.png)

Commit your changes and after a brief amount of time, Codefresh GitOps will fetch the new container image. If you now visit your application endpoint in your browser 
you will see the new status message. If you don't remember your endpoint URL go back to your GitOps dashboard, click on your application and then find your endpoint
in the second tab called *Services*.

{{% notice note %}}
You might think that deploying a new version involves too many manual steps. Will automate the version change in the next section of this workshop. The important point here is that deploying a new version happens only via new changes in Git and not clicking buttons on running scripts manually. Using only Git operations is the pillar of GitOps.
{{% /notice %}}






