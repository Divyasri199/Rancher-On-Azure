# Exercise 2: Install SUSE Rancher on Microsoft Azure Instance

Duration: 30 minutes

## Overview

An Azure Virtual Network (VNet) is a network or environment that can be used to run VMs and applications in the cloud. When it is created, the services and Virtual Machines within the Azure network interact securely with each other, the internet, and on-premises networks. You can find more references about the virtual network from here: ```https://docs.microsoft.com/en-us/azure/virtual-network/```

A subnet is a range of IP addresses in the virtual network. You can divide a virtual network into multiple subnets for organization and security.

Rancher is an open source software platform that enables organizations to run and manage Docker and Kubernetes in production. With Rancher, organizations no longer have to build a container services platform from scratch using a distinct set of open source technologies. Rancher supplies the entire software stack needed to manage containers in production. You can find more references about the Rancher server from here: ```https://rancher.com/docs/rancher/v1.2/en/```

In this exercise, you will create a Virtual Network with a subnet, Linux Virtual Machine with an image reference of SUSE:opensuse-leap-15-3:gen1:2021.10.12 and also you will install a Rancher Server on Linux VM.

## Task 1: Create a Linux Instance on Azure

In this task, let's create a linux instance on Azure to run SUSE Rancher.



### Task 1.1: Create a virtual network

In this task, you will create a virtual network with a subnet using Bash commands from Cloud Shell.

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
   
### Task 1.2: Create a Linux virtual machine using OpenSUSE Leap 15.3

In this task, you will create a Linux Virtual machine using Bash Command.

1. Copy and paste the below given Bash command to create an OpenSUSE Leap 15.3 Linux virtual machine instance attached to the rancher-subnet network.

   ```bash
   az vm create --resource-group Rancher \
     --name rancher \
     --admin-username suse \
     --image SUSE:opensuse-leap-15-3:gen1:2021.10.12 \
     --size Standard_B4ms \
     --generate-ssh-keys \
     --public-ip-sku Basic \
     --vnet-name mylab-vnet \
     --subnet rancher-subnet \
     --os-disk-size-gb 50 \
     --verbose 
   ```
1. It takes a 2-3 minutes to create this VM and supporting resources. 
 
1. The following example output shows the VM create operation was successful.

```
{
  "fqdns": "",
  "id": "/subscriptions/25283eec-b18f-4965-936c-7493761298c5/resourceGroups/Rancher/providers/Microsoft.Compute/virtualMachines/rancher",
  "location": "southeastasia",
  "macAddress": "00-0D-3A-A0-DA-67",
  "powerState": "VM running",
  "privateIpAddress": "10.0.0.4",
  "publicIpAddress": "20.212.112.32",
  "resourceGroup": "Rancher",
  "zones": ""
}
Command ran in 92.491 seconds (init: 0.115, invoke: 92.376)
```

### Task 1.3: Open port 443 for web traffic

1. By default, only SSH connections are opened when you create a Linux VM in Azure.  

1. Copy and paste the below given Bash command to configure the created Linux VM open to the TCP port 443 (https protocol) for the later use with Rancher Server instance:

```bash
az vm open-port --port 443 --resource-group rancher --name rancher
```

### Task 1.4: Connect to virtual machine

1. SSH to your VM as usual. Use the public IP address of your VM as noted in the previous output in your ssh command. Alternatively, use the script below to obtain the VM IP address and ssh into it.

```bash
export RANCHER_IP=$(az vm show -d -g Rancher -n rancher --query publicIps -o tsv)
ssh -o StrictHostKeyChecking=no suse@$RANCHER_IP
```

2. A new command prompt `suse@rancher:~>` should be shown as example below, indicating you have connected to the virtual machine successfully.

```
openSUSE Leap 15.3 x86_64 (64-bit)

If you are using extensions consider to enable the auto-update feature
of the extension agent and restarting the service. As root execute:

  - sed -i s/AutoUpdate.Enabled=n/AutoUpdate.Enabled=y/ /etc/waagent.conf
  - rcwaagent restart

As "root" use the:

- zypper command for package management
- yast command for configuration management

Have a lot of fun...
suse@rancher:~>
```


## Task 2: Install Rancher Server on Azure VM

In this task, you are going to run the scripts provided in the `rancher` virtual machine terminal. SSH into this VM as instructed in Task 1 if you have not done yet.

### Install software required for running script

Copy and paste the below given bash command to install the software required to execute the Rancher installation script.

```bash
sudo zypper install -y git jq
```
It will take 2-3 min to install the software.

### Download Rancher installation script

Copy and paste the below given bash command to download  the Rancher installation script.

```bash
git clone https://github.com/dsohk/rancher-on-azure-workshop/
cd rancher-on-azure-workshop/scripts
```

### Install Rancher Server

1. Copy and paste the below given bash command to install the **Rancher Server**

   ```bash
     ./install-rancher.sh
   ```
1. This script will **Install Kubernetes tools (kubectl and helm)** ,**Deploy Rancher and Install on RKE2 cluster**.

1. In about 5-10 minutes, your Rancher Server should be ready. If you see the example output shown below, this means you have successfully deployed Rancher Server on the virtual machine. Note down the Rancher URL and initial bootstrap password.

   ```
     ---------------------------------------------------------
     Your Rancher Server is ready.

     Your Rancher Server URL: https://rancher.52.187.36.166.sslip.io
     Bootstrap Password: xk5rxg9grjrf9522752t4rqd84b46krhg86mwgvtvbzsdw49bjlzmb
     ---------------------------------------------------------
   ```
1. Open a browser and navigate to the Rancher Server URL.

1. If you get a Privacy error exception, click on **Advanced**

   ![advanced](../main/Images/advanced.png)
   
1. Now click on the **Unsafe** URL.

   ![unsafe](../main/Images/unsafe.png)
   
1. On the **Welcome to Rancher** page enter your **Bootstrap password (1)** and click on **Login with Local User (2)**.

    ![login](../main/Images/login.png)
    
1. To reset the password, follow the below instructions:

    - Click on **Set a specific password to use (1)**

    - New Password : Enter **Password.1!! (2)**

    - Confirm New Password : Enter **Password.1!! (3)**

    - Check the box next to **I agree to the terms and conditions for using Rancher (4)**

    - Click on **Continue (5)**

       ![reset](../main/Images/reset%20password.png)
       
1. You can then land on the **Home** page of Rancher Server.

    ![rancher home](../main/Images/rancher%20home.png)
    
    
### Next steps

In this exercise, you deployed Rancher Server instance. In the next exercise, you will configure Rancher Server to create a few VMs on Azure and automate provisioning of a Kubernetes cluster, which integrates with Azure Load Balancer, on these VMs.

   
