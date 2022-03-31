---
title: "Create a second cluster and establish VPN connection"
date: 2022-01-05T01:00:00-01:00
draft: false
weight: 30 
tags: ["burst"] 
---

We can now proceed to create the new cluster ("HPCCluster") as the cloud
bursting target in the US-East region (Washington DC). Using the LSF tile, the
process is mostly identical as before, but with the following specific
parameters:

* Region (all occurances): us-east
* Zone: us-east-3
* vpc_name: leave empty
* vpn_enabled: true
* vpn_peer_address: OnPremCluster's VPN gateway IP
* vpn_peer_cidrs: OnPremCluster's CIDR
* vpn_preshared_key: any string of user's choice, to be used again in VPN connection
* management_node_count=1
* worker_node_min_count=2
* worker_node_max_count=2

Once all the resources are created, take note of the jump host and management
node addresses. Head to the VPN section and find its corresponding VPN's
gateway IP and CIDR. Also we need to make sure HPCCluster's VPN is enabled.

Suppose we end up with the following setup from the two clusters:
|        | OnPremCluster | HPCCluster   |
|-|-|-|
| Management node | icgen2host-10-240-128-21 (10.240.128.21) | icgen2host-10-240-128-21 (10.241.128.21) |
| VPN gateway     | 52.118.79.73  | 150.239.220.255 |
| CIDR   | 10.240.128.0/18 | 10.241.128.0/18 |
| Preshared key | n/a | lfsclustervpn |

<!--The cloud cluster labeled with "HPCCluster" uses a subnet
`10.241.0.32/27` and its master uses `10.248.0.37` (icgen2host-10-248-0-37)
without any static workers created. Both of the configuration directories are
in `/opt/ibm/lsf/conf`, but you can change the directory depending on your
cluster configuration.-->

The next step is to establish the VPN connection from OnPremCluster VPN. As
shown below, "Local" corresponds to OnPremCluster and "Peer" corresponds to
HPCCluster. The same "preshared key" used for creating the HPCCluster's VPN
must be used.

{{< figure src="/images/bursting/vpn-conn.png" alt="vpn-conn" >}}

Upon creation, it take about a minute before the VPN connection becomes active, as shown below:

{{< figure src="/images/bursting/vpn-conn-active.png" alt="vpn-conn" >}}

Unlike HPCCluster's subnet, OnPremCluster's subnet is not created without
an attached VPN. Its UDP ports 500 and 4500 must be manually made
accessible from outside. This is done by creating an open inbound rule in 
OnPremCluster's attached security group, as shown below:

{{< figure src="/images/bursting/onpremvpn-rule.png" alt="onpremvpn-rule" >}}

We also need to modify `/etc/hosts` on the management node on both cluster, so that the hostnames for the LSF masters are DNS-resolvable.
On OnPremCluster:
```
...
10.240.128.21 icgen2host-10-240-128-21
...
10.241.128.21 icgen2host-10-241-128-21  # added
```

On HPCCluster:
```
...
10.241.128.21 icgen2host-10-241-128-21
...
10.240.128.21 icgen2host-10-240-128-21  # added
```

To confirm VPN connection from OnPremCluster:
```
[lsfadmin@icgen2host-10-240-128-21 ~]$ ping icgen2host-10-241-128-21
PING icgen2host-10-241-128-21 (10.241.128.21) 56(84) bytes of data.
64 bytes from icgen2host-10-241-128-21 (10.241.128.21): icmp_seq=1 ttl=62 time=33.1 ms
...
```
