# WordPress + MySQL + NFS on DigitalOcean

## Cloud Config

```cloud-config.yaml```

The Cloud Config file should be added under user data when deploying a new droplet. 
This will configure NFS server for you. Make sure you add the correct ip subnet. You will want to use the worker node private network. 

```/mnt/volume_nyc1_01 <privateip/16>(rw,no_root_squash,no_subtree_check)```

## WordPress

```wordpress-nfs.yaml```

The WordPress yaml file can be deployed using the following command once the cluster have been built on DigitalOcean. 
Keep in mind that you will need Kubectl installed. You can find installation instructions [here](https://kubernetes.io/docs/tasks/tools/install-kubectl/). Getting started with kubernetes on DigitalOcean can be found [here](https://www.digitalocean.com/docs/kubernetes/quickstart/).

```kubectl create -f wordpress-nfs.yaml```

Make sure you add the correct ip address for NFS server in the wordpress yaml file. 

```# NFS Persistent Volume
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
    server: 10.136.5.230
    path: "/mnt/volume_nyc1_01"
