---
title: "Emulate an on-premise cluster"
date: 2022-01-02T01:00:00-01:00
draft: false
weight: 10 
tags: ["burst"] 
---

The first step is to create an LSF cluster in IBM Cloud US-South (Dallas)
region, which is to be used as an emulation of a "on-premise" cluster.

If you have not created an LSF cluster in **us-south**, you can repeat the steps in 
[Standing up and LSF Cluster](/02-setup-lsf-cluster/). For simplicity,
please use the following setup:
* management_node_count=1
* worker_node_min_count=2
* worker_node_max_count=2

Remember, if you want to start over, always do a __Destroy resources__ from the
Workspace interface first, to make sure no zombie resources are left behind.  A
common issue after re-creating a cluster on the same region is that the IP
ranges are often reused, so SSH will complain about dirty keys.  Please follow
the SSH's prompts to resolve the issue.

We can login and check the cluster's name once it is created:
```shell/bash
[lsfadmin@icgen2host-10-240-128-21 ~]$ lsclusters
CLUSTER_NAME   STATUS   MASTER_HOST               ADMIN    HOSTS  SERVERS
HPCCluster     ok       icgen2host-10-240-     lsfadmin        3        3
```
Note that the current LSF offering on IBM Cloud always creates a cluster
named "HPCCluster". For a multi-cluster setup, this would create confusion
among clusters, so we need to rename the default cluster name "HPCCluster"
to something else ("OnPremCluster").
```shell/bash
[lsfadmin@icgen2host-10-240-128-21 ~]$ lsfshutdown
Shutting down all server batch daemons ...

Shut down server batch daemon on all the hosts? [y/n] y
Shut down server batch daemon on <icgen2host-10-240-128-21> ...... done
Shut down server batch daemon on <icgen2host-10-240-128-22> ...... done
Shut down server batch daemon on <icgen2host-10-240-128-23> ...... done
Shutting down all RESes ...
Do you really want to shut down RES on all hosts? [y/n] y
Shut down RES on <icgen2host-10-240-128-21> ...... done
Shut down RES on <icgen2host-10-240-128-22> ...... done
Shut down RES on <icgen2host-10-240-128-23> ...... done
Shutting down all LIMs ...
Do you really want to shut down LIMs on all hosts? [y/n] y
Shut down LIM on <icgen2host-10-240-128-21> ...... done
Shut down LIM on <icgen2host-10-240-128-22> ...... done
Shut down LIM on <icgen2host-10-240-128-23> ...... done
```
This shuts down all the LSF daemons throughout the cluster.

Now we can safely rename the cluster:
```shell/bash
mv /opt/ibm/lsf/conf/lsf.datamanager.HPCCluster /opt/ibm/lsf/conf/lsf.datamanager.OnPremCluster
mv /opt/ibm/lsf/conf/lsf.cluster.HPCCluster /opt/ibm/lsf/conf/lsf.cluster.OnPremCluster
mv /opt/ibm/lsf/conf/lsbatch/HPCCluster /opt/ibm/lsf/conf/lsbatch/OnPremCluster
mv /opt/ibm/lsf/conf/ego/HPCCluster /opt/ibm/lsf/conf/ego/OnPremCluster
mv /opt/ibm/lsf/work/HPCCluster /opt/ibm/lsf/work/OnPremCluster
mv /opt/ibm/lsf/work/OnPremCluster/live_confdir/lsbatch/HPCCluster /opt/ibm/lsf/work/OnPremCluster/live_confdir/lsbatch/OnPremCluster
for fn in $(grep -r -e HPCCluster /opt/ibm/lsf/conf -lr | xargs ); do sed -i 's/HPCCluster/OnPremCluster/g' $fn; done
find /opt/ibm/lsf/work/ /opt/ibm/lsf/log/ -type f -delete
```

Finally, restart the LSF daemons. On the management node:
```shell/bash
[lsfadmin@icgen2host-10-240-128-21 ~]$ sudo lsf_daemons start
Starting the LSF subsystem
```

From your local computer:
```shell/bash
$ ssh -J root@52.118.83.233 root@10.240.128.22 lsf_daemons start
Starting the LSF subsystem

$ ssh -J root@52.118.83.233 root@10.240.128.23 lsf_daemons start
Starting the LSF subsystem
```

From the management node, we can confirm the cluster has been renamed and operational:
```shell/bash
[lsfadmin@icgen2host-10-240-128-21 ~]$ lsclusters
CLUSTER_NAME   STATUS   MASTER_HOST               ADMIN    HOSTS  SERVERS
OnPremCluster  ok       icgen2host-10-240-     lsfadmin        3        3

[lsfadmin@icgen2host-10-240-128-21 ~]$ bhosts
HOST_NAME          STATUS       JL/U    MAX  NJOBS    RUN  SSUSP  USUSP    RSV
icgen2host-10-240- closed          -      0      0      0      0      0      0
icgen2host-10-240- ok              -      4      0      0      0      0      0
icgen2host-10-240- ok              -      4      0      0      0      0      0
```


