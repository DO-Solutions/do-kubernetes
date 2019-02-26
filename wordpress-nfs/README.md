# Deploying WordPress On Kubernetes Using External NFS 

### Introduction

WordPress is a stateful application that relies on a filesystem and a MySQL database as backends. Since volumes cannot be attached to more than one worker we must use a service like NFS to share the file system across worker nodes.

In this guide, you will deploy a Kubernetes cluster, NFS server and deploy WordPress in Kubernetes. You will also be able to scale the WordPress application across multiple worker nodes. The WordPress pods will  automatically mount the NFS share and populate the NFS share with WordPress default data. 

![Diagram](https://raw.githubusercontent.com/DO-Solutions/do-kubernetes/master/wordpress-nfs/img/diagram.png)

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

* NFS Droplet must be Centos7 or above. 
* User Data and Private Networking must be selected under additional options. 
* You will insert the following code under User Data when selected. 

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

The following command checks the status of the NFS server. 

```
systemctl status nfs-server
```
The following command checks that the exports file was written to. 

```
cat /etc/exports
```

If things did not go as planned you can review the following log file. 

```
/var/log/cloud-init
```

You will also want to take note of the private IP of the NFS server since we will be using it later. 

```
ifconfig | grep inet
```

If everything checks out and there are no errors you can continue to the next section which is to deploy wordpress. 

## Step 3 — Deploying WordPress on Kubernetes. 

In this step we are going to deploy the WordPress application using the ```wordpress-nfs.yaml``` file.
You can simply download the yaml file or clone the entire repository in order to run it. 

The NFS persistent volume block is the only section that requires modification. 
You must add the private IP of the NFS server that we took note of earlier. 

```
# NFS Persistent Volume
apiVersion: v1
kind: PersistentVolume
metadata:
  name: nfs
spec:
  capacity:
    storage: 10Gi
  accessModes:
    - ReadWriteMany
  nfs:
    # Use NFS Service Private IP 
    server: <NFS-PRIVATE-IP>
    path: "/mnt/volume_nyc1_01"
```

We are now ready to deploy WordPress on Kubernetes. 

``` 
kubectl create -f wordpress-nfs.yaml
```

Give the deployment a couple fo minutes to run and run the following command. 

```
kubectl get all
```

Once completed you will have a similar output the one below. Note that I have modified the IP address from my output. 

```
NAME                             READY   STATUS    RESTARTS   AGE
pod/mysql-674dcfbd85-hlf62       1/1     Running   0          3d21h
pod/wordpress-5d4cdb599b-28dfm   1/1     Running   0          3d20h
pod/wordpress-5d4cdb599b-2j748   1/1     Running   0          3d20h
pod/wordpress-5d4cdb599b-5vvkk   1/1     Running   0          3d20h
pod/wordpress-5d4cdb599b-6bnxf   1/1     Running   0          3d20h
pod/wordpress-5d4cdb599b-7wkzx   1/1     Running   0          3d21h
pod/wordpress-5d4cdb599b-8lgdv   1/1     Running   0          3d20h
pod/wordpress-5d4cdb599b-fhp78   1/1     Running   0          3d20h
pod/wordpress-5d4cdb599b-rjv2q   1/1     Running   0          3d20h
pod/wordpress-5d4cdb599b-ttz7n   1/1     Running   0          3d20h
pod/wordpress-5d4cdb599b-v8kcv   1/1     Running   0          3d20h

NAME                 TYPE           CLUSTER-IP         EXTERNAL-IP      PORT(S)        AGE
service/kubernetes   ClusterIP      111.111.111.111    <none>           443/TCP        4d
service/mysql        ClusterIP      111.111.111.111    <none>           3306/TCP       3d21h
service/wordpress    LoadBalancer   111.111.111.111   111.111.111.111   80:31150/TCP   3d21h

NAME                        READY   UP-TO-DATE   AVAILABLE   AGE
deployment.apps/mysql       1/1     1            1           3d21h
deployment.apps/wordpress   10/10   10           10          3d21h

NAME                                   DESIRED   CURRENT   READY   AGE
replicaset.apps/mysql-674dcfbd85       1         1         1       3d21h
replicaset.apps/wordpress-5d4cdb599b   10        10        10      3d21h

```

You should now be able to navigate to the external-ip of the cluster and see the WordPress deployment. 
You should also be able to navigate to the NFS server and see the contents of WordPress under ```/mnt/volume_nyc1_01```.

## Conclusion

In this artcile you deployed WordPress on Kubernetes using an external NFS server. 

