# Manage workloads in Azure

##	migrate workloads using Azure Migrate

### Azure Migrate Tools

|Tool	|Assess and migrate	|Details|
|:--- |:--- |:--- |
|Azure Migrate: Server Assessment	|Assess servers.	|Discover and assess on-premises VMware VMs, Hyper-V VMs, and physical servers in preparation for migration to Azure.|
|Azure Migrate: Server Migration	|Migrate servers.	|Migrate VMware VMs, Hyper-V VMs, physical servers, other virtualized machines, and public cloud VMs to Azure.|
|Data Migration Assistant|	Assess SQL Server databases for migration to Azure SQL Database, Azure SQL Managed Instance, or Azure VMs running SQL Server.	|Data Migration Assistant helps pinpoint potential problems blocking migration. It identifies unsupported features, new features that can benefit you after migration, and the right path for database migration. [Learn more](https://docs.microsoft.com/en-us/azure/dms/dms-overview).|
|Azure Database Migration Service	|Migrate on-premises databases to Azure VMs running SQL Server, Azure SQL Database, or SQL Managed Instances.	|Learn more about Database Migration Service.|
|Movere	|Assess servers.	|Movere is a software as a service (SaaS) platform. It increases business intelligence by accurately presenting entire IT environments within a single day. [Learn more](https://docs.microsoft.com/en-us/azure/migrate/migrate-services-overview#movere) about Movere.|
|Web app migration assistant|	Assess on-premises web apps and migrate them to Azure.	|Use Azure App Service Migration Assistant to assess on-premises websites for migration to Azure App Service. Use Migration Assistant to migrate .NET and PHP web apps to Azure. [Learn more](https://appmigration.microsoft.com/) about Azure App Service Migration Assistant.|
|Azure Data Box|	Migrate offline data.	|Use Azure Data Box products to move large amounts of offline data to Azure. [Learn more](https://docs.microsoft.com/en-us/azure/databox/).|

### Azure Migrate: Server Assessment Tool

The Azure Migrate: Server Assessment tool discovers and assesses on-premises VMware VMs, Hyper-V VMs, and physical servers for migration to Azure.

Here's what the tool does:

- Azure readiness: Assesses whether on-premises machines are ready for migration to Azure.
- Azure sizing: Estimates the size of Azure VMs or number of Azure VMware nodes after migration.
- Azure cost estimation: Estimates costs for running on-premises servers in Azure.
- Dependency analysis: Identifies cross-server dependencies and optimization strategies for moving interdependent servers to Azure. Learn more about Server Assessment with dependency analysis.

Server Assessment uses a lightweight Azure Migrate appliance that you deploy on-premises.

- The appliance runs on a VM or physical server. You can install it easily using a downloaded template.
- The appliance discovers on-premises machines. It also continually sends machine metadata and performance data to Azure Migrate.
- Appliance discovery is agentless. Nothing is installed on discovered machines.
- After appliance discovery, you can gather discovered machines into groups and run assessments for each group.

### Azure Migrate: Server Migration tool

The Azure Migrate: Server Migration tool helps you migrate to Azure:

|Migrate	|Details|	
|:--- |:---|
|On-premises VMware VMs	|Migrate VMs to Azure using agentless or agent-based migration. For agentless migration, Server Migration uses an Azure Migrate appliance that you deploy on-premises. It's the same type of appliance you use for Server Assessment. For agent-based migration, Server Assessment uses a replication appliance.	|
|On-premises Hyper-V VMs	|Migrate VMs to Azure. Server Assessment uses provider agents installed on Hyper-V host for the migration.	|
|On-premises physical servers	|You can migrate physical machines to Azure. You can also migrate other virtualized machines, and VMs from other public clouds, by treating them as virtual machines for the purpose of migration.	Server Assessment uses a replication appliance for the migration.|

With both Azure and partner ISV tools built in, Azure Migrate has an extensive range of features, including:

- Discovery of virtual and physical servers.
- Performance-based rightsizing.
- Cost planning.
- Import-based assessments.
- Dependency analysis of agentless applications.

#### Links

- [About Azure Migrate](https://docs.microsoft.com/en-us/azure/migrate/migrate-services-overview)

### assess infrastructure

#### Prepare VMware VMs for assessment and migration to Azure

- Create an Azure AD account with "User can register applications" set to true, or the role **Application Developer** so that it can register Azure AD Apps 
- Create an account to access vCenter, use Read-Only mode
- Create an account to access the VMs
- Create an Azure Migrate project, specify Geography (e.g. United States)
  - The Azure Migrate: Server Assessment Tool should be available in the project.
- In Migration Goals -> Servers -> Azure Migrate: Server Assessment, select Discover
  - Generate an Azure Migrate project key
- Download the OVA template and install the appliance in vCenter
  - (Check it is secure before installing)
- Open a browser on any machine that can connect to the VM, and open the URL of the appliance web app: https://appliance name or IP address: 44368.
- Register the appliance with Azure Migrate
  - Paste the Azure Migrate project key
- Start continous discovery
  - Add vCenter Server credentials
  - Click on Add discovery source to specify the details of th appliance
  - Provide VM credentials to discover installed applications and to perform agentless dependency mapping
  - Click "Start discovery"

Discovery works as follows:

- It takes around 15 minutes for discovered VM metadata to appear in the portal.
- Discovery of installed applications, roles, and features takes some time. The duration depends on the number of VMs being discovered. For 500 VMs, it takes approximately one hour for the application inventory to appear in the Azure Migrate portal.

### Assess VMware VMs by using Azure Migrate Server Assessment

#### Decide which assessment to run

Decide whether you want to run an assessment using sizing criteria based on machine configuration data/metadata that's collected as-is on-premises, or on dynamic performance data.


|Assessment	|Details	|Recommendation|
|:--- |:--- |:--- | 
|As-is on-premises	|Assess based on machine configuration data/metadata.	|Recommended Azure VM size is based on the on-premises VM size. The recommended Azure disk type is based on what you select in the storage type setting in the assessment.|
|Performance-based	|Assess based on collected dynamic performance data.	|Recommended Azure VM size is based on CPU and memory utilization data. The recommended disk type is based on the IOPS and throughput of the on-premises disks.|

- Assess servers -> Assessment type -> select Azure VM
- In Discovery source:
  - If you discovered machines using the appliance, select Machines discovered from Azure Migrate appliance.
  - If you discovered machines using an imported CSV file, select Imported machines.

- In Assessment properties > Target Properties:
  - In Target location, specify the Azure region to which you want to migrate.
    - Size and cost recommendations are based on the location that you specify.
    - In Azure Government, you can target assessments in these regions
  - In Storage type,
    - If you want to use performance-based data in the assessment, select Automatic for Azure Migrate to recommend a storage type, based on disk IOPS and throughput.
    - Alternatively, select the storage type you want to use for VM when you migrate it.
  - In Reserved Instances, specify whether you want to use reserve instances for the VM when you migrate it.
    - If you select to use a reserved instance, you can't specify 'Discount (%), or VM uptime.
    - Learn more.
- In VM Size:
  - In Sizing criterion, select if you want to base the assessment on machine configuration data/metadata, or on performance-based data. If you use performance data:
    - In Performance history, indicate the data duration on which you want to base the assessment
    - In Percentile utilization, specify the percentile value you want to use for the performance sample.
  - In VM Series, specify the Azure VM series you want to consider.
    - If you're using performance-based assessment, Azure Migrate suggests a value for you.
    - Tweak settings as needed. For example, if you don't have a production environment that needs A-series VMs in Azure, you can exclude A-series from the list of series.
  - In Comfort factor, indicate the buffer you want to use during assessment. This accounts for issues like seasonal usage, short performance history, and likely increases in future usage. For example, if you use a comfort factor of two: Component | Effective utilization | Add comfort factor (2.0) Cores | 2 | 4 Memory | 8 GB | 16 GB
- In Pricing:
  - In Offer, specify the Azure offer if you're enrolled. Server Assessment estimates the cost for that offer.
  - In Currency, select the billing currency for your account.
  - In Discount (%), add any subscription-specific discounts you receive on top of the Azure offer. The default setting is 0%.
  - In VM Uptime, specify the duration (days per month/hour per day) that VMs will run.
    - This is useful for Azure VMs that won't run continuously.
    - Cost estimates are based on the duration specified.
    - Default is 31 days per month/24 hours per day.
  - In EA Subscription, specify whether to take an Enterprise Agreement (EA) subscription discount into account for cost estimation.
  - In Azure Hybrid Benefit, specify whether you already have a Windows Server license. If you do and they're covered with active Software Assurance of Windows Server Subscriptions, you can apply for the Azure Hybrid Benefit when you bring licenses to Azure.
- In Assess Servers, click Next.
- In Select machines to assess, select Create New, and specify a group name.
- Select the appliance, and select the VMs you want to add to the group. Then click Next.

**Note** For performance-based assessments, we recommend that you wait at least a day after starting discovery before you create an assessment. This provides time to collect performance data with higher confidence. Ideally, after you start discovery, wait for the performance duration you specify (day/week/month) for a high-confidence rating.

#### Review an assessment

An assessment describes:

- Azure readiness: Whether VMs are suitable for migration to Azure.
- Monthly cost estimation: The estimated monthly compute and storage costs for running the VMs in Azure.
- Monthly storage cost estimation: Estimated costs for disk storage after migration.

#### Review readiness

- Click Azure readiness.
- In Azure readiness, review the VM status:
  - Ready for Azure: Used when Azure Migrate recommends a VM size and cost estimates, for VMs in the assessment.
  - Ready with conditions: Shows issues and suggested remediation.
  - Not ready for Azure: Shows issues and suggested remediation.
  - Readiness unknown: Used when Azure Migrate can't assess readiness, because of data availability issues.
- Select an Azure readiness status. You can view VM readiness details. You can also drill down to see VM details, including compute, storage, and network settings.

#### Review cost estimates

The assessment summary shows the estimated compute and storage cost of running VMs in Azure.

- Review the monthly total costs. Costs are aggregated for all VMs in the assessed group.
  - Cost estimates are based on the size recommendations for a machine, its disks, and its properties.
  - Estimated monthly costs for compute and storage are shown.
  - The cost estimation is for running the on-premises VMs on Azure VMs. The estimation doesn't consider PaaS or SaaS costs.
- Review monthly storage costs. The view shows the aggregated storage costs for the assessed group, split over different types of storage disks.
- You can drill down to see cost details for specific VMs.

#### Review confidence rating

This is a 5 star rating.

#### Links

  - [Prepare VMware VMs for assessment and migration to Azure](https://docs.microsoft.com/en-us/azure/migrate/tutorial-prepare-vmware)
  - [Assess VMware VMs by using Azure Migrate Server Assessment](https://docs.microsoft.com/en-us/azure/migrate/tutorial-assess-vmware)

### select a migration method

#### VMware migration options

You can migrate VMware VMs to Azure using the Azure Migrate Server Migration tool. This tool offers a couple of options for VMware VM migration:

- Migration using agentless replication. Migrate VMs without needing to install anything on them.
- Migration with an agent for replication. Install an agent on the VM for replication.

#### Compare migration methods

Use these selected comparisons to help you decide which method to use. You can also review full support requirements for agentless and agent-based migration.

|Setting	|Agentless	|Agent-based|
|:--- |:---|:---|
|Azure permissions	|You need permissions to create an Azure Migrate project, and to register Azure AD apps created when you deploy the Azure Migrate appliance.|	You need Contributor permissions on the Azure subscription.|
|Replication	|A maximum of 300 VMs can be simultaneously replicated from a vCenter Server.<br /><br />If you have more than 50 VMs for migration, create multiple batches of VMs.<br /><br />Replicating more at a single time will impact performance.<br /><br />In the portal, you can select up to 10 machines at once for replication. To replicate more machines, add in batches of 10.	|Replication capacity increases by scaling the replication appliance.|
|Appliance deployment	|The Azure Migrate appliance is deployed on-premises.|	The Azure Migrate Replication appliance is deployed on-premises.|
|Site Recovery compatibility	|Compatible.	|You can't replicate with Azure Migrate Server Migration if you've set up replication for a machine using Site Recovery.|
|Target disk	|Managed disks	|Managed disks|
|Disk limits	|OS disk: 2 TB <br /><br />Data disk: 8 TB<br /><br />Maximum disks: 60|OS disk: 2 TB<br /><br />Data disk: 8 TB<br /><br />Maximum disks: 63|
|Passthrough disks	|Not supported|Supported|
|UEFI boot	|Not supported	|The migrated VM in Azure will be automatically converted to a BIOS boot VM.<br /><br />The OS disk should have up to four partitions, and volumes should be formatted with NTFS.|

#### Compare deployment steps

After reviewing the limitations, understanding the steps involved in deploying each solution might help you decide which option to choose.

|Task	|Details	|Agentless	|Agent-based	|
|:---|:---|:---|:---|
|Deploy the Azure Migrate appliance	|A lightweight appliance that runs on a VMware VM.<br /><br />The appliance is used to discover and assess machines, and to migrate machines using agentless migration.	|Required.<br /><br />If you've already set up the appliance for assessment, you can use the same appliance for agentless migration.	| Not required.<br /><br />If you've set up an appliance for assessment, you can leave it in place, or remove it if you're done with assessment.|	
|Use the Server Assessment tool|	Assess machines with the Azure Migrate:Server Assessment tool.	|You can assess machines before you migrate them, but you don't have to.	|Assessment is optional|	Assessment is optional.|
|Use the Server Migration tool	|Add the Azure Migrate Server Migration tool in the Azure Migrate project.	|Required	|Required|	
|Prepare VMware for migration	|Configure settings on VMware servers and VMs.	|Required	|Required|	
|Install the Mobility service on VMs	|Mobility service runs on each VM you want to replicate	|Not required	|Required|	
|Deploy the replication appliance	|The replication appliance is used for agent-based migration. It connects between the Mobility service running on VMs, and Server Migration.|	Not required	|Required	|
|Replicate VMs. Enable VM replication.	|Configure replication settings and select VMs to replicate	|Required	|Required|	
|Run a test migration	|Run a test migration to make sure everything's working as expected.	|Required	|Required	|
|Run a full migration	|Migrate the VMs.	| Required	|Required|

#### Links

- [Select a VMware migration option](https://docs.microsoft.com/en-us/azure/migrate/server-migrate-overview)

### prepare the on-premises for migration

#### Set up the Azure Migrate appliance

Azure Migrate Server Migration runs a lightweight VMware VM appliance that's used for discovery, assessment, and agentless migration of VMware VMs. If you follow the assessment tutorial, you've already set the appliance up. If you didn't, set it up now, using one of these methods:

- OVA template: Set up on a VMware VM using a downloaded OVA template.
- Script: Set up on a VMware VM or physical machine, using a PowerShell installer script. This method should be used if you can't set up a VM using an OVA template, or if you're in Azure Government.

#### Replicate VMs

After setting up the appliance and completing discovery, you can begin replication of VMware VMs to Azure.

- You can run up to 300 replications simultaneously.
- In the portal, you can select up to 10 VMs at once for migration. To migrate more machines, add them to groups in batches of 10.

1. In the Azure Migrate project -> Servers, Azure Migrate: Server Migration, click Replicate.
2. In Replicate, -> Source settings -> Are your machines virtualized?, select Yes, with VMware vSphere.
3. In On-premises appliance, select the name of the Azure Migrate appliance that you set up > OK.
4. In Virtual machines, select the machines you want to replicate. To apply VM sizing and disk type from an assessment if you've run one, in Import migration settings from an Azure Migrate assessment?, select Yes, and select the VM group and assessment name. If you aren't using assessment settings, select No.
5. In Virtual machines, select VMs you want to migrate. Then click Next: Target settings.
6. In Target settings, select the subscription and target region. Specify the resource group in which the Azure VMs reside after migration.
7. In Virtual Network, select the Azure VNet/subnet which the Azure VMs join after migration.
8. In Availability options, select:
  - Availability Zone to pin the migrated machine to a specific Availability Zone in the region. Use this option to distribute servers that form a multi-node application tier across Availability Zones. If you select this option, you'll need to specify the Availability Zone to use for each of the selected machine in the Compute tab. This option is only available if the target region selected for the migration supports Availability Zones
  - Availability Set to place the migrated machine in an Availability Set. The target Resource Group that was selected must have one or more availability sets in order to use this option.
  - No infrastructure redundancy required option if you don't need either of these availability configurations for the migrated machines.
9. In Azure Hybrid Benefit:
  - Select No if you don't want to apply Azure Hybrid Benefit. Then click Next.
  - Select Yes if you have Windows Server machines that are covered with active Software Assurance or Windows Server subscriptions, and you want to apply the benefit to the machines you're migrating. Then click Next.
10. In Compute, In Compute, review the VM name, size, OS disk type, and availability configuration (if selected in the previous step). VMs must conform with Azure requirements.
  - VM size: If you're using assessment recommendations, the VM size dropdown shows the recommended size. Otherwise Azure Migrate picks a size based on the closest match in the Azure subscription. Alternatively, pick a manual size in Azure VM size.
  - OS disk: Specify the OS (boot) disk for the VM. The OS disk is the disk that has the operating system bootloader and installer.
  - Availability Zone: Specify the Availability Zone to use.
  - Availability Set: Specify the Availability Set to use.
11. In Disks, specify whether the VM disks should be replicated to Azure, and select the disk type (standard SSD/HDD or premium-managed disks) in Azure. Then click Next.
12. In Review and start replication, review the settings, and click Replicate to start the initial replication for the servers.

#### Run a test migration

When delta replication begins, you can run a test migration for the VMs, before running a full migration to Azure. We highly recommend that you do this at least once for each machine, before you migrate it.

- Running a test migration checks that migration will work as expected, without impacting the on-premises machines, which remain operational, and continue replicating.
- Test migration simulates the migration by creating an Azure VM using replicated data (usually migrating to a non-production VNet in your Azure subscription).
- You can use the replicated test Azure VM to validate the migration, perform app testing, and address any issues before full migration.

#### Migrate VMs

After you've verified that the test migration works as expected, you can migrate the on-premises machines.

1. In the Azure Migrate project > Servers > Azure Migrate: Server Migration, click Replicating servers.
2. In Replicating machines, right-click the VM > Migrate.
3. In Migrate > Shut down virtual machines and perform a planned migration with no data loss, select Yes > OK.
  - By default Azure Migrate shuts down the on-premises VM, and runs an on-demand replication to synchronize any VM changes that occurred since the last replication occurred. This ensures no data loss.
  - If you don't want to shut down the VM, select No
4. A migration job starts for the VM. Track the job in Azure notifications.
5. After the job finishes, you can view and manage the VM from the Virtual Machines page.

#### Complete the migration

1. After the migration is done, right-click the VM > Stop Replication. This stops replication for the on-premises machine, and cleans up replication state information for the VM.
2. Install the Azure VM Windows or Linux agent on the migrated machines.
3. Perform any post-migration app tweaks, such as updating database connection strings, and web server configurations.
4. Perform final application and migration acceptance testing on the migrated application now running in Azure.
5. Cut over traffic to the migrated Azure VM instance.
6. Remove the on-premises VMs from your local VM inventory.
7. Remove the on-premises VMs from local backups.
8. Update any internal documentation to show the new location and IP address of the Azure VMs.

#### Post-migration best practices

- For increased resilience:
  - Keep data secure by backing up Azure VMs using the Azure Backup service. Learn more.
  - Keep workloads running and continuously available by replicating Azure VMs to a secondary region with Site Recovery. Learn more.
- For increased security:
  - Lock down and limit inbound traffic access with Azure Security Center - Just in time administration.
  - Restrict network traffic to management endpoints with Network Security Groups.
  - Deploy Azure Disk Encryption to help secure disks, and keep data safe from theft and unauthorized access.
  - Read more about securing IaaS resources, and visit the Azure Security Center.
- For monitoring and management:
  - Consider deploying Azure Cost Management to monitor resource usage and spending.

### Migrate VMware VMs to Azure (agent-based)

#### Prepare Azure

|Task|Details|
|:--- |:--- |
|Create an Azure Migrate project	|Your Azure account needs Contributor or Owner permissions to create a project.|
|Verify Azure account permissions	|Your Azure account needs permissions to create a VM, and write to an Azure managed disk.|
|Set up an Azure network	|Set up a network that Azure VMs will join after migration.|

#### Assign Azure account permissions

Assign the Virtual Machine Contributor role to the account, so that you have permissions to:

- Create a VM in the selected resource group.
- Create a VM in the selected virtual network.
- Write to an Azure managed disk.

#### Set up an Azure network

Set up an Azure network. On-premises machines are replicated to Azure managed disks. When you fail over to Azure for migration, Azure VMs are created from these managed disks, and joined to the Azure network you set up.

#### VMware account permissions

|Task	|Role/Permissions	|Details|
|:--- |:--- |:--- |
|VM discovery	|At least a read-only user<br /><br />  Data Center object –> Propagate to Child Object, role=Read-only	|User assigned at datacenter level, and has access to all the objects in the datacenter.<br /><br />  To restrict access, assign the No access role with the Propagate to child object, to the child objects (vSphere hosts, datastores, VMs, and networks).|
|Replication	|Create a role (Azure_Site_Recovery) with the required permissions, and then assign the role to a VMware user or group<br /><br />  Data Center object –> Propagate to Child Object, role=Azure_Site_Recovery<br /><br /> Datastore -> Allocate space, browse datastore, low-level file operations, remove file, update virtual machine files<br /><br /> Network -> Network assign<br /><br /> Resource -> Assign VM to resource pool, migrate powered off VM, migrate powered on VM<br /><br /> Tasks -> Create task, update task<br /><br /> Virtual machine -> Configuration<br /><br /> Virtual machine -> Interact -> answer question, device connection, configure CD media, configure floppy media, power off, power on, VMware tools install<br /><br /> Virtual machine -> Inventory -> Create, register, unregister<br /><br /> Virtual machine -> Provisioning -> Allow virtual machine download, allow virtual machine files upload<br /><br /> Virtual machine -> Snapshots -> Remove snapshots	|User assigned at datacenter level, and has access to all the objects in the datacenter.<br /><br /> To restrict access, assign the No access role with the Propagate to child object, to the child objects (vSphere hosts, datastores, VMsa, nd networks).|

#### Prepare an account for Mobility service installation

**The Mobility service must be installed on machines you want to replicate.**

- The Azure Migrate replication appliance can do a push installation of this service when you enable replication for a machine, or you can install it manually, or using installation tools.
- In this tutorial, we're going to install the Mobility service with the push installation.
- For push installation, you need to prepare an account that Azure Migrate Server Migration can use to access the VM. This account is used only for the push installation, if you don't install the Mobility service manually.

Prepare the account as follows:

1. Prepare a domain or local account with permissions to install on the VM.
2. For Windows VMs, if you're not using a domain account, disable Remote User Access control on the local machine by adding the DWORD entry LocalAccountTokenFilterPolicy, with a value of in the registry, under HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System
3. For Linux VMs, prepare a root account on the source Linux server.

#### Prepare a machine for the replication appliance

The appliance is used to replication machines to Azure. The appliance is single, highly available, on-premises VMware VM that hosts these components:

- Configuration server: The configuration server coordinates communications between on-premises and Azure, and manages data replication.
- Process server: The process server acts as a replication gateway. It receives replication data; optimizes it with caching, compression, and encryption, and sends it to a cache storage account in Azure. The process server also installs the Mobility Service agent on VMs you want to replicate, and performs automatic discovery of on-premises VMware VMs.

Prepare for the appliance as follows:

- Review appliance requirements. Generally, you set up the replication appliance a a VMware VM using a downloaded OVA file. The template creates a appliance that complies with all requirements.
- MySQL must be installed on the appliance. Review installation methods.
- Review the public cloud URLs, and Azure Government URLs that the appliance machine needs to access.
- Review the ports that the replication appliance machine needs to access.

#### Check VMware requirements

#### Add the Azure Migrate:Server Migration tool

#### Set up the replication appliance

This procedure describes how to set up the appliance with a downloaded Open Virtualization Application (OVA) template. If you can't use this method, you can set up the appliance using a script.

#### Import the template in VMware

After downloading the OVF template, you import it into VMware to create the replication application on a VMware VM running Windows Server 2016.

#### Start appliance setup

- When you sign in to the appliance, the Azure Site Recovery Configuration Tool will start running
- The VM will connect to Azure
- The tool will register an Azure AD App to identify the appliance

#### Register the replication appliance

1. In appliance setup, select Setup connectivity.
2. Select the NIC (by default there's only one NIC) that the replication appliance uses for VM discovery, and to do a push installation of the Mobility service on source machines.
3. Select the NIC that the replication appliance uses for connectivity with Azure. Then select Save. You cannot change this setting after it's configured.
4. If the appliance is located behind a proxy server, you need to specify proxy settings.
  - Specify the proxy name as http://ip-address, or http://FQDN. HTTPS proxy servers aren't supported.
5. When prompted for the subscription, resource groups, and vault details, add the details that you noted when you downloaded the appliance template.
6. In Install third-party software, accept the license agreement. Select Download and Install to install MySQL Server.
7. Select Install VMware PowerCLI. Make sure all browser windows are closed before you do this. Then select Continue.
8. In Validate appliance configuration, prerequisites are verified before you continue.
9. In Configure vCenter Server/vSphere ESXi server, enter the FQDN or IP address of the vCenter server, or vSphere host, where the VMs you want to replicate are located. Enter the port on which the server is listening. Enter a friendly name to be used for the VMware server in the vault.
10. Enter the credentials for the account you created for VMware discovery. Select Add > Continue.
11. In Configure virtual machine credentials, enter the credentials you created for push installation of the Mobility service, when you enable replication for VMs.
  - For Windows machines, the account needs local administrator privileges on the machines you want to replicate.
  - For Linux, provide details for the root account.
12. Select Finalize configuration to complete registration.

After the replication appliance is registered, Azure Migrate Server Assessment connects to VMware servers using the specified settings, and discovers VMs. You can view discovered VMs in Manage > Discovered items, in the Other tab.

#### Replicate VMs

Select VMs for migration.

**Note** In the portal you can select up to 10 machines at once for replication. If you need to replicate more, then group them in batches of 10.

7. In Virtual Machines, select the machines that you want to replicate.
  - If you've run an assessment for the VMs, you can apply VM sizing and disk type (premium/standard) recommendations from the assessment results. To do this, in Import migration settings from an Azure Migrate assessment?, select the Yes option.
  - If you didn't run an assessment, or you don't want to use the assessment settings, select the No options.
  - If you selected to use the assessment, select the VM group, and assessment name.
8. In Availability options, select:
  - Availability Zone to pin the migrated machine to a specific Availability Zone in the region. Use this option to distribute servers that form a multi-node application tier across Availability Zones. If you select this option, you'll need to specify the Availability Zone to use for each of the selected machine in the Compute tab. This option is only available if the target region selected for the migration supports Availability Zones
  - Availability Set to place the migrated machine in an Availability Set. The target Resource Group that was selected must have one or more availability sets in order to use this option.
  - No infrastructure redundancy required option if you don't need either of these availability configurations for the migrated machines.
9. Check each VM you want to migrate. Then click Next: Target settings.
10. In Target settings, select the subscription, and target region to which you'll migrate, and specify the resource group in which the Azure VMs will reside after migration.
11. In Virtual Network, select the Azure VNet/subnet to which the Azure VMs will be joined after migration.
12. In Availability options, select:
  - Availability Zone to pin the migrated machine to a specific Availability Zone in the region. Use this option to distribute servers that form a multi-node application tier across Availability Zones. If you select this option, you'll need to specify the Availability Zone to use for each of the selected machine in the Compute tab. This option is only available if the target region selected for the migration supports Availability Zones
  - Availability Set to place the migrated machine in an Availability Set. The target Resource Group that was selected must have one or more availability sets in order to use this option.
  - No infrastructure redundancy required option if you don't need either of these availability configurations for the migrated machines.
13. In Azure Hybrid Benefit:
  - Select No if you don't want to apply Azure Hybrid Benefit. Then click Next.
  - Select Yes if you have Windows Server machines that are covered with active Software Assurance or Windows Server subscriptions, and you want to apply the benefit to the machines you're migrating. Then click Next.
14. In Compute, review the VM name, size, OS disk type, and availability configuration (if selected in the previous step). VMs must conform with Azure requirements.
  - VM size: If you're using assessment recommendations, the VM size dropdown shows the recommended size. Otherwise Azure Migrate picks a size based on the closest match in the Azure subscription. Alternatively, pick a manual size in Azure VM size. - OS disk: Specify the OS (boot) disk for the VM. The OS disk is the disk that has the operating system bootloader and installer. - Availability Zone: Specify the Availability Zone to use. - Availability Set: Specify the Availability Set to use.
15. In Disks, specify whether the VM disks should be replicated to Azure, and select the disk type (standard SSD/HDD or premium managed disks) in Azure. Then click Next.
  - You can exclude disks from replication.
  - If you exclude disks, won't be present on the Azure VM after migration.
16. In Review and start replication, review the settings, and click Replicate to start the initial replication for the servers.

**Note** You can update replication settings any time before replication starts, Manage > Replicating machines. Settings can't be changed after replication starts.

#### Track and monitor

1. Track job status in the portal notifications
2. To monitor replication status, click Replicating servers in Azure Migrate: Server Migration.

Replication occurs as follows:

- When the Start Replication job finishes successfully, the machines begin their initial replication to Azure.
- After initial replication finishes, delta replication begins. Incremental changes to on-premises disks are periodically replicated to the replica disks in Azure.

#### Run a test migration

When delta replication begins, you can run a test migration for the VMs, before running a full migration to Azure. We highly recommend that you do this at least once for each machine, before you migrate it.

#### Migrate VMs

After you've verified that the test migration works as expected, you can migrate the on-premises machines.

1. In the Azure Migrate project > Servers > Azure Migrate: Server Migration, click Replicating servers.
2. In Replicating machines, right-click the VM > Migrate.
3. In Migrate > Shut down virtual machines and perform a planned migration with no data loss, select Yes > OK.
  - By default Azure Migrate shuts down the on-premises VM to ensure minimum data loss.
  - If you don't want to shut down the VM, select No

#### Complete the migration

1. After the migration is done, right-click the VM > Stop migration. This does the following:
  - Stops replication for the on-premises machine.
  - Removes the machine from the Replicating servers count in Azure Migrate: Server Migration.
  - Cleans up replication state information for the VM.
2. Install the Azure VM Windows or Linux agent on the migrated machines.
3. Perform any post-migration app tweaks, such as updating database connection strings, and web server configurations.
4. Perform final application and migration acceptance testing on the migrated application now running in Azure.
5. Cut over traffic to the migrated Azure VM instance.
6. Remove the on-premises VMs from your local VM inventory.
7. Remove the on-premises VMs from local backups.
8. Update any internal documentation to show the new location and IP address of the Azure VMs.

#### Post-migration best practices

- On-premises
  - Move app traffic over to the app running on the migrated Azure VM instance.
  - Remove the on-premises VMs from your local VM inventory.
  - Remove the on-premises VMs from local backups.
  - Update any internal documentation to show the new location and IP address of the Azure VMs.
- Tweak Azure VM settings after migration:
  - The Azure VM agent manages VM interaction with the Azure Fabric Controller. It's required for some Azure services, such as Azure Backup, Site Recovery, and Azure Security. When migrating VMare VMs with agent-based migration, the Mobility Service installer installs Azure VM agent on Windows machines. On Linux VMs, we recommend that you install the agent after migration.
  - Manually uninstall the Mobility service from the Azure VM after migration.
  - Manually uninstall VMware tools after migration.
- In Azure:
  - Perform any post-migration app tweaks, such as updating database connection strings, and web server configurations.
  - Perform final application and migration acceptance testing on the migrated application now running in Azure.
- Business continuity/disaster recovery
  - Keep data secure by backing up Azure VMs using the Azure Backup service. Learn more.
  - Keep workloads running and continuously available by replicating Azure VMs to a secondary region with Site Recovery. Learn more.
- For increased security:
  - Lock down and limit inbound traffic access with Azure Security Center - Just in time administration.
  - Restrict network traffic to management endpoints with Network Security Groups.
  - Deploy Azure Disk Encryption to help secure disks, and keep data safe from theft and unauthorized access.
  - Read more about securing IaaS resources, and visit the Azure Security Center.
- For monitoring and management:
  - Consider deploying Azure Cost Management to monitor resource usage and spending.

#### Links

  - [Migrate VMware VMs to Azure (agentless)](https://docs.microsoft.com/en-us/azure/migrate/tutorial-migrate-vmware)
  - [Migrate VMware VMs to Azure (agent-based)](https://docs.microsoft.com/en-us/azure/migrate/tutorial-migrate-vmware-agent)

### recommend target infrastructure

TODO

## implement Azure Backup for VMs

[An overview of Azure VM backup](https://docs.microsoft.com/en-us/azure/backup/backup-azure-vms-introduction)

This article describes how the Azure Backup service backs up Azure virtual machines (VMs).

Azure Backup provides independent and isolated backups to guard against unintended destruction of the data on your VMs. Backups are stored in a Recovery Services vault with built-in management of recovery points. Configuration and scaling are simple, backups are optimized, and you can easily restore as needed.

As part of the backup process, a snapshot is taken, and the data is transferred to the Recovery Services vault with no impact on production workloads. The snapshot provides different levels of consistency, as described here.

Azure Backup also has specialized offerings for database workloads like SQL Server and SAP HANA that are workload-aware, offer 15 minute RPO (recovery point objective), and allow backup and restore of individual databases.

### Backup process

Here's how Azure Backup completes a backup for Azure VMs:

1. For Azure VMs that are selected for backup, Azure Backup starts a backup job according to the backup schedule you specify.
2. During the first backup, a backup extension is installed on the VM if the VM is running.
  - For Windows VMs, the VMSnapshot extension is installed.
  - For Linux VMs, the VMSnapshotLinux extension is installed.
3. For Windows VMs that are running, Backup coordinates with Windows Volume Shadow Copy Service (VSS) to take an app-consistent snapshot of the VM.
  - By default, Backup takes full VSS backups.
  - If Backup can't take an app-consistent snapshot, then it takes a file-consistent snapshot of the underlying storage (because no application writes occur while the VM is stopped).
4. For Linux VMs, Backup takes a file-consistent backup. For app-consistent snapshots, you need to manually customize pre/post scripts.
5. After Backup takes the snapshot, it transfers the data to the vault.
  - The backup is optimized by backing up each VM disk in parallel.
  - For each disk that's being backed up, Azure Backup reads the blocks on the disk and identifies and transfers only the data blocks that changed (the delta) since the previous backup.
  - Snapshot data might not be immediately copied to the vault. It might take some hours at peak times. Total backup time for a VM will be less than 24 hours for daily backup policies.
6. Changes made to a Windows VM after Azure Backup is enabled on it are:
  - Microsoft Visual C++ 2013 Redistributable(x64) - 12.0.40660 is installed in the VM
  - Startup type of Volume Shadow Copy service (VSS) changed to automatic from manual
  - IaaSVmProvider Windows service is added
7. When the data transfer is complete, the snapshot is removed, and a recovery point is created.

![Azure virtual machine backup architecture](https://docs.microsoft.com/en-us/azure/backup/media/backup-azure-vms-introduction/vmbackup-architecture.png)

### Encryption of Azure VM backups

When you back up Azure VMs with Azure Backup, VMs are encrypted at rest with Storage Service Encryption (SSE). Azure Backup can also back up Azure VMs that are encrypted by using Azure Disk Encryption.

|Encryption	|Details	|Support|
|:--|:--|:--|
|SSE	|With SSE, Azure Storage provides encryption at rest by automatically encrypting data before storing it. Azure Storage also decrypts data before retrieving it. Azure Backup supports backups of VMs with two types of Storage Service Encryption:<br /> - SSE with platform-managed keys: This encryption is by default for all disks in your VMs. See more here.<br /> - SSE with customer-managed keys. With CMK, you manage the keys used to encrypt the disks. See more here.|Azure Backup uses SSE for at-rest encryption of Azure VMs.|
|Azure Disk Encryption	|Azure Disk Encryption encrypts both OS and data disks for Azure VMs.<br /><br />Azure Disk Encryption integrates with BitLocker encryption keys (BEKs), which are safeguarded in a key vault as secrets. Azure Disk Encryption also integrates with Azure Key Vault key encryption keys (KEKs).	|Azure Backup supports backup of managed and unmanaged Azure VMs encrypted with BEKs only, or with BEKs together with KEKs.<br /><br />Both BEKs and KEKs are backed up and encrypted.<br /><br />Because KEKs and BEKs are backed up, users with the necessary permissions can restore keys and secrets back to the key vault if needed. These users can also recover the encrypted VM.<br /><br />Encrypted keys and secrets can't be read by unauthorized users or by Azure.|

For managed and unmanaged Azure VMs, Backup supports both VMs encrypted with BEKs only or VMs encrypted with BEKs together with KEKs.

The backed-up BEKs (secrets) and KEKs (keys) are encrypted. They can be read and used only when they're restored back to the key vault by authorized users. Neither unauthorized users, or Azure, can read or use backed-up keys or secrets.

BEKs are also backed up. So, if the BEKs are lost, authorized users can restore the BEKs to the key vault and recover the encrypted VMs. Only users with the necessary level of permissions can back up and restore encrypted VMs or keys and secrets.

### Snapshot creation

Azure Backup takes snapshots according to the backup schedule.

- **Windows VMs**: For Windows VMs, the Backup service coordinates with VSS to take an app-consistent snapshot of the VM disks. By default, Azure Backup takes a full VSS backup (it truncates the logs of application such as SQL Server at the time of backup to get application level consistent backup). If you're using a SQL Server database on Azure VM backup, then you can modify the setting to take a VSS Copy backup (to preserve logs). For more information, see this article.
- **Linux VMs**: To take app-consistent snapshots of Linux VMs, use the Linux pre-script and post-script framework to write your own custom scripts to ensure consistency.
  - Azure Backup invokes only the pre/post scripts written by you.
  - If the pre-scripts and post-scripts execute successfully, Azure Backup marks the recovery point as application-consistent. However, when you're using custom scripts, you're ultimately responsible for the application consistency.
  - Learn more about how to configure scripts.

### Snapshot consistency

The following table explains the different types of snapshot consistency:

|Snapshot	|Details	|Recovery	|Consideration|
|Application-consistent	|App-consistent backups capture memory content and pending I/O operations. App-consistent snapshots use a VSS writer (or pre/post scripts for Linux) to ensure the consistency of the app data before a backup occurs.|	When you're recovering a VM with an app-consistent snapshot, the VM boots up. There's no data corruption or loss. The apps start in a consistent state.	|Windows: All VSS writers succeeded<br /><br />Linux: Pre/post scripts are configured and succeeded|
|File-system consistent	|File-system consistent backups provide consistency by taking a snapshot of all files at the same time.|When you're recovering a VM with a file-system consistent snapshot, the VM boots up. There's no data corruption or loss. Apps need to implement their own "fix-up" mechanism to make sure that restored data is consistent.|Windows: Some VSS writers failed<br /><br />Linux: Default (if pre/post scripts aren't configured or failed)|
|Crash-consistent	|Crash-consistent snapshots typically occur if an Azure VM shuts down at the time of backup. Only the data that already exists on the disk at the time of backup is captured and backed up.	|Starts with the VM boot process followed by a disk check to fix corruption errors. Any in-memory data or write operations that weren't transferred to disk before the crash are lost. Apps implement their own data verification. For example, a database app can use its transaction log for verification. If the transaction log has entries that aren't in the database, the database software rolls transactions back until the data is consistent.	|VM is in shutdown (stopped/ deallocated) state.|
 
> Note
>
> If the provisioning state is succeeded, Azure Backup takes file-system consistent backups. If the provisioning state is unavailable or failed, crash-consistent backups are taken. If the provisioning state is creating or deleting, that means Azure Backup is retrying the operations.

### Best practices

When you're configuring VM backups, we suggest following these practices:

- Modify the default schedule times that are set in a policy. For example, if the default time in the policy is 12:00 AM, increment the timing by several minutes so that resources are optimally used.
- If you're restoring VMs from a single vault, we highly recommend that you use different general-purpose v2 storage accounts to ensure that the target storage account doesn't get throttled. For example, each VM must have a different storage account. For example, if 10 VMs are restored, use 10 different storage accounts.
- For backup of VMs that are using premium storage with Instant Restore, we recommend allocating 50% free space of the total allocated storage space, which is required only for the first backup. The 50% free space isn't a requirement for backups after the first backup is complete
- The limit on the number of disks per storage account is relative to how heavily the disks are being accessed by applications that are running on an infrastructure as a service (IaaS) VM. As a general practice, if 5 to 10 disks or more are present on a single storage account, balance the load by moving some disks to separate storage accounts.

### Backup costs

Azure VMs backed up with Azure Backup are subject to Azure Backup pricing.

Billing doesn't start until the first successful backup finishes. At this point, the billing for both storage and protected VMs begins. Billing continues as long as any backup data for the VM is stored in a vault. If you stop protection for a VM, but backup data for the VM exists in a vault, billing continues.

Billing for a specified VM stops only if the protection is stopped and all backup data is deleted. When protection stops and there are no active backup jobs, the size of the last successful VM backup becomes the protected instance size used for the monthly bill.

The protected-instance size calculation is based on the actual size of the VM. The VM's size is the sum of all the data in the VM, excluding the temporary storage. Pricing is based on the actual data that's stored on the data disks, not on the maximum supported size for each data disk that's attached to the VM.

Similarly, the backup storage bill is based on the amount of data that's stored in Azure Backup, which is the sum of the actual data in each recovery point.

For example, take an A2-Standard-sized VM that has two additional data disks with a maximum size of 32 TB each. The following table shows the actual data stored on each of these disks:

|Disk	|Max size	|Actual data present|
|:--|:--|:--|
|OS disk	|32 TB	|17 GB|
|Local/temporary disk	|135 GB|	5 GB (not included for backup)|
|Data disk 1	|32 TB|	30 GB|
|Data disk 2	|32 TB	|0 GB|

The actual size of the VM in this case is 17 GB + 30 GB + 0 GB = 47 GB. This protected-instance size (47 GB) becomes the basis for the monthly bill. As the amount of data in the VM grows, the protected-instance size used for billing changes to match.

[Get improved backup and restore performance with Azure Backup Instant Restore capability](https://docs.microsoft.com/en-us/azure/backup/backup-instant-restore-capability)

Based on feedback from users, we've renamed VM backup stack V2 to Instant Restore to reduce confusion with Azure Stack functionality. All Azure Backup users have now been upgraded to Instant Restore.

The new model for Instant Restore provides the following feature enhancements:

- Ability to use snapshots taken as part of a backup job that's available for recovery without waiting for data transfer to the vault to finish. It reduces the wait time for snapshots to copy to the vault before triggering restore.
- Reduces backup and restore times by retaining snapshots locally, for two days by default. This default snapshot retention value is configurable to any value between 1 to 5 days.
- Supports disk sizes up to 32 TB. Resizing of disks isn't recommended by Azure Backup.
- Supports Standard SSD disks along with Standard HDD disks and Premium SSD disks.
- Ability to use an unmanaged VMs original storage accounts (per disk), when restoring. This ability exists even when the VM has disks that are distributed across storage accounts. It speeds up restore operations for a wide variety of VM configurations.
- For backup of VMs that are using unmanaged premium disks in storage accounts, with Instant Restore, we recommend allocating 50% free space of the total allocated storage space, which is required only for the first backup. The 50% free space isn't a requirement for backups after the first backup is complete.

### What's new in this feature

Currently, the backup job consists of two phases:

1. Taking a VM snapshot.
2. Transferring a VM snapshot to the Azure Recovery Services vault.

A recovery point is considered created only after phases 1 and 2 are completed. As a part of this upgrade, a recovery point is created as soon as the snapshot is finished and this recovery point of snapshot type can be used to perform a restore using the same restore flow. You can identify this recovery point in the Azure portal by using “snapshot” as the recovery point type, and after the snapshot is transferred to the vault, the recovery point type changes to “snapshot and vault”.

![Backup job in VM backup stack Resource Manager deployment model--storage and vault](https://docs.microsoft.com/en-us/azure/backup/media/backup-azure-vms/instant-rp-flow.png)

By default, snapshots are retained for two days. This feature allows restore operation from these snapshots there by cutting down the restore times. It reduces the time required to transform and copy data back from the vault.

## implement disaster recovery

[Set up disaster recovery to a secondary Azure region for an Azure VM](https://docs.microsoft.com/en-us/azure/site-recovery/azure-to-azure-quickstart)

The Azure Site Recovery service contributes to your business continuity and disaster recovery (BCDR) strategy by keeping your business applications online during planned and unplanned outages. Site Recovery manages and orchestrates disaster recovery of on-premises machines and Azure virtual machines (VM), including replication, failover, and recovery.

This quickstart describes how to set up disaster recovery for an Azure VM by replicating it to a secondary Azure region. In general, default settings are used to enable replication.

### Prerequisites

To complete this tutorial, you need an Azure subscription and a VM.

- If you don't have an Azure account with an active subscription, you can create an account for free.
- A VM with a minimum 1 GB of RAM is recommended. Learn more about how to create a VM.

### Enable replication for the Azure VM

The following steps enable VM replication to a secondary location.

1. On the Azure portal, from Home > Virtual machines menu, select a VM to replicate.
2. In Operations select Disaster recovery.
3. From Basics > Target region, select the target region.
4. To view the replication settings, select Review + Start replication. If you need to change any defaults, select Advanced settings.
5. To start the job that enables VM replication select Start replication.

![Screenshot of disaster recovery](https://docs.microsoft.com/en-us/azure/site-recovery/media/azure-to-azure-quickstart/enable-replication1.png)

### Verify settings

After the replication job finishes, you can check the replication status, modify replication settings, and test the deployment.

1. On the Azure portal menu, select Virtual machines and select the VM that you replicated.
2. In Operations select Disaster recovery.
3. To view the replication details from the Overview select Essentials. More details are shown in the Health and status, Failover readiness, and the Infrastructure view map.

![Screenshot of health status](https://docs.microsoft.com/en-us/azure/site-recovery/media/azure-to-azure-quickstart/replication-status.png)

### Clean up resources

To stop replication of the VM in the primary region, you must disable replication:

- The source replication settings are cleaned up automatically.
- The Site Recovery extension installed on the VM during replication isn't removed.
- Site Recovery billing for the VM stops.

To disable replication, do these steps:

1. On the Azure portal menu, select Virtual machines and select the VM that you replicated.
2. In Operations select Disaster recovery.
3. From the Overview, select Disable Replication.
4. To uninstall the Site Recovery extension, go to the VM's Settings > Extensions.

## implement Azure Update Management

[Update Management overview](https://docs.microsoft.com/en-us/azure/automation/update-management/overview)

You can use Update Management in Azure Automation to manage operating system updates for your Windows and Linux virtual machines in Azure, in on-premises environments, and in other cloud environments. You can quickly assess the status of available updates on all agent machines and manage the process of installing required updates for servers.

> Note
>
> You can't use a machine configured with Update Management to run custom scripts from Azure Automation. This machine can only run the Microsoft-signed update script.

To download and install available Critical and Security patches automatically on your Azure VM, review Automatic VM guest patching for Windows VMs.

Before deploying Update Management and enabling your machines for management, make sure that you understand the information in the following sections.

### About Update Management

Machines that are managed by Update Management rely on the following to perform assessment and to deploy updates:

- Log Analytics agent for Windows or Linux
- PowerShell Desired State Configuration (DSC) for Linux
- Automation Hybrid Runbook Worker (automatically installed when you enable Update Management on the machine)
- Microsoft Update or Windows Server Update Services (WSUS) for Windows machines
- Either a private or public update repository for Linux machines

The following diagram illustrates how Update Management assesses and applies security updates to all connected Windows Server and Linux servers in a workspace:

![Update Management workflow](https://docs.microsoft.com/en-us/azure/automation/update-management/media/overview/update-mgmt-updateworkflow.png)

Update Management can be used to natively deploy to machines in multiple subscriptions in the same tenant.

You can deploy and install software updates on machines that require the updates by creating a scheduled deployment. Updates classified as optional aren't included in the deployment scope for Windows machines. Only required updates are included in the deployment scope.

The scheduled deployment defines which target machines receive the applicable updates. It does so either by explicitly specifying certain machines or by selecting a computer group that's based on log searches of a specific set of machines (or on an Azure query that dynamically selects Azure VMs based on specified criteria). These groups differ from scope configuration, which is used to control the targeting of machines that receive the configuration to enable Update Management. This prevents them from performing and reporting update compliance, and install approved required updates.

While defining a deployment, you also specify a schedule to approve and set a time period during which updates can be installed. This period is called the maintenance window. A 20-minute span of the maintenance window is reserved for reboots, assuming one is needed and you selected the appropriate reboot option. If patching takes longer than expected and there's less than 20 minutes in the maintenance window, a reboot won't occur.

Updates are installed by runbooks in Azure Automation. You can't view these runbooks, and they don't require any configuration. When an update deployment is created, it creates a schedule that starts a master update runbook at the specified time for the included machines. The master runbook starts a child runbook on each agent to install the required updates.

### Enable Update Management

Here are the ways that you can enable Update Management and select machines to be managed:

- Using an Azure Resource Manager template to deploy Update Management to a new or existing Automation account and Azure Monitor Log Analytics workspace in your subscription. It does not configure the scope of machines that should be managed, this is performed as a separate step after using the template.
- From your Automation account for one or more Azure and non-Azure machines, including Arc enabled servers.
- Using the Enable-AutomationSolution runbook method.
- For a selected Azure VM from the Virtual machines page in the Azure portal. This scenario is available for Linux and Windows VMs.
- For multiple Azure VMs by selecting them from the Virtual machines page in the Azure portal.

> Note
>
> Update Management requires linking a Log Analytics workspace to your Automation account. For a definitive list of supported regions, see Azure Workspace mappings. The region mappings don't affect the ability to manage VMs in a separate region from your Automation account.
