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

   ![create azure1](../main/Images/create%20azure1.png)

1. On **Cluster : Create Azure** page, enter the below information:

    - Cluster name: **rke2 (1)**
    - Setup 2 Machine Pools
    - Pool Name: **master (2)**
     - Machine Count: **1** **(3)**
     - Roles: **etcd, Control Plane (4)**
     - Location: **This should be same as the region of your Rancher resource group (5)**
     - Resource Group: **Rancher (6)**
     - Availability Set: **rke2-master-as (7)**
     - Image: **SUSE:opensuse-leap-15-3:gen1:2021.10.12 (8)**
     - VM Size: **Standard_A4_v2 (9)**
     - Click on **Show Advanced (10)**

       ![master cluster](../main/Images/master%20cluster.png)
       
       - Subnet: **rke2-master-subnet**
       - Subnet Prefix: **10.0.1.0/24**
       - Virtual Network: **mylab-vnet**
       - Public IP Options: **No Public IP**
       - Network Security Group: **rke2-master-nsg**

       ![show advance](../main/Images/show%20advance.png)
    - Now click on **+** to add one more machine pool.

      ![add cluster](../main/Images/add%20cluster.png)
      
    - Pool Name: **worker (1)**
     - Machine Count: **1 (2)**
     - Roles: **Worker (3)**
     - Location: **This should be same as the region of your Rancher resource group (4)**
     - Resource Group: **Rancher (5)**
     - Availability Set: **rke2-worker-as (6)**
     - Image: **SUSE:opensuse-leap-15-3:gen1:2021.10.12 (7)**
     - VM Size: **Standard_A4_v2 (8)**
     - Click on **Show Advanced (9)**

         ![worker](../main/Images/worker.png)
         
       - Subnet: **rke2-worker-subnet (1)**
       - Subnet Prefix: **10.0.2.0/24 (2)**
       - Virtual Network: **mylab-vnet (3)**
       - Public IP Options: **No Public IP (4)**
       - Network Security Group: **rke2-worker-nsg (5)**

          ![worker1](../main/Images/worker1.png)
          
   - Scroll down to the **Cluster Configuration** section, under the  **Basics** section, choose **Cloud Provider** as **Azure**. Under **Cloud Provider Config** paste the below given code.

    
   ```
   {
       "cloud": "AzurePublicCloud",
       "tenantId": "my-tenant-id",
       "aadClientId": "my-client-id",
       "aadClientSecret": "my-secret",
       "subscriptionId": "my-subscription-id",
       "resourceGroup": "Rancher",
       "location": "This should be same as the region of your AVD-RG resource group",
       "subnetName": "rke2-worker-subnet",
       "securityGroupName": "rke2-worker-nsg",
       "securityGroupResourceGroup": "Rancher",
       "vnetName": "mylab-vnet",
       "vnetResourceGroup": "Rancher",
       "primaryAvailabilitySetName": "rke2-worker-as",
       "routeTableResourceGroup": "Rancher",
       "cloudProviderBackOff": false,
       "useManagedIdentityExtension": false,
       "useInstanceMetadata": true
   }
   ```
   
   > Note: For details of this configuration, please refer to the [Azure Cloud Provider](https://kubernetes-sigs.github.io/cloud-provider-azure/install/configs/) documentation site.

   ![cluster config](../main/Images/cluster%20config.png)
   
   - Under **Advanced** section, click on **Add** under **Additional Controller Manager Args** and paste the below command

       ```
         --configure-cloud-routes=false
       ```
       
      ![config](../main/Images/config.png)
      
      
   - Click **Create** button to start provisioning.


1. In about 15-20 mins, the RKE2 cluster will then be provisioned and setup. If you click on the cluster name `rke` in the cluster list, you will see 2 VMs are being provisioned by Rancher for building up this cluster.
 
    ![cluster provision](../main/Images/cluster%20provision.png)
