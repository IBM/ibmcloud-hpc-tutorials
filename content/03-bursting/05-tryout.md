---
title: "Try cloud bursting"
date: 2022-01-06T01:00:00-01:00
draft: false
weight: 60 
tags: ["burst"] 
---

Now you can forward jobs from on-premises to the cloud. This tutorial tries to request three CPUs by starting three concurrent `sleep 10s` although the example on-premise cluster has only two CPUs. At your on-premises cluster, you can test the following command:

```
$ for i in `seq 1 3`; do bsub -q send_q sleep 10s; done
```

The cloud cluster needs a few minutes to allocate a dynamic host. After a while, you can check if cloud bursting works at `on-premise-master`.

```
$ bjobs -aw
```

```
JOBID   USER    STAT  QUEUE      FROM_HOST   EXEC_HOST   JOB_NAME   SUBMIT_TIME
1       lsfadmin DONE  send_q     on-premise-master on-premise-worker-0 sleep 10s  Oct  7 01:02
2       lsfadmin DONE  send_q     on-premise-master on-premise-worker-1 sleep 10s  Oct  7 01:02
3       lsfadmin DONE  send_q     on-premise-master icgen2host-10-248-0-38@HPCCluster sleep 10s  Oct  7 01:02
```

You can also see that the job comes to a node at `HPCCluster`.

```
$ bjobs -aw
```

```
JOBID   USER    STAT  QUEUE      FROM_HOST   EXEC_HOST   JOB_NAME   SUBMIT_TIME
101     lsfadmin DONE  recv_q     on-premise-master@OnPremiseCluster:3 icgen2host-10-248-0-38 sleep 10s  Oct  7 01:02
```

You can see icgen2host-10-248-0-38 has been created. If you do not start any jobs for a while, the host is automatically released (marked as `closed_RC`).

```
$ bhosts -aw
```

```
HOST_NAME          STATUS          JL/U    MAX  NJOBS    RUN  SSUSP  USUSP    RSV 
icgen2host-10-248-0-37 closed_Full     -      0      0      0      0      0      0
icgen2host-10-248-0-38 closed_RC       -      1      0      0      0      0      0
```

