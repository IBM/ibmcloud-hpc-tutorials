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

The next step is to establish the VPN connection from OnPremCluster VPN. "Local"
corresponds to OnPremCluster and "Peer" corresponds to HPCCluster, as shown below

{{< figure src="/images/bursting/vpn-conn.png" alt="vpn-conn" >}}


{{< figure src="/images/bursting/vpn-conn-active.png" alt="vpn-conn" >}}

Typical VPN configurations require a public IP address for the local VPN server, a local CIDR, a preshared key, a peer IP address, and a peer CIDR. In the example above, you first need to configure your local VPN server with public IP address, local CIDR, and a preshared key, which are identical to what you specified for `vpn_peer_address`, `vpn_peer_cidr`, and `vpn_preshared_key` at Step 1, respectively. Then, your local VPN configuration needs to add a peer IP address to be `163.68.aaa.bbb` and a peer CIDR to be `10.248.0.32/27` according to the output of Step 2. Finally, UDP ports 500 and 4500 must be accessible from the VPN gateway on IBM Cloud by configuring your local network devices (e.g., routers). For more details on configuring your VPN, see [Connecting to your on-premises network](/docs/vpc?topic=vpc-vpn-onprem-example).

1. The following is an example of the `/etc/hosts` file for the cloud cluster. You need to make sure that the hostnames for the LSF masters are DNS-resolvable.

    ```
    ...
    10.248.0.61 icgen2host-10-248-0-61
    10.248.0.62 icgen2host-10-248-0-62
    10.248.0.63 icgen2host-10-248-0-63

    192.168.3.21 on-premise-master   # added
    ```

    For the on-premises `/etc/hosts` file, make sure to add the information about the master node in the cloud cluster:

    ```
    192.168.3.21 on-premise-master
    192.168.3.22 on-premise-worker-0
    192.168.3.23 on-premise-worker-1

    10.248.0.37 icgen2host-10-248-0-37 #added
    ```

