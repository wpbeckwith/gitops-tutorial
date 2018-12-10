Kubecon Seattle 2018 Gitops Tutorial
====================================

This repository contains the hands-on Gitops tutorial given at Kubecon 2018 in Seattle. You can find the slides by clicking the image below. Follow the tutorial by reading through this README.

If you see errors, mistakes or typos in this tutorial, please feel free to submit a pull request.

[![](resources/presentation.png?raw=true)](https://docs.google.com/presentation/d/1ujRd4k2s8dG0-AMHIWMTyA8JoTUkXRQwXQ4izmDWeTI/edit?usp=sharing)

Prerequisites
-------------

In order to go through this tutorial, you'll need two things.

### 1. A Kubernetes cluster

In order to conplete this tutorial, you will need a test kubernetes cluster. You have several options to quickly get a development cluster to test out Gitops.

We will use [Katacoda's Kubernetes playground](https://www.katacoda.com/courses/kubernetes/playground). This will allow you to access a small Kubernetes cluster on demand. The `kubectl` command line is set up by default and should be used from the master node.
<!--
2. Run [Minikube](https://github.com/kubernetes/minikube) locally. This will require you to have [Virtualbox](https://www.virtualbox.org/) or some other hypervisor installed. This is great if you have virtualbox installed, but the download is over 2GB. See the [setup instructions for Minikube](docs/minikube-install.md).
3. Use [Google Kubernetes Engine](https://cloud.google.com/kubernetes-engine/). You'll need to have a google cloud account set up to do this, and it will cost you a small amount to keep your cluster running. See the [setup instructions for GKE](docs/gke-install.md)
-->

But you may have another way of getting a kubernetes cluster up and running. If that's the case, great! This tutorial should work with any valid kubernetes installation. 

Before you begin, you should make sure that the `kubectl` command works and you can query basic information from kubernetes. for example, run

```
➤ kubectl get pods --all-namespaces
```

To get a listing of all the running pod on your cluster, or

```
➤ kubectl cluster-info
```

To get a quick status check on your cluster. If these commands complete succesfully, you're ready to go onto the next step.

### 2. A Github or Bitbucket account
You'll want to create a new repository for this tutorial and make it available from your cluster through a git URL. If you're using Bitbucket, make sure you have [SSH keys enabled](https://confluence.atlassian.com/bitbucketserver/enabling-ssh-access-to-git-repositories-in-bitbucket-server-776640358.html) as we'll need these to configure the deploy operator access. 

Of course, you can also configure the Flux operator to [use a private git host](https://github.com/weaveworks/flux/blob/master/site/standalone-setup.md#using-a-private-git-host), but configuring this is outside the scope of this introductory tutorial.

Getting started with Gitops
---------------------------

### 1. Fork & Clone this repository 
In order to control the operation of your cluster using gitops, you'll need to have a control repository in which the state of your cluster can be defined. `github.com/bricef/gitops-tutorial` is already set up with all the files you'll need to follow along. Fork it to your Bitbucket or github account. When you have your own remote repository, clone it to your local workspace:

```
➤ git clone <url of your forked repository>
```


### 2. Install the flux operator on your cluster
Now that you have a repository set up, it's time to install the deployment operators on your cluster. For this we'll use Weavework's [Flux](https://github.com/weaveworks/flux). 

The needed deployment manifests are kept in this repository's `flux` directory. 

If you have `kubectl` configured correctly, you'll be able to install the Flux operator by applying the manifests to your cluster:

```
➤ cd gitops-tutorial
➤ kubectl apply -f ./flux/
```

You should see that this will create several objects in your cluster

```
serviceaccount/flux created
clusterrole.rbac.authorization.k8s.io/flux created
clusterrolebinding.rbac.authorization.k8s.io/flux created
deployment.apps/flux created
secret/flux-git-deploy created
deployment.apps/memcached created
service/memcached created
```

Alongside flux, we have also installed a memcache service for flux to use, and appropriate permissions for the flux operator to act on the cluster itself through a role and rolebinding. 

You should now be able to see the flux pod running in the cluster:

```
➤ kubectl get pods
NAME                        READY     STATUS    RESTARTS   AGE
flux-9d69f6fc4-5t5w6        1/1       Running   0          18m
memcached-dbb59cb58-phk9s   1/1       Running   0          18m
```

Now that the operator is running in your cluster, we'll need to configure it to have access to your repository, so that it can read changes you make, as well as create commits when a service is automatically deployed.

To do this, we'll need to do two things. Firstly, we'll need to allow the flux operator to act on the repository; secondly, we'll need the flux operator to be configured to look for cluster configuration in a particular repository.

### 3. Sharing the operator's public key with the remote git repository
To allow flux to operate on the repository, we'll need to copy its public SSH key to Github or Bitbucket. 

We can get the operator's public key using the [fluxctl](https://github.com/weaveworks/flux/blob/master/site/fluxctl.md) tool.

First, we'll need to install the `fluxctl` tool. There are [different ways of installing it](https://github.com/weaveworks/flux/blob/master/site/fluxctl.md#installing-fluxctl) depending on your platoform. For this tutorial, we can grab the binary release directly.

```
➤ wget https://github.com/weaveworks/flux/releases/download/1.8.1/fluxctl_linux_amd64
➤ chmod +x fluxctl_linux_amd64
```

Once installed and executable, we can use the fluxctl command line tool to get the Flux operator's public key:

```
➤ fluxctl_linux_amd64 identity
```

XXX: HERE


### 4. Configure the operator to read from your repository
- Configure the deploy operator to point to your cloned repository, to the `deploy/kubernetes` folder


### 5. Add a yaml file 
Now that the operator is set up to react to changes in your repository's `deploy/kubernetes` directory, we need to provide it with some configuration for it to synchronise.

### 6. Manually delete a deployment
Now that we know that the gitops operator is running in your cluster, let's check the control group 

### 7. Modify the configuration and commit your changes
- look at number of running pod for service
- scale up deployment in the yaml
- commit
- watch the cluster for the change

### 8. Automate deployment
- look at version of deployment
- add automation annotation
- watch to see the cluster update to latest version

### 9. Bonus: Deploy monitoring in a gitops way
- copy the monitoring folder yamls
- Look at the exposed service for grafana
- modify the dashboard script to add a new dashboard
- commit the changes.






Finally
-------

Now you're done with this tutorial, make sure you delete your cluster so you don't incur running costs. See the 

- [Teardown instructions for Minikube](docs/minikube-teardown.md)
- [Teardown instructions for GKE](docs/gke-teardown.md)

Further resources
-----------------

You can find much more about Gitops online.

- 📄 The [original Gitops blog post](https://www.weave.works/blog/gitops-operations-by-pull-request)
- 📄 A [more recent follow up blog post](https://www.weave.works/blog/what-is-gitops-really)
- 📄 A [comprehensive Gitops primer](https://www.weave.works/technologies/gitops/)
- 📄 The [Gitops FAQ](https://www.weave.works/technologies/gitops-frequently-asked-questions/)
- 🎥 A [recorded presentation on Gitops and its implementation at Weaveworks](https://vimeo.com/293138562/5aa199fa9e)




