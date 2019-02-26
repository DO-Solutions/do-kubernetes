# Deploying WordPress On Kubernetes Using External NFS 

### Introduction

WordPress is a stateful application that relies on a filesystem and a MySQL database as backends. Since volumes cannot be attached to more than one worker we must use a service like NFS to share the file system across worker nodes. 
In this guide, you will deploy a Kubernetes cluster, NFS server and deploy WordPress in Kubernetes. You will also be able to scale the WordPress application across multiple worker nodes. The WordPress pods will  automatically mount the NFS share and populate the NFS share with WordPress default data. 

## Prerequisites

Before you begin this guide you'll need the following:

* Kubectl installed on your workstation. You can find instructions on how to install kubectl in the following [article](https://www.digitalocean.com/docs/kubernetes/how-to/connect-with-kubectl/).

## Step 1 — Creating Kubernetes Cluster. 

In this step we are going to deploy the Kubernetes cluster using the DigitalOcean Cloud portal.
Documentation for creating a k8s cluster can be found [here](https://www.digitalocean.com/docs/kubernetes/how-to/create-clusters/).

* Kubernetes must be created in the NYC1 region.
* Once the cluster has been provisioned you will want to download the k8s config. 
* We will now want to connect to the cluster. You can achieve this by moving the downloaded k8s configuration file under ```~/.kube/config```.

You can run the following command in terminal. If the path does not yet exist you must create it. 

``` mv ~/Downloads/<Downloaded config file> ~/.kube/config ```

You can verify that the connection is running by executing the following command. 

``` kubectl get nodes -o wide ```

Take note of the internal IP for the worker nodes since we will need this information later when deploying the NFS server. 
This completes step 1 you have successfully created a kubernetes cluster and have connected to the cluster using kubectl. 

## Step 2 — Creating NFS Cluster. 

In this step we are going to deploy the NFS Server using the DigitalOcean Cloud portal.
You will use the following cloud config file to automaticly install and enable the NFS services. 
If you are not familiar with the cloud config we recomend that you read the following [article](https://www.digitalocean.com/community/tutorials/an-introduction-to-cloud-config-scripting).

* Create a droplet in DigitalOcean portal. 
* NFS Droplet must be Centos7 or above. 
* User Data and Private Networking must be selected under additional options. 
* You will inset the following code under User Data when selected. 

<$>[note]
**Note:** You must modify the config-config below and enter one of the worker private IP address we noted above. 
<$>

```
#cloud-config

package_upgrade: true

packages:
  - nfs-utils
  - nfs-utils-lib

runcmd:
- [ systemctl, enable, nfs-server.service ]
- [ systemctl, start, nfs-server.service ]

write_files:
  - path: /etc/exports
    content: |
      /mnt/volume_nyc1_01 WORKER_IP_ADDRESS/16(rw,no_root_squash,no_subtree_check)
```

Once the NFS server has been provision give it a couple of minutes to complete the Cloud-Config scritps.
You can ssh into NFS server and run the following commands to check if Cloud-Config executed as expected. 

```
systemctl status nfs-server
```

```
cat /etc/exports
```

If things did not go as planned you can check the following review the following log file. 

```
/var/log/cloud-init
```

If everything checks out and there are no errors you can continue to the next section which is to deploy wordpress. 

## Step 3 — Deploying WordPress on Kubernetes. 

Another introduction

Your content

Transition to the next step.

## Conclusion

In this article you [configured/set up/built/deployed] [something]. Now you can....

