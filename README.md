# Deploying Cloudera To Azure Step-By-Step

## Data Lake Store

1. Search for the **Data Lake Store** service and note the locations it is available on.
2. Determine the best option for your deployment.

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

Useful: [Azure ExpressRoute](https://azure.microsoft.com/en-us/services/expressroute/)

## Network Security Groups

1. Create Network Security Group
   * Name: **ClouderaOnAzure-NetworkSecurityGroup-ClouderaManager**
   * Subscription: Same as Resource Group
   * Resource Group: **ClouderaOnAzure**
   * Location: Same as Data Lake Store
2. Create Network Security Group
   * Name: **ClouderaOnAzure-NetworkSecurityGroup-MasterNodes**
   * Subscription: Same as Resource Group
   * Resource Group: **ClouderaOnAzure**
   * Location: Same as Data Lake Store
3. Create Network Security Group
   * Name: **ClouderaOnAzure-NetworkSecurityGroup-EdgeNodes**
   * Subscription: Same as Resource Group
   * Resource Group: **ClouderaOnAzure**
   * Location: Same as Data Lake Store
4. Create Network Security Group
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