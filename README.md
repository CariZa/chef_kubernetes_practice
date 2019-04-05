# Chef code for Terraform

## Files

Not a complete chef repo, just the important stuff. The recipes being used to automate the setup of the kubernetes nodes.

## Kubernetes Setup

Recipes:

    /cookbooks/kubernetes_setup/recipes/server.rb

General apt update & upgrade & install.

Installs:

* docker
* kubernetes
* kubeadm
* kubelet
* kubectl

Set up:

* new user "normal" to run commands without root access
* ssh access for normal user
* activates docker

## Kubernetes Init with CNI

Recipes:

    /cookbooks/kubernetes_cni/recipes/default.rb

This recipe is responsible for running the kubeadm init with the cni ip range:

    $ kubeadm init --pod-network-cidr 192.168.0.0/16

## Kubernetes Leader

Recipes:

    /cookbooks/kubernetes_leaders/recipes/server.rb

This recipe handles more of the configuration of the leader node.

Sets up:
* cni - calico
* stores some value to be used by minions to auto join the cluster:
  * leader ip
  * kubernetes crt value
  * kubernetes join token

Connects to a chef databag, which allows the script to store the access tokens that the minions will use to "auto join" the cluster.

Stored as plain text for now, just for short term sand boxing, to be improved in future. Can look at using vault.



## Kubernetes Minion

Recipes:

    /cookbooks/kubernetes_minion/recipes/server.rb

Connects with the chef data bag, pulls in the values it needs to join the cluster.

Handles:
* runs kubeadm join, current node to cluster as a minion
* moves config to "normal" user profile (not really needed anymore, was using this to do some testing on the nodes)

