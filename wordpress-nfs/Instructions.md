# Deploying WordPress On Kubernetes Using External NFS 

### Introduction

WordPress is a stateful application that relies on a filesystem and a MySQL database as backends. Since volumes cannot be attached to more than one worker we must use a service like NFS to share the file system across worker nodes. 

In this guide, you will deploy a Kubernetes cluster, NFS server and deploy WordPress in Kubernetes. You will also be able to scale the WordPress application across multiple worker nodes. The WordPress pods will  automatically mount the NFS share and populate the NFS share with WordPress default data. 

## Prerequisites

Before you begin this guide you'll need the following:

* Kubectl installed on your workstation. You can find instructions on how to install kubectl in the following [article](https://www.digitalocean.com/docs/kubernetes/how-to/connect-with-kubectl/).


## Step 1 — Creating Kubernetes Cluster. 

In this step we are going to deploy the Kubernetes cluster using the DigitalOcean Cloud portal. 

First, lets login to the DigitalOcean portal and on the left hand panel you should see kubernetes. Click on kubernetes and it should take you to a page similar the one below. 

![Step1-000](https://raw.githubusercontent.com/areyesjr/do-kubernetes/master/wordpress-nfs/img/Step1-000.png)

Next, let's create the cluster. Click on "create a kubernetes cluster". For this demo you will want to select Kubernetes version 1.13.2. Select NYC1 and leave the rest of the settings as default. 

![Step1-001](https://github.com/areyesjr/do-kubernetes/blob/master/wordpress-nfs/img/Step1-001.png?raw=true)

Once all of the options have been selected you will want to click on "create cluster". This is a quick process on DigitalOcean and usually takes around 4 minutes to create a 3 node cluster. 

![Step1-002](https://github.com/areyesjr/do-kubernetes/blob/master/wordpress-nfs/img/Step1-002.png?raw=true)

Now that the cluster has been completed we will want to download the config file. Click on "Download Config File"

![Step1-003](https://github.com/areyesjr/do-kubernetes/blob/master/wordpress-nfs/img/Step1-003.png?raw=true)

Now that you have the config file you will want to pass it to kubectl. You can achieve this by moving the downloaded configuration file under ~.kube/config.

You can run the following command in terminal. If the path does not yet exist you must create it. 

``` mv ~/Downloads/<Downloaded config file> ~/.kube/config ```

You can verify that the connection is running by executing the following command. 

``` kubectl get nodes -o wide ```

![Step1-004](https://github.com/areyesjr/do-kubernetes/blob/master/wordpress-nfs/img/Step1-004.png?raw=true)

Take note of the internal ip for the worker nodes since we will need this information later when deploying the NFS server. 

This completes Step 1 you have successfully created a kubernetes cluster and have connected to the cluster using kubectl. 

## Step 2 — Creating NFS Cluster. 

Another introduction

Your content

Transition to the next step.

## Step 3 — Deploying the Kubernetes WordPress Deployment. 

Another introduction

Your content

Transition to the next step.

## Conclusion

In this article you [configured/set up/built/deployed] [something]. Now you can....

<!-- Speak  to reader benefits of this technique or procedure and optionally provide places for further exploration. -->



<!-- Some examples of how to mark up various things

This is _italics_ and this is **bold**.

Only use italics and bold for specific things. Learn more at https://do.co/style#bold-and-italics

This is `inline code`. Use it for referencing package names and commands.

Here's a command someone types in the Terminal:

```command
sudo nano /etc/nginx/sites-available/default
```

Here's a configuration file. The label on the first line lets you clearly state the file that's being shown or modified:

```nginx
[label /etc/nginx/sites-available/default]
server {
    listen 80 default_server;
    listen [::]:80 default_server ipv6only=on;

    root <^>/usr/share/nginx/html<^>;
    index index.html index.htm;

    server_name localhost;

    location / {
        try_files $uri $uri/ =404;
    }
}
```

Here's output from a command:

```
[secondary_label Output]
Could not connect to Redis at 127.0.0.1:6379: Connection refused
```

Learn about formatting commands and terminal output at https://do.co/style#code

Key presses should be written in ALLCAPS with in-line code formatting: `ENTER`.

Use a plus symbol (+) if keys need to be pressed simultaneously: `CTRL+C`.

This is a <^>variable<^>.

This is an `<^>in-line code variable<^>`

Learn more about how to use variables to highlight important items at https://do.co/style#variables

Use `<^>your_server_ip<^>` when referencing the IP of the server.  Use `111.111.111.111` and `222.222.222.222` if you need other IP addresses in examples.

Learn more about host names and domains at https://do.co/style#users-hostnames-and-domains

<$>[note]
**Note:** This is a note.
<$>

<$>[warning]
**Warning:** This is a warning.
<$>

Learn more about notes at https://do.co/style#notes-and-warnings

Screenshots should be in PNG format and hosted on imgur. Embed them in the article using the following format:

![Alt text for screen readers](/path/to/img.png)

Learn more about images at https://do.co/style#images-and-other-assets
-->

