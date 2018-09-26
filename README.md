# Using GitLab for Sustainable Kubernetes Management
> Written by Lucy Nunley 

## Abstract
With the DevOps industry having adapted the 'infrastructure-as-code' mentality, Git has become the source of truth for the code. In order to ensure continuous integration and delivery, it is necessary to sufficiently automate its deployment. Inevitably many engineers will write their own tools for achieving this. The practical engineer will adapt these tools to her own use. This document details the implementation of an off-the-shelf solution for this type of automation for Kubernetes.

## Problem Statement

> Kubernetes is managed through YAML or JSON files that help describe the state of the environment. These YAML files are numerous and sometimes very complex. We need a system to help manage these YAML files and use them to deploy the latest code to all of our environments.

### Objectives
* Maintainability and extendibility
* Addressing the difficulty of adding new environments dynamically
* Easily pluggable into existing CI/CD pipelines
* Support a dynamic multi-environment setup
* Gitlab integration preferred

## Discussion
This situation could be addressed with homemade scripts called by a CI runner, but this would neither be easily maintainable or extendable. Instead, a solution should be found which utilizes existing assets and minimizes complications to the devops setup. 

The following are solutions which could work but were not chosen:
1. Skaffold 
    * More oriented towards developers maintaining their own Kubernetes cluster
    * Would require more complex integration with Gitlab's CI runner
2. Amazon EKS/Platform9/etc
    * We don't necessarily need a new cloud host
3. Weaveworks Flux
    * Weaveworks products tend to try to draw you into their costly ecosystem
    * Requires working a Flux node into each cluster

## Proposal

Our solution is to use Gitlab CI's Kubernetes cluster feature. Since this is built into GitLab, there is no need for us to use 3rd party software. As GitLab says on their website, "having it all in one place simplifies toolchain complexity and speeds up cycle times".

Implementing this solution is simple, and should come with no downtime. Edenmal details this in his guide referenced below. Essentially, the existing cluster will be added to GitLab's integration, a `.gitlab-ci.yml` pipeline config file has to be created, and the manifest has to be added to the repository being pointed to. The creation of the GitLab CI file is where the bulk of the work for this solution is. Only having one file to maintain is a perk however. 

Other benefits of this solution:
* Easily supports a dynamic multi-cluster environment
* More fully utilizes existing product already paid for
* Automatic CD pipeline for the dev cluster

When an engineer makes a commit changing the cluster's configuration files, GitLab will run through the stages defined in the `.gitlab-ci.yml` file. This allows for code deployment. 

## Conclusion
By using a feature in a tool already being utilized, we save time that would have been spent on another tool. While more complete solutions like WeaveWorks Flux or Amazon EKS could work, they add extra legs to the DevOps pipeline. GitLab's Kubernetes integration should have all the functionality necessary for this situation.

## References
* Weaveworks - The GitOps Pipeline https://www.weave.works/blog/the-gitops-pipeline
* Weaveworks - Flux Github Readme https://github.com/weaveworks/flux
* Edenmal - Gitlab + Kubernetes: Using GitLab's CI Kubernetes Cluster feature https://edenmal.moe/post/2018/GitLab-Kubernetes-Using-GitLab-CIs-Kubernetes-Cluster-feature/
