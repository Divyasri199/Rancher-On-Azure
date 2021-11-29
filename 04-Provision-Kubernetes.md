# Exercise 2: Provision Kubernetes on Microsoft Azure within SUSE Rancher

Duration: 30 minutes


## Overview

In this exercise, you will configure Rancher to automate VM provisioning on Azure and then deploy Kubernetes on these VMs. You are going to provision a RKE2 cluster using Cluster API within SUSE Rancher.


## Task 1: Provision RKE2 on Azure VMs

In this exercise, configure a Rancher and provision a Kubernetes cluster (RKE2) of 2 VMs (one master node and one worker node) on the same Resource Group where the Rancher Server is running on. 


1. On the Rancher home page click the **3-line bar icon (1)** from the top left corner to expand the navigation menu and click **Cluster Management** menu item.

    ![cluster management](../main/Images/cluster%20management.png)
    
1. Choose **Clusters (1)** in the left side menu, click **Create (2)** button on the right to create a new Kubernetes cluster.

    ![create cluster](../main/Images/create%20cluster.png)
    
1. On **Create Cluster** page, toggle the switch to **RKE2/K3s (1)** . Then, choose **Azure (2)** to continue.

    ![azure](../main/Images/azure.png)
    
1. At this point, we need to configure Rancher to be able to automate provisioning on Azure with a proper cloud credential. Fill in the form with your own credential.
   - Subscription ID: Enter your **Subscription ID (1)**
   - Client ID: Enter **Client ID (2)**
   - Client Secret: Enter **Secret Key (3)**
   - Click on **Continue (4)**

   > Note : You can find the above values in **my-azure.txt** flie from desktop

    ![create azure](../main/Images/create%20azure.png)
    
1. You will now be shown a Create Cluster on Azure form. You are going to name the cluster, create 2 machine pools for it (one for master node pool and one for worker nodes pool), and configure Azure cloud provider for this cluster.

   ![create azure1](/main/Images/create%20azure1.png)

1. On **Cluster : Create Azure** page, enter the below information:

  - Cluster name: **rke2**
  - Setup 2 Machine Pools
   - Pool Name: **master**
     - Machine Count: **1**
     - Roles: **etcd, Control Plane**
     - Location: **SouthEastAsia**
     - Resource Group: **Rancher**
     - Availability Set: **rke2-master-as**
     - Image: **SUSE:opensuse-leap-15-3:gen1:2021.10.12**
     - VM Size: **Standard_A4_v2**
     - (Show Advanced)
       - Subnet: **rke2-master-subnet**
       - Subnet Prefix: **10.0.1.0/24**
       - Virtual Network: **mylab-vnet**
       - Public IP Options: **No Public IP**
       - Network Security Group: **rke2-master-nsg**
   - Pool Name: **worker**
     - Machine Count: **1**
     - Roles: **Worker**
     - Location: **SouthEastAsia**
     - Resource Group: **Rancher**
     - Availability Set: **rke2-worker-as**
     - Image: **SUSE:opensuse-leap-15-3:gen1:2021.10.12**
     - VM Size: **Standard_A4_v2**
     - (Show Advanced)
       - Subnet: **rke2-worker-subnet**
       - Subnet Prefix: **10.0.2.0/24**
       - Virtual Network: **mylab-vnet**
       - Public IP Options: **No Public IP**
       - Network Security Group: **rke2-worker-nsg**
