+++
title = "Run Unit tests"
chapter = false
weight = 22
+++

Getting a basic build is great as a starting point. The pipeline we have now
can be improved by adding some extra steps for verifying the image in regards
to quality and security.

We enhance the pipeline with extra steps that

1. run unit tests on the source code
2. deploy the application in a temporary environment and run integration tests against it
3. scan the container image for security issues.

This is the enhanced pipeline:

![Enhanced pipeline](/images/basic_ci/enhanced-pipeline.png)

## Add unit tests

Unit tests are the pillars of modern software quality. They should be easy to setup and easy to run requiring only the source code and nothing else.

Our example application already has [JUnit tests](https://junit.org) that are normally executed with the command `mvn test` via [Maven](https://maven.apache.org/).

To replicate the same thing in Codefresh we choose a Docker image that contains Maven and then use it in a [freestyle step](https://codefresh.io/docs/docs/codefresh-yaml/steps/freestyle/), that allows you to run any docker container in the context of a pipeline.


Add this snippet in your pipeline after the `main_clone` step.

```yaml
  run_unit_tests:
    title: Unit test
    stage: package
    image: 'maven:3.5.2-jdk-8-alpine'
    commands:
      - mvn -Dmaven.repo.local=/codefresh/volume/m2_repository test
```

In a similar way you could run tests with gradle, gulp, node, jasmine, protractor and any other programming framework that you use. Simply choose a docker image that contains the tool and its dependencies and pass it to a freestyle step. Most times all popular tools already have a public container image available in Dockerhub.

In the case of Maven we also want to cache all dependencies so we place the Maven repository on the [Codefresh volume](https://codefresh.io/docs/docs/configure-ci-cd-pipeline/pipeline-caching/#traditional-build-caching) which is a shared volume between all steps all the pipeline. It is also saved for any subsequent runs of the same pipeline.

## Add security scanning

Apart from quality checks, another best practice is the automated security scans of your application to detect security issues and vulnerabilities.

There are any solutions for [security scanning](https://codefresh.io/docs/docs/testing/security-scanning/) and several of them support scanning both the source code of the application as well as the resulting container images.

For the purposes of this workshop we will use the free [Trivy scanner](https://github.com/aquasecurity/trivy) that is completely open source and self-contained (it doesn't need a subscription or online account to a security service).

There is already a [public Dockerhub image](https://hub.docker.com/r/aquasec/trivy) for Trivy. This means that we can use it right away in a Codefresh freestyle step
right in the pipeline.

Add this snippet in your pipeline after the `build_my_image` step.

```yaml
  scan_image:
    title: Container security scan
    stage: verify
    image: 'aquasec/trivy'
    commands:
      - trivy image docker.io/<your docker username>/my-app-image:${{CF_SHORT_REVISION}}

```

 {{% notice note %}}
Remember to replace `<your docker username>` with your own Dockerhub username.
{{% /notice %}}

We run Trivy in the simplest way possible. In this mode it will only scan the image
and then print on the log out any issues it finds in a text report.

![Security scans](/images/basic_ci/security-scan.png)

In a real application you would probably have a security dashboard to track all vulnerabilities over time and you would have multiple security steps (that also focus on the source code as well and not just the container image).

{{% notice info %}}
In this example application, the pipeline will always continue running regardless of any security issues. With Codefresh you also have the capability to stop the pipeline
if a certain threshold is reached (i.e. critical security issues).
{{% /notice %}}


## Add integration tests

Simple unit tests (that only need the source code) are trivial to run in the middle of the pipeline. Several times however you also have integration/component/end2end tests
that need the running application to be deployed first before the tests can "hit" it.

Codefresh offers [service containers](https://codefresh.io/docs/docs/codefresh-yaml/service-containers/) that make [integration tests](https://codefresh.io/docs/docs/testing/integration-tests/) very easy to run.

With service containers you can launch a container image inline with the pipeline
with a well known DNS name that can be used by your pipeline steps. The syntax is similar to Docker compose.


Add this snippet in your pipeline after the `scan_image` step.

```yaml
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
```

Here the pipeline takes the application image and launches it under the name `my-spring-app` also exposing port 8080.

Then the pipeline is running the `mvn verify` goal for running integration tests while also passing as a parameter to the tests the url `http://my-spring-app` that the tests will use.

To make sure that the application is actually up and ready to serve requests before the tests run we define in the `readiness` property a quick check with curl.

For more details see the [Checking readiness of a service](https://codefresh.io/docs/docs/codefresh-yaml/service-containers/#checking-readiness-of-a-service) section in the Codefresh documentation.

{{% notice warning %}}
To make sure that your pipeline has enough resources to execute integration tests, click the *Settings* tab on the pipeline editor screen and then the 
*Runtime* section on the left menu. [Assign your pipeline](https://codefresh.io/docs/docs/configure-ci-cd-pipeline/pipelines/#runtime)
to a *Medium* instance.
If you don't see these options then your Codefresh plan is already using Medium instances and there is nothing you need to do.
{{% /notice %}}

{{% notice tip %}}
Codefresh has several more features for service containers such as preloading test data to databases, binding containers to localhost, reusing containers for all pipeline steps and so on.
{{% /notice %}}

## Running the pipeline

Here is how your whole pipeline should look now:

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
  run_unit_tests:
    title: Compile/Unit test
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
```

{{% notice info %}}
Remember to change the value of `<your docker username>` to your own Dockerhub username.
{{% /notice %}}


Click the *Run* button at the bottom of the screen to launch a new build.

Once the pipeline is running you can click on any individual step and see the log messages of that step:

![Integration tests](/images/basic_ci/integration-tests.png)

You are now practising Continuous integration. Every time somebody commits a code change,
an automated process creates a deployment artifact that is tested and secure.

