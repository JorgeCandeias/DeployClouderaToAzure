# Deploying Cloudera To Azure Step-By-Step

## Data Lake Store

1. Lookup **Data Lake Store** on [Azure Products by Region](https://azure.microsoft.com/en-us/global-infrastructure/services/) and note location availability.
2. Determine the best location for your deployment. All primary resources will reside in the same location for optimal performance.

## Resource Group

Create Resource Group **ClouderaOnAzure**:

* Subscription: Of choice.
* Location: Same as Data Lake Store.

## Virtual Network

Create Virtual Network **ClouderaOnAzure-VirtualNetwork**:

* Address Space: **10.0.0.0/16**
* Subscription: Same as the Resource Group.
* Resource Group: **ClouderaOnAzure**
* Location: Same as Data Lake Store.
* Subnet Name: **ClouderaOnAzure-SubNet-ClouderaManager**
* Subnet Address Range: **10.0.0.0/24**

## Subnets

1. Open Virtual Network **ClouderaOnAzure-VirtualNetwork**
2. Open tab *Subnets*
3. Add Subnet:
   * Name: **ClouderaOnAzure-SubNet-MasterNodes**
   * Address Space: **10.0.1.0/24**
4. Add Subnet:
   * Name: **ClouderaOnAzure-SubNet-EdgeNodes**
   * Address Space: **10.0.2.0/24**
5. Add Subnet:
   * Name: **ClouderaOnAzure-SubNet-WorkerNodes**
   * Address Space: **10.0.3.0/24**

Good-To-Know: [Azure ExpressRoute](https://azure.microsoft.com/en-us/services/expressroute/)

## Network Security Groups

### For Cloudera Manager

Create Network Security Group:

   * Name: **ClouderaOnAzure-NetworkSecurityGroup-ClouderaManager**
   * Subscription: Same as Resource Group
   * Resource Group: **ClouderaOnAzure**
   * Location: Same as Data Lake Store

### For Master Nodes

Create Network Security Group:

   * Name: **ClouderaOnAzure-NetworkSecurityGroup-MasterNodes**
   * Subscription: Same as Resource Group
   * Resource Group: **ClouderaOnAzure**
   * Location: Same as Data Lake Store

### For Edge Nodes

Create Network Security Group:

   * Name: **ClouderaOnAzure-NetworkSecurityGroup-EdgeNodes**
   * Subscription: Same as Resource Group
   * Resource Group: **ClouderaOnAzure**
   * Location: Same as Data Lake Store

### For Worker Nodes

Create Network Security Group:

   * Name: **ClouderaOnAzure-NetworkSecurityGroup-WorkerNodes**
   * Subscription: Same as Resource Group
   * Resource Group: **ClouderaOnAzure**
   * Location: Same as Data Lake Store

## SSH Inbound Security Rules

> Attention: The steps below will expose SSH port 22 to the internet.
> Therefore, only use this for testing temporary environments.
> For production environments, use a VPN or private connection instead.

### For Cloudera Manager Nodes

1. Open Network Security Group **ClouderaOnAzure-NetworkSecurityGroup-ClouderaManager**
1. Open tab *Inbound Security Rules*.
1. Add Rule:
   * Source: **Any**
   * Source Port Ranges: **\***
   * Destination: **Any**
   * Destination Port Ranges: **22**
   * Protocol: **Any**
   * Action: **Allow**
   * Priority: **101**
   * Name: **Inbound-SSH-22**

### For Master Nodes

1. Open Network Security Group **ClouderaOnAzure-NetworkSecurityGroup-MasterNodes**
1. Open tab *Inbound Security Rules*.
1. Add Rule:
   * Source: **Any**
   * Source Port Ranges: **\***
   * Destination: **Any**
   * Destination Port Ranges: **22**
   * Protocol: **Any**
   * Action: **Allow**
   * Priority: **101**
   * Name: **Inbound-SSH-22**

### For Edge Nodes

1. Open Network Security Group **ClouderaOnAzure-NetworkSecurityGroup-EdgeNodes**
1. Open tab *Inbound Security Rules*.
1. Add Rule:
   * Source: **Any**
   * Source Port Ranges: **\***
   * Destination: **Any**
   * Destination Port Ranges: **22**
   * Protocol: **Any**
   * Action: **Allow**
   * Priority: **101**
   * Name: **Inbound-SSH-22**

### For Worker Nodes

1. Open Network Security Group **ClouderaOnAzure-NetworkSecurityGroup-WorkerNodes**
1. Open tab *Inbound Security Rules*.
1. Add Rule:
   * Source: **Any**
   * Source Port Ranges: **\***
   * Destination: **Any**
   * Destination Port Ranges: **22**
   * Protocol: **Any**
   * Action: **Allow**
   * Priority: **101**
   * Name: **Inbound-SSH-22**

## Associate Network Security Group With Subnet

1. Open Virtual Network **ClouderaOnAzure-VirtualNetwork**
1. Open tab *Subnets*

### For Cloudera Manager Nodes

1. Open Subnet **ClouderaOnAzure-SubNet-ClouderaManager**
1. Open *Network Security Group*
1. Select Network Security Group **ClouderaOnAzure-NetworkSecurityGroup-ClouderaManager**
1. Save & Close Blade

### For Master Nodes

1. Open Subnet **ClouderaOnAzure-SubNet-MasterNodes**
1. Open *Network Security Group*
1. Select Network Security Group **ClouderaOnAzure-NetworkSecurityGroup-MasterNodes**
1. Save & Close Blade

### For Edge Nodes

1. Open Subnet **ClouderaOnAzure-SubNet-EdgeNodes**
1. Open *Network Security Group*
1. Select Network Security Group **ClouderaOnAzure-NetworkSecurityGroup-EdgeNodes**
1. Save & Close Blade

### For Edge Nodes

1. Open Subnet **ClouderaOnAzure-SubNet-WorkerNodes**
1. Open *Network Security Group*
1. Select Network Security Group **ClouderaOnAzure-NetworkSecurityGroup-WorkerNodes**
1. Save & Close Blade

## Availability Sets

> Microsoft and Cloudera recomendations for High Availability:
> * Place machines with the same Role in the same Availability Set.
> * Use a distinct Availability Set per Role.
> * If more than 3 VMs in a single Availability Group then associate each VM
with Azure assigned Fault Domain or Update Domain.

### For Cloudera Manager Nodes

Create Availability Set:

* Name: **ClouderaOnAzure-AvailabilitySet-ClouderaManager**
* Subscription: Same as Resource Group
* Resource Group: **ClouderaOnAzure**
* Location: Same as Data Lake Store
* Fault Domains: **Max (3)**
* Update Domains: **Max (20)**
* Use Managed Disks: **Yes**

### For Master Nodes

Create Availability Set:

* Name: **ClouderaOnAzure-AvailabilitySet-MasterNodes**
* Subscription: Same as Resource Group
* Resource Group: **ClouderaOnAzure**
* Location: Same as Data Lake Store
* Fault Domains: **Max (3)**
* Update Domains: **Max (20)**
* Use Managed Disks: **Yes**

### For Edge Nodes

Create Availability Set:

* Name: **ClouderaOnAzure-AvailabilitySet-EdgeNodes**
* Subscription: Same as Resource Group
* Resource Group: **ClouderaOnAzure**
* Location: Same as Data Lake Store
* Fault Domains: **Max (3)**
* Update Domains: **Max (20)**
* Use Managed Disks: **Yes**

### For Worker Nodes

Create Availability Set:

* Name: **ClouderaOnAzure-AvailabilitySet-WorkerNodes**
* Subscription: Same as Resource Group
* Resource Group: **ClouderaOnAzure**
* Location: Same as Data Lake Store
* Fault Domains: **Max (3)**
* Update Domains: **Max (20)**
* Use Managed Disks: **Yes**

## Virtual Machines

### Notes On VM Sizes

General Purpose:
> These VMs have a balanced CPU-to-Memory ratio.
> Use for general workloads such as the Cloudera Manager and Master nodes.
* B, Dsv3, Dv3, DSv2, Av2
* Details @ [Microsoft](https://docs.microsoft.com/en-us/azure/virtual-machines/windows/sizes-general)

For Compute:
> These VMs have a high CPU-to-Memory ratio.
> Use for compute heavy roles such as Machine Learning.
* Fsv2, Fs, F
* Details @ [Microsoft](https://docs.microsoft.com/en-us/azure/virtual-machines/windows/sizes-compute)

For Memory:
> These VMs have a high Memory-to-CPU ratio.
> Use for batch processing roles with high memory needs.
* Esv3, Ev3, M, GS, G, DSv2, Dv2
* Details @ [Microsoft](https://docs.microsoft.com/en-us/azure/virtual-machines/windows/sizes-memory)

For Storage:
> These VMs offer high disk throughput.
> Use for IO bound roles such as databases and importing and exporting files.
* LS
* Details @ [Microsoft](https://docs.microsoft.com/en-us/azure/virtual-machines/windows/sizes-storage)

For GPU Use:
> These series boast powerful NVIDIA discrete GPUs.
> Use for specialized workloads such as GPU-optimized machine learning.
* NC, NCv2, NCv3, ND, NV
* Details @ [Microsoft](https://docs.microsoft.com/en-us/azure/virtual-machines/windows/sizes-gpu)

For High-Performance Compute:
> These series boast the most powerful CPU, Memory, Disk and Network specifications.
> Use for high-end scientific workloads such as molecular modeling and fluid dynamics.
* H
* Details @ [Microsoft](https://docs.microsoft.com/en-us/azure/virtual-machines/windows/sizes-hpc)

### Create DNS Entry-Point Virtual Machine

Notes:

* [Cloudera Supported Operating Systems](https://www.cloudera.com/documentation/enterprise/release-notes/topics/rn_consolidated_pcm.html#cdh_cm_supported_os)
* [How to create an SSH public-private key pair on Linux or MacOS](https://docs.microsoft.com/en-us/azure/virtual-machines/linux/mac-create-ssh-keys)
* [How to create an SSH public-private key pair on Windows](https://docs.microsoft.com/en-us/azure/virtual-machines/linux/ssh-from-windows)

Create Virtual Machine:

* Type: **Cloudera CentOS 7.4** (at time of writing - choose a newer one if available)
* Basics:
  * Name: **Cloudera-DNS**
  * Username: **cloudera**
  * Authentication Type: **SSH Public Key**
  * SSH Public Key: *Your Public Key*
  * Subscription: *Same as Resource Group*
  * Resource Group: **ClouderaOnAzure**
* Size: **DS1_v2 Standard**

## Azure Data Lake Store

Create Data Lake Store:
* Name: **clouderadls123**
  * Note: The name must be unique across Azure. The above name is only for guide purposes. You will have to choose your own unique name.
* Subscription: *Same as Resource Group*
* Resource Group: **ClouderaOnAzure**
* Location: *As chosen in the beginning of this guide*
* Pricing Package:
  * Use **Pay-as-you-go** for testing deployments when you don't yet know resource requirements. Probably as you are doing right now.
  * Use **Monthly commitment** for cost savings when you know resource requirements up-front.
* Encryption Settings: **Enabled** (Default)