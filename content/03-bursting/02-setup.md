---
title: "Set up multi-cluster and job forwarding"
date: 2022-01-05T01:00:00-01:00
draft: true
weight: 20 
tags: ["burst"] 
---

et up the LSF multi-cluster and job forwarding. This tutorial assumes that the small on-premise cluster labeled with "OnPremiseCluster" uses a subnet `192.168.3.0/24`. The cluster runs a master node at `192.168.3.21` (on-premise-master) and two single-CPU workers (i.e., up to two CPUs are available for jobs). The cloud cluster labeled with "HPCCluster" uses a subnet `10.248.0.32/27` and its master uses `10.248.0.37` (icgen2host-10-248-0-37) without any static workers created. Both of the configuration directories are in `/opt/ibm/lsf/conf`, but you can change the directory depending on your cluster configuration.

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

2. Both clusters need to recognize each other, so you need to modify `/opt/ibm/lsf/conf/lsf.shared`. This configuration file should be identical in both clusters.

    ```
    ...
    Begin Cluster
    ClusterName        Servers                   # Keyword    # modified
    HPCCluster         (icgen2host-10-248-0-37)  # modified
    OnPremiseCluster   (on-premise-master)       # modified
    End Cluster
    ...
    ```

3. The two clusters are configured to have different `lsb.queues` files. In the cloud cluster, you need to append the following lines to `/opt/ibm/lsf/conf/lsbatch/HPCCluster/configdir/lsb.queues` to register a receive queue:

    ```
    ...
    Begin Queue
    QUEUE_NAME=recv_q
    RCVJOBS_FROM=OnPremiseCluster
    PRIORITY=30
    NICE=20
    RC_HOSTS=all
    End Queue
    ```

    The on-premises cluster is configured to have a send queue at `/opt/ibm/lsf/conf/lsbatch/OnPremiseCluster/configdir/lsb.queues`:

    ```
    ...
    Begin Queue
    QUEUE_NAME=send_q
    SNDJOBS_TO=recv_q@allclusters
    PRIORITY=30
    NICE=20
    End Queue
    ```

4. Restart both clusters by running the following command:

    ```
    $ lsfrestart
    ```

5. Check if two clusters are connected

    ```
    $ lsclusters -w
    ```

    ```
    CLUSTER_NAME     STATUS   MASTER_HOST                   ADMIN    HOSTS  SERVERS
    OnPremiseCluster ok       on-premise-master          lsfadmin        3        3
    HPCCluster       ok       icgen2host-10-248-0-37     lsfadmin        1        1
    ```

