+++
title = "Automated deployments"
chapter = false
weight = 41
+++

So far we have setup two separate Git repositories. One Git repository
for the application source code and one Git repository for the Kubernetes
manifests (Helm chart in our example application).

In the previous section you have seen how to deploy new versions of your application
by manually opening pull requests in the configuration repository.

We can fully automate this process by changing the Kubernetes manifests as part of the application pipeline. This means that instead of having a human changing both the application source code and the manifests, we will have our pipeline automatically update the manifests whenever the application source code is changed with the proper image tag.

## Changing automatically the Kubernetes Helm charts

Here is the pipeline that we will create.

![Full pipeline](/images/basic_cd/full-pipeline.png)

Edit your Codefresh YAML to the following:

`codefresh.yml`
```yaml
version: '1.0'
stages:
  - checkout
  - package
  - verify
  - gitops
steps:
  main_clone:
    title: Cloning main repository...
    type: git-clone
    repo: '${{CF_REPO_OWNER}}/${{CF_REPO_NAME}}'
    revision: '${{CF_REVISION}}'
    stage: checkout
  run_unit_tests:
    title: Unit test
    stage: package
    image: 'maven:3.5.2-jdk-8-alpine'
    commands:
      - mvn -Dmaven.repo.local=/codefresh/volume/m2_repository test    
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
  scan_image:
    title: Container security scan
    stage: verify
    image: 'aquasec/trivy'
    commands:
      - trivy image docker.io/<your docker username>/my-app-image:${{CF_SHORT_REVISION}}  
  run_integration_tests:
    title: Integration tests
    stage: verify
    image: maven:3.5.2-jdk-8-alpine
    commands:
     - mvn -Dmaven.repo.local=/codefresh/volume/m2_repository verify -Dserver.host=http://my-spring-app 
    services:
      composition:
        my-spring-app:
          image: '${{build_my_image}}'
          ports:
            - 8080
      readiness:
        timeoutSeconds: 30
        periodSeconds: 15
        image: byrnedo/alpine-curl
        commands:
          - "curl http://my-spring-app:8080/"    
  clone_gitops:
     title: cloning gitops repo
     type: git-clone
     arguments:
       repo: '${{CF_REPO_OWNER}}/aws-gitops-app-manifests'
       revision: 'main'
     stage: "gitops"
     when:
       branch:
         only:
           - main    
  change_manifest:
    title: "Update Helm values"
    image: "mikefarah/yq:3" 
    commands:
    - yq w -i simple-java-app/values.yaml image.tag ${{CF_SHORT_REVISION}}
    - cat simple-java-app/values.yaml
    working_directory: "${{clone_gitops}}" 
    stage: "gitops"
    when:
      branch:
        only:
          - main 
  commit_and_push:
    title: Commit Helm values
    type: git-commit
    stage: "gitops"
    arguments:
      repo: '${{CF_REPO_OWNER}}/aws-gitops-app-manifests'
      git: github
      working_directory: '/codefresh/volume/aws-gitops-app-manifests'
      commit_message: Updated Helm values
      git_user_name: ${{CF_COMMIT_AUTHOR}}
      git_user_email: <your email address>
    when:
      branch:
        only:
          - main                
```

{{% notice info %}}
Remember to change the value of `<your docker username>` to your own Dockerhub username and `<your email address>` to your Github email address.

In a real application you should also use [Codefresh variables](https://codefresh.io/docs/docs/configure-ci-cd-pipeline/shared-configuration/)
instead of hardcoding everything in the pipeline
{{% /notice %}}

The new steps of this pipeline do the following.

1. Clone the repository that contains the Helm chart of the application
2. Use the [yq](https://github.com/mikefarah/yq) utility to change the value of the image tag
3. Use the [Codefresh commit plugin](https://codefresh.io/steps/step/git-commit) to push the changes back to the repo. This is a key step in this pipeline because it means that we no longer need to manually edit the manifests and commit/push changes
there. The pipeline will take care of this.

Notice that for the deployment steps we [use conditionals](https://codefresh.io/docs/docs/codefresh-yaml/conditional-execution-of-steps/) to restrict them only to the main branch because we don't want to deploy to our "production" environment when a Pull request happens to an unrelated branch.

## Running the full pipeline

You can trigger this pipeline as before by making any simple change to the application repository (try changing again the hello world message again at the `aws-gitops-app/src/main/java/sample/actuator/HelloWorldService.java` file). Once you commit and push
the pipeline will be automatically triggered. 

![Change deployment tag](/images/basic_cd/change-tag.png)

It will then do the following:

1. Checkout the application source code first
1. Run unit tests
1. Create a Docker image
1. Scan the docker image for security issues
1. Run integration tests
1. Checkout the manifests and update the tag in the Helm values
1. Commit/Push the results back in the manifests repository.

At this point the Codefresh GitOps agent will perform the deployment automatically.
After a few minutes if you visit the application again in your browser you will see the new application version deployed.




