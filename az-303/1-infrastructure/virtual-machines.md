# Implement VMs for Windows and Linux

[Virtual Machines - Azure Docs](https://docs.microsoft.com/en-us/azure/virtual-machines/)

## configure High Availability

[Availability options for virtual machines in Azure](https://docs.microsoft.com/en-us/azure/virtual-machines/windows/availability)

### High availability

Workloads are typically spread across different virtual machines to gain high throughput, performance, and to create redundancy in case a VM is impacted due to an update or other event.

There are few options that Azure provides to achieve High Availability. First let’s talk about basic constructs.

### Availability zones

Availability zones expand the level of control you have to maintain the availability of the applications and data on your VMs. **An Availability Zone is a physically separate zone, within an Azure region.** There are three Availability Zones per supported Azure region.

Each Availability Zone has a distinct power source, network, and cooling. By architecting your solutions to use replicated VMs in zones, you can protect your apps and data from the loss of a datacenter. If one zone is compromised, then replicated apps and data are instantly available in another zone.

### Fault domains

**A fault domain is a logical group of underlying hardware that share a common power source and network switch, similar to a rack within an on-premises datacenter.**

### Update domains

**An update domain is a logical group of underlying hardware that can undergo maintenance or be rebooted at the same time.**

This approach ensures that at least one instance of your application always remains running as the Azure platform undergoes periodic maintenance. The order of update domains being rebooted may not proceed sequentially during maintenance, but only one update domain is rebooted at a time.

### Virtual Machines Scale Sets

Azure virtual machine scale sets let you create and manage a group of load balanced VMs. The number of VM instances can automatically increase or decrease in response to demand or a defined schedule. Scale sets provide high availability to your applications, and allow you to centrally manage, configure, and update many VMs. We recommended that two or more VMs are created within a scale set to provide for a highly available application and to meet the 99.95% Azure SLA. There is no cost for the scale set itself, you only pay for each VM instance that you create. When a single VM is using Azure premium SSDs, the Azure SLA applies for unplanned maintenance events. **Virtual machines in a scale set can be deployed across multiple update domains and fault domains to maximize availability and resilience to outages due to data center outages, and planned or unplanned maintenance events.** Virtual machines in a scale set can also be deployed into a single Availability zone, or regionally. Availability zone deployment options may differ based on the orchestration mode.

#### Fault domains and update domains

**Virtual machine scale sets simplify designing for high availability by aligning fault domains and update domains.** You will only have to define fault domains count for the scale set. The number of fault domains available to the scale sets may vary by region. See Manage the availability of virtual machines in Azure.

### Availability sets

**An availability set is a logical grouping of VMs within a datacenter** that allows Azure to understand how your application is built to provide for redundancy and availability. We recommended that two or more VMs are created within an availability set to provide for a highly available application and to meet the 99.95% Azure SLA. There is no cost for the Availability Set itself, you only pay for each VM instance that you create. When a single VM is using Azure premium SSDs, the Azure SLA applies for unplanned maintenance events.

In an availability set, VMs are automatically distributed across these fault domains. This approach limits the impact of potential physical hardware failures, network outages, or power interruptions.

For VMs using Azure Managed Disks, VMs are aligned with managed disk fault domains when using a managed availability set. This alignment ensures that all the managed disks attached to a VM are within the same managed disk fault domain.

**Only VMs with managed disks can be created in a managed availability set.** The number of managed disk fault domains varies by region - either two or three managed disk fault domains per region. You can read more about these managed disk fault domains for Linux VMs or Windows VMs.

### [Manage the availability of Windows virtual machines in Azure](https://docs.microsoft.com/en-us/azure/virtual-machines/windows/manage-availability)

### Understand VM Reboots - maintenance vs. downtime

There are three scenarios that can lead to virtual machine in Azure being impacted: unplanned hardware maintenance, unexpected downtime, and planned maintenance.

- **Unplanned Hardware Maintenance Event** occurs when the Azure platform predicts that the hardware or any platform component associated to a physical machine, is about to fail. When the platform predicts a failure, it will issue an unplanned hardware maintenance event to reduce the impact to the virtual machines hosted on that hardware. Azure uses Live Migration technology to migrate the Virtual Machines from the failing hardware to a healthy physical machine. Live Migration is a VM preserving operation that only pauses the Virtual Machine for a short time. Memory, open files, and network connections are maintained, but performance might be reduced before and/or after the event. In cases where Live Migration cannot be used, the VM will experience Unexpected Downtime, as described below.

- **An Unexpected Downtime** is when the hardware or the physical infrastructure for the virtual machine fails unexpectedly. This can include local network failures, local disk failures, or other rack level failures. When detected, the Azure platform automatically migrates (heals) your virtual machine to a healthy physical machine in the same datacenter. During the healing procedure, virtual machines experience downtime (reboot) and in some cases loss of the temporary drive. The attached OS and data disks are always preserved.

Virtual machines can also experience downtime in the unlikely event of an outage or disaster that affects an entire datacenter, or even an entire region. For these scenarios, Azure provides protection options including availability zones and paired regions.

- **Planned Maintenance events** are periodic updates made by Microsoft to the underlying Azure platform to improve overall reliability, performance, and security of the platform infrastructure that your virtual machines run on. Most of these updates are performed without any impact upon your Virtual Machines or Cloud Services (see Maintenance that doesn't require a reboot). While the Azure platform attempts to use VM Preserving Maintenance in all possible occasions, there are rare instances when these updates require a reboot of your virtual machine to apply the required updates to the underlying infrastructure. In this case, you can perform Azure Planned Maintenance with Maintenance-Redeploy operation by initiating the maintenance for their VMs in the suitable time window. For more information, see Planned Maintenance for Virtual Machines.

To reduce the impact of downtime due to one or more of these events, we recommend the following high availability best practices for your virtual machines:

- Use Availabiilty Zones to protect from datacenter failures
- Configure multiple virtual machines in an availability set for redundancy
- Use managed disks for VMs in an availability set
- Use scheduled events to proactively respond to VM impacting events
- Configure each application tier into separate availability sets
- Combine a load balancer with availability zones or sets
- Use availability zones to protect from datacenter level failures

### Use availability zones to protect from datacenter level failures

Availability zones expand the level of control you have to maintain the availability of the applications and data on your VMs. Availability Zones are unique physical locations within an Azure region. Each zone is made up of one or more datacenters equipped with independent power, cooling, and networking. To ensure resiliency, there are a minimum of three separate zones in all enabled regions. The physical separation of Availability Zones within a region protects applications and data from datacenter failures. Zone-redundant services replicate your applications and data across Availability Zones to protect from single-points-of-failure.

An Availability Zone in an Azure region is a combination of a fault domain and an update domain.

### Configure multiple virtual machines in an availability set for redundancy

Availability sets are another datacenter configuration to provide VM redundancy and availability. This configuration within a datacenter ensures that during either a planned or unplanned maintenance event, at least one virtual machine is available and meets the 99.95% Azure SLA. For more information, see the SLA for Virtual Machines.

> Important: A single instance virtual machine in an availability set by itself should use Premium SSD or Ultra Disk for all operating system disks and data disks in order to qualify for the SLA for Virtual Machine connectivity of at least 99.9%.
>
> A single instance virtual machine with a Standard SSD will have an SLA of at least 99.5%, while a single instance virtual machine with a Standard HDD will have an SLA of at least 95%. See SLA for Virtual Machines

Each virtual machine in your availability set is assigned an update domain and a fault domain by the underlying Azure platform

### Use managed disks for VMs in an availability set

If you are currently using VMs with unmanaged disks, we highly recommend you covert from unmanaged to managed disks for Linux and Windows.

Managed disks provide better reliability for Availability Sets by ensuring that the disks of VMs in an Availability Set are sufficiently isolated from each other to avoid single points of failure. It does this by automatically placing the disks in different storage fault domains (storage clusters) and aligning them with the VM fault domain. If a storage fault domain fails due to hardware or software failure, only the VM instance with disks on the storage fault domain fails.

### Use scheduled events to proactively respond to VM impacting events

When you subscribe to scheduled events, your VM is notified about upcoming maintenance events that can impact your VM. When scheduled events are enabled, your virtual machine is given a minimum amount of time before the maintenance activity is performed.

### Combine a load balancer with availability zones or sets

Combine the Azure Load Balancer with an availability zone or set to get the most application resiliency. The Azure Load Balancer distributes traffic between multiple virtual machines. For our Standard tier virtual machines, the Azure Load Balancer is included. Not all virtual machine tiers include the Azure Load Balancer. For more information about load balancing your virtual machines, see Load Balancing virtual machines for Linux or Windows.

## configure storage for VMs

### [Attach a managed data disk to a Windows VM by using the Azure portal](https://docs.microsoft.com/en-us/azure/virtual-machines/windows/attach-managed-disk-portal)

#### Add a data disk

1. Go to the Azure portal to add a data disk. Search for and select Virtual machines.
2. Select a virtual machine from the list.
3. On the Virtual machine page, select Disks.
4. On the Disks page, select Add data disk.
5. In the drop-down for the new disk, select Create disk.
6. In the Create managed disk page, type in a name for the disk and adjust the other settings as necessary. When you're done, select Create.
7. In the Disks page, select Save to save the new disk configuration for the VM.
8. After Azure creates the disk and attaches it to the virtual machine, the new disk is listed in the virtual machine's disk settings under Data disks.

#### Initialize a new data disk

1. Connect to the VM.
2. Select the Windows Start menu inside the running VM and enter diskmgmt.msc in the search box. The Disk Management console opens.
3. Disk Management recognizes that you have a new, uninitialized disk and the Initialize Disk window appears.
4. Verify the new disk is selected and then select OK to initialize it.
5. The new disk appears as unallocated. Right-click anywhere on the disk and select New simple volume. The New Simple Volume Wizard window opens.
6. Proceed through the wizard, keeping all of the defaults, and when you're done select Finish.
7. Close Disk Management.
8. A pop-up window appears notifying you that you need to format the new disk before you can use it. Select Format disk.
9. In the Format new disk window, check the settings, and then select Start.
10. A warning appears notifying you that formatting the disks erases all of the data. Select OK.
11. When the formatting is complete, select OK.

### [Attach a data disk to a Windows VM with PowerShell](https://docs.microsoft.com/en-us/azure/virtual-machines/windows/attach-disk-ps)

First, review these tips:

- The size of the virtual machine controls how many data disks you can attach. For more information, see Sizes for virtual machines.
- To use premium SSDs, you'll need a premium storage-enabled VM type, like the DS-series or GS-series virtual machine.

#### Add an empty data disk to a virtual machine

This example shows how to add an empty data disk to an existing virtual machine.

##### Using managed disks

    $rgName = 'myResourceGroup'
    $vmName = 'myVM'
    $location = 'East US' 
    $storageType = 'Premium_LRS'
    $dataDiskName = $vmName + '_datadisk1'

    $diskConfig = New-AzDiskConfig -SkuName $storageType -Location $location -CreateOption Empty -DiskSizeGB 128
    $dataDisk1 = New-AzDisk -DiskName $dataDiskName -Disk $diskConfig -ResourceGroupName $rgName

    $vm = Get-AzVM -Name $vmName -ResourceGroupName $rgName 
    $vm = Add-AzVMDataDisk -VM $vm -Name $dataDiskName -CreateOption Attach -ManagedDiskId $dataDisk1.Id -Lun 1

    Update-AzVM -VM $vm -ResourceGroupName $rgName

#### Initialize the disk

After you add an empty disk, you'll need to initialize it. To initialize the disk, you can sign in to a VM and use disk management. If you enabled WinRM and a certificate on the VM when you created it, you can use remote PowerShell to initialize the disk. You can also use a custom script extension:

    $location = "location-name"
    $scriptName = "script-name"
    $fileName = "script-file-name"
    Set-AzVMCustomScriptExtension -ResourceGroupName $rgName -Location $locName -VMName $vmName -Name $scriptName -TypeHandlerVersion "1.4" -StorageAccountName "mystore1" -StorageAccountKey "primary-key" -FileName $fileName -ContainerName "scripts"

The script file can contain code to initialize the disks, for example:

    $disks = Get-Disk | Where partitionstyle -eq 'raw' | sort number

    $letters = 70..89 | ForEach-Object { [char]$_ }
    $count = 0
    $labels = "data1","data2"

    foreach ($disk in $disks) {
        $driveLetter = $letters[$count].ToString()
        $disk | 
        Initialize-Disk -PartitionStyle MBR -PassThru |
        New-Partition -UseMaximumSize -DriveLetter $driveLetter |
        Format-Volume -FileSystem NTFS -NewFileSystemLabel $labels[$count] -Confirm:$false -Force
	$count++
    }

##### Using managed disks in an Availability Zone

To create a disk in an Availability Zone, use New-AzDiskConfig with the -Zone parameter. The following example creates a disk in zone 1.

    $diskConfig = New-AzDiskConfig -SkuName $storageType -Location $location -CreateOption Empty -DiskSizeGB 128 -Zone 1

#### Attach an existing data disk to a VM

You can attach an existing managed disk to a VM as a data disk.

    $rgName = "myResourceGroup"
    $vmName = "myVM"
    $location = "East US" 
    $dataDiskName = "myDisk"
    $disk = Get-AzDisk -ResourceGroupName $rgName -DiskName $dataDiskName 
    
    $vm = Get-AzVM -Name $vmName -ResourceGroupName $rgName 
    
    $vm = Add-AzVMDataDisk -CreateOption Attach -Lun 0 -VM $vm -ManagedDiskId $disk.Id
    
    Update-AzVM -VM $vm -ResourceGroupName $rgName

### [What disk types are available in Azure?](https://docs.microsoft.com/en-us/azure/virtual-machines/windows/disks-types)

#### Disk comparison

The following table provides a comparison of ultra disks, premium solid-state drives (SSD), standard SSD, and standard hard disk drives (HDD) for managed disks to help you decide what to use.

|Detail	|Ultra disk	|Premium SSD	|Standard SSD	|Standard HDD|
|:--|:--|:--|:--|:--|
|Disk type	|SSD	|SSD	|SSD	|HDD|
|Scenario	|IO-intensive workloads such as SAP HANA, top tier databases (for example, SQL, Oracle), and other transaction-heavy workloads.	|Production and performance sensitive workloads	|Web servers, lightly used enterprise applications and dev/test	|Backup, non-critical, infrequent access|
|Max disk size	|65,536 gibibyte (GiB)	|32,767 GiB	|32,767 GiB	|32,767 GiB|
|Max throughput	|2,000 MB/s	|900 MB/s	|750 MB/s	|500 MB/s|
|Max IOPS	|160,000	|20,000|	6,000	|2,000|

#### Ultra disk

Azure ultra disks deliver high throughput, high IOPS, and consistent low latency disk storage for Azure IaaS VMs. Some additional benefits of ultra disks include the ability to dynamically change the performance of the disk, along with your workloads, without the need to restart your virtual machines (VM). Ultra disks are suited for data-intensive workloads such as SAP HANA, top tier databases, and transaction-heavy workloads. Ultra disks can only be used as data disks. We recommend using premium SSDs as OS disks.

[Ultra Disk Sizes](https://docs.microsoft.com/en-us/azure/virtual-machines/disks-types#disk-size)

#### Premium SSD

Azure premium SSDs deliver high-performance and low-latency disk support for virtual machines (VMs) with input/output (IO)-intensive workloads. To take advantage of the speed and performance of premium storage disks, you can migrate existing VM disks to Premium SSDs. Premium SSDs are suitable for mission-critical production applications. Premium SSDs can only be used with VM series that are premium storage-compatible.

[Premium SSD Sizes](https://docs.microsoft.com/en-us/azure/virtual-machines/disks-types#disk-size-1)

When you provision a premium storage disk, unlike standard storage, you are guaranteed the capacity, IOPS, and throughput of that disk.

#### Standard SSD

Azure standard SSDs are a cost-effective storage option optimized for workloads that need consistent performance at lower IOPS levels. Standard SSD offers a good entry level experience for those who wish to move to the cloud, especially if you experience issues with the variance of workloads running on your HDD solutions on premises. Compared to standard HDDs, standard SSDs deliver better availability, consistency, reliability, and latency. Standard SSDs are suitable for Web servers, low IOPS application servers, lightly used enterprise applications, and Dev/Test workloads. Like standard HDDs, standard SSDs are available on all Azure VMs.

[Standard SSD Sizes](https://docs.microsoft.com/en-us/azure/virtual-machines/disks-types#disk-size-2)

Standard SSDs are designed to provide single-digit millisecond latencies and the IOPS and throughput up to the limits described in the preceding table 99% of the time. Actual IOPS and throughput may vary sometimes depending on the traffic patterns. Standard SSDs will provide more consistent performance than the HDD disks with the lower latency.

#### Standard HDD

Azure standard HDDs deliver reliable, low-cost disk support for VMs running latency-insensitive workloads. With standard storage, the data is stored on hard disk drives (HDDs). Latency, IOPS, and Throughput of Standard HDD disks may vary more widely as compared to SSD-based disks. Standard HDD Disks are designed to deliver write latencies under 10ms and read latencies under 20ms for most IO operations, however the actual performance may vary depending on the IO size and workload pattern. When working with VMs, you can use standard HDD disks for dev/test scenarios and less critical workloads. Standard HDDs are available in all Azure regions and can be used with all Azure VMs.

[Standard HDD Sizes](https://docs.microsoft.com/en-us/azure/virtual-machines/disks-types#disk-size-3)

#### Billing

When using managed disks, the following billing considerations apply:

- Disk type
- Managed disk size
- Snapshots
- Outbound data transfers
- Number of transactions

#### Azure disk reservation

Disk reservation is the option to purchase one year of disk storage in advance at a discount, reducing your total cost.

## select virtual machine size

### [Sizes for Windows virtual machines in Azure](https://docs.microsoft.com/en-us/azure/virtual-machines/windows/sizes)

[Azure virtual machine sizes naming conventions](https://docs.microsoft.com/en-us/azure/virtual-machines/vm-naming-conventions)

|Type	|Sizes	|Description|
|:--|:--|:--|
|[General purpose](https://docs.microsoft.com/en-us/azure/virtual-machines/sizes-general)	|B, Dsv3, Dv3, Dasv4, Dav4, DSv2, Dv2, Av2, DC, DCv2, Dv4, Dsv4, Ddv4, Ddsv4	|Balanced CPU-to-memory ratio. Ideal for testing and development, small to medium databases, and low to medium traffic web servers.|
|[Compute optimized](https://docs.microsoft.com/en-us/azure/virtual-machines/sizes-compute)|	F, Fs, Fsv2	|High CPU-to-memory ratio. Good for medium traffic web servers, network appliances, batch processes, and application servers.|
|[Memory optimized](https://docs.microsoft.com/en-us/azure/virtual-machines/sizes-memory)|	Esv3, Ev3, Easv4, Eav4, Ev4, Esv4, Edv4, Edsv4, Mv2, M, DSv2, Dv2	|High memory-to-CPU ratio. Great for relational database servers, medium to large caches, and in-memory analytics.|
|[Storage optimized](https://docs.microsoft.com/en-us/azure/virtual-machines/sizes-storage)	|Lsv2	|High disk throughput and IO ideal for Big Data, SQL, NoSQL databases, data warehousing and large transactional databases.|
|[GPU](https://docs.microsoft.com/en-us/azure/virtual-machines/sizes-gpu)	|NC, NCv2, NCv3, NCasT4_v3 (Preview), ND, NDv2 (Preview), NV, NVv3, NVv4	|Specialized virtual machines targeted for heavy graphic rendering and video editing, as well as model training and inferencing (ND) with deep learning. Available with single or multiple GPUs.|
|[High performance compute](https://docs.microsoft.com/en-us/azure/virtual-machines/sizes-hpc)	|HB, HBv2, HC, H	|Our fastest and most powerful CPU virtual machines with optional high-throughput network interfaces (RDMA).|

#### REST API

For information on using the REST API to query for VM sizes, see the following:

- [List available virtual machine sizes for resizing](https://docs.microsoft.com/en-us/rest/api/compute/virtualmachines/listavailablesizes)
- [List available virtual machine sizes for a subscription](https://docs.microsoft.com/en-us/rest/api/compute/resourceskus/list)
- [List available virtual machine sizes in an availability set](https://docs.microsoft.com/en-us/rest/api/compute/availabilitysets/listavailablesizes)

#### ACU

Learn more about how [Azure compute units (ACU)](https://docs.microsoft.com/en-us/azure/virtual-machines/acu) can help you compare compute performance across Azure SKUs.

#### Pricing

[Windows VM Pricing](https://azure.microsoft.com/en-gb/pricing/details/virtual-machines/windows/#Windows)

### [Sizes for Linux virtual machines in Azure](https://docs.microsoft.com/en-us/azure/virtual-machines/linux/sizes) (same as above)

#### Pricing

[Linux VM Pricing](https://azure.microsoft.com/pricing/details/virtual-machines/#Linux)

## implement Azure Dedicated Hosts

### [Azure Dedicated Hosts](https://docs.microsoft.com/en-us/azure/virtual-machines/windows/dedicated-hosts)

Azure Dedicated Host is a service that provides physical servers - able to host one or more virtual machines - dedicated to one Azure subscription. Dedicated hosts are the same physical servers used in our data centers, provided as a resource. You can provision dedicated hosts within a region, availability zone, and fault domain. Then, you can place VMs directly into your provisioned hosts, in whatever configuration best meets your needs.

#### Benefits

Reserving the entire host provides the following benefits:

- Hardware isolation at the physical server level. No other VMs will be placed on your hosts. Dedicated hosts are deployed in the same data centers and share the same network and underlying storage infrastructure as other, non-isolated hosts.
- Control over maintenance events initiated by the Azure platform. While the majority of maintenance events have little to no impact on your virtual machines, there are some sensitive workloads where each second of pause can have an impact. With dedicated hosts, you can opt-in to a maintenance window to reduce the impact to your service.
- With the Azure hybrid benefit, you can bring your own licenses for Windows and SQL to Azure. Using the hybrid benefits provides you with additional benefits. For more information, see Azure Hybrid Benefit.

#### Groups, hosts, and VMs

A host group is a resource that represents a collection of dedicated hosts. You create a host group in a region and an availability zone, and add hosts to it.

A host is a resource, mapped to a physical server in an Azure data center. The physical server is allocated when the host is created. A host is created within a host group. A host has a SKU describing which VM sizes can be created. Each host can host multiple VMs, of different sizes, as long as they are from the same size series.

#### High Availability considerations

For high availability, you should deploy multiple VMs, spread across multiple hosts (minimum of 2). With Azure Dedicated Hosts, you have several options to provision your infrastructure to shape your fault isolation boundaries.

#### Use Availability Zones for fault isolation

Availability zones are unique physical locations within an Azure region. Each zone is made up of one or more datacenters equipped with independent power, cooling, and networking. A host group is created in a single availability zone. Once created, all hosts will be placed within that zone. To achieve high availability across zones, you need to create multiple host groups (one per zone) and spread your hosts accordingly.

If you assign a host group to an availability zone, all VMs created on that host must be created in the same zone.

#### Use Fault Domains for fault isolation

A host can be created in a specific fault domain. Just like VM in a scale set or availability set, hosts in different fault domains will be placed on different physical racks in the data center. When you create a host group, you are required to specify the fault domain count. When creating hosts within the host group, you assign fault domain for each host. The VMs do not require any fault domain assignment.

Fault domains are not the same as colocation. Having the same fault domain for two hosts does not mean they are in proximity with each other.

Fault domains are scoped to the host group. You should not make any assumption on anti-affinity between two host groups (unless they are in different availability zones).

VMs deployed to hosts with different fault domains, will have their underlying managed disks services on multiple storage stamps, to increase the fault isolation protection.

#### Using Availability Zones and Fault Domains

You can use both capabilities together to achieve even more fault isolation. In this case, you will specify the availability zone and fault domain count in for each host group, assign a fault domain to each of your hosts in the group, and assign an availability zone to each of your VMs

The Resource Manager sample template found [here](https://github.com/Azure/azure-quickstart-templates/blob/master/201-vm-dedicated-hosts/README.md) uses zones and fault domains to spread hosts for maximum resiliency in a region.

#### Manual vs. automatic placement

When creating a VM in Azure, you can select which dedicated host to use. You can also use the option to automatically place your VMs on existing hosts, within a host group.

When creating a new host group, make sure the setting for automatic VM placement is selected. When creating your VM, select the host group and let Azure pick the best host for your VM.

Host groups that are enabled for automatic placement do not require all the VMs to be automatically placed. You will still be able to explicitly pick a host, even when automatic placement is selected for the host group.

#### Limitations

Known issues and limitations when using automatic VM placement:

- You will not be able to apply Azure Hybrid Benefits on your dedicated hosts.
- You will not be able to redeploy your VM.
- You will not be able to control maintenance for your dedicated hosts.
- You will not be able to use Lsv2, NVasv4, NVsv3, Msv2, or M-series VMs with dedicated hosts

#### Virtual machine scale set support (public preview)

Virtual machine scale sets let you treat a group of virtual machines as a single resource, and apply availability, management, scaling and orchestration policies as a group. Your existing dedicated hosts can also be used for virtual machine scale sets.

#### Pricing

Users are charged per dedicated host, regardless how many VMs are deployed. In your monthly statement you will see a new billable resource type of hosts. The VMs on a dedicated host will still be shown in your statement, but will carry a price of 0.

The host price is set based on VM family, type (hardware size), and region. A host price is relative to the largest VM size supported on the host.

Software licensing, storage and network usage are billed separately from the host and VMs. There is no change to those billable items.

For more information, see [Azure Dedicated Host pricing](https://aka.ms/ADHPricing).

### [Deploy VMs to dedicated hosts using the portal](https://docs.microsoft.com/en-us/azure/virtual-machines/windows/dedicated-hosts-portal)

#### Create a host group

A host group is a resource that represents a collection of dedicated hosts. You create a host group in a region and an availability zone, and add hosts to it. When planning for high availability, there are additional options. You can use one or both of the following options with your dedicated hosts:

- Span across multiple availability zones. In this case, you are required to have a host group in each of the zones you wish to use.
- Span across multiple fault domains which are mapped to physical racks.

In either case, you are need to provide the fault domain count for your host group. If you do not want to span fault domains in your group, use a fault domain count of 1.

You can also decide to use both availability zones and fault domains.

In this example, we will create a host group using 1 availability zone and 2 fault domains.

1. Open the Azure portal. If you would like to try the preview for Automatic placement, use this URL: https://aka.ms/vmssadh.
2. Select Create a resource in the upper left corner.
3. Search for Host group and then select Host Groups from the results.
4. In the Host Groups page, select Create.
5. Select the subscription you would like to use, and then select Create new to create a new resource group.
6. Type myDedicatedHostsRG as the Name and then select OK.
7. For Host group name, type myHostGroup.
8. For Location, select East US.
9. For Availability Zone, select 1.
10. For Fault domain count, select 2.
11. If you used the Automatic placement URL, select this option to automatically assign VMs and scale set instances to an available host in this group.
12. Select Review + create and then wait for validation.
13. Once you see the Validation passed message, select Create to create the host group.

It should only take a few moments to create the host group.

#### Create a dedicated host

Now create a dedicated host in the host group. In addition to a name for the host, you are required to provide the SKU for the host. Host SKU captures the supported VM series as well as the hardware generation for your dedicated host.

For more information about the host SKUs and pricing, see [Azure Dedicated Host pricing](https://aka.ms/ADHPricing).

If you set a fault domain count for your host group, you will be asked to specify the fault domain for your host.

1. Select Create a resource in the upper left corner.
2. Search for Dedicated host and then select Dedicated hosts from the results.
3. In the Dedicated Hosts page, select Create.
4. Select the subscription you would like to use.
5. Select myDedicatedHostsRG as the Resource group.
6. In Instance details, type myHost for the Name and select East US for the location.
7. In Hardware profile, select Standard Es3 family - Type 1 for the Size family, select myHostGroup for the Host group and then select 1 for the Fault domain. Leave the defaults for the rest of the fields.
8. When you are done, select Review + create and wait for validation.
9. Once you see the Validation passed message, select Create to create the host.

#### Create a VM

1. Choose Create a resource in the upper left corner of the Azure portal.
2. In the search box above the list of Azure Marketplace resources, search for and select the image you want use, then choose Create.
3. In the Basics tab, under Project details, make sure the correct subscription is selected and then select myDedicatedHostsRG as the Resource group.
4. Under Instance details, type myVM for the Virtual machine name and choose East US for your Location.
5. In Availability options select Availability zone, select 1 from the drop-down.
6. For the size, select Change size. In the list of available sizes, choose one from the Esv3 series, like Standard E2s v3. You may need to clear the filter in order to see all of the available sizes.
7. Complete the rest of the fields on the Basics tab as needed.
8. At the top of the page, select the Advanced tab and in the Host section, select myHostGroup for Host group and myHost for the Host. 
9. Leave the remaining defaults and then select the Review + create button at the bottom of the page.
10. When you see the message that validation has passed, select Create.

It will take a few minutes for your VM to be deployed.

#### Create a scale set (preview)

When you deploy a scale set, you specify the host group.

1. Search for Scale set and select Virtual machine scale sets from the list.
2. Select Add to create a new scale set.
3. Complete the fields on the Basics tab as you usually would, but make sure you select a VM size that is from the series you chose for your dedicated host, like Standard E2s v3.
4. On the Advanced tab, for Spreading algorithm select Max spreading.
5. In Host group, select the host group from the drop-down. If you recently created the group, it might take a minute to get added to the list.

#### Add an existing VM

You can add an exiting VM to a dedicated host, but the VM must first be Stop\Deallocated. Before you move a VM to a dedicated host, make sure that the VM configuration is supported:

- The VM size must be in the same size family as the dedicated host. For example, if your dedicated host is DSv3, then the VM size could be Standard_D4s_v3, but it could not be a Standard_A4_v2.
- The VM needs to be located in same region as the dedicated host.
- The VM can't be part of a proximity placement group. Remove the VM from the proximity placement group before moving it to a dedicated host. For more information, see Move a VM out of a proximity placement group
- The VM can't be in an availability set.
- If the VM is in an availability zone, it must be the same availability zone as the host group. The availability zone settings for the VM and the host group must match.

Move the VM to a dedicated host using the portal.

1. Open the page for the VM.
2. Select Stop to stop\deallocate the VM.
3. Select Configuration from the left menu.
4. Select a host group and a host from the drop-down menus.
5. When you are done, select Save at the top of the page.
6. After the VM has been added to the host, select Overview from the left menu.
7. At the top of the page, select Start to restart the VM.

## deploy and configure scale sets

### [What are virtual machine scale sets?](https://docs.microsoft.com/en-us/azure/virtual-machine-scale-sets/overview)

Azure virtual machine scale sets let you create and manage a group of load balanced VMs. The number of VM instances can automatically increase or decrease in response to demand or a defined schedule. Scale sets provide high availability to your applications, and allow you to centrally manage, configure, and update a large number of VMs. With virtual machine scale sets, you can build large-scale services for areas such as compute, big data, and container workloads.

#### Why use virtual machine scale sets?

- Easy to create and manage multiple VMs
  - When you have many VMs that run your application, it's important to maintain a consistent configuration across your environment. For reliable performance of your application, the VM size, disk configuration, and application installs should match across all VMs.
  - With scale sets, all VM instances are created from the same base OS image and configuration. This approach lets you easily manage hundreds of VMs without additional configuration tasks or network management.
  - Scale sets support the use of the Azure load balancer for basic layer-4 traffic distribution, and Azure Application Gateway for more advanced layer-7 traffic distribution and TLS termination.
- Provides high availability and application resiliency
  - Scale sets are used to run multiple instances of your application. If one of these VM instances has a problem, customers continue to access your application through one of the other VM instances with minimal interruption.
  - For additional availability, you can use Availability Zones to automatically distribute VM instances in a scale set within a single datacenter or across multiple datacenters.
- Allows your application to automatically scale as resource demand changes
  - Customer demand for your application may change throughout the day or week. To match customer demand, scale sets can automatically increase the number of VM instances as application demand increases, then reduce the number of VM instances as demand decreases.
  - Autoscale also minimizes the number of unnecessary VM instances that run your application when demand is low, while customers continue to receive an acceptable level of performance as demand grows and additional VM instances are automatically added. This ability helps reduce costs and efficiently create Azure resources as required.
- Works at large-scale
  - Scale sets support up to 1,000 VM instances. If you create and upload your own custom VM images, the limit is 600 VM instances.
  - For the best performance with production workloads, use Azure Managed Disks.

#### Differences between virtual machines and scale sets

Scale sets are built from virtual machines. With scale sets, the management and automation layers are provided to run and scale your applications. You could instead manually create and manage individual VMs, or integrate existing tools to build a similar level of automation. The following table outlines the benefits of scale sets compared to manually managing multiple VM instances.

|Scenario	|Manual group of VMs	|Virtual machine scale set|
|:--|:--|:--|
|Add additional VM instances	|Manual process to create, configure, and ensure compliance	|Automatically create from central configuration|
|Traffic balancing and distribution	|Manual process to create and configure Azure load balancer or Application Gateway	|Can automatically create and integrate with Azure load balancer or Application Gateway|
|High availability and redundancy	|Manually create Availability Set or distribute and track VMs across Availability Zones	|Automatic distribution of VM instances across Availability Zones or Availability Sets|
|Scaling of VMs	|Manual monitoring and Azure Automation	|Autoscale based on host metrics, in-guest metrics, Application Insights, or schedule|

There is no additional cost to scale sets. You only pay for the underlying compute resources such as the VM instances, load balancer, or Managed Disk storage. The management and automation features, such as autoscale and redundancy, incur no additional charges over the use of VMs.

#### How to monitor your scale sets

Use Azure Monitor for VMs, which has a simple onboarding process and will automate the collection of important CPU, memory, disk, and network performance counters from the VMs in your scale set. It also includes additional monitoring capabilities and pre-defined visualizations that help you focus on the availability and performance of your scale sets.

Enable monitoring for your virtual machine scale set application with Application Insights to collect detailed information about your application including page views, application requests, and exceptions. Further verify the availability of your application by configuring an availability test to simulate user traffic.

#### [Create a VM scale set in the Azure Portal](https://docs.microsoft.com/en-us/azure/virtual-machine-scale-sets/quick-create-portal)

- Create a load balancer
- Create a virtual machine scale set
  - Type Scale set in the search box. In the results, under Marketplace, select Virtual machine scale sets. Select Create on the Virtual machine scale sets page, which will open the Create a virtual machine scale set page.
  - Leave the default value of ScaleSet VMs for Orchestration mode.
  - Select a marketplace image for Image. In this example, we have chosen Ubuntu Server 18.04 LTS.
  - On the Networking page, under Load balancing, select Yes to put the scale set instances behind a load balancer.
  - In Load balancing options, select Azure load balancer.
  - In Select a load balancer, select myLoadBalancer that you created earlier.
  - For Select a backend pool, select Create new, type myBackendPool, then select Create.

## configure Azure Disk Encryption

### [Azure Disk Encryption for Linux VMs](https://docs.microsoft.com/en-us/azure/virtual-machines/linux/disk-encryption-overview)

Azure Disk Encryption helps protect and safeguard your data to meet your organizational security and compliance commitments. It uses the DM-Crypt feature of Linux to provide volume encryption for the OS and data disks of Azure virtual machines (VMs), and is integrated with Azure Key Vault to help you control and manage the disk encryption keys and secrets.

If you use Azure Security Center, you're alerted if you have VMs that aren't encrypted. The alerts show as High Severity and the recommendation is to encrypt these VMs.

- [Create and encrypt a Linux VM with Azure CLI quickstart](https://docs.microsoft.com/en-us/azure/virtual-machines/linux/disk-encryption-cli-quickstart)
- [Create and encrypt a Linux VM with Azure PowerShell quickstart](https://docs.microsoft.com/en-us/azure/virtual-machines/linux/disk-encryption-powershell-quickstart)

#### Supported VMs

Linux VMs are available in a range of sizes. Azure Disk Encryption is not available on Basic, A-series VMs, or on virtual machines that do not meet these minimum memory requirements:

|Virtual machine	|Minimum memory requirement|
|:--|:--|
|Linux VMs when only encrypting data volumes	|2 GB|
|Linux VMs when encrypting both data and OS volumes, and where the root (/) file system usage is 4GB or less	|8 GB|
|Linux VMs when encrypting both data and OS volumes, and where the root (/) file system usage is greater than 4GB	|The root file system usage * 2. For instance, a 16 GB of root file system usage requires at least 32GB of RAM|

Once the OS disk encryption process is complete on Linux virtual machines, the VM can be configured to run with less memory.

Azure Disk Encryption is also available for VMs with premium storage.

Azure Disk Encryption is not available on Generation 2 VMs and Lsv2-series VMs. For more exceptions, see [Azure Disk Encryption: Unsupported scenarios.](https://docs.microsoft.com/en-us/azure/virtual-machines/linux/disk-encryption-linux#unsupported-scenarios)

#### Supported operating systems

Azure Disk Encryption is supported on a subset of the Azure-endorsed Linux distributions, which is itself a subset of all Linux server possible distributions.

#### Encryption key storage requirements

Azure Disk Encryption requires an Azure Key Vault to control and manage disk encryption keys and secrets. Your key vault and VMs must reside in the same Azure region and subscription.

For details, see [Creating and configuring a key vault for Azure Disk Encryption](https://docs.microsoft.com/en-us/azure/virtual-machines/linux/disk-encryption-key-vault).

### [Azure Disk Encryption for Windows VMs](https://docs.microsoft.com/en-us/azure/virtual-machines/windows/disk-encryption-overview)

Azure Disk Encryption helps protect and safeguard your data to meet your organizational security and compliance commitments. It uses the Bitlocker feature of Windows to provide volume encryption for the OS and data disks of Azure virtual machines (VMs), and is integrated with Azure Key Vault to help you control and manage the disk encryption keys and secrets.

If you use Azure Security Center, you're alerted if you have VMs that aren't encrypted. The alerts show as High Severity and the recommendation is to encrypt these VMs.

- [Create and encrypt a Windows VM with Azure CLI quickstart](https://docs.microsoft.com/en-us/azure/virtual-machines/windows/disk-encryption-cli-quickstart)
- [Create and encrypt a Windows VM with Azure Powershell quickstart](https://docs.microsoft.com/en-us/azure/virtual-machines/windows/disk-encryption-powershell-quickstart)

#### Supported VMs

Windows VMs are available in a range of sizes. Azure Disk Encryption is not available on Basic, A-series VMs, or on virtual machines with a less than 2 GB of memory.

Azure Disk Encryption is also available for VMs with premium storage.

Azure Disk Encryption is not available on Generation 2 VMs and Lsv2-series VMs. For more exceptions, see [Azure Disk Encryption: Unsupported scenarios](https://docs.microsoft.com/en-us/azure/virtual-machines/windows/disk-encryption-windows#unsupported-scenarios).

#### Supported operating systems

- Windows client: Windows 8 and later.
- Windows Server: Windows Server 2008 R2 and later.

> Note:
>
> Windows Server 2008 R2 requires the .NET Framework 4.5 to be installed for encryption; install it from Windows Update with the optional update Microsoft .NET Framework 4.5.2 for Windows Server 2008 R2 x64-based systems (KB2901983).
>
> Windows Server 2012 R2 Core and Windows Server 2016 Core requires the bdehdcfg component to be installed on the VM for encryption.

#### Group Policy requirements

Azure Disk Encryption uses the BitLocker external key protector for Windows VMs. For domain joined VMs, don't push any group policies that enforce TPM protectors. For information about the group policy for "Allow BitLocker without a compatible TPM," see BitLocker Group Policy Reference.

BitLocker policy on domain joined virtual machines with custom group policy must include the following setting: Configure user storage of BitLocker recovery information -> Allow 256-bit recovery key. Azure Disk Encryption will fail when custom group policy settings for BitLocker are incompatible. On machines that didn't have the correct policy setting, apply the new policy, force the new policy to update (gpupdate.exe /force), and then restarting may be required.

Azure Disk Encryption will fail if domain level group policy blocks the AES-CBC algorithm, which is used by BitLocker.

#### Encryption key storage requirements

Azure Disk Encryption requires an Azure Key Vault to control and manage disk encryption keys and secrets. Your key vault and VMs must reside in the same Azure region and subscription.

For details, see [Creating and configuring a key vault for Azure Disk Encryption](https://docs.microsoft.com/en-us/azure/virtual-machines/windows/disk-encryption-key-vault).
