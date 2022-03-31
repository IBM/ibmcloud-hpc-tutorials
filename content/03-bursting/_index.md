---
title: "Cloud Bursting"
date: 2021-12-01T09:00:00Z
draft: false
weight: 30
pre: "<b>III ⁃ </b>"
tags: ["HPC", "Introduction", "LSF", "Optional"]
---

IBM Cloud offers virtual private clouds (VPCs), which enable elastic provisioning of x86 virtual servers with the pay-as-you-go model. Users can optimize both the cost and performance of their target workloads by maintaining the number of virtual servers and their resource configurations. VPCs support various instance flavors of virtual machines with from 1 to 128 virtual CPUs, from 4GB to 1TB RAM, and various capacity and performance settings of storage and networking.

In this post, we focus on an automation package for Spectrum LSF.

[IBM Spectrum LSF](https://www.ibm.com/cloud/blog/announcements/ibm-spectrum-lsf-is-now-available-on-ibm-cloud) is widely used to manage parallel distributed HPC workloads. The automation package deploys LSF management and compute hosts with a shared filesystem. It also configures auto-scaling of compute hosts with its resource connector for IBM Cloud. The deployed cluster runs the Red Hat Enterprise Linux 7.7 with pre-installed software packages such as OpenMPI.

{{< figure src=/images/bursting/bursting.png alt="bursting" class="img-lg">}}

The automation package enables users to quickly create an HPC cluster on IBM Cloud. Users can import data to the shared filesystem and submit HPC jobs using the bsub command. The automation can be regarded as a quick starter kit for HPC in the cloud, but also it enables cloud bursting, a technique to deal with the temporal insufficiency of computing resources at an on-premise cluster using the elastic cloud infrastructures.

LSF can support **cloud bursting** using multi-cluster setups and job forwarding. With these features, LSF can forward jobs from on-premise to a cloud cluster when the on-premise cannot satisfy requests for computing resources. On the other hand, the cloud cluster can be scaled down to save the costs for compute nodes during idle time.

The automation package also supports the configuration of a virtual private network (VPN) for an LSF cluster. VPN enables secure network connections between on-premise and cloud clusters as if they were directly connected. On top of the VPN connectivity, two LSF clusters can securely share cluster information and forward jobs over the internet.

This tutorial section consists of these parts:
- Reconfiguring an existing LSF cluster on IBM Cloud to emulate an private "on-premise cluster" with its own VPN server
- Set up VPN for the on-premise cluster
- Creating a new LSF cluster on IBM Cloud, which is the target of the workload bursting
- Setting up VPN connection between these two cluster
- Setting up multi-cluster and trying cloud bursting with dummy workloads
