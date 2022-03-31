---
title: "Set up VPN for the on-premise cluster"
date: 2022-01-02T01:00:00-01:00
draft: false
weight: 20 
tags: ["burst"] 
---

<!--The automation package is implemented as a Terraform template for VPCs on
IBM Cloud. Users can configure and start their cluster creation with
Schematics, a Terraform platform with Web UI on IBM Cloud. The template is
publicly available at [GitHub for IBM
Cloud](https://github.com/IBM-Cloud/hpc-cluster-lsf). All you need to create a
cloud HPC cluster is to set the GitHub link to a Schematics workspace, edit
pre-defined variables, and click several buttons. This tutorial gives a
step-by-step guide to creating an example VPN deployment.  Before you begin,
make sure to complete the first four steps for [getting started with IBM
Spectrum
LSF](https://cloud.ibm.com/docs/ibm-spectrum-lsf?topic=ibm-spectrum-lsf-getting-started-tutorial).
-->

The following figure is an example of a VPN deployment among the two cluster.

{{< figure src="/images/bursting/hpcc_vpn-diagram.png" alt="vpn" width="800" >}}

In this step, however, we will create a VPN gateway for OnPremCluster, but will
NOT create a VPN connection yet. The two VPN gateways from the two clusters
must know each other's gateway IP address and CIDR. For tutorial's purpose we
will rely on the cloud's control plane to assign new VPN gateway IPs. As a
result, the actual VPN connection must be made after both VPN gateways are
created, to avoid a chicken-and-egg situation.

To find the CIDR, go to the VPC created by LSF automation package ("LSF tile").
Since us-south-3 is used, the corresponding IP range is `10.240.128.0/18`.

{{< figure src=/images/bursting/onprem-vpn-vpc.png alt="onprem-vpc" class="img-sm">}}

Now select **_VPC Infrastructure_** > **_VPNs_**. In the "VPNs for VPC" page, make
sure Dallas is selected as the region, and then click **_Create_**. We need to use
the following setup:
* VPN type: Site-to-site gateway
* Resource group: hpca_resgrp
* Virtual private cloud: the VPC used by the OnPremCluster
* Subnet: this should be automatically selected since there is only one subnet
was created in OnPremCluster's VPC
* Mode: Policy-based
* VPN connection for VPC: deselected

Upon clicking **_Create VPN gateway_**, it takes a moment for the gateway IP to
be allocated and reveal in the VPN page. This is the gateway IP we will need during
the creation of the next cluster.

{{< figure src="/images/bursting/onprem-vpn-gatewayip.png" alt="onprem-vpn-ip" class="img-ty">}}

<!--Also, you need to know the public IP address of your local VPN server, a
local CIDR accessing the VPN environment, and a preshared key to authenticate
your VPN connection. The preshared key can be any random string.-->


