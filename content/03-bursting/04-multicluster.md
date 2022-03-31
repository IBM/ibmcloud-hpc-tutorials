---
title: "Set up LSF multi-cluster and job forwarding"
date: 2022-01-06T01:00:00-01:00
draft: false
weight: 40 
tags: ["Cloud bursting"] 
---

We now proceed to set up the LSF multi-cluster and job forwarding.  Both
clusters need to recognize each other, so you need to modify
`/opt/ibm/lsf/conf/lsf.shared`. This configuration file should be identical in
both clusters.

```
...
Begin Cluster
ClusterName Servers
HPCCluster icgen2host-10-241-128-21
OnPremCluster icgen2host-10-240-128-21
End Cluster
...
```

The two clusters are configured to have different `lsb.queues` files. In the
HPCCluster, you need to append the following lines to
`/opt/ibm/lsf/conf/lsbatch/HPCCluster/configdir/lsb.queues` to register a
receive queue:

```
...
Begin Queue
QUEUE_NAME=recv_q
RCVJOBS_FROM=OnPremCluster
PRIORITY=30
NICE=20
RC_HOSTS=all
End Queue
...
```

OnPremCluster is configured to have a send queue at `/opt/ibm/lsf/conf/lsbatch/OnPremiseCluster/configdir/lsb.queues`:

```
...
Begin Queue
QUEUE_NAME=send_q
SNDJOBS_TO=recv_q@allclusters
PRIORITY=30
NICE=20
End Queue
...
```

Restart both clusters by running the following command:

```
$ lsfrestart
```

Check if two clusters are connected

```
[lsfadmin@icgen2host-10-240-128-21 ~]$ lsclusters -w
CLUSTER_NAME   STATUS   MASTER_HOST                     ADMIN    HOSTS  SERVERS
OnPremCluster  ok       icgen2host-10-240-128-21     lsfadmin        3        3
HPCCluster     ok       icgen2host-10-241-128-21     lsfadmin        3        3
```

Now you can forward jobs from OnPremCluster to HPCCluster. If we submit
20 concurrent `sleep 10s` jobs, some of the jobs will be scheduled on HPCCluster:
```
$ for i in `seq 1 20`; do bsub -q send_q sleep 10s; done
```
after a few seconds:
```
[lsfadmin@icgen2host-10-240-128-21 ~]$ bjobs -w
JOBID   USER    STAT  QUEUE      FROM_HOST   EXEC_HOST   JOB_NAME   SUBMIT_TIME
397     lsfadmin RUN   send_q     icgen2host-10-240-128-21 icgen2host-10-240-128-23 sleep 10s  Mar 30 21:25
398     lsfadmin RUN   send_q     icgen2host-10-240-128-21 icgen2host-10-240-128-23 sleep 10s  Mar 30 21:25
399     lsfadmin RUN   send_q     icgen2host-10-240-128-21 icgen2host-10-240-128-23 sleep 10s  Mar 30 21:25
400     lsfadmin RUN   send_q     icgen2host-10-240-128-21 icgen2host-10-240-128-23 sleep 10s  Mar 30 21:25
401     lsfadmin RUN   send_q     icgen2host-10-240-128-21 icgen2host-10-240-128-22 sleep 10s  Mar 30 21:25
402     lsfadmin RUN   send_q     icgen2host-10-240-128-21 icgen2host-10-240-128-22 sleep 10s  Mar 30 21:25
403     lsfadmin RUN   send_q     icgen2host-10-240-128-21 icgen2host-10-240-128-22 sleep 10s  Mar 30 21:25
404     lsfadmin RUN   send_q     icgen2host-10-240-128-21 icgen2host-10-240-128-22 sleep 10s  Mar 30 21:25
405     lsfadmin RUN   send_q     icgen2host-10-240-128-21 icgen2host-10-241-128-23@HPCCluster sleep 10s  Mar 30 21:25
406     lsfadmin RUN   send_q     icgen2host-10-240-128-21 icgen2host-10-241-128-23@HPCCluster sleep 10s  Mar 30 21:25
407     lsfadmin RUN   send_q     icgen2host-10-240-128-21 icgen2host-10-241-128-23@HPCCluster sleep 10s  Mar 30 21:25
408     lsfadmin RUN   send_q     icgen2host-10-240-128-21 icgen2host-10-241-128-23@HPCCluster sleep 10s  Mar 30 21:25
409     lsfadmin RUN   send_q     icgen2host-10-240-128-21 icgen2host-10-241-128-22@HPCCluster sleep 10s  Mar 30 21:25
410     lsfadmin RUN   send_q     icgen2host-10-240-128-21 icgen2host-10-241-128-22@HPCCluster sleep 10s  Mar 30 21:25
411     lsfadmin RUN   send_q     icgen2host-10-240-128-21 icgen2host-10-241-128-22@HPCCluster sleep 10s  Mar 30 21:25
412     lsfadmin RUN   send_q     icgen2host-10-240-128-21 icgen2host-10-241-128-22@HPCCluster sleep 10s  Mar 30 21:25
413     lsfadmin PEND  send_q     icgen2host-10-240-128-21    -        sleep 10s  Mar 30 21:25
414     lsfadmin PEND  send_q     icgen2host-10-240-128-21    -        sleep 10s  Mar 30 21:25
415     lsfadmin PEND  send_q     icgen2host-10-240-128-21    -        sleep 10s  Mar 30 21:25
416     lsfadmin PEND  send_q     icgen2host-10-240-128-21    -        sleep 10s  Mar 30 21:25
```

