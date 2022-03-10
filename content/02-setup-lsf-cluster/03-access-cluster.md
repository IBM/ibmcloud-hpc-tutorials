---
title: "Access Your Cluster"
date: 2021-12-08T10:03:16-05:00
draft: true
weight: 60 
tags: ["LSF"] 
---

### Overview of the HPC Cluster
The HPC Cluster consists of a **login node**, a **storage node** where the block storage volume is being attached to, 1 to 3 **LSF management nodes**, and a number of **LSF worker nodes**. 
<!--![lsf arch](/images/setup-lsf-cluster/lsf_arch.png) -->
{{< figure src="/images/setup-lsf-cluster/lsf_arch.png" alt="lsf_arch" width="860" >}}

* The **login node** is served as a jump host and it is the only node which has the public IP address. Other nodes would only have private IP addresses and the only way to reach to these nodes is through the login node. Users should log in to the primary LSF management node (or LSF master) and do most of the operations from the LSF master. By default, lsfadmin is the only user id being created on the cluster. The ssh passwordless setup is configured between the LSF master and workers. Users can reach to any other worker node with the lsfadmin user id from the LSF master. 

* The **worker node** can be a static resource. In this case its lifecycle is managed by Schematics/Terraform. Users can request a number of static worker nodes and these workers remain available in the LSF cluster until a Schematics/Terraform destroy action is being performed. The LSF Resource Connector functionality creates additional workers when there is not enough capacity to run jobs and destroys workers when the demands decrease. The lifecycle of these dynamic workers is managed by the LSF Resource Connector. Users should wait these dynamic resources returned to the Cloud before destroying the entire VPC cluster through Schematics/Terraform.

* The **storage node** is configured as an NFS server and the block storage volume is mounted to /data which is exported to share with LSF cluster nodes. At the NSF client end, the LSF cluster nodes in this case, we mount the remote directory, `/data`, to `/mnt/data` locally. A soft link, `/home/lsfadmin/shared`, also points to `/mnt/data`. Users can use `/home/lsfadmin/shared` as a shared file system for their applications.

### Schematics Workspace
After you click **_Install_**, you will be taken to a Schematics workspace created in your Cloud account show as below. 
![ws](/images/setup-lsf-cluster/lsf_ws.png)
This is where you manage your HPC cluster.
You can learn more about IBM Cloud Schematics Solution [here](https://www.ibm.com/cloud/schematics).

Click on **_Jobs_** and you will be updated with the progress of cluster creation. Once it's completed, you will be given
a ssh command towards the end of this log file. Go to the terminal where you have your SSH key and run the ssh command.
You can use _lsid_ to display the LSF cluster information.
```
% ssh -J root@141.125.159.202  lsfadmin@10.242.64.37
Last login: Mon Dec  6 14:38:05 2021 from 10.242.64.4

[lsfadmin@icgen2host-10-242-64-37 ~]$ lsid
IBM Spectrum LSF Standard 10.1.0.11, Jul 16 2021
Copyright International Business Machines Corp. 1992, 2016.
US Government Users Restricted Rights - Use, duplication or disclosure restricted by GSA ADP Schedule Contract with IBM Corp.

My cluster name is HPCCluster
My master name is icgen2host-10-242-64-37
```

That's it. You are on the LSF management node and the cluster is ready for you to run your HPC workloads.
