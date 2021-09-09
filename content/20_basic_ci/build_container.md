+++
title = "Build Container"
chapter = false
weight = 21
+++

Applications that are destined for a Kubernetes cluster, need to be packaged in a Docker container first.

We will setup an automated pipeline that does the following

1. Checks out source code from Git
2. Builds a Docker image with the source code
3. Stores the Docker image in a registry (Dockerhub in our case)

This is the pipeline that we will create:

![Create a Github fork](/images/basic_ci/pipeline-overview.png)

## Fork the sample application

First fork the example application so that it resized in your own Github account.

Go to [https://github.com/codefresh-contrib/aws-gitops-app](https://github.com/codefresh-contrib/aws-gitops-app) and click on the fork button.

![Create a Github fork](/images/basic_ci/fork.png)

Wait a bit until Github says that the fork is finished.


## Create a Codefresh project

Codefresh pipelines are grouped under projects. Project names can be anything you want with the most common example being the name of an application where all pipelines in the project are packaging/deploying the different microservices. You can think of projects as “folders/directories” for your pipelines.

Log into Codefresh and make sure that you have selected Projects from the left sidebar. Then click on the *New project* button on the top right corner to get started.

Enter a name for your project (e.g. my-first-project) and choose a sample icon that you like. Leave the *Project tags* field empty.

Click the Create button once you are done. You now have a new project and can start adding pipelines in it.

## Create a new pipeline

Click the *New pipeline button* in order to create a pipeline. Enter any name (e.g. basic-build).

![Create a Github fork](/images/basic_ci/create-pipeline.png)

Find your repository from the list and select it. Make sure that the option *Add Git commit trigger* is selected. This way your pipeline will be automatically launched when a commit happens on this repository.

Click the Create button. Your pipeline was created and you should now see the pipeline editor. Here you can describe what the pipeline will do using [Codefresh YAML](https://codefresh.io/docs/docs/codefresh-yaml/what-is-the-codefresh-yaml/).

## Build the docker image

Codefresh has already created a sample pipeline which we will not use for this tutorial.

You will create a very simple pipeline that checks out the source code and builds a docker image. Delete the existing contents on the editor and paste the following:

`codefresh.yml`
```yaml
version: '1.0'
stages:
  - checkout
  - package
  - verify
steps:
  main_clone:
    title: Cloning main repository...
    type: git-clone
    repo: '${{CF_REPO_OWNER}}/${{CF_REPO_NAME}}'
    revision: '${{CF_REVISION}}'
    stage: checkout
  build_my_image:
    title: Building Docker Image
    type: build
    stage: package
    image_name: <your docker username>/my-app-image
    working_directory: ./
    tags:
    - "${{CF_SHORT_REVISION}}"
    - latest
    dockerfile: Dockerfile
    registry: dockerhub
 ```     

 {{% notice note %}}
Remember to replace `<your docker username>` with your own Dockerhub username. Notice also the `registry` property that tells Codefresh where to push the image after it is built. This is the name of the Dockerhub integration that we setup in the previous section ("Connect Registry").
{{% /notice %}}



This pipeline contains just two steps.

1. A [git-clone step](https://codefresh.io/docs/docs/codefresh-yaml/steps/git-clone/) for checking out the code
1. A [build step](https://codefresh.io/docs/docs/codefresh-yaml/steps/build/) for building the docker image AND pushing it to the connected Docker registry.

The clone step is also using some built-in pipeline variables. They instruct the pipeline to checkout the exact code that is described from the commit of the trigger. 

The build step uses a Dockerfile that is located at the root folder of the project and creates a Docker image with a tag same as the hash of the Git commit.

Click the Save button to apply your changes. Then click the Run button to start your pipeline. On the dialog that will appear leave the default selections.

## Running the pipeline

Once the build is started Codefresh will navigate you to the build progress of the sample application.

You can click on any step and see its logs.

![Push image](/images/basic_ci/image-push.png)

 {{% notice info %}}
The Codefresh build step both builds **and** pushes the image to the registry. Even though Codefresh has a separate push step (for pushing an existing image to a container registry), it is completely optional.
{{% /notice %}}

The first time the pipeline runs, Codefresh will download all dependencies of the application. Every subsequent run will be much faster as the pipeline cache will take effect.

After a while the build should finish with success. All previous runs are in the [Builds page](https://g.codefresh.io/builds2) from now on and are accessible by clicking on *Builds* in the left sidebar.

![Previous builds](/images/basic_ci/builds.png)

## Inspecting your Docker image

With a successful run of the pipeline you will be able to see your created image in the [images dashboard](https://g.codefresh.io/images/) accessible by clicking on *Images* on the left sidebar.

![View the image](/images/basic_ci/registry.png)

You can click on the row of the image and see additional details and metadata of the image. Feel free to explore the tabs at the top for more information.

The *layers* tab shows how the image was created and which files/layers contribute to its total size.

![Docker layers](/images/basic_ci/layers.png)

Since we used Dockerhub as a registry, you can also find the same image in the Dockerhub UI at [`https://hub.docker.com/r/<your dockerhub id>/my-app-image/tags`](https://hub.docker.com/r/<your dockerhub id>/my-app-image/tags).

![Dockerhub tag](/images/basic_ci/dockerhub.png)

We now have a container image created in a fully automated way. Every time you perform a commit in the source code, Codefresh will run the pipeline again creating another tag with the new Git hash.