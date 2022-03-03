---
title: "Prepare SSH and API Keys"
date: 2021-12-08T10:01:27-05:00
draft: true
weight: 40 
tags: ["LSF"] 
---

### Create Your SSH KEY
After you sign in to https://cloud.ibm.com/ with your account, start with the Navigation Menu on the dashboard in the top left
corner of the page:

1. Click **_VPC Infrastructure_** > **_SSH Keys_**

2. Click **_Create_**
* Add name (e.g. demo-ssh-key), type your resource group name and select region
* Copy the public key and paste it in the `public_key` field(e.g.: contents in .ssh/id_rsa.pub)

3. Click on **_Add SSH Key_**

![SSHKEY](/images/setup-lsf-cluster/sshkey.png)

If you do not have SSH keys on your machine where you plan to log in to your HPC cluster, use the **_ssh-keygen_** command to generate.
```
[root@hf-ocp-login1 ~]# ssh-keygen
Generating public/private rsa key pair.
Enter file in which to save the key (/root/.ssh/id_rsa): 
Enter passphrase (empty for no passphrase): 
Enter same passphrase again: 
Your identification has been saved in /root/.ssh/id_rsa.
Your public key has been saved in /root/.ssh/id_rsa.pub.
```
Copy and paste the content in /root/.ssh/id_rsa.pub to the public key field in Step 2.

Please remember your ssh key name (e.g., demo-ssh-key), the resource group name and the region you have selected. You will need to use
those in the next step when creating a cluster.

### Create Your IBM Cloud API Key
1.   Go to _**Manage**_ > _**Access (IAM)**_
2.   Click **_API keys_**
3.   Click **_Create an IBM Cloud API key_**

      3.1. Enter a name for your API key and Click **_Create_**

      3.2. Then, click **__Show__** to display the API key. Or, click **_Copy_** to copy and save it for later, or click **_Download_**
![SSHKEY](/images/setup-lsf-cluster/apikey.png)

Please keep the API key saved in Step 3.2 in a secure place. You will need to use this in the next step when creating the cluster.

