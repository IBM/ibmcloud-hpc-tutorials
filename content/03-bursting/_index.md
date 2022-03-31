---
title: "Cloud Bursting"
date: 2021-12-01T09:00:00Z
draft: false
weight: 30
pre: "<b>III ⁃ </b>"
tags: ["Bursting", "Introduction"]
---

<!--IBM Cloud offers virtual private clouds (VPCs), which enable elastic
provisioning of x86 virtual servers with the pay-as-you-go model. Users can
optimize both the cost and performance of their target workloads by maintaining
the number of virtual servers and their resource configurations. VPCs support
various instance flavors of virtual machines with from 1 to 128 virtual CPUs,
from 4GB to 1TB RAM, and various capacity and performance settings of storage
and networking.-->

LSF can support **cloud bursting** using multi-cluster setups and job
forwarding. With these features, LSF can forward jobs from on-premise to a
cloud cluster when the on-premise cannot satisfy requests for computing
resources. On the other hand, the cloud cluster can be scaled down to save the
costs for compute nodes during idle time.

{{< figure src=/images/bursting/bursting.png alt="bursting" class="img-md">}}

The automation package also supports the configuration of a virtual private
network (VPN) for an LSF cluster. VPN enables secure network connections
between on-premise and cloud clusters as if they were directly connected. On
top of the VPN connectivity, two LSF clusters can securely share cluster
information and forward jobs over the internet.

This tutorial section consists of these parts:
- Reconfiguring an existing LSF cluster on IBM Cloud to emulate an private "on-premise cluster" with its own VPN server
- Set up VPN for the on-premise cluster
- Creating a new LSF cluster on IBM Cloud, which is the target of the workload bursting
- Setting up VPN connection between these two cluster
- Setting up multi-cluster and trying cloud bursting with dummy workloads
