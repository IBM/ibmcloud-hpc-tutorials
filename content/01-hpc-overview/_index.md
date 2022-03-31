---
title: "HPC on Cloud Overview"
date: 2021-12-10:05:54Z
weight: 10
pre: "<b>I ⁃ </b>"
---

High Performance Computing (HPC) in the IBM Cloud can accelerate results by
scaling a vast number of tasks and reduce costs by optimizing the right
technology to meet your specifc goals.  The cloud is always ready whenever you
are ready to scale up.  Whether you need a single CPU core or tens of thousands
cores, a traditional MPI-oriented cluster or fully containerized Openshift
cluster, IBM Cloud has the solution to meet the needs. 

For traditional MPI-centric HPC workloads, the most natural way to utilize
cloud resources is the Virtual Private Cloud (VPC). Essentially, it provides a
set of configurable resources for building a secure cluster on a public cloud.
In its most essential form, a virtual private cloud consists of a number of virtual
machine instances, connected through a software-define network which is isolated
from other VPCs on the same public cloud.  VPCs are a "best of both worlds"
approach to cloud computing. They give customers many of the advantages of
private clouds, while leveraging public cloud resources and savings.

| Component            | Traditional HPC cluster             | VPC cluster                      |
|----------------------|-------------------------------------|----------------------------------|
| Unit of compute node | A bare-metal node, fixed configuration | A virtual machine instance (VSI), flexible |
| Interconnect         | Physical Ethernet, Infiniband, etc. | Software defined network         |

On this website we focus exclusively on IBM Cloud Gen2 VPC. In such context, a
"VPC" resource actually refers to the software-defined network at a specific
datacenter. Unless otherwise indicated, the term "VPC" in this website always
referred to this narrow definition. Each VPC consists of one or multiple
subnets, each of which is mapped to a zone within that datacenter. Once the VPC
is established, VSIs can be created within subnets. Such process is the cloud
analog to installing a physical cluster - putting physical computer
nodes inside a server room and connect them with networks so that they can be
accessed from outside.

While it is possible to build a cluster from basic componenets, there is a
much easier way to deploy a HPC-oriented cluster on IBM Cloud. In fact,
Most of the tutorials on this website are based on the automation package for Spectrum LSF.
[IBM Spectrum LSF](https://www.ibm.com/cloud/blog/announcements/ibm-spectrum-lsf-is-now-available-on-ibm-cloud)
is widely used to manage parallel distributed HPC workloads. The automation
package deploys LSF management and compute hosts with a shared filesystem. It
also configures auto-scaling of compute hosts with its resource connector for
IBM Cloud. The deployed cluster runs the Red Hat Enterprise Linux 7.7 with
pre-installed software packages such as OpenMPI.

The automation package enables users to quickly create an HPC cluster on IBM
Cloud. Users can import data to the shared filesystem and submit HPC jobs using
the bsub command. The automation can be regarded as a quick starter kit for HPC
in the cloud, but also it enables cloud bursting, a technique to deal with the
temporal insufficiency of computing resources at an on-premise cluster using
the elastic cloud infrastructures.
