# Deploying Cloudera To Azure Step-By-Step IaaS DIY

## Overview

This step-by-step guide details **one way** to deploy a sample Cloudera CDH reference cluster to Microsoft Azure IaaS using a Do-It-Yourself process.

The purpose of this guide is to assist studying Data Engineers getting their first Azure enterprise cluster up and running fast while gaining awareness of the distinct moving parts in the deployment of the cluster.

It is therefore a mix of *Path A* and *Path B* from the offical installation manuals, with *some* automation along the way, and it is time-consuming.

For enterprise-grade production deployments and management needs, there are far more efficient approaches, such as using [Cloudera Director](https://www.cloudera.com/products/product-components/cloudera-director.html) or [Cloudera Altus](https://www.cloudera.com/products/altus.html) from the start.

General Notes:

* [Supported Configurations with Virtualization and Cloud Platforms](https://www.cloudera.com/documentation/enterprise/release-notes/topics/rn_consolidated_pcm.html#virtual_platforms)
* [Cloudera Enterprise Reference Architecture for Azure Deployments](https://www.cloudera.com/documentation/other/reference-architecture/PDF/cloudera_ref_arch_azure.pdf)
* [Support Limitations for CDH and Cloudera Manager in Microsoft Azure](https://www.cloudera.com/documentation/enterprise/release-notes/topics/rn_consolidated_pcm.html#concept_y5l_43m_bbb)

For Funsies:
* [Cloudera Enterprise Reference Architecture for AWS Deployments](http://www.cloudera.com/content/cloudera/en/documentation/reference-architecture/latest/PDF/cloudera_ref_arch_aws.pdf)
* [Cloudera Enterprise Reference Architecture for Google Cloud Platform Deployments](http://www.cloudera.com/content/www/en-us/documentation/other/reference-architecture/PDF/cloudera_ref_arch_gcp.pdf)
* This guide uses the Cloudera-built Azure CentOS Linux images for the VMs. You can use any supported non-Cloudera Linux distribution instead, however you must bootstrap such machines with the [Azure Bootstrap Scripts](https://github.com/cloudera/director-scripts/tree/master/azure-bootstrap-scripts) to make them Cloudera-compatible.

## ***TODO: Add Azure introduction here***

Todo: Add Azure specific pointers for relevant services.

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

> **Attention**: The steps below will expose SSH port 22 to the internet.
> This is a simple way to access your temporary VMs in the comfort of your home, while studying this guide.
> However, this opens an attack surface on your cluster.
> For long-lived corporate environments, make sure to use a private connection as described in the [Azure Virtual Networks Overview](https://docs.microsoft.com/en-us/azure/virtual-network/virtual-networks-overview#communicate-with-on-premises-resources).

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

### DNS Entry-Point Virtual Machine

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
* Size: General Purpose / MIN 3.5GB RAM
* Settings:
  * Availability Set: **ClouderaOnAzure-AvailabilitySet-ClouderaManager**
  * Use Managed Disks: **Yes**
  * Virtual Network: **ClouderaOnAzure-VirtualNetwork**
  * Subnet: **ClouderaOnAzure-SubNet-ClouderaManager**
  * Public IP Address: Default (New)
  * Network Security Group: **Advanced**
  * Network Security Group (Firewall): **ClouderaOnAzure-NetworkSecurityGroup-ClouderaManager**

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

## Dynamic DNS

Notes:

* CDH requires both forward and reverse Dynamic DNS for internal services.
* However, at this time, reverse Dynamic DNS support in Azure is not adequate.
* The steps below detail how to setup a custom Dynamic DNS Server using [BIND](https://www.isc.org/downloads/bind/).
* You can also read the [Cloudera documentation](https://www.cloudera.com/documentation/director/latest/topics/director_get_started_azure_ddns.html) for detailed information.
* These instructions use the [Cloudera Azure DNS Scripts](https://github.com/cloudera/director-scripts/tree/master/azure-dns-scripts) at GitHub to speed things up. However, only use these scripts if making a short-lived test deployment. For permanent production deployments, take time to understand what the detailed steps imply and create your own custom deployment scripts.

Install BIND:

* Open VM **Cloudera-DNS**
* Note the **Public IP Address**
* Login to **Cloudera-DNS** from your SSH client using the **Public IP Address**.
  * E.g. in Linux Bash run ```ssh cloudera@xxx.xxx.xxx.xxx``` where the mask is your VM's IP address.
  * The above assumes you created the SSH Key Pair as detailed in this guide.
* Open the [raw content](https://raw.githubusercontent.com/cloudera/director-scripts/master/azure-dns-scripts/bind-dns-setup.sh) for the **bind-dns-setup.sh** script on the [Cloudera Azure DNS Scripts](https://github.com/cloudera/director-scripts/tree/master/azure-dns-scripts) GitHub page.
* Note the url for the raw code.
* On the VM console:
  * Run
```wget https://raw.githubusercontent.com/cloudera/director-scripts/master/azure-dns-scripts/bind-dns-setup.sh``` where the url is the correct raw url for the **bind-dns-setup.sh** file.
  * Run ```chmod +x bind-dns-setup.sh``` to make the script executable.
  * Run ```sudo ./bind-dns-setup.sh``` to execute the script.
    * Note the warning message! If you already setup a BIND server on the cluster, do not proceed with the script. This guide assumes this is your first and only DNS server. Accept if so.
    * Enter internal host FQDN suffix: **cloudera**
    * When the script promps you to go to Azure, take note of the private IP address for the host, for example **10.0.0.4**. Do not proceed with the script yet.
* On Azure, open Virtual Network **ClouderaOnAzure-VirtualNetwork**.
  * Open tab **DNS Servers**.
  * Select **Custom**.
  * Enter the private IP address for the DNS host, for example **10.0.0.4**.
  * Save.
* On the VM console:
  * Press Enter to complete the setup.

## Configuration Database

### Notes:

* [CDH and Cloudera Manager Supported Databases](https://www.cloudera.com/documentation/enterprise/release-notes/topics/rn_consolidated_pcm.html#cdh_cm_supported_db)
* [Supported Software and Distributions](https://www.cloudera.com/documentation/director/latest/topics/director_deployment_requirements.html#concept_fhh_ygd_nt)
* [Installing MySQL Server](https://www.cloudera.com/documentation/enterprise/latest/topics/cm_ig_mysql.html#cmig_topic_5_5_1)
* This guide uses MySQL for demonstration purposes.
* [Azure Database for MySQL](https://azure.microsoft.com/en-us/pricing/details/mysql/) is a new PaaS MySQL database offering in Azure. However Cloudera has not yet announced official support at the time of writing this guide. Feel free to use it instead if this has changed in the meantime.

### Create Virtual Machine:

* Type: **Cloudera CentOS 7.4** (at time of writing - choose a newer one if available)
* Basics:
  * Name: **Cloudera-DNS**
  * VM Disk Type: **SSD**
  * Username: **cloudera**
  * Authentication Type: **SSH Public Key**
  * SSH Public Key: Your SSH Public Key
  * Subscription: Same as Resource Group
  * Resource Group: **ClouderaOnAzure**
* Size: General Purpose / Min 7GB RAM
* Settings:
  * Availability Set: **ClouderaOnAzure-AvailabilitySet-ClouderaManager**
  * Use Managed Disks: **Yes**
  * Virtual Network: **ClouderaOnAzure-VirtualNetwork**
  * Subnet: **ClouderaOnAzure-SubNet-ClouderaManager**
  * Public IP Address: Default (New)
  * Network Security Group: **Advanced**
  * Network Security Group (Firewall): **ClouderaOnAzure-NetworkSecurityGroup-ClouderaManager**

### Install MySQL Server:

> These steps just follow the Cloudera documentation linked above.

* Connect to **Cloudera-MySQL** via your SSH client.
* On the VM Console run:
  * ```wget http://repo.mysql.com/mysql-community-release-el7-5.noarch.rpm```
  * ```sudo rpm -ivh mysql-community-release-el7-5.noarch.rpm```
  * ```sudo yum update```
  * ```sudo yum install mysql-server```
  * ```sudo systemctl start mysqld```
  * ```systemctl status mysqld```
  * Ensure status shown by previous step is "active (running)".

### Configure MySQL Server Service:

> These steps just follow the Cloudera documentation on [Configuring and Starting the MySQL Server](https://www.cloudera.com/documentation/enterprise/latest/topics/cm_ig_mysql.html)

* Connect to **Cloudera-MySQL** via your SSH client.
* Enter ```systemctl status mysqld``` to check the status of the service.
* If the service is running, enter ```sudo systemctl stop mysqld``` to stop the service.
* Enter ```cat /etc/my.cnf``` to examine the current MySQL configuration file. You will replace this configuration file with the one provided by Cloudera. Therefore, it is a good idea to back it up.
* Enter ```sudo cp /etc/my.cnf /etc/my.cnf.backup``` to backup the current configuration file.
* Enter ```sudo truncate --size 0 /etc/my.cnf``` to clear the configuration file.
* Enter ```sudo nano /etc/my.cnf``` to edit the configuration file (will now be empty).
* On your own machine, go to the documentation page linked above and copy the content for the *option file with Cloudera recommended settings*.
* Back to the VM, paste the content into the nano editor.
* Type **Control-X** then **Y** then **Enter** to save your changes and exit nano.
* Enter ```cat /etc/my.cnf``` to verify that your changes were applied.
* Enter ```sudo systemctl start mysqld``` to start MySQL service.
* Enter ```systemctl status mysqld``` and ensure the MySQL service is now active and running. If the service did not start, restore the backup configuration file and try to start it again. If it now works, go through the above instructions again. You can also run ```sudo cat /var/log/mysqld.log``` to see if MySQL generated any helpful error logs.

### Configure MySQL Secure Authentication

Enter ```/usr/bin/mysql_secure_installation``` to start the secure installation script for MySQL. Use the following options:
* **Root Password prompt**: Leave blank and hit **Enter**. You have just installed MySQL Server so you there isn't one yet.
 * **Set Root Password**: **Y**.
 * **MySQL Root Password**: Choose your new *MySQL root password*. Do use a strong password as database root access is a common attack vector.
 * **Remove Anonymous Users**: **Y**.
 * **Disallow Root Login Remotely**: **N**.
 * **Remove Test Database**: **Y**. Ignore any errors here.
 * **Reload Priviledge Tables**: **Y**.

 ### Install MySQL JDBC Driver

 > Note: You must apply this step in every single cluster that requires direct configuration database access.

On your own machine:
 * Go to the [MySQL Connector/J download page](https://dev.mysql.com/downloads/connector/j/5.1.html) and try to download the tar.gz files.
 * On the confirmation page, locate the *no thanks, just start my dowload* link and copy it.

On **Cloudera-MySQL**:
 * Enter ```wget``` plus the download link you just copied, e.g. ```wget https://dev.mysql.com/get/Downloads/Connector-J/mysql-connector-java-5.1.46.tar.gz```
   * If you get a DNS resolution error, ensure the VM **Cloudera-DNS** is active.
* Enter ```tar zxvf mysql-connector-java-5.1.46.tar.gz``` (or the equivalent for your file name) to extract the driver files.
* Enter ```sudo mkdir -p /usr/share/java/``` to make a new shared folder for the driver.
* Enter ```cd mysql-connector-java-5.1.46``` to move into the extracted driver folder.
* Enter ```sudo cp mysql-connector-java-5.1.46-bin.jar /usr/share/java/mysql-connector-java.jar``` to copy the downloaded driver to the shared driver folder.

### Create Databases for Cloudera Software

On **Cloudera-MySQL**:
  * Enter ```mysql -u root -p``` to log on to the MySQL command prompt.
  * Enter your MySQL password.

For Cloudera Manager Server:
  * ```CREATE DATABASE scm DEFAULT CHARACTER SET utf8 DEFAULT COLLATE utf8_general_ci;```
  * ```GRANT ALL ON scm.* TO 'scm_user'@'%' IDENTIFIED BY 'scm_password';```

For Activity Monitor:
  * ```CREATE DATABASE amon DEFAULT CHARACTER SET utf8 DEFAULT COLLATE utf8_general_ci;```
  * ```GRANT ALL ON amon.* TO 'amon_user'@'%' IDENTIFIED BY 'amon_password';```

For Reports Manager:
  * ```CREATE DATABASE rman DEFAULT CHARACTER SET utf8 DEFAULT COLLATE utf8_general_ci;```
  * ```GRANT ALL ON rman.* TO 'rman_user'@'%' IDENTIFIED BY 'rman_password';```

For Hue:
  * ```CREATE DATABASE hue DEFAULT CHARACTER SET utf8 DEFAULT COLLATE utf8_general_ci;```
  * ```GRANT ALL ON hue.* TO 'hue_user'@'%' IDENTIFIED BY 'hue_password';```

For Hive Metastore Server:
  * ```CREATE DATABASE metastore DEFAULT CHARACTER SET utf8 DEFAULT COLLATE utf8_general_ci;```
  * ```GRANT ALL ON metastore.* TO 'hive_user'@'%' IDENTIFIED BY 'hive_password';```

For Sentry Server:
  * ```CREATE DATABASE sentry DEFAULT CHARACTER SET utf8 DEFAULT COLLATE utf8_general_ci;```
  * ```GRANT ALL ON sentry.* TO 'sentry_user'@'%' IDENTIFIED BY 'sentry_password';```

For Cloudera Navigator Audit Server:
  * ```CREATE DATABASE nav DEFAULT CHARACTER SET utf8 DEFAULT COLLATE utf8_general_ci;```
  * ```GRANT ALL ON nav.* TO 'nav_user'@'%' IDENTIFIED BY 'nav_password';```

For Cloudera Navigator Metadata Server:
  * ```CREATE DATABASE navms DEFAULT CHARACTER SET utf8 DEFAULT COLLATE utf8_general_ci;```
  * ```GRANT ALL ON navms.* TO 'navms_user'@'%' IDENTIFIED BY 'navms_password';```

For Oozie:
  * ```CREATE DATABASE oozie DEFAULT CHARACTER SET utf8 DEFAULT COLLATE utf8_general_ci;```
  * ```GRANT ALL ON oozie.* TO 'oozie_user'@'%' IDENTIFIED BY 'oozie_password';```

Check all databases:
  * ```SHOW DATABASES;```