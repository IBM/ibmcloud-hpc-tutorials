---
title: "Prepare SSH and API Keys"
date: 2021-12-08T10:01:27-05:00
draft: false
weight: 40 
tags: ["LSF"] 
---

### Create Your SSL KEY key pair (if you don't have one already)

VSI access do not allow password-based authentication by default, so this step is essential
for anyone to get access to the VSIs after they are created. If you do not have SSH keys on
your machine where you plan to log in to your HPC cluster, use the **_ssh-keygen_** command to generate.
This command is commonly available in modern operating systems and can be run by any user.
In Linux, for example:
```
$ ssh-keygen
Generating public/private rsa key pair.
Enter file in which to save the key (/home/someone/.ssh/id_rsa): 
Enter passphrase (empty for no passphrase): 
Enter same passphrase again: 
Your identification has been saved in /home/someone/.ssh/id_rsa.
Your public key has been saved in /home/someone/.ssh/id_rsa.pub.
```
Copy and paste the content in /home/someone/.ssh/id_rsa.pub to the public key field in Step 2 below.

### Create Your IBM Cloud SSH Key
This step allows you to upload the public key of your existing public-private SSL key pair.
The public key then becomes a managed resource on IBM Cloud, which can be referred to by its resource id
during creation processes of other resources, such as VSIs.

When selecting the region, please use one of these 4 regions 
* us-south (Dallas)
* us-east (Washington DC)
* eu-gb (London)
* eu-de (Frankfurt)

After you sign in to https://cloud.ibm.com/ with your account, start with the Navigation Menu on the dashboard in the top left
corner of the page:

1. Click **_VPC Infrastructure_** > **_SSH Keys_**

2. Click **_Create_**
* Add name (e.g. demo-ssh-key), type your resource group name and select region
* Copy the public key and paste it in the `public_key` field(e.g.: contents in .ssh/id_rsa.pub)

3. Click on **_Add SSH Key_**

![SSHKEY](/images/setup-lsf-cluster/sshkey.png)


Please remember your ssh key name (e.g., demo-ssh-key), the resource group name and the region you have selected. You will need to use
those in the next step when creating a cluster.

### Create Your IBM Cloud API Key
This step allows you to create an **API key** from IBM Cloud. The API key is usually kept on the user's local machine,
which can be used as an authentication mechanism for IBM Cloud API calls. Without the API key,
many API calls would require you to re-authenticate with other, more cumbersome, mechanisms.
API keys are not bound to any specific datacenter.

1.   Go to _**Manage**_ > _**Access (IAM)**_
2.   Click **_API keys_**
3.   Click **_Create an IBM Cloud API key_**

      3.1. Enter a name for your API key and Click **_Create_**

      3.2. Then, click **__Show__** to display the API key. Or, click **_Copy_** to copy and save it for later, or click **_Download_**
![SSHKEY](/images/setup-lsf-cluster/apikey.png)

Please keep the API key saved in Step 3.2 in a secure place. You will need to use this in the next step when creating the cluster.

