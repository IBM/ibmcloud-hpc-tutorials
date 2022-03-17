---
title: "Run LSF Jobs Using Auto-Scaling"
date: 2021-12-08T10:03:30-05:00
draft: false
weight: 70 
tags: ["LSF"] 
---

_worker_node_max_count_ and _worker_node_min_count are the parameters for you to configure auto-scaling.
![lsf arch](/images/setup-lsf-cluster/autoscaling_param.png)
If you leave _worker_node_max_count_ and _worker_node_min_count parameters with default values, you currently would have no worker node in your cluster. 

In the example below, icgen2host-10-242-64-37 is the only node in your LSF cluser and is served as the LSF management node which is configured not to run users' workloads.
```
[lsfadmin@icgen2host-10-242-64-37 ~]$ bhosts -w
HOST_NAME          STATUS          JL/U    MAX  NJOBS    RUN  SSUSP  USUSP    RSV 
icgen2host-10-242-64-37 closed_Full     -      0      0      0      0      0      0
```

In addition, since the  _worker_node_max_count_ parameter is set to 10, you can only submit jobs that use no more than 10 nodes.
Here, we submit a simple sleep job by requesting 2 worker nodes and observe how auto-scaling works behind the scene to dynamically
provision two nodes and add to the existing cluster.

```
[lsfadmin@icgen2host-10-242-64-37 ~]$ bsub -n 2 -R "span[ptile=1]" sleep 10
Job <3> is submitted to default queue <normal>.
```

Wait for few minutes. Run _bhosts_ again and you will see two new worker nodes added to the cluster by auto-scaling. 
```
[lsfadmin@icgen2host-10-242-64-37 ~]$ bhosts -w
HOST_NAME          STATUS          JL/U    MAX  NJOBS    RUN  SSUSP  USUSP    RSV 
icgen2host-10-242-64-37 closed_Full     -      0      0      0      0      0      0
icgen2host-10-242-64-38 ok              -      4      0      0      0      0      0
icgen2host-10-242-64-40 ok              -      4      0      0      0      0      0
```

The submitted job is shown as completed running the sleep command on these two new nodes.
```
[lsfadmin@icgen2host-10-242-64-37 ~]$ bjobs -a -w
JOBID   USER    STAT  QUEUE      FROM_HOST               EXEC_HOST     JOB_NAME   SUBMIT_TIME
3       lsfadmin DONE  normal    icgen2host-10-242-64-37 icgen2host-10-242-64-38:icgen2host-10-242-64-40 sleep 10   Dec  7 11:55
                                                         icgen2host-10-242-64-40
```
After the new nodes have been idling for 10 minutes, auto-scaling will destroy the resources and have those removed from your cluster.

Now let's run a simple MPI job. Go to /home/lsfadmin/shared and this is the directory which is visible to all nodes in your cluster. 
Threfore, you should place your MPI workloads under this directory. OpenMPI 4.1 is already installed under /usr/local/openmpi-4.1.0
and you can include its bin/ directory to your PATH environment variable.

```
lsfadmin@icgen2host-10-242-64-37:~>cd shared/
lsfadmin@icgen2host-10-242-64-37:~/shared>pwd
/home/lsfadmin/shared

lsfadmin@icgen2host-10-242-64-37:~/shared> export PATH=/usr/local/openmpi-4.1.0/bin:$PATH
lsfadmin@icgen2host-10-242-64-37:~/shared>which mpicc
/usr/local/openmpi-4.1.0/bin/mpicc
```
Let's create a MPI program named hello.c and build it using mpicc from OpenMPI 4.1.
```
lsfadmin@icgen2host-10-242-64-37:~/shared>cat > hello.c << EOF
> #include <mpi.h>
> #include <stdio.h>
> #include <stdlib.h>
> #include <unistd.h>
> 
> int main(int argc, char *argv[])
> {
>   int myrank;
>   char hostname[50];
> 
>   gethostname(hostname, 50);
>   printf("Hello from %s (before MPI_Init)...\n", hostname);
> 
>   MPI_Init(&argc, &argv);
>   //sleep(600);
>   MPI_Comm_rank(MPI_COMM_WORLD, &myrank);
>   printf("Hello from %s, rank %d...\n", hostname, myrank);
> 
>   MPI_Finalize();
> 
>   return 0;
> }
> EOF
lsfadmin@icgen2host-10-242-64-37:~/shared>mpicc -g -o hello hello.c
```

We now run a MPI job with 4 MPI tasks on two nodes, each node with 2 MPI ranks.
```
lsfadmin@icgen2host-10-242-64-37:~/shared>bsub -o %J.out -e %J.err -n 4 -R "span[ptile=2]" mpirun ./hello
Job <5> is submitted to default queue <normal>.
```

Check the 5.out file:
```
lsfadmin@icgen2host-10-242-64-37:~/shared>cat 5.out 
Sender: LSF System <lsfadmin@icgen2host-10-242-64-38>
Subject: Job 5: <mpirun ./hello> in cluster <HPCCluster> Done

Job <mpirun ./hello> was submitted from host <icgen2host-10-242-64-37> by user <lsfadmin> in cluster <HPCCluster> at Tue Dec  7 13:43:30 2021
Job was executed on host(s) <2*icgen2host-10-242-64-38>, in queue <normal>, as user <lsfadmin> in cluster <HPCCluster> at Tue Dec  7 13:45:28 2021
                            <2*icgen2host-10-242-64-43>
</home/lsfadmin> was used as the home directory.
</home/lsfadmin/shared> was used as the working directory.
Started at Tue Dec  7 13:45:28 2021
Terminated at Tue Dec  7 13:45:30 2021
Results reported at Tue Dec  7 13:45:30 2021

Your job looked like:

------------------------------------------------------------
# LSBATCH: User input
mpirun ./hello
------------------------------------------------------------

Successfully completed.

Resource usage summary:

    CPU time :                                   0.27 sec.
    Max Memory :                                 -
    Average Memory :                             -
    Total Requested Memory :                     -
    Delta Memory :                               -
    Max Swap :                                   -
    Max Processes :                              -
    Max Threads :                                -
    Run time :                                   4 sec.
    Turnaround time :                            120 sec.

The output (if any) follows:

Hello from icgen2host-10-242-64-38 (before MPI_Init)...
Hello from icgen2host-10-242-64-38 (before MPI_Init)...
Hello from icgen2host-10-242-64-43 (before MPI_Init)...
Hello from icgen2host-10-242-64-43 (before MPI_Init)...
Hello from icgen2host-10-242-64-38, rank 0...
Hello from icgen2host-10-242-64-38, rank 1...
Hello from icgen2host-10-242-64-43, rank 2...
Hello from icgen2host-10-242-64-43, rank 3...


PS:

Read file <5.err> for stderr output of this job.
```

### More information about LSF
You can learn more about LSF [here](https://www.ibm.com/docs/en/spectrum-lsf/10.1.0?topic=best-practices-tips). 

Below is a cheat sheet for useful LSF commands:
![LSF](/images/setup-lsf-cluster/lsf_commands.png)
