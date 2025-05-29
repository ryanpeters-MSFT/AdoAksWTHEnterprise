# AKS Enterprise WTH - Building ADO Pipelines

*This hack challenge is an addendum to the [AKS Enterprise What-The-Hack](https://microsoft.github.io/WhatTheHack/039-AKSEnterpriseGrade/) and assumes a working baseline cluster has been created and the application is operational.*

## Introduction

In this challenge, we'll implement an ADO pipeline based on our Kubernetes manifests, utilizing Helm and Azure CLI tasks to create our container image and release our images to our AKS cluster.

## Resource Folders

- [**app**](./app/) - Contains the "api" and "web" source code for your pipeline build step.
- [**helm**](./helm/) - Contains a base Helm chart to use for deploying the new release. *This is optional, and you may use whatever deployment methodology desired.*

## Description

The challenge does require the use of the Kubernetes manifest files that were built in previous challenges for use in our ADO pipeline. The goal is to create a pipeline that, at a minimum:

1. Retrieves the source code from a repositiory
2. Builds and creates an image from the source code
3. Push that image to a registry (e.g., Azure Container Registry)
4. Update our Kubernetes deployments to use the new image tag/SHA

## Building from Source

The source code for "API" and "web" are included in this repository. These files are currently on your workstation, but they will need to be pushed to a remote Git repository for use by the pipeline. ADO files is probably the easiest option, but another repository is fine, as long as you can authenticate to it from your pipeline. 

There are multiple ways to build the image. While you are welcome to use `docker build`, remember that Azure Container Registry has options to build from source (and does not require that docker is installed on the runner).

## Pushing a Release

Once you have authenticated to your cluster from your pipeline, there are multiple ways to push the changes. So far you have (typically) been using `kubectl apply`, supplying a deployment file with a specific tag, and while this may work, can be tedious and cumbersome with many files. 

Using [Kustomize](https://kubernetes.io/docs/tasks/manage-kubernetes-objects/kustomization/) is also an option, as this still requires the use of building a patch file. Another option is using `kubectl patch`, but this is not recommended, as this is more for "on-the-fly" updates and does not keep versions of releases.

Using Helm would generally be recommended, as this allows for more dynamic (templated) use of our manifests and the ability to tokenize parameters for our build (e.g., image repository, tag name, version). A basic [Helm](./helm/) chart structure has been supplied for you.

## Success Criteria

*Participants can decide if they would like to release both "web" and "API" as a single pipeline, or split them out into multiple pipelines.*

- Source code has been pushed to a remote Git repository
- A ADO pipeline has been created that build upon a commit to the "master" or "main" branch
- Any changes to the web application, for example, are reflected on the updated deployment to the cluster (e.g., a minor HTML change in the web app)
- The pipeline completes multiple builds without error

## Learning Resources

- [Build and deploy to Azure Kubernetes Service with Azure Pipelines](https://learn.microsoft.com/en-us/azure/aks/devops-pipeline?tabs=cli)
- [Quickstart: Develop on Azure Kubernetes Service (AKS) with Helm](https://learn.microsoft.com/en-us/azure/aks/quickstart-helm?tabs=azure-cli)