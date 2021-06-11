# Implement cloud infrastructure monitoring 

[Azure Monitor on Azure Docs](https://docs.microsoft.com/en-us/azure/azure-monitor/overview)

## monitor security

### [Azure Security Center](https://docs.microsoft.com/en-us/azure/security-center/security-center-monitoring)

After you enable security policies for a subscription’s resources, Security Center analyzes the security of your resources to identify potential vulnerabilities. Information about your network configuration is available instantly. Depending on the number of VMs and computers that you have with the agent installed, it may take an hour or more to collect information about VMs and computer's configuration, such as security update status and operating system configuration, to become available. You can view a full list of issues and ways to harden your network and remediate risk in the Recommendations tile.

### [Security Control: Logging and Monitoring](https://docs.microsoft.com/en-us/azure/security/benchmarks/security-control-logging-monitoring)

Security logging and monitoring focuses on activities related to enabling, acquiring, and storing audit logs for Azure services.

- 2.1: Use approved time synchronization sources - Responsibility: Microsoft
- 2.2: Configure central security log management - Responsibility: Customer
- 2.3: Enable audit logging for Azure resources - Responsibility: Customer
- 2.4: Collect security logs from operating systems - Responsibility: Customer 
- 2.5: Configure security log storage retention - Responsibility: Customer
- 2.6: Monitor and review Logs - Responsibility: Customer  
- 2.7: Enable alerts for anomalous activities - Responsibility: Customer
- 2.8: Centralize anti-malware logging - Responsibility: Customer
- 2.9: Enable DNS query logging - Responsibility: Customer
- 2.10: Enable command-line audit logging - Responsibility: Customer

### [Azure infrastructure monitoring](https://docs.microsoft.com/en-us/azure/security/fundamentals/infrastructure-monitoring)

- Configuration and change management
- Vulnerability management
- Vulnerability scanning
- Protective monitoring
- Incident management
  - Microsoft implements a security incident management process to facilitate a coordinated response to incidents, should one occur.
  - If Microsoft becomes aware of unauthorized access to customer data that's stored on its equipment or in its facilities, or it becomes aware of unauthorized access to such equipment or facilities resulting in loss, disclosure, or alteration of customer data, Microsoft takes the following actions:
    - Promptly notifies the customer of the security incident.
    - Promptly investigates the security incident and provides customers detailed information about the security incident.
    - Takes reasonable and prompt steps to mitigate the effects and minimize any damage resulting from the security incident.

## monitor performance

### [configure diagnostic settings on resources](https://docs.microsoft.com/en-us/azure/azure-monitor/platform/diagnostic-settings)

Platform logs and metrics can be sent to the destinations in the following table.

#### Destinations

|Destination	|Description|
|:---|:---|
|Log Analytics workspace	|Sending logs and metrics to a Log Analytics workspace allows you to analyze them with other monitoring data collected by Azure Monitor using powerful log queries and also to leverage other Azure Monitor features such as alerts and visualizations.|
|Event hubs	|Sending logs and metrics to Event Hubs allows you to stream data to external systems such as third-party SIEMs and other log analytics solutions.|
|Azure storage account	|Archiving logs and metrics to an Azure storage account is useful for audit, static analysis, or backup. Compared to Azure Monitor Logs and a Log Analytics workspace, Azure storage is less expensive and logs can be kept there indefinitely.|

#### Destination Requirements

|Destination|	Requirements|
|:--- |:---|
|Log Analytics workspace	|The workspace does not need to be in the same region as the resource being monitored.|
|Event hubs	|The shared access policy for the namespace defines the permissions that the streaming mechanism has. Streaming to Event Hubs requires Manage, Send, and Listen permissions. To update the diagnostic setting to include streaming, you must have the ListKey permission on that Event Hubs authorization rule.<br /><br />The event hub namespace needs to be in the same region as the resource being monitored if the resource is regional.|
|Azure storage account	|You should not use an existing storage account that has other, non-monitoring data stored in it so that you can better control access to the data. If you are archiving the Activity log and resource logs together though, you may choose to use the same storage account to keep all monitoring data in a central location.<br /><br />To send the data to immutable storage, set the immutable policy for the storage account as described in Set and manage immutability policies for Blob storage. You must follow all steps in this article including enabling protected append blobs writes.<br /><br />The storage account needs to be in the same region as the resource being monitored if the resource is regional.|

### [create a performance baseline for resources](https://www.lynda.com/Azure-tutorials/Create-baseline-resources/782139/5010821-4.html)
  -	[ ] monitor for unused resources - [Use Azure Advisor](https://docs.microsoft.com/en-us/azure/advisor/)
    - Azure Advisor scans your Azure configuration and recommends changes to optimize deployments, increase security, and save you money.
  -	[ ] monitor performance capacity - [How to chart performance with Azure Monitor for VMs](https://docs.microsoft.com/en-us/azure/azure-monitor/insights/vminsights-performance)
  -	[ ] [visualize diagnostics data using Azure Monitor](https://docs.microsoft.com/en-us/azure/azure-monitor/visualizations)

### [Create diagnostic setting to collect platform logs and metrics in Azure](https://docs.microsoft.com/en-us/azure/azure-monitor/platform/diagnostic-settings)

See notes above

### [Create diagnostic setting in Azure using a Resource Manager template](https://docs.microsoft.com/en-us/azure/azure-monitor/platform/diagnostic-settings-template)

You can enable the various categories of diagnostics settings by using an Azure RM template.

### [Metric Baseline – Get](https://docs.microsoft.com/en-us/rest/api/monitor/metricbaseline/get)

The Microsoft.Insights/baseline API can be queries for baseline values for a specific metric.

### [Azure Monitor overview](https://docs.microsoft.com/en-us/azure/azure-monitor/overview)

Azure Monitor collects data automatically from a range of components. For example:
	
- Application data: Data that relates to your custom application code.
- Operating system data: Data from the Windows or Linux virtual machines that host your application.
- Azure resource data: Data that relates to the operations of an Azure resource, such as a web app or a load balancer.
- Azure subscription data: Data that relates to your subscription. It includes data about Azure health and availability.
- Azure tenant data: Data about your Azure organization-level services, such as Azure Active Directory.

Because Azure Monitor is an automatic system, it begins to collect data from these sources as soon as you create Azure resources such as virtual machines and web apps. You can extend the data that Azure Monitor collects by:

- Enabling diagnostics: For some resources, such as Azure SQL Database, you receive full information about a resource only after you have enabled diagnostic logging for it. You can use the Azure portal, the Azure CLI, or PowerShell to enable diagnostics.
- Adding an agent: For virtual machines, you can install the Log Analytics agent and configure it to send data to a Log Analytics workspace. This agent increases the amount of information that's sent to Azure Monitor.

**Logs** - Timestamped information
**Metrics** - Numerical values that describe an aspect of a system at a point in time
**Kusto Query Language** - can be used to query your logs

Events, captured from the event logs of monitored computers, are just one type of data source. Azure Monitor provides many other types of data sources. For example, the Heartbeat data source reports the health of all computers that report to your Log Analytics workspace. You can also capture data from performance counters, and update management records.

#### Custom sources

Azure Monitor can collect log data from any REST client using the Data Collector API. This allows you to create custom monitoring scenarios and extend monitoring to resources that don't expose telemetry through other sources.

#### Application Insights

Application Insights monitors the availability, performance, and usage of your web applications whether they're hosted in the cloud or on-premises. It leverages the powerful data analysis platform in Azure Monitor to provide you with deep insights into your application's operations and diagnose errors without waiting for a user to report them. Application Insights includes connection points to a variety of development tools and integrates with Visual Studio to support your DevOps processes.

#### Azure Monitor for containers

Azure Monitor for containers is a feature designed to monitor the performance of container workloads deployed to managed Kubernetes clusters hosted on Azure Kubernetes Service (AKS). It gives you performance visibility by collecting memory and processor metrics from controllers, nodes, and containers that are available in Kubernetes through the Metrics API. Container logs are also collected. After you enable monitoring from Kubernetes clusters, these metrics and logs are automatically collected for you through a containerized version of the Log Analytics agent for Linux.

#### Azure Monitor for VMs

Azure Monitor for VMs monitors your Azure virtual machines (VM) at scale by analyzing the performance and health of your Windows and Linux VMs, including their different processes and interconnected dependencies on other resources and external processes. The solution includes support for monitoring performance and application dependencies for VMs hosted on-premises or another cloud provider.

#### Monitoring solutions

Monitoring solutions in Azure Monitor are packaged sets of logic that provide insights for a particular application or service. They include logic for collecting monitoring data for the application or service, queries to analyze that data, and views for visualization. Monitoring solutions are available from Microsoft and partners to provide monitoring for various Azure services and other applications.

#### Responding to critical situations

In addition to allowing you to interactively analyze monitoring data, an effective monitoring solution must be able to proactively respond to critical conditions identified in the data that it collects. This could be sending a text or mail to an administrator responsible for investigating an issue. Or you could launch an automated process that attempts to correct an error condition.

- Autoscale
- Visualizing monitoring data
- Dashboards - Azure dashboards allow you to combine different kinds of data, including both metrics and logs, into a single pane in the Azure portal
- Views - visually present log data in Azure Monitor

#### Integrate and export data

You'll often have the requirement to integrate Azure Monitor with other systems and to build custom solutions that use your monitoring data. Other Azure services work with Azure Monitor to provide this integration.

- Event Hub: Azure Event Hubs is a streaming platform and event ingestion service that can transform and store data using any real-time analytics provider or batching/storage adapters. Use Event Hubs to stream Azure Monitor data to partner SIEM and monitoring tools.
- Logic Apps: Logic Apps is a service that allows you to automate tasks and business processes using workflows that integrate with different systems and services. Activities are available that read and write metrics and logs in Azure Monitor, which allows you to build workflows integrating with a variety of other systems.
- API: Multiple APIs are available to read and write metrics and logs to and from Azure Monitor in addition to accessing generated alerts. You can also configure and retrieve alerts. This provides you with essentially unlimited possibilities to build custom solutions that integrate with Azure Monitor.

### [Quickstart: Monitor an Azure resource with Azure Monitor](https://docs.microsoft.com/en-us/azure/azure-monitor/learn/quick-monitor-azure-resource)

### [Azure Monitor Workbooks](https://docs.microsoft.com/en-us/azure/azure-monitor/platform/workbooks-overview)

Workbooks provide a flexible canvas for data analysis and the creation of rich visual reports within the Azure portal. They allow you to tap into multiple data sources from across Azure, and combine them into unified interactive experiences.

Workbooks are currently compatible with the following data sources:

- Logs
- Metrics
- Azure Resource Graph
- Alerts (Preview)
- Workload Health
- Azure Resource Health
- Azure Data Explorer

### [Azure Monitor workbook visualizations](https://docs.microsoft.com/en-us/azure/azure-monitor/platform/workbooks-visualizations)

Workbooks provide a rich set of capabilities for visualizing your data. For detailed examples of each visualization type you can consult the example links below:

- Text
- Charts
- Grids
- Tiles
- Trees
- Graphs
- Composite bar

### [Collect data from an Azure virtual machine with Azure Monitor](https://docs.microsoft.com/en-us/azure/azure-monitor/learn/quick-collect-azurevm)

## monitor health and availability

### monitor networking - [Network monitoring solutions](https://docs.microsoft.com/en-us/azure/networking/network-monitoring-overview)

Azure offers a host of solutions to monitor your networking assets. Azure has solutions and utilities to monitor network connectivity, the health of ExpressRoute circuits, and analyze network traffic in the cloud.

#### Network Performance Monitor (NPM)

Network Performance Monitor (NPM) is a suite of capabilities, each of which is geared towards monitoring the health of your network, network connectivity to your applications, and provides insights into the performance of your network. NPM is cloud-based and provides a hybrid network monitoring solution that monitors connectivity between:

- Cloud deployments and on-premises locations
- Multiple data centers and branch offices
- Mission critical multi-tier applications/micro-services
- User locations and web-based applications (HTTP/HTTPs)

Performance Monitor, ExpressRoute Monitor, and Service Connectivity Monitor are monitoring capabilities within NPM and are described below.

- **Performance Monitor** - part of NPM and is network monitoring for cloud, hybrid, and on-premises environments
- **ExpressRoute Monitor** - offers comprehensive ExpressRoute monitoring for Azure Private peering and Microsoft peering connections. You can monitor E2E connectivity and performance between your branch offices and Azure over ExpressRoute
- **Service Connectivity Monitor** - with Service Connectivity monitoring, you can now test reachability of applications and detect performance bottlenecks across on-premises, carrier networks and cloud/private data centers
- **Traffic Analytics** - Traffic Analytics is a cloud-based solution that provides visibility into user and application activity on your cloud networks
- **DNS Analytics** - Built for DNS Administrators, this solution collects, analyzes, and correlates DNS logs to provide security, operations, and performance-related insights. 

### monitor service health - [Azure Service Health](https://azure.microsoft.com/en-us/features/service-health)

- Review active incidents, planned maintenance, and health advisories in a personalized dashboard for service health based on your Azure subscriptions, services, and regions.
- Get alerts about active incidents and upcoming maintenance
- Download official reports and root cause analyses (RCAs)

### [Resource Health overview](https://docs.microsoft.com/en-us/azure/service-health/resource-health-overview)

**Azure Resource Health** helps you diagnose and get support for service problems that affect your Azure resources. It reports on the current and past health of your resources.

[Azure status](https://status.azure.com/) reports on service problems that affect a broad set of Azure customers. Resource Health gives you a personalized dashboard of the health of your resources. Resource Health shows all the times that your resources have been unavailable because of Azure service problems. This data makes it easy for you to see if an SLA was violated.

#### Resource definition and health assessment

A resource is a specific instance of an Azure service, such as a virtual machine, web app, or SQL Database. Resource Health relies on signals from different Azure services to assess whether a resource is healthy.

#### Health status

- Available - means that there are no events detected that affect the health of the resource. In cases where the resource recovered from unplanned downtime during the last 24 hours, you'll see a "Recently resolved" notification.
- Unavailable - means that the service detected an ongoing platform or non-platform event that affects the health of the resource.
  - Platform events are triggered by multiple components of the Azure infrastructure. They include both scheduled actions (for example, planned maintenance) and unexpected incidents (for example, an unplanned host reboot or degraded host hardware that is predicted to fail after a specified time window).
  - Non-platform events are triggered by user actions. Examples include stopping a virtual machine or reaching the maximum number of connections to Azure Cache for Redis.
- Unknown - means that Resource Health hasn't received information about the resource for more than 10 minutes. Although this status isn't a definitive indication of the state of the resource, it's an important data point for troubleshooting.
  - If the resource is running as expected, the status of the resource will change to Available after a few minutes.
- Degraded - means that your resource detected a loss in performance, although it's still available for use.

## monitor cost

### [Monitoring your cloud costs](https://docs.microsoft.com/en-us/azure/architecture/framework/cost/monitoring)

### [Use cost alerts to monitor usage and spending](https://docs.microsoft.com/en-us/azure/cost-management-billing/costs/cost-mgt-alerts-monitor-usage-spending)

### [Download or view your Azure billing invoice and daily usage data](https://docs.microsoft.com/en-us/azure/cost-management-billing/manage/download-azure-invoice-daily-usage-date)

### monitor spend - [Use cost alerts to monitor usage and spending](https://docs.microsoft.com/en-us/azure/cost-management/cost-mgt-alerts-monitor-usage-spending)

### report on spend - [Tutorial: Manage costs by using Cloudyn](https://docs.microsoft.com/en-us/azure/cost-management/tutorial-manage-costs)

## configure advanced logging

### implement and configure Azure Monitor insights, including App Insights, Networks, Containers - [Overview of Insights in Azure Monitor](https://docs.microsoft.com/en-us/azure/azure-monitor/insights/insights-overview)

#### configure a Log Analytics workspace - [Designing your Azure Monitor Logs deployment](https://docs.microsoft.com/en-us/azure/azure-monitor/platform/design-logs-deployment)

A Log Analytics workspace provides:

- A geographic location for data storage.
- Data isolation by granting different users access rights following one of our recommended design strategies.
- Scope for configuration of settings like pricing tier, retention, and data capping.

## configure logging for workloads

### initiate automated responses by using Action Groups - [Create action group](https://docs.microsoft.com/en-us/azure/azure-monitor/platform/action-groups#create-an-action-group-by-using-the-azure-portal)

- A Signal can be a resource stopping as well as a metric 
- The Criteria/Logic Test will output a boolean
- In an Action you can run PowerShell code as well as perform actions on resources
- Monitor conditions are recorded over time so can be audited

When defining an alert you specify:

When defining, you specify:

- Resource
- Condition
  - Signal Type is Metrics or Activity Log (administrative actions - create VM, stop VM)
  - You get an estimated monthly cost for the Condition
- Actions
- The Rule Name, Description, Severity

- Action Groups with a notification type of Email are rate limited to 1 message every 1 minute
- Action Groups with a notification type of SMS are rate limited to 1 message every 5 minutes

#### Notification Types

- ITSM is for creating a ticket in your ticketing system.
- You can have a Voice Call.
- Can kick off a Runbook to run some PowerShell

Define your Action Groups first so that you can share them between Alerts.

## configure and manage advanced alerts

### [collect alerts and metrics across multiple subscriptions](https://docs.microsoft.com/en-us/azure/azure-monitor/platform/alerts-overview#alerts-experience)

You can filter the **All Alerts** view by selecting values in the drop-down menus at the top of the page.

|Column	|Description|
|:--- |:---|
|Subscription	|Select the Azure subscriptions for which you want to view the alerts. You can optionally choose to select all your subscriptions. Only alerts that you have access to in the selected subscriptions are included in the view.|
|Resource group	|Select a single resource group. Only alerts with targets in the selected resource group are included in the view.|
|Time range	|Only alerts fired within the selected time range are included in the view. Supported values are the past hour, the past 24 hours, the past 7 days, and the past 30 days.|

Select the following values at the top of the Alerts page to open another page:

|Value	|Description|
|:---|:---|
|Total alerts	|The total number of alerts that match the selected criteria. Select this value to open the All Alerts view with no filter.|
|Smart groups	|The total number of smart groups that were created from the alerts that match the selected criteria. Select this value to open the smart groups list in the All Alerts view.|
|Total alert rules	|The total number of alert rules in the selected subscription and resource group. Select this value to open the Rules view filtered on the selected subscription and resource group.|

### [view Alerts in Azure Monitor logs](https://docs.microsoft.com/en-us/azure/azure-monitor/platform/alerts-log)

Log alerts allow users to use a Log Analytics query to evaluate resources logs every set frequency, and fire an alert based on the results. Rules can trigger one or more actions using Action Groups. Learn more about functionality and terminology of log alerts.

This article shows you how to create and manage log alerts using Azure Monitor. Alert rules are defined by three components:

- Target: A specific Azure resource to monitor.
- Criteria: Logic to evaluate. If met, the alert fires.
- Action: Notifications or automation - email, SMS, webhook, and so on.
