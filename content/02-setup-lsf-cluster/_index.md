---
title: "Standing up an LSF Cluster"
date: 2021-12-01T09:00:00Z
weight: 20
pre: "<b>II ⁃ </b>"
tags: ["HPC", "Introduction", "LSF", "Optional"]
---

[IBM Spectrum LSF](https://www.ibm.com/cloud/blog/announcements/ibm-spectrum-lsf-is-now-available-on-ibm-cloud) 
is part of the HPC Clustering Services on IBM Cloud to enable users to stand up a HPC environment within minutes. 
LSF is an advanced workload management solution for demanding HPC environments. HPC cluster deployment starts with
the definition of the desired cluster configuration; for example, count of management nodes, worker nodes, amount
of storage and so on. Open-source technologies like Terraform and Ansible have been used to automate the provisioning
and configuration of all associated IBM Cloud VPC resources and LSF. 

In this section, you are using the [IBM Spectrum LSF catalog tile](https://cloud.ibm.com/catalog/content/terraform-1623200063-71606cab-c6e1-4f95-a47a-2ce541dcbed8-global) to create a HPC cluster in IBM Cloud and run a LSF job as you would on-premises. This includes the following steps:

* Prepare your SSH key and IBM Cloud API keys.
* Configure cluster parameters from your IBM Cloud web console.
* Create your first cluster.
* Submit a sample job and check what is happening in the background.
* Delete the cluster.
