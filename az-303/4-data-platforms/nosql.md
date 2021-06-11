# configure storage account tables

## Azure Table storage

_The content in this article applies to the original Azure Table storage. However, there is now a premium offering for table storage: the Azure Cosmos DB Table API. This API offers throughput-optimized tables, global distribution, and automatic secondary indexes. There are some feature differences between Table API in Azure Cosmos DB and Azure table storage. For more information, and to try out the premium experience, see Azure Cosmos DB Table API._

Common uses of Table storage include:

- Storing TBs of structured data capable of serving web scale applications
- Storing datasets that don't require complex joins, foreign keys, or stored procedures and can be denormalized for fast access
- Quickly querying data using a clustered index
- Accessing data using the OData protocol and LINQ queries with WCF Data Service .NET Libraries

### Table storage concepts

**URL format**: Azure Table Storage accounts use this format: `http://<storage account>.table.core.windows.net/<table>`

Azure Cosmos DB Table API accounts use this format: `http://<storage account>.table.cosmosdb.azure.com/<table>`

You can address Azure tables directly using this address with the OData protocol. For more information, see OData.org.

**Accounts**: All access to Azure Storage is done through a storage account. For more information about storage accounts, see Storage account overview.

All access to Azure Cosmos DB is done through a Table API account. See Create a Table API account for details creating a Table API account.

**Table**: A table is a collection of entities. Tables don't enforce a schema on entities, which means a single table can contain entities that have different sets of properties.

**Entity**: An entity is a set of properties, similar to a database row. An entity in Azure Storage can be up to 1MB in size. An entity in Azure Cosmos DB can be up to 2MB in size.

**Properties**: A property is a name-value pair. Each entity can include up to 252 properties to store data. Each entity also has three system properties that specify a partition key, a row key, and a timestamp. Entities with the same partition key can be queried more quickly, and inserted/updated in atomic operations. An entity's row key is its unique identifier within a partition.

## Understanding the Table service data model

The Table service offers structured storage in the form of tables. The following sections outline the Table service data model.

A storage account is a globally unique entity within the storage system. The storage account is the parent namespace for the Table service, and is the basis for authorization. You can create any number of tables within a given storage account, as long as each table is uniquely named.

The storage account must always be specified in the request URI. The base URI for accessing the Table service is as follows:

    https://myaccount.table.core.windows.net  

### Tables, Entities, and Properties

Tables store data as collections of entities. Entities are similar to rows. An entity has a primary key and a set of properties. A property is a name, typed-value pair, similar to a column.

The Table service does not enforce any schema for tables, so two entities in the same table may have different sets of properties. Developers may choose to enforce a schema on the client side. A table may contain any number of entities.

#### System Properties

An entity always has the following system properties:

- PartitionKey property
  - Tables are partitioned to support load balancing across storage nodes. A table's entities are organized by partition. A partition is a consecutive range of entities possessing the same partition key value. The partition key is a unique identifier for the partition within a given table, specified by the PartitionKey property.
  - You must include the PartitionKey property in every insert, update, and delete operation.
- RowKey property
  - You must include the RowKey property in every insert, update, and delete operation.
- Timestamp property
  - The Timestamp property is a DateTime value that is maintained on the server side to record the time an entity was last modified. The Table service uses the Timestamp property internally to provide optimistic concurrency.

These system properties are automatically included for every entity in a table. The names of these properties are reserved and cannot be changed. The developer is responsible for inserting and updating the values of PartitionKey and RowKey. The server manages the value of Timestamp, which cannot be modified.

#### Property Types

The Table service supports a subset of data types defined by the OData Protocol Specification. 

By default a property is created as type String, unless you specify a different type. To explicitly type a property, specify its data type by using the appropriate OData data type for an Insert Entity or Update Entity operation.

The Table service does not persist null values for properties. When querying entities, the above property types are all non-nullable. When writing entities, the above property types are all nullable, and any property with a null value is handled as if the payload did not contain that property.

# select appropriate CosmosDB APIs

## Service Quotas

https://docs.microsoft.com/en-gb/azure/cosmos-db/concepts-limits

## Security and Compliance in Azure Cosmos DB

### Security

- By default provides encryption at rest and in transit for documents and backups in all Azure regions without requiring and configuration from you.

#### Inbound Request Filtering

- The first defense you can turn on is IP address-based access control. The default is allowed, provided a request is with a valid authorization token.
- Can add a single IP or IP ranges using CIDR, or by subnet of the Vnet.

#### Fine-Grained Access

- Uses a hash-based message authorization (HMAC) to authorize access at the account level or even at the resource level like database, container, or items.
- Access to the account and resources is granted by either the master key or a resource token. The master key is used for full administrative access; the resource token approach is based on the fine-grained Role-Based Access Code (RBAC) security principle.
	
https://docs.microsoft.com/en-gb/azure/cosmos-db/database-security

### Compliance

- Has a major industry certification to help the customer meet their compliance obligations across regulated industries and markets worldwide.

https://docs.microsoft.com/en-gb/azure/cosmos-db/compliance

## Understand the Cosmos DB account

- The Azure Cosmos Account is a logical construct that has a globally unique DNS name.
- For HA you add or remove regions to the Account at any time.
- You can set up multiple master/write replicas across different regions in your Cosmos Account.
- You can create 100 Cosmos DB Accounts under one Azure subscription.
- Within the account you create Databases
- Within the database you create Containers
  - This is the fundamental unit of scalability - it is a logical resource composed of one or more partitions
  - The Items in the Container can be Collections, Tables, Graphs
- You must specify an API (Core (SQL), MongoDB, Azure Table etc) when creating the Account
  - You must create a separate Cosmos Account for each API

## Create, read, update, and delete data by appropriate APIs

- As of writing the exam ref, you can only create one API per account
- Data is stored in JSON format, and you can use multi-model APIs:
  - SQL API
  - MongoDB API
  - Gremlin (for graph modelling between entities)
  - Table API
  - Cassandra API
- The MongoDB and Cassandra APIs match the real APIs
- Use the Table API if you are using Azure Table Storage but want to take advantage of:
  - Better performance
  - Turnkey global distribution
  - Automatic indexing

There is an Azure Cosmos DB local development emulator: https://docs.microsoft.com/en-gb/azure/cosmos-db/local-emulator?tabs=ssl-netstd21

## SQL API

- SQL and Gremlin APIs give you the option to use stored procedures, user defined functions (UDFs) and triggers.
  - These are essentially JavaScript functions scoped at the container level.
- Key considerations when writing server-side code with Cosmos DB:
  - Scope
    - Stored procedures and triggers are scoped at the partition key and must be supplied with an input parameter for the partition key
	- UDFs are scoped at the database level
  - Stored procedures and triggers guarantee atomicity (ACID) like in any relational database.
    - Transactions are automatically rolled back in case of any exception, otherwise they're committed as a single unit of work.
  - Writes
	- Queries using stored procedures and triggers are always executed on the primary replica as these are intended for write operations to guarantee strong consistency for secondary replicas
	- UDFs can be written to the primary or secondary replica
  - The server-side code must complete within a specified timeout threshold limit, or you must implement a continuation batch model for long-running code.
    - If the code doesn't complete withing the time, Cosmos DB will roll back the whole transaction automatically
  - There are two types of triggers you can set up:
	- Pre-triggers As the name defines, you can invoke some logic on the database containers before the items are created, updated, or deleted
	- Post-triggers Run after the data is written or updated in the database

SQL query reference: https://docs.microsoft.com/en-gb/azure/cosmos-db/sql-query-getting-started

# set up replicas in CosmosDB

## Global Distribution and Multiple Write Replicas

- In the Replicate Data Globally menu, you can add or remove regions to the Account.
- You cannot turn off or disable multi-region writes after it's enabled.

## Business Continuity and Disaster Recovery

- Global distribution with the ability to have multiple read/write replicas provides an option to either automate failover to the secondary database in case of a regional outage or do it manually when needed.
- Cosmos DB also automatically backups up your database every 4 hours and stores it in the GRS blob storage for DR.
  - At any given time, at least the last 2 backups are retained for 30 days.
- Options for backup and restore: https://docs.microsoft.com/en-gb/azure/cosmos-db/online-backup-and-restore#options-to-manage-your-own-backups
	
## Manage scalability and implementing partioning schemes for Cosmos DB

- Uses hash-based partioning to spread logical partitions across physical partitions.
- Queries that access data within a single logical partition are more cost-effective than queries that access multiple partitions.
- You must be mindful when choosing a partition key to query data efficiently and avoid "hot spots" within a partition.

### Considerations

- A single partition has an upper limit of 20 Gb
- Containers have a minimum throughput of 400 RUs/s (request units per second)
  - RUs are a blended measure of computation cost (CPU, memory, disk I/O, network I/O). 1 RU corresponds to the throughput of reading a 1KB document.
  - All requests on the partition are charged in the form of RUs.
  - If the request exceeds the provisioned RUs on the partition, Cosmos DB throws RequestRateTooLargeException or HTTP Status Code 429.
- Choose a partition key that has a wide range of values and access patterns that are evenly spread across logical partitions
- Candidates for partition keys might include properties that frequently appear as a filter in your queries

Best practices to choose a partition key: https://docs.microsoft.com/en-gb/azure/cosmos-db/synthetic-partition-keys

### Cross-partition query

- Azure Cosmos DB automatically handles the queries against the single partition that has a partition key in the header request.
- If a query doesn't filter on the partition key, Azure Cosmos DB fans out the query across partitions.
  - The fan-out is done by issuing individual queries to all partitions, and it's not default behaviour. You have to explicitly mention in the using Feed options by setting EnableCrossPartitionQuery property to "on".

### Defining a partition key

You can only define a partition key on a container during its creation; it cannot be changed after the container is created. So be very thoughtful while choosing a partition.

## Data consistency

- If you read data that isn't the latest version it is consider a dirty read. 
- Data consistency, latency, and performance don't seem to show much of a difference within a data centre as data replication is much faster and takes only one millisecond. However in the geo-distribution scenario, when data replication takes several hundred milliseconds, the chance of dirty reads increases.

### Options

Cosmos DB provides the following data consistency options with trade-offs between latency, availability, and performance:

[Consistency Levels](https://docs.microsoft.com/en-gb/azure/cosmos-db/consistency-levels)
[Consistency Levels across APIs](https://docs.microsoft.com/en-us/azure/cosmos-db/consistency-levels-across-apis)
[Consistency, availability, and performance tradeoffs](https://docs.microsoft.com/en-us/azure/cosmos-db/consistency-levels-tradeoffs)

#### Strong

-  Ensures no dirty reads.
-  Client always reads the latest version of data committed across the multiple read replicas in single or multi-regions.
-  Trade off is performance.
  - When you write to the database, everyone waits for Cosmos DB to serve the latest writes after it has been saved across all read replicas.

#### Bounded Staleness

- Gives you an ability to decide how much staleness of data in terms of updates in a time interval an application can tolerate.
- You can specify the lag by an x version of updates of an item or by time interval T by which read lags behind a write.
- The typical use case is to guarantee low latency for writes for globally distributed applications

#### Session

- Ensures that there are no dirty reads on the write regions.
- A session is scoped to a client session, and the client may read what they wrote instead of having to wait for it to be globally committed.

#### Consistent Prefix

- Guarantees reads are never out of order of writes.
- If the database is updates 3 times with versions V1, V2, V3 then the client will always see V1, V1V2, or V1V2V3. They will never see V2V1V3 for example.

#### Eventual 

- Use when you're least worried about the freshness of data across read replicas and the order of writes over time, but you need the highest level of availability and low latency.

### Modifying data consistency

Navigate to the Cosmos DB account and the Data consistency pane, and select the desired consistency from the five consistency levels. Bounded staleness will also let you set the lag in time or number of missing updates an application can tolerate.

## Business Continuity and Disaster Recovery

https://docs.microsoft.com/en-gb/azure/cosmos-db/high-availability

- For HA, it's recommended that you configure Cosmos DB with multi-region writes (at least two regions). In the event of a regional disruption, the failover is instantaneous, and the application doesn't undergo any change; it transparently happens behind the scenes.
- Also if you're using a default consistency level of Strong, there will not be any data loss before and after the failover. 
- For Bounded Staleness you may encounter a potential data loss up to the lag (time or operations) you've set up.
- For Session, Consistent Prefix, and Eventual consistency options, the data loss could be up to a maximum of 5 seconds.
