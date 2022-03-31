---
title: "Create Your Cluster using IBM Spectrum LSF"
date: 2021-12-08T10:05:34-05:00
draft: false
weight: 50 
tags: ["LSF"]
---


After you sign in to https://cloud.ibm.com/ with your account, search "HPC" or "Spectrum LSF" in IBM Cloud catalog. Find "IBM Spectrum LSF" and select the service. It will lead you to the HPC Cluster solution page. Or the tile can be directly accessed from [here](https://cloud.ibm.com/catalog/content/terraform-1623200063-71606cab-c6e1-4f95-a47a-2ce541dcbed8-global).

{{< figure src="/images/setup-lsf-cluster/hpc_catalog.png" alt="HPC Cluster Catalog" class="img-ty">}}

Scroll down to the **Configure your workspace** section.
<!--![catalog](/images/setup-lsf-cluster/config-ws.png)-->
{{< figure src="/images/setup-lsf-cluster/config-ws.png" alt="catalog" class="img-md">}}
The resource group parameter in this section is where the Schematics workspace is provisioned on your IBM Cloud account. 
The value for this parameter can be different than the one used for SSH key.
You can leave the other fields with the default.

Scroll down to the **Set the deployment values** section. You will need to fill out three fields in this **_Parameters without default values_** section.
![catalog](/images/setup-lsf-cluster/hpc_required_values.png)

You can toggle _Use an existing secret_ to _No_ for the _api_key_ parameter and paste your api key saved earlier.
Enter "true" for _lsf_license_confirmatin_ and your ssh_key_name (e.g.: demo-ssh-key).

Continue with the **_Parameters with default values_** section. Though you can leave most of the parameters with default values, you need to
modify a few parameters depending on your cloud account and ssh key.

![catalog](/images/setup-lsf-cluster/hpc_fixed_values.png)

* Please select a cluster_prefix string that would be unique to you (e.g.: use your initial here).
* The resource group value needs to be consistent with the one which you use to create your SSH key.
* The region (may not be required in the future) and zone *need to match where your ssh key is created in the previous step*.
  * A list of supported regions: eu-de,eu-gb,us-east,us-south
  * Each region has 3 zones (e.g.: us-east-1, us-east-2, us-east-3 for the us-east region)
* Please just use _1_ for _management_node_count_.

To learn more about cluster configuration parameters, please refer to this [tutorial](https://cloud.ibm.com/docs/ibm-spectrum-lsf?topic=ibm-spectrum-lsf-using-hpc-cluster) in Step 4.

Now you are ready to create the cluster. In the bottom right corner of the page, select the license agreements button and click **_Install_**. It will lead to your Schematics workspace and will take few minutes for your cluster to be ready.
<!--![catalog](/images/setup-lsf-cluster/lsf_install.png)-->
{{< figure src="/images/setup-lsf-cluster/lsf_install.png" alt="lsf_install" class="img-ty">}}
