+++
title = "2.  GitOps Deployments"
chapter = true
weight = 30
+++

# Deploying with GitOps

GitOps is a set of best-practices where the entire code delivery process is controlled via Git, including infrastructure and application definition as code and automation to complete updates and rollbacks.


The Key GitOps Principles: 

1. The entire system (infrastructure and applications) is described declaratively
2. The canonical desired system state is versioned in Git.
3. Changes are happening via Git commits, pushes and merges
4. Software agents to ensure correctness and alert on divergence. 

In the case of Kubernetes, GitOps deployments happen in the following manner

1. A GitOps agent is deployed on the cluster
2. The GitOps agent is monitoring one or more Git repositories that define applications and contain Kubernetes manifests (or Helm charts or Kustomize files)
3. Once a Git commit happens the GitOps agent is instructing the cluster
to reach the same state as what is described in Git
4. Developers, operators and other stakeholders perform all changes
via Git operations and never directly touch the cluster (or perform manual kubectl commands)


{{% children showhidden="false" %}}


