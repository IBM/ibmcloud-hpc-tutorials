---
title: "Customize Software on the VSI"
date: 2022-01-24T15:55:41-05:00
weight: 20
draft: true
---

From your local machine, login to the newly created VSI by running the following command (Note: After the VSI is in running state you might still need to wait a few minutes before you can connect to it via ssh
):

```
ssh -J root@<jump-node-ip>  root@<new-instance-ip>
```

(Explain how to get the jump node IP)

Now customize the software on the VSI boot volume.