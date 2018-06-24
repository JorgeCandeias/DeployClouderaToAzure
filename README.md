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

1. Open Virtual Network **ClouderaOnAzure-VirtualNetwork**.
2. Open the *Subnets* tab.
3. Add Subnet:
 * Name: **ClouderaOnAzure-SubNet-MasterNodes**
 * Address Space: **10.0.1.0/24**
4: Add Subnet:
 * Name: **ClouderaOnAzure-SubNet-EdgeNodes**
 * Address Space: **10.0.2.0/24**
5: Add Subnet:
 * Name: **ClouderaOnAzure-SubNet-WorkerNodes**
 * Address Space: **10.0.3.0/24**

Useful: [Azure ExpressRoute](https://azure.microsoft.com/en-us/services/expressroute/)

