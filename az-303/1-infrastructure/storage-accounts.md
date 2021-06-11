# Implement Storage Accounts

## select storage account options based on a use case

### [Storage account overview - Azure Docs](https://docs.microsoft.com/en-us/azure/storage/common/storage-account-overview)

[Introduction to Azure Storage](https://docs.microsoft.com/en-us/azure/storage/common/storage-introduction)

#### Types of storage accounts

- General-purpose v2 accounts: Basic storage account type for blobs, files, queues, and tables. Recommended for most scenarios using Azure Storage.
- General-purpose v1 accounts: Legacy account type for blobs, files, queues, and tables. Use general-purpose v2 accounts instead when possible.
- BlockBlobStorage accounts: Storage accounts with premium performance characteristics for block blobs and append blobs. Recommended for scenarios with high transactions rates, or scenarios that use smaller objects or require consistently low storage latency.
- FileStorage accounts: Files-only storage accounts with premium performance characteristics. Recommended for enterprise or high performance scale applications.
- BlobStorage accounts: Legacy Blob-only storage accounts. Use general-purpose v2 accounts instead when possible.

|Storage account type	|Supported services	|Supported performance tiers	|Supported access tiers	|Replication options	|Deployment model| Encryption|
|:---|:---|:---|:---|:---|:---|:---|
|General-purpose V2	|Blob, File, Queue, Table, Disk, and Data Lake Gen2|Standard, Premium|Hot, Cool, Archive|LRS, GRS, RA-GRS, ZRS, GZRS (preview), RA-GZRS (preview)|Resource Manager|	Encrypted|
|General-purpose V1	|Blob, File, Queue, Table, and Disk	|Standard, Premium|N/A	|LRS, GRS, RA-GRS	|Resource Manager, Classic	|Encrypted|
|BlockBlobStorage	|Blob (block blobs and append blobs only)	|Premium	|N/A	|LRS, ZRS|Resource Manager	|Encrypted|
|FileStorage	|File only	|Premium	|N/A	|LRS, ZRS|Resource Manager	|Encrypted|
|BlobStorage	|Blob (block blobs and append blobs only)	|Standard	|Hot, Cool, Archive|LRS, GRS, RA-GRS	|Resource Manager	|Encrypted|

**Microsoft recommends using a general-purpose v2 storage account for most scenarios. You can easily upgrade a general-purpose v1 or Blob storage account to a general-purpose v2 account with no downtime and without the need to copy data.**

#### Access Tiers

The available access tiers are:

- The **Hot** access tier. This tier is optimized for frequent access of objects in the storage account. Accessing data in the hot tier is most cost-effective, while storage costs are higher. New storage accounts are created in the hot tier by default.
- The **Cool** access tier. This tier is optimized for storing large amounts of data that is infrequently accessed and stored for at least 30 days. Storing data in the cool tier is more cost-effective, but accessing that data may be more expensive than accessing data in the hot tier.
- The **Archive** tier. This tier is available only for individual block blobs. The archive tier is optimized for data that can tolerate several hours of retrieval latency and that will remain in the archive tier for at least 180 days. The archive tier is the most cost-effective option for storing data. However, accessing that data is more expensive than accessing data in the hot or cool tiers.

#### Redundancy

Redundancy options for a storage account include:

- **Locally redundant storage (LRS)**: A simple, low-cost redundancy strategy. Data is copied synchronously three times within the primary region.
- **Zone-redundant storage (ZRS)**: Redundancy for scenarios requiring high availability. Data is copied synchronously across three Azure availability zones in the primary region.
- **Geo-redundant storage (GRS)**: Cross-regional redundancy to protect against regional outages. Data is copied synchronously three times in the primary region, then copied asynchronously to the secondary region. For read access to data in the secondary region, enable read-access geo-redundant storage (RA-GRS).
- **Geo-zone-redundant storage (GZRS)** (preview): Redundancy for scenarios requiring both high availability and maximum durability. Data is copied synchronously across three Azure availability zones in the primary region, then copied asynchronously to the secondary region. For read access to data in the secondary region, enable read-access geo-zone-redundant storage (RA-GZRS).

#### Encryption

All data in your storage account is encrypted on the service side. For more information about encryption, see Azure Storage Service Encryption for data at rest.

#### Storage account endpoints

A storage account provides a unique namespace in Azure for your data. Every object that you store in Azure Storage has an address that includes your unique account name. The combination of the account name and the Azure Storage service endpoint forms the endpoints for your storage account.

For example, if your general-purpose storage account is named mystorageaccount, then the default endpoints for that account are:

- **Blob storage**: https://*mystorageaccount*.blob.core.windows.net
- **Table storage**: https://*mystorageaccount*.table.core.windows.net
- **Queue storage**: https://*mystorageaccount*.queue.core.windows.net
- **Azure Files**: https://*mystorageaccount*.file.core.windows.net
- **Azure Data Lake Storage Gen2**: https://*mystorageaccount*.dfs.core.windows.net (Uses the ABFS driver optimized specifically for big data.)

> Note: Block blob and blob storage accounts expose only the Blob service endpoint.

#### Control access to account data

By default, the data in your account is available only to you, the account owner. You have control over who may access your data and what permissions they have.

Every request made against your storage account must be authorized. At the level of the service, the request must include a valid Authorization header. Specifically, this header includes all of the information necessary for the service to validate the request before executing it.

You can grant access to the data in your storage account using any of the following approaches:

- **Azure Active Directory**: Use Azure Active Directory (Azure AD) credentials to authenticate a user, group, or other identity for access to blob and queue data. If authentication of an identity is successful, then Azure AD returns a token to use in authorizing the request to Azure Blob storage or Queue storage. For more information, see Authenticate access to Azure Storage using Azure Active Directory.
- **Shared Key authorization**: Use your storage account access key to construct a connection string that your application uses at runtime to access Azure Storage. The values in the connection string are used to construct the Authorization header that is passed to Azure Storage. For more information, see Configure Azure Storage connection strings.
- **Shared access signature**: Use a shared access signature to delegate access to resources in your storage account, if you aren't using Azure AD authorization. A shared access signature is a token that encapsulates all of the information needed to authorize a request to Azure Storage on the URL. You can specify the storage resource, the permissions granted, and the interval over which the permissions are valid as part of the shared access signature. For more information, see Using shared access signatures (SAS).

> Note: Authenticating users or applications using Azure AD credentials provides superior security and ease of use over other means of authorization. While you can continue to use Shared Key authorization with your applications, using Azure AD circumvents the need to store your account access key with your code. You can also continue to use shared access signatures (SAS) to grant fine-grained access to resources in your storage account, but Azure AD offers similar capabilities without the need to manage SAS tokens or worry about revoking a compromised SAS.
>
> Microsoft recommends using Azure AD authorization for your Azure Storage blob and queue applications when possible.

#### Copying data into a storage account

Microsoft provides utilities and libraries for importing your data from on-premises storage devices or third-party cloud storage providers. Which solution you use depends on the quantity of data you're transferring.

- AzCopy
- Data movement library
  - The Azure Storage data movement library for .NET is based on the core data movement framework that powers AzCopy. The library is designed for high-performance, reliable, and easy data transfer operations similar to AzCopy. 
- REST API or client library
  - You can create a custom application to migrate your data from a general-purpose v1 storage account into a Blob storage account. Use one of the Azure client libraries or the Azure Storage services REST API.

#### Storage account billing

You're billed for Azure Storage based on your storage account usage. All objects in a storage account are billed together as a group.

Storage costs are calculated according to the following factors:

- **Region** refers to the geographical region in which your account is based.
- **Account type** refers to the type of storage account you're using.
- **Access tier** refers to the data usage pattern you've specified for your general-purpose v2 or Blob storage account.
- **Storage Capacity** refers to how much of your storage account allotment you're using to store data.
- **Replication** determines how many copies of your data are maintained at one time, and in what locations.
- **Transactions** refer to all read and write operations to Azure Storage.
- **Data egress** refers to any data transferred out of an Azure region. When the data in your storage account is accessed by an application that isn't running in the same region, you're charged for data egress. For information about using resource groups to group your data and services in the same region to limit egress charges, see What is an Azure resource group?.

## configure Azure Files and blob storage

### Blob Storage

[Introduction to Azure Blob Storage](https://docs.microsoft.com/en-us/azure/storage/blobs/storage-blobs-introduction)

Blob storage is designed for:

- Serving images or documents directly to a browser.
- Storing files for distributed access.
- Streaming video and audio.
- Writing to log files.
- Storing data for backup and restore, disaster recovery, and archiving.
- Storing data for analysis by an on-premises or Azure-hosted service.

Users or client applications can access objects in Blob storage via HTTP/HTTPS, from anywhere in the world. Objects in Blob storage are accessible via the Azure Storage REST API, Azure PowerShell, Azure CLI, or an Azure Storage client library. Client libraries are available for different languages.

#### Blob storage resources

Blob storage offers three types of resources:

- The **storage account**
- A **container** in the storage account
- A **blob** in a container

#### Blobs

Azure Storage supports three types of blobs:

- **Block blobs** store text and binary data. Block blobs are made up of blocks of data that can be managed individually. Block blobs store up to about 4.75 TiB of data. Larger block blobs are available in preview, up to about 190.7 TiB
- **Append blobs** are made up of blocks like block blobs, but are optimized for append operations. Append blobs are ideal for scenarios such as logging data from virtual machines.
- **Page blobs** store random access files up to 8 TB in size. Page blobs store virtual hard drive (VHD) files and serve as disks for Azure virtual machines. For more information about page blobs, see Overview of Azure page blobs

#### Move data to Blob storage

A number of solutions exist for migrating existing data to Blob storage:

- **AzCopy** is an easy-to-use command-line tool for Windows and Linux that copies data to and from Blob storage, across containers, or across storage accounts. For more information about AzCopy, see Transfer data with the AzCopy v10.
- The **Azure Storage Data Movement library** is a .NET library for moving data between Azure Storage services. The AzCopy utility is built with the Data Movement library. For more information, see the reference documentation for the Data Movement library.
- **Azure Data Factory** supports copying data to and from Blob storage by using the account key, a shared access signature, a service principal, or managed identities for Azure resources. For more information, see Copy data to or from Azure Blob storage by using Azure Data Factory.
- **Blobfuse** is a virtual file system driver for Azure Blob storage. You can use blobfuse to access your existing block blob data in your Storage account through the Linux file system. For more information, see How to mount Blob storage as a file system with blobfuse.
- **Azure Data Box** service is available to transfer on-premises data to Blob storage when large datasets or network constraints make uploading data over the wire unrealistic. Depending on your data size, you can request Azure Data Box Disk, Azure Data Box, or Azure Data Box Heavy devices from Microsoft. You can then copy your data to those devices and ship them back to Microsoft to be uploaded into Blob storage.
- The **Azure Import/Export service** provides a way to import or export large amounts of data to and from your storage account using hard drives that you provide. For more information, see Use the Microsoft Azure Import/Export service to transfer data to Blob storage.

### Azure Files

[Create and Azure File Share](https://docs.microsoft.com/en-us/azure/storage/files/storage-how-to-create-file-share)

To create an Azure file share, you need to answer three questions about how you will use it:

- What are the performance requirements for your Azure file share?
  - Azure Files offers standard file shares (including transaction optimized, hot, and cool file shares), which are hosted on hard disk-based (HDD-based) hardware, and premium file shares, which are hosted on solid-state disk-based (SSD-based) hardware.
- What size file share do you need?
  - Standard file shares can span up to 100 TiB, however this feature is not enabled by default; if you need a file share that is larger than 5 TiB, you will need to enable the large file share feature for your storage account. Premium file shares can span up to 100 TiB without any special setting, however premium file shares are provisioned, rather than pay as you go like standard file shares. This means that provisioning a file share much larger than what you need will increase the total cost of storage.
- What are your redundancy requirements for your Azure file share?
  - Standard file shares offer locally-redundant (LRS), zone redundant (ZRS), geo-redundant (GRS), or geo-zone-redundant (GZRS) storage, however the large file share feature is only supported on locally redundant and zone redundant file shares. Premium file shares do not support any form of geo-redundancy.
  - Premium file shares are available with locally redundancy in most regions that offer storage accounts and with zone redundancy in a smaller subset of regions. To find out if premium file shares are currently available in your region, see the products available by region page for Azure. For information about regions that support ZRS, see Azure Storage redundancy.

#### Create a storage account

Azure supports multiple types of storage accounts for different storage scenarios customers may have, but there are two main types of storage accounts for Azure Files. Which storage account type you need to create depends on whether you want to create a standard file share or a premium file share:

- **General purpose version 2 (GPv2) storage accounts**: GPv2 storage accounts allow you to deploy Azure file shares on standard/hard disk-based (HDD-based) hardware. In addition to storing Azure file shares, GPv2 storage accounts can store other storage resources such as blob containers, queues, or tables. File shares can be deployed into the transaction optimized (default), hot, or cool tiers.
- **FileStorage storage accounts**: FileStorage storage accounts allow you to deploy Azure file shares on premium/solid-state disk-based (SSD-based) hardware. FileStorage accounts can only be used to store Azure file shares; no other storage resources (blob containers, queues, tables, etc.) can be deployed in a FileStorage account.

> Important: Selecting the blob access tier does not affect the tier of the file share.

#### The Advanced blade

The advanced section contains several important settings for Azure file shares:

- **Secure transfer required**: This field indicates whether the storage account requires encryption in transit for communication to the storage account. We recommend this is left enabled, however, if you require SMB 2.1 support, you must disable this. We recommend if you disable encryption that you constrain your storage account access to a virtual network with service endpoints and/or private endpoints.
- **Large file shares**: This field enables the storage account for file shares spanning up to 100 TiB. Enabling this feature will limit your storage account to only locally redundant and zone redundant storage options. Once a GPv2 storage account has been enabled for large file shares, you cannot disable the large file share capability. FileStorage storage accounts (storage accounts for premium file shares) do not have this option, as all premium file shares can scale up to 100 TiB.

#### Create file share

Once you've created your storage account, all that is left is to create your file share. This process is mostly the same regardless of whether you're using a premium file share or a standard file share. You should consider the following differences.

Standard file shares may be deployed into one of the standard tiers: transaction optimized (default), hot, or cool. This is a per file share tier that is not affected by the blob access tier of the storage account (this property only relates to Azure Blob storage - it does not relate to Azure Files at all). You can change the tier of the share at any time after it has been deployed. Premium file shares cannot be directly converted to standard file shares in any standard tier.

> Important: You can move file shares between tiers within GPv2 storage account types (transaction optimized, hot, and cool). Share moves between tiers incur transactions: moving from a hotter tier to a cooler tier will incur the cooler tier's write transaction charge for each file in the share, while a move from a cooler tier to a hotter tier will incur the cool tier's read transaction charge for each file the share.

The **quota** property means something slightly different between premium and standard file shares:

- For standard file shares, it's an upper boundary of the Azure file share, beyond which end-users cannot go. The primary purpose for quota for a standard file share is budgetary: "I don't want this file share to grow beyond this point". If a quota is not specified, standard file share can span up to 100 TiB (or 5 TiB if the large file shares property is not set for a storage account).
- For premium file shares, quota is overloaded to mean **provisioned size**. The provisioned size is the amount that you will be billed for, regardless of actual usage. When you provision a premium file share, you want to consider two factors: 1) the future growth of the share from a space utilization perspective and 2) the IOPS required for your workload. Every provisioned GiB entitles you to additional reserved and burst IOPS. For more information on how to plan for a premium file share, see provisioning premium file shares.

#### Changing the tier of an Azure file share

File shares deployed in general purpose v2 (GPv2) storage account can be in the transaction optimized, hot, or cool tiers. You can change the tier of the Azure file share at any time, subject to transaction costs as described above.

## configure network access to the storage account

[Configure Azure Storage firewalls and virtual networks](https://docs.microsoft.com/en-us/azure/storage/common/storage-network-security)

### Change the default network access rule

By default, storage accounts accept connections from clients on any network. To limit access to selected networks, you must first change the default action.

### Grant access from a virtual network

You can configure storage accounts to allow access only from specific subnets. The allowed subnets may belong to a VNet in the same subscription, or those in a different subscription, including subscriptions belonging to a different Azure Active Directory tenant.

Enable a [Service endpoint](https://docs.microsoft.com/en-us/azure/virtual-network/virtual-network-service-endpoints-overview) for Azure Storage within the VNet. The service endpoint routes traffic from the VNet through an optimal path to the Azure Storage service. The identities of the subnet and the virtual network are also transmitted with each request. Administrators can then configure network rules for the storage account that allow requests to be received from specific subnets in a VNet. Clients granted access via these network rules must continue to meet the authorization requirements of the storage account to access the data.

Each storage account supports up to 200 virtual network rules, which may be combined with IP network rules.

### Available virtual network regions

In general, service endpoints work between virtual networks and service instances in the same Azure region. When using service endpoints with Azure Storage, this scope grows to include the paired region. Service endpoints allow continuity during a regional failover and access to read-only geo-redundant storage (RA-GRS) instances. Network rules that grant access from a virtual network to a storage account also grant access to any RA-GRS instance.

When planning for disaster recovery during a regional outage, you should create the VNets in the [paired region](https://docs.microsoft.com/en-us/azure/best-practices-availability-paired-regions) in advance. Enable service endpoints for Azure Storage, with network rules granting access from these alternative virtual networks. Then apply these rules to your geo-redundant storage accounts.

> Note: Service endpoints don't apply to traffic outside the region of the virtual network and the designated region pair. You can only apply network rules granting access from virtual networks to storage accounts in the primary region of a storage account or in the designated paired region.

### Required permissions

To apply a virtual network rule to a storage account, the user must have the appropriate permissions for the subnets being added. The permission needed is Join Service to a Subnet and is included in the Storage Account Contributor built-in role. It can also be added to custom role definitions.

Storage account and the virtual networks granted access may be in different subscriptions, including subscriptions that are a part of a different Azure AD tenant.

> Note: Configuration of rules that grant access to subnets in virtual networks that are a part of a different Azure Active Directory tenant are currently only supported through Powershell, CLI and REST APIs. Such rules cannot be configured through the Azure portal, though they may be viewed in the portal.

### Grant access from an internet IP range

You can configure storage accounts to allow access from specific public internet IP address ranges. This configuration grants access to specific internet-based services and on-premises networks and blocks general internet traffic.

Provide allowed internet address ranges using CIDR notation in the form 16.17.18.0/24 or as individual IP addresses like 16.17.18.19.

### Configuring access from on-premises networks

To grant access from your on-premises networks to your storage account with an IP network rule, you must identify the internet facing IP addresses used by your network. Contact your network administrator for help.

If you are using ExpressRoute from your premises, for public peering or Microsoft peering, you will need to identify the NAT IP addresses that are used. For public peering, each ExpressRoute circuit by default uses two NAT IP addresses applied to Azure service traffic when the traffic enters the Microsoft Azure network backbone.

### Exceptions

[Trusted Microsoft services](https://docs.microsoft.com/en-us/azure/storage/common/storage-network-security#trusted-microsoft-services)

### Storage analytics data access

In some cases, access to read resource logs and metrics is required from outside the network boundary. When configuring trusted services access to the storage account, you can allow read-access for the log files, metrics tables, or both. Learn more about working with storage analytics.

## implement Shared Access Signatures and access policies

[Shared access signature overview](https://docs.microsoft.com/en-us/azure/storage/common/storage-sas-overview)

### Types of shared access signatures

Azure Storage supports three types of shared access signatures:

- **User delegation SAS**. A user delegation SAS is secured with Azure Active Directory (Azure AD) credentials and also by the permissions specified for the SAS. A user delegation SAS applies to Blob storage only.
  - For more information about the user delegation SAS, see [Create a user delegation SAS (REST API)](https://docs.microsoft.com/en-us/rest/api/storageservices/create-user-delegation-sas).
- **Service SAS**. A service SAS is secured with the storage account key. A service SAS delegates access to a resource in only one of the Azure Storage services: Blob storage, Queue storage, Table storage, or Azure Files.
  - For more information about the service SAS, see [Create a service SAS (REST API)](https://docs.microsoft.com/en-us/rest/api/storageservices/create-service-sas).
- **Account SAS**. An account SAS is secured with the storage account key. An account SAS delegates access to resources in one or more of the storage services. All of the operations available via a service or user delegation SAS are also available via an account SAS. Additionally, with the account SAS, you can delegate access to operations that apply at the level of the service, such as Get/Set Service Properties and Get Service Stats operations. You can also delegate access to read, write, and delete operations on blob containers, tables, queues, and file shares that are not permitted with a service SAS.
  - For more information about the account SAS, [Create an account SAS (REST API)](https://docs.microsoft.com/en-us/rest/api/storageservices/create-account-sas).

> Note: Microsoft recommends that you use Azure AD credentials when possible as a security best practice, rather than using the account key, which can be more easily compromised. When your application design requires shared access signatures for access to Blob storage, use Azure AD credentials to create a user delegation SAS when possible for superior security. For more information, see [Authorize access to blobs and queues using Azure Active Directory](https://docs.microsoft.com/en-us/azure/storage/common/storage-auth-aad).

A shared access signature can take one of two forms:

- **Ad hoc SAS**: When you create an ad hoc SAS, the start time, expiry time, and permissions for the SAS are all specified in the SAS URI (or implied, if start time is omitted). Any type of SAS can be an ad hoc SAS.
- **Service SAS with stored access policy**: A stored access policy is defined on a resource container, which can be a blob container, table, queue, or file share. The stored access policy can be used to manage constraints for one or more service shared access signatures. When you associate a service SAS with a stored access policy, the SAS inherits the constraints—the start time, expiry time, and permissions—defined for the stored access policy.

> Note: A user delegation SAS or an account SAS must be an ad hoc SAS. Stored access policies are not supported for the user delegation SAS or the account SAS.

### How a shared access signature works

A shared access signature is a signed URI that points to one or more storage resources and includes a token that contains a special set of query parameters. The token indicates how the resources may be accessed by the client. One of the query parameters, the signature, is constructed from the SAS parameters and signed with the key that was used to create the SAS. This signature is used by Azure Storage to authorize access to the storage resource.

SAS signature and authorization

You can sign a SAS token in one of two ways:

- With a user delegation key that was created using Azure Active Directory (Azure AD) credentials. A user delegation SAS is signed with the user delegation key.

  To get the user delegation key and create the SAS, an Azure AD security principal must be assigned an Azure role that includes the Microsoft.Storage/storageAccounts/blobServices/generateUserDelegationKey action. For detailed information about Azure roles with permissions to get the user delegation key, see Create a user delegation SAS (REST API).

- With the storage account key (Shared Key). Both a service SAS and an account SAS are signed with the storage account key. To create a SAS that is signed with the account key, an application must have access to the account key.

When a request includes a SAS token, that request is authorized based on how that SAS token is signed. The access key or credentials that you use to create a SAS token are also used by Azure Storage to grant access to a client that possesses the SAS.

The following table summarizes how each type of SAS token is authorized when it is included on a request to Azure Storage:

|Type of SAS	|Type of authorization|
|:-- |:-- |
|User delegation SAS (Blob storage only)	|Azure AD|
|Service SAS	|Shared Key|
|Account SAS	|Shared Key|

Microsoft recommends using a user delegation SAS when possible for superior security.

### SAS token

The SAS token is a string that you generate on the client side, for example by using one of the Azure Storage client libraries. The SAS token is not tracked by Azure Storage in any way. You can create an unlimited number of SAS tokens on the client side. After you create a SAS, you can distribute it to client applications that require access to resources in your storage account.

When a client application provides a SAS URI to Azure Storage as part of a request, the service checks the SAS parameters and signature to verify that it is valid for authorizing the request. If the service verifies that the signature is valid, then the request is authorized. Otherwise, the request is declined with error code 403 (Forbidden).

### When to use a shared access signature

Use a SAS when you want to provide secure access to resources in your storage account to any client who does not otherwise have permissions to those resources.

A common scenario where a SAS is useful is a service where users read and write their own data to your storage account. In a scenario where a storage account stores user data, there are two typical design patterns:

1. Clients upload and download data via a front-end proxy service, which performs authentication. This front-end proxy service has the advantage of allowing validation of business rules, but for large amounts of data or high-volume transactions, creating a service that can scale to match demand may be expensive or difficult.
2. A lightweight service authenticates the client as needed and then generates a SAS. Once the client application receives the SAS, they can access storage account resources directly with the permissions defined by the SAS and for the interval allowed by the SAS. The SAS mitigates the need for routing all data through the front-end proxy service.

Many real-world services may use a hybrid of these two approaches. For example, some data might be processed and validated via the front-end proxy, while other data is saved and/or read directly using SAS.

### [Best practices when using SAS](https://docs.microsoft.com/en-us/azure/storage/common/storage-sas-overview#best-practices-when-using-sas)

When you use shared access signatures in your applications, you need to be aware of two potential risks:

- If a SAS is leaked, it can be used by anyone who obtains it, which can potentially compromise your storage account.
- If a SAS provided to a client application expires and the application is unable to retrieve a new SAS from your service, then the application's functionality may be hindered.

## implement Azure AD authentication for storage

[Authorize access to blobs and queues using Azure Active Directory](https://docs.microsoft.com/en-us/azure/storage/common/storage-auth-aad)

Azure Storage supports using Azure Active Directory (Azure AD) to authorize requests to Blob and Queue storage. With Azure AD, you can use Azure role-based access control (Azure RBAC) to grant permissions to a security principal, which may be a user, group, or application service principal. The security principal is authenticated by Azure AD to return an OAuth 2.0 token. The token can then be used to authorize a request against Blob or Queue storage.

Authorizing requests against Azure Storage with Azure AD provides superior security and ease of use over Shared Key authorization. Microsoft recommends using Azure AD authorization with your blob and queue applications when possible to minimize potential security vulnerabilities inherent in Shared Key.

Authorization with Azure AD is available for all general-purpose and Blob storage accounts in all public regions and national clouds. Only storage accounts created with the Azure Resource Manager deployment model support Azure AD authorization.

### Azure built-in roles for blobs and queues

Azure provides the following Azure built-in roles for authorizing access to blob and queue data using Azure AD and OAuth:

- Storage Blob Data Owner: Use to set ownership and manage POSIX access control for Azure Data Lake Storage Gen2. For more information, see Access control in Azure Data Lake Storage Gen2.
- Storage Blob Data Contributor: Use to grant read/write/delete permissions to Blob storage resources.
- Storage Blob Data Reader: Use to grant read-only permissions to Blob storage resources.
- Storage Blob Delegator: Get a user delegation key to use to create a shared access signature that is signed with Azure AD credentials for a container or blob.
- Storage Queue Data Contributor: Use to grant read/write/delete permissions to Azure queues.
- Storage Queue Data Reader: Use to grant read-only permissions to Azure queues.
- Storage Queue Data Message Processor: Use to grant peek, retrieve, and delete permissions to messages in Azure Storage queues.
- Storage Queue Data Message Sender: Use to grant add permissions to messages in Azure Storage queues.

Only roles explicitly defined for data access permit a security principal to access blob or queue data. Built-in roles such as **Owner**, **Contributor**, and **Storage Account Contributor** permit a security principal to manage a storage account, but do not provide access to the blob or queue data within that account via Azure AD. However, if a role includes the **Microsoft.Storage/storageAccounts/listKeys/action**, then a user to whom that role is assigned can access data in the storage account via Shared Key authorization with the account access keys.

To learn how to assign an Azure built-in role to a security principal, see one of the following articles:

- [Use the Azure portal to assign an Azure role for access to blob and queue data](https://docs.microsoft.com/en-us/azure/storage/common/storage-auth-aad-rbac-portal)
- [Use the Azure CLI to assign an Azure role for access to blob and queue data](https://docs.microsoft.com/en-us/azure/storage/common/storage-auth-aad-rbac-cli)
- [Use the Azure PowerShell module to assign an Azure role for access to blob and queue data](https://docs.microsoft.com/en-us/azure/storage/common/storage-auth-aad-rbac-powershell)

### Resource scope

Before you assign an Azure role to a security principal, determine the scope of access that the security principal should have. Best practices dictate that it's always best to grant only the narrowest possible scope.

The following list describes the levels at which you can scope access to Azure blob and queue resources, starting with the narrowest scope:

- **An individual container.** At this scope, a role assignment applies to all of the blobs in the container, as well as container properties and metadata.
- **An individual queue.** At this scope, a role assignment applies to messages in the queue, as well as queue properties and metadata.
- **The storage account.** At this scope, a role assignment applies to all containers and their blobs, or to all queues and their messages.
- **The resource group.** At this scope, a role assignment applies to all of the containers or queues in all of the storage accounts in the resource group.
- **The subscription.** At this scope, a role assignment applies to all of the containers or queues in all of the storage accounts in all of the resource groups in the subscription.
- **A management group.** At this scope, a role assignment applies to all of the containers or queues in all of the storage accounts in all of the resource groups in all of the subscriptions in the management group.

## manage access keys

[Manage storage account access keys](https://docs.microsoft.com/en-us/azure/storage/common/storage-account-keys-manage)

When you create a storage account, Azure generates two 512-bit storage account access keys. These keys can be used to authorize access to data in your storage account via Shared Key authorization.

Microsoft recommends that you use Azure Key Vault to manage your access keys, and that you regularly rotate and regenerate your keys. Using Azure Key Vault makes it easy to rotate your keys without interruption to your applications. You can also manually rotate your keys.

>  Note: Microsoft recommends using Azure Active Directory (Azure AD) to authorize requests against blob and queue data if possible, instead of Shared Key. Azure AD provides superior security and ease of use over Shared Key. For more information about authorizing access to data with Azure AD, see Authorize access to Azure blobs and queues using Azure Active Directory.

### View Account Access Keys

#### Portal

In Storage Account under Settings -> Access Keys

#### PowerShell

    $storageAccountKey = `
        (Get-AzStorageAccountKey `
        -ResourceGroupName <resource-group> `
        -Name <storage-account>).Value[0]

#### Azure CLI

    az storage account keys list \
      --resource-group <resource-group> \
      --account-name <storage-account>

You can use either of the two keys to access Azure Storage, but in general it's a good practice to use the first key, and reserve the use of the second key for when you are rotating keys.

To view or read an account's access keys, the user must either be a **Service Administrator**, or must be assigned an Azure role that includes the **Microsoft.Storage/storageAccounts/listkeys/action**. Some Azure built-in roles that include this action are the **Owner**, **Contributor**, and **Storage Account Key Operator Service Role** roles.

### Use Azure Key Vault to manage your access keys

Microsoft recommends using Azure Key Vault to manage and rotate your access keys. Your application can securely access your keys in Key Vault, so that you can avoid storing them with your application code. For more information about using Key Vault for key management, see the following articles:

- [Manage storage account keys with Azure Key Vault and PowerShell](https://docs.microsoft.com/en-us/azure/key-vault/secrets/overview-storage-keys-powershell)
- [Manage storage account keys with Azure Key Vault and the Azure CLI](https://docs.microsoft.com/en-us/azure/key-vault/secrets/overview-storage-keys)

### Manually rotate access keys

Microsoft recommends that you rotate your access keys periodically to help keep your storage account secure. If possible, use Azure Key Vault to manage your access keys. If you are not using Key Vault, you will need to rotate your keys manually.

Two access keys are assigned so that you can rotate your keys. Having two keys ensures that your application maintains access to Azure Storage throughout the process.

#### Portal

In Storage Account under Settings -> Access Keys -> Regenerate

#### PowerShell

    New-AzStorageAccountKey -ResourceGroupName <resource-group> `
      -Name <storage-account> `
      -KeyName key1

#### Azure CLI

    az storage account keys renew \
      --resource-group <resource-group> \
      --account-name <storage-account>
      --key primary

> Note: Microsoft recommends using only one of the keys in all of your applications at the same time. If you use Key 1 in some places and Key 2 in others, you will not be able to rotate your keys without some application losing access.

To rotate an account's access keys, the user must either be a Service Administrator, or must be assigned an Azure role that includes the **Microsoft.Storage/storageAccounts/regeneratekey/action**. Some Azure built-in roles that include this action are the **Owner**, **Contributor**, and **Storage Account Key Operator Service Role** roles.

## implement Azure storage replication

- [Azure Storage redundancy](https://docs.microsoft.com/en-us/azure/storage/common/storage-redundancy)
- [Disaster recovery and account failover (preview)](https://docs.microsoft.com/en-us/azure/storage/common/storage-disaster-recovery-guidance)

Azure Storage always stores multiple copies of your data so that it is protected from planned and unplanned events, including transient hardware failures, network or power outages, and massive natural disasters. Redundancy ensures that your storage account meets the Service-Level Agreement (SLA) for Azure Storage even in the face of failures.

When deciding which redundancy option is best for your scenario, consider the tradeoffs between lower costs and higher availability and durability. The factors that help determine which redundancy option you should choose include:

- How your data is replicated in the primary region
- Whether your data is replicated to a second region that is geographically distant to the primary region, to protect against regional disasters
- Whether your application requires read access to the replicated data in the secondary region if the primary region becomes unavailable for any reason

### Redundancy in the primary region

Data in an Azure Storage account is always replicated three times in the primary region. Azure Storage offers two options for how your data is replicated in the primary region:

- **Locally redundant storage (LRS)** copies your data synchronously three times within a single physical location in the primary region. LRS is the least expensive replication option, but is not recommended for applications requiring high availability.
- **Zone-redundant storage (ZRS)** copies your data synchronously across three Azure availability zones in the primary region. For applications requiring high availability, Microsoft recommends using ZRS in the primary region, and also replicating to a secondary region.

#### Locally redundant storage

Locally redundant storage (LRS) replicates your data three times within a single physical location in the primary region. LRS provides at least 99.999999999% (11 nines) durability of objects over a given year.

LRS is a good choice for the following scenarios:

- If your application stores data that can be easily reconstructed if data loss occurs, you may opt for LRS.
- If your application is restricted to replicating data only within a country or region due to data governance requirements, you may opt for LRS. In some cases, the paired regions across which the data is geo-replicated may be in another country or region. For more information on paired regions, see Azure regions.

#### Zone-redundant storage

Zone-redundant storage (ZRS) replicates your Azure Storage data synchronously across three Azure availability zones in the primary region. Each availability zone is a separate physical location with independent power, cooling, and networking. ZRS offers durability for Azure Storage data objects of at least 99.9999999999% (12 9's) over a given year.

With ZRS, your data is still accessible for both read and write operations even if a zone becomes unavailable. If a zone becomes unavailable, Azure undertakes networking updates, such as DNS repointing. These updates may affect your application if you access data before the updates have completed. When designing applications for ZRS, follow practices for transient fault handling, including implementing retry policies with exponential back-off.

A write request to a storage account that is using ZRS happens synchronously. The write operation returns successfully only after the data is written to all replicas across the three availability zones.

Microsoft recommends using ZRS in the primary region for scenarios that require consistency, durability, and high availability. We also recommend using ZRS if you want to restrict an application to replicate data only within a country or region because of data governance requirements.

ZRS provides excellent performance, low latency, and resiliency for your data if it becomes temporarily unavailable. However, ZRS by itself may not protect your data against a regional disaster where multiple zones are permanently affected. For protection against regional disasters, Microsoft recommends using geo-zone-redundant storage (GZRS), which uses ZRS in the primary region and also geo-replicates your data to a secondary region.

### Redundancy in a secondary region

For applications requiring high availability, you can choose to additionally copy the data in your storage account to a secondary region that is hundreds of miles away from the primary region. If your storage account is copied to a secondary region, then your data is durable even in the case of a complete regional outage or a disaster in which the primary region isn't recoverable.

When you create a storage account, you select the primary region for the account. The paired secondary region is determined based on the primary region, and can't be changed. For more information about regions supported by Azure, see Azure regions.

Azure Storage offers two options for copying your data to a secondary region:

- **Geo-redundant storage (GRS)** copies your data synchronously three times within a single physical location in the primary region using LRS. It then copies your data asynchronously to a single physical location in the secondary region.
- **Geo-zone-redundant storage (GZRS)** copies your data synchronously across three Azure availability zones in the primary region using ZRS. It then copies your data asynchronously to a single physical location in the secondary region.

The primary difference between GRS and GZRS is how data is replicated in the primary region. Within the secondary region, data is always replicated synchronously three times using LRS. LRS in the secondary region protects your data against hardware failures.

With GRS or GZRS, the data in the secondary region isn't available for read or write access unless there is a failover to the secondary region. For read access to the secondary region, configure your storage account to use read-access geo-redundant storage (RA-GRS) or read-access geo-zone-redundant storage (RA-GZRS).

If the primary region becomes unavailable, you can choose to fail over to the secondary region. After the failover has completed, the secondary region becomes the primary region, and you can again read and write data. 

>  Important: Because data is replicated to the secondary region asynchronously, a failure that affects the primary region may result in data loss if the primary region cannot be recovered. The interval between the most recent writes to the primary region and the last write to the secondary region is known as the recovery point objective (RPO). The RPO indicates the point in time to which data can be recovered. Azure Storage typically has an RPO of less than 15 minutes, although there's currently no SLA on how long it takes to replicate data to the secondary region.

#### Geo-redundant storage

Geo-redundant storage (GRS) copies your data synchronously three times within a single physical location in the primary region using LRS. It then copies your data asynchronously to a single physical location in a secondary region that is hundreds of miles away from the primary region. GRS offers durability for Azure Storage data objects of at least 99.99999999999999% (16 9's) over a given year.

A write operation is first committed to the primary location and replicated using LRS. The update is then replicated asynchronously to the secondary region. When data is written to the secondary location, it's also replicated within that location using LRS.

#### Geo-zone-redundant storage

Geo-zone-redundant storage (GZRS) combines the high availability provided by redundancy across availability zones with protection from regional outages provided by geo-replication. Data in a GZRS storage account is copied across three Azure availability zones in the primary region and is also replicated to a secondary geographic region for protection from regional disasters. Microsoft recommends using GZRS for applications requiring maximum consistency, durability, and availability, excellent performance, and resilience for disaster recovery.

With a GZRS storage account, you can continue to read and write data if an availability zone becomes unavailable or is unrecoverable. Additionally, your data is also durable in the case of a complete regional outage or a disaster in which the primary region isn't recoverable. GZRS is designed to provide at least 99.99999999999999% (16 9's) durability of objects over a given year.

Only general-purpose v2 storage accounts support GZRS and RA-GZRS. For more information about storage account types, see Azure storage account overview. GZRS and RA-GZRS support block blobs, page blobs (except for VHD disks), files, tables, and queues.

### Read access to data in the secondary region

Geo-redundant storage (with GRS or GZRS) replicates your data to another physical location in the secondary region to protect against regional outages. However, that data is available to be read only if the customer or Microsoft initiates a failover from the primary to secondary region. When you enable read access to the secondary region, your data is available to be read at all times, including in a situation where the primary region becomes unavailable. For read access to the secondary region, enable read-access geo-redundant storage (RA-GRS) or read-access geo-zone-redundant storage (RA-GZRS).

> Note: Azure Files does not support read-access geo-redundant storage (RA-GRS) and read-access geo-zone-redundant storage (RA-GZRS).

### Design your applications for read access to the secondary

If your storage account is configured for read access to the secondary region, then you can design your applications to seamlessly shift to reading data from the secondary region if the primary region becomes unavailable for any reason.

The secondary region is available for read access after you enable RA-GRS or RA-GZRS, so that you can test your application in advance to make sure that it will properly read from the secondary in the event of an outage. For more information about how to design your applications for high availability, see [Use geo-redundancy to design highly available applications](https://docs.microsoft.com/en-us/azure/storage/common/geo-redundant-design).

When read access to the secondary is enabled, your application can be read from the secondary endpoint as well as from the primary endpoint. The secondary endpoint appends the suffix –secondary to the account name. For example, if your primary endpoint for Blob storage is myaccount.blob.core.windows.net, then the secondary endpoint is myaccount-secondary.blob.core.windows.net. The account access keys for your storage account are the same for both the primary and secondary endpoints.

### Supported storage account types

The following table shows which redundancy options are supported by each type of storage account. For information for storage account types, see Storage account overview.

|LRS	|ZRS	|GRS/RA-GRS	|GZRS/RA-GZRS|
|:--- |:--- |:--- |:--- |
|General-purpose v2<br />General-purpose v1<br />Block blob storage<br />Blob storage<br />File storage|General-purpose v2<br />Block blob storage<br />File storage|General-purpose v2<br />General-purpose v1<br />Blob storage<br />|General-purpose v2|

> Note: Azure Premium Disk Storage currently supports only locally redundant storage (LRS). Block blob storage accounts support locally redundant storage (LRS) and zone redundant storage (ZRS) in certain regions.

## implement Azure storage account failover

[Initiate a storage account failover (preview)](https://docs.microsoft.com/en-us/azure/storage/common/storage-initiate-account-failover)

If the primary endpoint for your geo-redundant storage account becomes unavailable for any reason, you can initiate an account failover. An account failover updates the secondary endpoint to become the primary endpoint for your storage account. Once the failover is complete, clients can begin writing to the new primary region. Forced failover enables you to maintain high availability for your applications.

> Warning: An account failover typically results in some data loss. To understand the implications of an account failover and to prepare for data loss, review Understand the account failover process.

### Initiate the failover

#### Portal 

Storage Account -> Settings -> Geo-replication

1. Verify that your storage account is configured for geo-redundant storage (GRS) or read-access geo-redundant storage (RA-GRS). If it's not, then select Configuration under Settings to update your account to be geo-redundant.
2. The Last Sync Time property indicates how far the secondary is behind from the primary. Last Sync Time provides an estimate of the extent of data loss that you will experience after the failover is completed. For more information about checking the Last Sync Time property, see Check the Last Sync Time property for a storage account.
3. Select Prepare for failover.
4. Review the confirmation dialog. When you are ready, enter Yes to confirm and initiate the failover.

#### Az PowerShell

    Invoke-AzStorageAccountFailover -ResourceGroupName <resource-group-name> -Name <account-name>

#### Azure CLI

    az storage account show \ --name accountName \ --expand geoReplicationStats
    az storage account failover \ --name accountName

### Important implications of account failover

When you initiate an account failover for your storage account, the DNS records for the secondary endpoint are updated so that the secondary endpoint becomes the primary endpoint. Make sure that you understand the potential impact to your storage account before you initiate a failover.

To estimate the extent of likely data loss before you initiate a failover, check the Last Sync Time property. For more information about checking the Last Sync Time property, see [Check the Last Sync Time property for a storage account](https://docs.microsoft.com/en-us/azure/storage/common/last-sync-time-get).

After the failover, your storage account type is automatically converted to locally redundant storage (LRS) in the new primary region. You can re-enable geo-redundant storage (GRS) or read-access geo-redundant storage (RA-GRS) for the account. Note that converting from LRS to GRS or RA-GRS incurs an additional cost.
