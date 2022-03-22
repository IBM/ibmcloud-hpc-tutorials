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


