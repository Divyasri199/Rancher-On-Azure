# Exercise 1: Install SUSE Rancher on Microsoft Azure Instance

Duration: 30 minutes

At this point, we are going to setup an instance of SUSE Rancher Server on Azure.

## Task 1: Create a Linux Instance on Azure

In this task, let's create a linux instance on Azure to run SUSE Rancher.



### Create a virtual network

1. In the **Azure portal**, open the **Azure Cloud Shell** by clicking on the cloud shell icon in the top menu bar. Alternatively, you can open cloud shell by navigating to `https://shell.azure.com`.

    ![cloudshell](../main/Images/cloudshell.png)
    
1. After launching the Azure Cloud Shell, select the **Bash** option.

     ![bash](../main/Images/bash.png)
    
1. Now on **You have no storage mounted** dialog box click on **Show advanced settings**.

    ![advance-settings](/Images/advance-settings.png)
    
1. Enter the below given information to attach the storageaccount:

    - Subscription : Select your **Subscription (1)**.

    - Cloud Shell region : This should be same as the region of your Rancher resource group **(2)**.

    - Resource group : Click on **Use existing (3)** and select **Rancher (4)**.

    - Storage account : Click on **Use existing (5)** and select **storage<inject key="DID" enableCopy="false"/> (6)**.

    - File share : Click on **Use existing (7)** and enter **blob (8)**.

    - Click on **Attach storage (9)**.

      ![attach storage](../main/Images/attach%20storage.png)

1. Copy and paste the below Bash command to create a virtual network named **mylab-vnet** with the a subnet to host Rancher VM instance in this workshop.

   ```bash
    az network vnet create --resource-group Rancher \
  --name mylab-vnet --address-prefix 10.0.0.0/16 \
  --subnet-name rancher-subnet --subnet-prefix 10.0.0.0/24
  ```
