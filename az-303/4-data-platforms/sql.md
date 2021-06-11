
# Implement Azure SQL databases

Microsoft manages the following key characteristics of any cloud-based service offerings:

- High availability and on-demand scale
- Business continuity
- Automatic backups
- Enterprise-grade security and compliance

## configure Azure SQL database settings

### Offerings

Service has the following offerings:

- Single database
  - You assign pre-allocated compute and storage to the database
- Elastic pools
  - You create a database inside a pool of databases and they share the same resources to meet unpredictable usage demand
- Managed Instance
  - Gives close to 100% compatability with SQL Server Enterprise Edition

**Regional availability**: https://azure.microsoft.com/en-gb/global-infrastructure/services/?products=sql-database

### Purchase Options:

- DTUs (Database Transaction Units) model
  - Blend of Compute, Storage, and IO Resource
  - For a single database, capacity is measured in DTUs
  - For elastic databases it's measured in eDTUs.
  - Three Service Tiers for single and elastic pool databases:
    - Basic, Standard, Premium
    - Each has it's own differentiated range of compute, storage, fixed retention, backup options, and pricing levels
- vCore (Virtual Core) model
  - Recommended purchasing model where you get the flexibility of independently choosing compute and storage to meet your application needs
  - Can use an existing SQL Server license to save 55% of the costs
  - Three service tiers: General Purpose, Hyperscale, and Business Critical
    - Each has its own range of compute sizes, types and sizes of storage, latency, and IO ranges.
  - In vCore model you can create a single, elastic, or managed instance database

### Create a SQL Azure database using Azure portal

- Databases reside on a logical SQL database server, and the server must exist before you can create a database on it.
- The security firewall setting, auditing, and other threat protection policies on the server automatically apply to all the databases on the server.
- The databases are always in the same region as their logical server.

Specify:

- Server Name
- Admin Username/Password
- Location
- The "Allow Azure services to access server" option is checked by default and enables other Azure IP addresses and subnets to be able to connect to the SQL Azure server.

#### Networking

- After the database is created, you need to setup firewall rules to allow inbound connections to the databases.
- By default all inbound connections are blocked by the SQL database firewall.
- The firewall rules can be at the server level or the database level
  - The server level rules apply to all databases on the server

Another option to control access to the Azure SQL database is to use virtual network rules. This gives more fine grained control than "Allow Azure services to access server" as that can allow access from Azure Ips and subnets not owned by you.

#### Create elastic pools for Azure SQL databases

Elastic database pools are best suited to applications that have predictable usage patterns.

Choosing a flexible database pool is the best option to save cost when you have many databases, and overall average utilization of DTUs or vCores is low. The more databases you add in the pool, the more cost it saves you because of efficiently unused shared DTUs and vCores across the databases.

### Links

[What is the Azure SQL Database service?](https://docs.microsoft.com/en-us/azure/sql-database/sql-database-technical-overview)

### Purchasing models

SQL Database offers the following purchasing models:

- The vCore-based purchasing model lets you choose the number of vCores, the amount of memory, and the amount and speed of storage. The vCore-based purchasing model also allows you to use Azure Hybrid Benefit for SQL Server to gain cost savings. For more information about the Azure Hybrid Benefit, see the "Frequently asked questions" section later in this article.
- The DTU-based purchasing model offers a blend of compute, memory, and I/O resources in three service tiers, to support light to heavy database workloads. Compute sizes within each tier provide a different mix of these resources, to which you can add additional storage resources.
- The serverless model automatically scales compute based on workload demand, and bills for the amount of compute used per second. The serverless compute tier also automatically pauses databases during inactive periods when only storage is billed, and automatically resumes databases when activity returns.

### Service tiers

Azure SQL Database offers three service tiers that are designed for different types of applications:

- General Purpose/Standard service tier designed for common workloads. It offers budget-oriented balanced compute and storage options.
- Business Critical/Premium service tier designed for OLTP applications with high transaction rate and lowest-latency I/O. It offers the highest resilience to failures by using several isolated replicas.
- Hyperscale service tier designed for very large OLTP database and the ability to autoscale storage and scale compute fluidly.

### Elastic pools to maximize resource utilization

For many businesses and applications, being able to create single databases and dial performance up or down on demand is enough, especially if usage patterns are relatively predictable. Unpredictable usage patterns can make it hard to manage costs and your business model. Elastic pools are designed to solve this problem. You allocate performance resources to a pool rather than an individual database. You pay for the collective performance resources of the pool rather than for single database performance.

![Graphic that shows elastic pools in basic, standard, and premium editions](https://docs.microsoft.com/en-us/azure/azure-sql/database/media/sql-database-paas-overview/sqldb_elastic_pools.png)

With elastic pools, you don't need to focus on dialing database performance up and down as demand for resources fluctuates. The pooled databases consume the performance resources of the elastic pool as needed. Pooled databases consume but don't exceed the limits of the pool, so your cost remains predictable even if individual database usage doesn't.

You can add and remove databases to the pool, scaling your app from a handful of databases to thousands, all within a budget that you control. You can also control the minimum and maximum resources available to databases in the pool, to ensure that no database in the pool uses all the pool resources, and that every pooled database has a guaranteed minimum amount of resources. To learn more about design patterns for software as a service (SaaS) applications that use elastic pools, see Design patterns for multi-tenant SaaS applications with SQL Database.

Scripts can help with monitoring and scaling elastic pools. For an example, see Use PowerShell to monitor and scale an elastic pool in Azure SQL Database.

### Blend single databases with pooled databases

You can blend single databases with elastic pools, and change the service tiers of single databases and elastic pools to adapt to your situation. You can also mix and match other Azure services with SQL Database to meet your unique app design needs, drive cost and resource efficiencies, and unlock new business opportunities.

- [Quickstart: Create a single database in Azure SQL Database using the Azure portal, PowerShell, and Azure CLI](https://docs.microsoft.com/en-us/azure/sql-database/sql-database-single-database-get-started)
- [Choose the right deployment option in Azure SQL](https://docs.microsoft.com/en-us/azure/sql-database/sql-database-paas-vs-sql-server-iaas)

### Azure SQL Database

Azure SQL Database is a relational database-as-a-service (DBaaS) hosted in Azure that falls into the industry category of Platform-as-a-Service (PaaS).

- Best for modern cloud applications that want to use the latest stable SQL Server features and have time constraints in development and marketing.
- A fully managed SQL Server database engine, based on the latest stable Enterprise Edition of SQL Server. SQL Database has two deployment options built on standardized hardware and software that is owned, hosted, and maintained by Microsoft.

With SQL Server, you can use built-in features and functionality that requires extensive configuration (either on-premises or in an Azure virtual machine). When using SQL Database, you pay-as-you-go with options to scale up or out for greater power with no interruption. SQL Database has some additional features that are not available in SQL Server, such as built-in high availability, intelligence, and management.

Azure SQL Database offers the following deployment options:

- As a single database with its own set of resources managed via a logical SQL server. A single database is similar to a contained database in SQL Server. This option is optimized for modern application development of new cloud-born applications. Hyperscale and serverless options are available.
- An elastic pool, which is a collection of databases with a shared set of resources managed via a logical SQL server. Single databases can be moved into and out of an elastic pool. This option is optimized for modern application development of new cloud-born applications using the multi-tenant SaaS application pattern. Elastic pools provide a cost-effective solution for managing the performance of multiple databases that have variable usage patterns.

### Azure SQL Managed Instance

Azure SQL Managed Instance falls into the industry category of Platform-as-a-Service (PaaS), and is best for most migrations to the cloud. SQL Managed Instance is a collection of system and user databases with a shared set of resources that is lift-and-shift ready.

- Best for new applications or existing on-premises applications that want to use the latest stable SQL Server features and that are migrated to the cloud with minimal changes. An instance of SQL Managed Instance is similar to an instance of the Microsoft SQL Server database engine offering shared resources for databases and additional instance-scoped features.
- SQL Managed Instance supports database migration from on-premises with minimal to no database change. This option provides all of the PaaS benefits of Azure SQL Database but adds capabilities that were previously only available in SQL Server VMs. This includes a native virtual network and near 100% compatibility with on-premises SQL Server. Instances of SQL Managed Instance provide full SQL Server access and feature compatibility for migrating SQL Servers to Azure.

### SQL Server on Azure VM

SQL Server on Azure VM falls into the industry category Infrastructure-as-a-Service (IaaS) and allows you to run SQL Server inside a fully managed virtual machine (VM) in Azure.

- Best for migrations and applications requiring OS-level access. SQL virtual machines in Azure are lift-and-shift ready for existing applications that require fast migration to the cloud with minimal changes or no changes. SQL virtual machines offer full administrative control over the SQL Server instance and underlying OS for migration to Azure.

- Rapid development and test scenarios when you do not want to buy on-premises non-production SQL Server hardware. SQL virtual machines also run on standardized hardware that is owned, hosted, and maintained by Microsoft. When using SQL virtual machines, you can either pay-as-you-go for a SQL Server license already included in a SQL Server image or easily use an existing license. You can also stop or resume the VM as needed.

- SQL Server installed and hosted in the cloud runs on Windows Server or Linux virtual machines running on Azure, also known as an infrastructure as a service (IaaS). SQL virtual machines are a good option for migrating on-premises SQL Server databases and applications without any database change. All recent versions and editions of SQL Server are available for installation in an IaaS virtual machine.

- The most significant difference from SQL Database and SQL Managed Instance is that SQL Server on Azure Virtual Machines allows full control over the database engine. You can choose when to start maintenance/patching, change the recovery model to simple or bulk-logged, pause or start the service when needed, and you can fully customize the SQL Server database engine. With this additional control comes the added responsibility to manage the virtual machine.

- Optimized for migrating existing applications to Azure or extending existing on-premises applications to the cloud in hybrid deployments. In addition, you can use SQL Server in a virtual machine to develop and test traditional SQL Server applications. With SQL virtual machines, you have the full administrative rights over a dedicated SQL Server instance and a cloud-based VM. It is a perfect choice when an organization already has IT resources available to maintain the virtual machines. These capabilities allow you to build a highly customized system to address your application’s specific performance and availability requirements.

Additional differences are listed in the following table, but both SQL Database and SQL Managed Instance are optimized to reduce overall management costs to the minimum for provisioning and managing many databases. Ongoing administration costs are reduced since you do not have to manage any virtual machines, operating system, or database software. You do not have to manage upgrades, high availability, or backups.

In general, SQL Database and SQL Managed Instance can dramatically increase the number of databases managed by a single IT or development resource. Elastic pools also support SaaS multi-tenant application architectures with features including tenant isolation and the ability to scale to reduce costs by sharing resources across databases. SQL Managed Instance provides support for instance-scoped features enabling easy migration of existing applications, as well as sharing resources among databases.

[Comparison Table](https://docs.microsoft.com/en-us/azure/azure-sql/azure-sql-iaas-vs-paas-what-is-overview#comparison-table)

### Administration

For many businesses, the decision to transition to a cloud service is as much about offloading complexity of administration as it is cost. With IaaS and PaaS, Azure administers the underlying infrastructure and automatically replicates all data to provide disaster recovery, configures and upgrades the database software, manages load balancing, and does transparent failover if there is a server failure within a data center.

- With **SQL Database** and **SQL Managed Instance**, you can continue to administer your database, but you no longer need to manage the database engine, the operating system, or the hardware. Examples of items you can continue to administer include databases and logins, index and query tuning, and auditing and security. Additionally, configuring high availability to another data center requires minimal configuration and administration.
- With **SQL on Azure VM**, you have full control over the operating system and SQL Server instance configuration. With a VM, it's up to you to decide when to update/upgrade the operating system and database software and when to install any additional software such as anti-virus. Some automated features are provided to dramatically simplify patching, backup, and high availability. In addition, you can control the size of the VM, the number of disks, and their storage configurations. Azure allows you to change the size of a VM as needed. For information, see Virtual Machine and Cloud Service Sizes for Azure.

## implement Azure SQL Database managed instances

Unlike a single or pooled database, a managed instance of SQL Azure database doesn't have a public endpoint. There are two ways to connect to the managed instance databases securely: https://docs.microsoft.com/en-gb/azure/azure-sql/managed-instance/quickstart-content-reference-guide

For client application access, you can either create a VM in the same VNet (different subnet) or create a point-to-site VPN connection to the VNet from your client computer using one of these quickstarts:

- Enable public endpoint on your SQL Managed Instance in order to access your data directly from your environment.
- Create Azure Virtual Machine in the SQL Managed Instance VNet for client application connectivity, including SQL Server Management Studio.
- Set up point-to-site VPN connection to your SQL Managed Instance from your client computer on which you have SQL Server Management Studio and other client connectivity applications. This is other of two options for connectivity to your SQL Managed Instance and to its VNet.

### Configure with PowerShell

https://docs.microsoft.com/en-gb/azure/azure-sql/managed-instance/scripts/create-configure-managed-instance-powershell

### Configure with AzureRM

https://docs.microsoft.com/en-gb/azure/azure-sql/managed-instance/create-template-quickstart?tabs=azure-powershell

### Links

[SQL managed instance](https://docs.microsoft.com/en-us/azure/sql-database/sql-database-managed-instance-index)

Azure SQL Managed Instance is the intelligent, scalable cloud database service that combines the broadest SQL Server database engine compatibility with all the benefits of a fully managed and evergreen platform as a service. SQL Managed Instance has near 100% compatibility with the latest SQL Server (Enterprise Edition) database engine, providing a native virtual network (VNet) implementation that addresses common security concerns, and a business model favorable for existing SQL Server customers. SQL Managed Instance allows existing SQL Server customers to lift and shift their on-premises applications to the cloud with minimal application and database changes. At the same time, SQL Managed Instance preserves all PaaS capabilities (automatic patching and version updates, automated backups, high availability) that drastically reduce management overhead and TCO.

The following diagram outlines key features of SQL Managed Instance:

![Key features](https://docs.microsoft.com/en-us/azure/azure-sql/managed-instance/media/sql-managed-instance-paas-overview/key-features.png)

Azure SQL Managed Instance is designed for customers looking to migrate a large number of apps from an on-premises or IaaS, self-built, or ISV provided environment to a fully managed PaaS cloud environment, with as low a migration effort as possible. Using the fully automated Azure Data Migration Service, customers can lift and shift their existing SQL Server instance to SQL Managed Instance, which offers compatibility with SQL Server and complete isolation of customer instances with native VNet support. For more information on migration options and tools, see Migration overview: SQL Server to Azure SQL Managed Instance.
With Software Assurance, you can exchange your existing licenses for discounted rates on SQL Managed Instance using the Azure Hybrid Benefit for SQL Server. SQL Managed Instance is the best migration destination in the cloud for SQL Server instances that require high security and a rich programmability surface.

- [What is Azure SQL Database managed instance?](https://docs.microsoft.com/en-us/azure/sql-database/sql-database-managed-instance)
- [Quickstart: Create an Azure SQL Database managed instance](https://docs.microsoft.com/en-us/azure/sql-database/sql-database-managed-instance-get-started)

## configure HA for an Azure SQL database

### Geo-replication and automatic backups

One of the appealing features of Azure SQL database (single or pooled) is an ability to spin up four read-only copies of secondary databases in the same or different regions.

The feature is specially designed for a business continuity solution where you have an option to failover (manually or via some automation) to the secondary database in the event of a regional disruption or large-scale outages.

In active geo-replication, the data is replicated to secondary databases immediately after the transactions are committed on the primary database.

#### Backups

Regardless of the service tiers you choose, Azure automatically takes a backup of your databases as part of a disaster recovery solution. The backups are retained automatically between 7 and 35 days  based on your service tier (basic or higher) and do not incur any cost.

If your application needs require backups for longer than 35 days, Azure provides an option to keep the backups for up to 10 years.

### Links

- [High-availability and Azure SQL Database](https://docs.microsoft.com/en-us/azure/sql-database/sql-database-high-availability)

## publish an Azure SQL database

### Data Migration Options

https://datamigration.microsoft.com/

### Links

- [Azure SQL database deployment](https://docs.microsoft.com/en-us/azure/devops/pipelines/targets/azure-sqldb)
- [Tutorial: Deploy your ASP.NET app and Azure SQL Database code by using Azure DevOps Projects](https://docs.microsoft.com/en-us/azure/devops-project/azure-devops-project-sql-database)
