# Implement an application infrastructure 

## create and configure Azure App Service

### Links

[App Service overview](https://docs.microsoft.com/en-us/azure/app-service/overview)

Here are some key features of App Service:

- **Multiple languages and frameworks** - App Service has first-class support for ASP.NET, ASP.NET Core, Java, Ruby, Node.js, PHP, or Python. You can also run PowerShell and other scripts or executables as background services.
- **Managed production environment** - App Service automatically patches and maintains the OS and language frameworks for you. Spend time writing great apps and let Azure worry about the platform.
- **Containerization and Docker** - Dockerize your app and host a custom Windows or Linux container in App Service. Run multi-container apps with Docker Compose. Migrate your Docker skills directly to App Service.
- **DevOps optimization** - Set up continuous integration and deployment with Azure DevOps, GitHub, BitBucket, Docker Hub, or Azure Container Registry. Promote updates through test and staging environments. Manage your apps in App Service by using Azure PowerShell or the cross-platform command-line interface (CLI).
- **Global scale with high availability** - Scale up or out manually or automatically. Host your apps anywhere in Microsoft's global datacenter infrastructure, and the App Service SLA promises high availability.
- **Connections to SaaS platforms and on-premises data** - Choose from more than 50 connectors for enterprise systems (such as SAP), SaaS services (such as Salesforce), and internet services (such as Facebook). Access on-premises data using Hybrid Connections and Azure Virtual Networks.
- **Security and compliance** - App Service is ISO, SOC, and PCI compliant. Authenticate users with Azure Active Directory, Google, Facebook, Twitter, or Microsoft account. Create IP address restrictions and manage service identities.
- **Application templates** - Choose from an extensive list of application templates in the Azure Marketplace, such as WordPress, Joomla, and Drupal.
- **Visual Studio and Visual Studio Code integration** - Dedicated tools in Visual Studio and Visual Studio Code streamline the work of creating, deploying, and debugging.
- **API and mobile features** - App Service provides turn-key CORS support for RESTful API scenarios, and simplifies mobile app scenarios by enabling authentication, offline data sync, push notifications, and more.
- **Serverless code** - Run a code snippet or script on-demand without having to explicitly provision or manage infrastructure, and pay only for the compute time your code actually uses (see Azure Functions).

Besides App Service, Azure offers other services that can be used for hosting websites and web applications. For most scenarios, App Service is the best choice. For microservice architecture, consider Azure Spring-Cloud Service or Service Fabric. If you need more control over the VMs on which your code runs, consider Azure Virtual Machines. For more information about how to choose between these Azure services, see Azure App Service, Virtual Machines, Service Fabric, and Cloud Services comparison.

### App Service on Linux

App Service can also host web apps natively on Linux for supported application stacks. It can also run custom Linux containers (also known as Web App for Containers).

### Built-in languages and frameworks

App Service on Linux supports a number of language specific built-in images. Just deploy your code. Supported languages include: Node.js, Java (JRE 8 & JRE 11), PHP, Python, .NET Core and Ruby. Run az webapp list-runtimes --linux to view the latest languages and supported versions. If the runtime your application requires is not supported in the built-in images, you can deploy it with a custom container.

### Limitations

- App Service on Linux is not supported on Shared pricing tier.
- You can't mix Windows and Linux apps in the same App Service plan.
- Within the same resource group, you can't mix Windows and Linux apps in the same region.
- The Azure portal shows only features that currently work for Linux apps. As features are enabled, they're activated on the portal.
- When deployed to built-in images, your code and content are allocated a storage volume for web content, backed by Azure Storage. The disk latency of this volume is higher and more variable than the latency of the container filesystem. Apps that require heavy read-only access to content files may benefit from the custom container option, which places files in the container filesystem instead of on the content volume.

[Introduction to the App Service Environments](https://docs.microsoft.com/en-us/azure/app-service/environment/intro)

### Overview

The Azure App Service Environment is an Azure App Service feature that provides a fully isolated and dedicated environment for securely running App Service apps at high scale. This capability can host your:

- Windows web apps
- Linux web apps
- Docker containers
- Mobile apps
- Functions

App Service environments (ASEs) are appropriate for application workloads that require:

- Very high scale.
- Isolation and secure network access.
- High memory utilization.

Customers can create multiple ASEs within a single Azure region or across multiple Azure regions. This flexibility makes ASEs ideal for horizontally scaling stateless application tiers in support of high requests per second (RPS) workloads.

ASEs host applications from only one customer and do so in one of their VNets. Customers have fine-grained control over inbound and outbound application network traffic. Applications can establish high-speed secure connections over VPNs to on-premises corporate resources.

- ASE comes with its own pricing tier, learn how the Isolated offering helps drive hyper-scale and security.
- App Service Environments v2 provide a surrounding to safeguard your apps in a subnet of your network and provides your own private deployment of Azure App Service.
- Multiple ASEs can be used to scale horizontally. For more information, see how to set up a geo-distributed app footprint.
- ASEs can be used to configure security architecture, as shown in the AzureCon Deep Dive. To see how the security architecture shown in the AzureCon Deep Dive was configured, see the article on how to implement a layered security architecture with App Service environments.
- Apps running on ASEs can have their access gated by upstream devices, such as web application firewalls (WAFs). For more information, see Web application firewall (WAF).
- App Service Environments can be deployed into Availability Zones (AZ) using zone pinning. See App Service Environment Support for Availability Zones for more details.

### Dedicated environment

An ASE is dedicated exclusively to a single subscription and can host 100 App Service Plan instances. The range can span 100 instances in a single App Service plan to 100 single-instance App Service plans, and everything in between.

An ASE is composed of front ends and workers. Front ends are responsible for HTTP/HTTPS termination and automatic load balancing of app requests within an ASE. Front ends are automatically added as the App Service plans in the ASE are scaled out.

Workers are roles that host customer apps. Workers are available in three fixed sizes:

- One vCPU/3.5 GB RAM
- Two vCPU/7 GB RAM
- Four vCPU/14 GB RAM

Customers do not need to manage front ends and workers. All infrastructure is automatically added as customers scale out their App Service plans. As App Service plans are created or scaled in an ASE, the required infrastructure is added or removed as appropriate.

There is a flat monthly rate for an ASE that pays for the infrastructure and doesn't change with the size of the ASE. In addition, there is a cost per App Service plan vCPU. All apps hosted in an ASE are in the Isolated pricing SKU. For information on pricing for an ASE, see the App Service pricing page and review the available options for ASEs.

### Virtual network support

The ASE feature is a deployment of the Azure App Service directly into a customer's Azure Resource Manager virtual network. To learn more about Azure virtual networks, see the Azure virtual networks FAQ. An ASE always exists in a virtual network, and more precisely, within a subnet of a virtual network. You can use the security features of virtual networks to control inbound and outbound network communications for your apps.

An ASE can be either internet-facing with a public IP address or internal-facing with only an Azure internal load balancer (ILB) address.

### App Service Environment v1

App Service Environment has two versions: ASEv1 and ASEv2. The preceding information was based on ASEv2. This section shows you the differences between ASEv1 and ASEv2.

In ASEv1, you need to manage all of the resources manually. That includes the front ends, workers, and IP addresses used for IP-based SSL. Before you can scale out your App Service plan, you need to first scale out the worker pool where you want to host it.

ASEv1 uses a different pricing model from ASEv2. In ASEv1, you pay for each vCPU allocated. That includes vCPUs used for front ends or workers that aren't hosting any workloads. In ASEv1, the default maximum-scale size of an ASE is 55 total hosts. That includes workers and front ends. One advantage to ASEv1 is that it can be deployed in a classic virtual network and a Resource Manager virtual network. To learn more about ASEv1, see App Service Environment v1 introduction.

[Custom configuration and application settings in Azure Web Sites](https://azure.microsoft.com/en-us/resources/videos/configuration-and-app-settings-of-azure-web-sites)

A video

[Configure an App Service app in the Azure portal](https://docs.microsoft.com/en-us/azure/app-service/configure-common)

Can edit in the CLI with:

    az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings <setting-name>="<value>"

Can also do `az webapp config appsettings list` and  `az webapp config appsettings delete`.

For ASP.NET and ASP.NET Core developers, setting connection strings in App Service are like setting them in <connectionStrings> in Web.config, but the values you set in App Service override the ones in Web.config. You can keep development settings (for example, a database file) in Web.config and production secrets (for example, SQL Database credentials) safely in App Service. The same code uses your development settings when you debug locally, and it uses your production secrets when deployed to Azure.

For other language stacks, it's better to use app settings instead, because connection strings require special formatting in the variable keys in order to access the values. Here's one exception, however: certain Azure database types are backed up along with the app if you configure their connection strings in your app. For more information, see What gets backed up. If you don't need this automated backup, then use app settings.

At runtime, connection strings are available as environment variables, prefixed with the following connection types:

- SQLServer: `SQLCONNSTR_`
- MySQL: `MYSQLCONNSTR_`
- SQLAzure: `SQLAZURECONNSTR_`
- Custom: `CUSTOMCONNSTR_`
- PostgreSQL: `POSTGRESQLCONNSTR_`

Connection strings are always encrypted when stored (encrypted-at-rest).

> Note
>
> Connection strings can also be resolved from Key Vault using Key Vault references.

### Containerized apps

You can add custom storage for your containerized app. Containerized apps include all Linux apps and also the Windows and Linux custom containers running on App Service. Click New Azure Storage Mount and configure your custom storage as follows:

- Name: The display name.
- Configuration options: Basic or Advanced.
- Storage accounts: The storage account with the container you want.
- Storage type: Azure Blobs or Azure Files.
  > Note
  >
  > Windows container apps only support Azure Files.
- Storage container: For basic configuration, the container you want.
- Share name: For advanced configuration, the file share name.
- Access key: For advanced configuration, the access key.
- Mount path: The absolute path in your container to mount the custom storage.
- For more information, see Access Azure Storage as a network share from a container in App Service.

[Buy a custom domain name for Azure App Service](https://docs.microsoft.com/en-us/azure/app-service/manage-custom-dns-buy-domain)

App Service domains are custom domains that are managed directly in Azure. They make it easy to manage custom domains for Azure App Service. This tutorial shows you how to buy an App Service domain and assign DNS names to Azure App Service.

For Azure VM or Azure Storage, see Assign App Service domain to Azure VM or Azure Storage. For Cloud Services, see Configuring a custom domain name for an Azure cloud service.

[Create an ASP.NET Core web app in Azure](https://docs.microsoft.com/en-us/azure/app-service/app-service-web-get-started-dotnet)

Deploy the code in your local folder (hellodotnetcore) using the az webapp up command:

    az webapp up --sku F1 --name <app-name> --os-type linux

- If the `az` command isn't recognized, be sure you have the Azure CLI installed as described in [Set up your initial environment](https://docs.microsoft.com/en-us/azure/app-service/quickstart-dotnetcore?tabs=netcore31&pivots=platform-linux#set-up-your-initial-environment).
- Replace `<app-name>` with a name that's unique across all of Azure (valid characters are `a-z`, `0-9`, and `-`). A good pattern is to use a combination of your company name and an app identifier.
- The `--sku F1` argument creates the web app on the Free pricing tier. Omit this argument to use a faster premium tier, which incurs an hourly cost.
- You can optionally include the argument `--location <location-name>` where `<location-name>` is an available Azure region. You can retrieve a list of allowable regions for your Azure account by running the az account list-locations command.

> Note
>
> The az webapp up command does the following actions:
> 
> - Create a default resource group.
> - Create a default app service plan.
> - Create an app with the specified name.
> - Zip deploy files from the current working directory to the app.

## create an App Service Web App for Containers

### Links

[Deploy a custom Linux container to Azure App Service](https://docs.microsoft.com/en-us/azure/app-service/containers/quickstart-docker)

### Create an image

To complete this quickstart, you will need a suitable web app image stored in an Azure Container Registry. Follow the instructions in Quickstart: Create a private container registry using the Azure portal, but use the mcr.microsoft.com/azuredocs/go image instead of the hello-world image. For reference, the sample Dockerfile is found in Azure Samples repo.

> Important
>
> Be sure to set the Admin User option to Enable when you create the container registry. You can also set it from the Access keys section of your registry page in the Azure portal. This setting is required for App Service access.

### Sign in

Next, launch VS Code and log into your Azure account using the App Service extension. To do this, select the Azure logo in the Activity Bar, navigate to the **APP SERVICE** explorer, then select Sign in to Azure and follow the instructions.

### Check prerequisites

Now you can check whether you have all the prerequisites installed and configured properly.

In VS Code, you should see your Azure email address in the Status Bar and your subscription in the **APP SERVICE** explorer.

Next, verify that you have Docker installed and running. The following command will display the Docker version if it is running.

    docker --version

Finally, ensure that your Azure Container Registry is connected. To do this, select the Docker logo in the Activity Bar, then navigate to REGISTRIES.

### Deploy the image to Azure App Service

Now that everything is configured, you can deploy your image to Azure App Service directly from the Docker extension explorer.

Find the image under the **Registries** node in the **DOCKER** explorer, and expand it to show its tags. Right-click a tag and then select **Deploy Image to Azure App Service**.

From here, follow the prompts to choose a subscription, a globally unique app name, a Resource Group, and an App Service Plan. Choose B1 Basic for the pricing tier, and a region.

After deployment, your app is available at `http://<app name>.azurewebsites.net`.

A Resource Group is a named collection of all your application's resources in Azure. For example, a Resource Group can contain a reference to a website, a database, and an Azure Function.

An App Service Plan defines the physical resources that will be used to host your website. This quickstart uses a Basic hosting plan on Linux infrastructure, which means the site will be hosted on a Linux machine alongside other websites. If you start with the Basic plan, you can use the Azure portal to scale up so that yours is the only site running on a machine.

### Browse the website

The Output panel will open during deployment to indicate the status of the operation. When the operation completes, find the app you created in the APP SERVICE explorer, right-click it, then select Browse Website to open the site in your browser.

## create and configure an App Service plan

### Links

[Azure App Service plan overview](https://docs.microsoft.com/en-us/azure/app-service/overview-hosting-plans)

In App Service (Web Apps, API Apps, or Mobile Apps), an app always runs in an App Service plan. In addition, Azure Functions also has the option of running in an App Service plan. An App Service plan defines a set of compute resources for a web app to run. These compute resources are analogous to the server farm in conventional web hosting. One or more apps can be configured to run on the same computing resources (or in the same App Service plan).

When you create an App Service plan in a certain region (for example, West Europe), a set of compute resources is created for that plan in that region. Whatever apps you put into this App Service plan run on these compute resources as defined by your App Service plan. Each App Service plan defines:

- Region (West US, East US, etc.)
- Number of VM instances
- Size of VM instances (Small, Medium, Large)
- Pricing tier (Free, Shared, Basic, Standard, Premium, PremiumV2, PremiumV3, Isolated)

The pricing tier of an App Service plan determines what App Service features you get and how much you pay for the plan. There are a few categories of pricing tiers:

- **Shared compute**: **Free** and **Shared**, the two base tiers, runs an app on the same Azure VM as other App Service apps, including apps of other customers. These tiers allocate CPU quotas to each app that runs on the shared resources, and the resources cannot scale out.
- **Dedicated compute**: The **Basic**, **Standard**, **Premium**, **PremiumV2**, and **PremiumV3** tiers run apps on dedicated Azure VMs. Only apps in the same App Service plan share the same compute resources. The higher the tier, the more VM instances are available to you for scale-out.
- **Isolated**: This tier runs dedicated Azure VMs on dedicated Azure Virtual Networks. It provides network isolation on top of compute isolation to your apps. It provides the maximum scale-out capabilities.

> Note
>
> App Service Free and Shared (preview) hosting plans are base tiers that run on the same Azure virtual machines as other App Service apps. Some apps might belong to other customers. These tiers are intended to be used only for development and testing purposes.

Each tier also provides a specific subset of App Service features. These features include custom domains and TLS/SSL certificates, autoscaling, deployment slots, backups, Traffic Manager integration, and more. The higher the tier, the more features are available. To find out which features are supported in each pricing tier, see App Service plan details.

> Note
>
> The new PremiumV3 pricing tier guarantees machines with faster processors (minimum 195 ACU per virtual CPU), SSD storage, and quadruple memory-to-core ratio compared to Standard tier. PremiumV3 also supports higher scale via increased instance count while still providing all the advanced capabilities found in Standard tier. All features available in the existing PremiumV2 tier are included in PremiumV3.
>
>Similar to other dedicated tiers, three VM sizes are available for this tier:
>
> - Small (2 CPU core, 8 GiB of memory)
> - Medium (4 CPU cores, 16 GiB of memory)
> - Large (8 CPU cores, 32 GiB of memory) 
> For PremiumV3 pricing information, see App Service Pricing.
>
> To get started with the new PremiumV3 pricing tier, see [Configure PremiumV3 tier for App Service](https://docs.microsoft.com/en-us/azure/app-service/app-service-configure-premium-tier).

### How does my app run and scale?

In the **Free** and **Shared** tiers, an app receives CPU minutes on a shared VM instance and cannot scale out. In other tiers, an app runs and scales as follows.

When you create an app in App Service, it is put into an App Service plan. When the app runs, it runs on all the VM instances configured in the App Service plan. If multiple apps are in the same App Service plan, they all share the same VM instances. If you have multiple deployment slots for an app, all deployment slots also run on the same VM instances. If you enable diagnostic logs, perform backups, or run WebJobs, they also use CPU cycles and memory on these VM instances.

In this way, the App Service plan is the scale unit of the App Service apps. If the plan is configured to run five VM instances, then all apps in the plan run on all five instances. If the plan is configured for autoscaling, then all apps in the plan are scaled out together based on the autoscale settings.

### How much does my App Service plan cost?

This section describes how App Service apps are billed. For detailed, region-specific pricing information, see [App Service Pricing](https://azure.microsoft.com/pricing/details/app-service/).

Except for Free tier, an App Service plan carries a charge on the compute resources it uses.

- In the **Shared** tier, each app receives a quota of CPU minutes, so each app is charged for the CPU quota.
- In the dedicated compute tiers (**Basic**, **Standard**, **Premium**, **PremiumV2**, **PremiumV3**), the App Service plan defines the number of VM instances the apps are scaled to, so each VM instance in the App Service plan is charged. These VM instances are charged the same regardless how many apps are running on them. To avoid unexpected charges, see Clean up an App Service plan.
- In the **Isolated** tier, the App Service Environment defines the number of isolated workers that run your apps, and each worker is charged. In addition, there's a flat Stamp Fee for the running the App Service Environment itself.

You don't get charged for using the App Service features that are available to you (configuring custom domains, TLS/SSL certificates, deployment slots, backups, etc.). The exceptions are:

- App Service Domains - you pay when you purchase one in Azure and when you renew it each year.
- App Service Certificates - you pay when you purchase one in Azure and when you renew it each year.
- IP-based TLS connections - There's an hourly charge for each IP-based TLS connection, but some Standard tier or above gives you one IP-based TLS connection for free. SNI-based TLS connections are free.
 Note

If you integrate App Service with another Azure service, you may need to consider charges from these other services. For example, if you use Azure Traffic Manager to scale your app geographically, Azure Traffic Manager also charges you based on your usage. To estimate your cross-services cost in Azure, see Pricing calculator.

### Want to optimize and save on your cloud spending?

Azure services cost money. Azure Cost Management helps you set budgets and configure alerts to keep spending under control. Analyze, manage, and optimize your Azure costs with Cost Management. To learn more, see the quickstart on analyzing your costs.

[Manage an App Service plan in Azure](https://docs.microsoft.com/en-us/azure/app-service/app-service-plan-manage)

*Basic App Service Plan management*

### Move an app to another App Service plan

You can move an app to another App Service plan, as long as the source plan and the target plan are in the same resource group and geographical region.

### Move an app to a different region

The region in which your app runs is the region of the App Service plan it's in. However, you cannot change an App Service plan's region. If you want to run your app in a different region, one alternative is app cloning. Cloning makes a copy of your app in a new or existing App Service plan in any region.

### Scale an App Service plan

To scale up an App Service plan's pricing tier, see Scale up an app in Azure.

To scale out an app's instance count, see Scale instance count manually or automatically.

### Delete an App Service plan

To avoid unexpected charges, when you delete the last app in an App Service plan, App Service also deletes the plan by default. If you choose to keep the plan instead, you should change the plan to Free tier so you're not charged.

> Important
>
> App Service plans that have no apps associated with them still incur charges because they continue to reserve the configured VM instances.

## configure an App Service

### Links

[Custom configuration and application settings in Azure Web Sites](https://azure.microsoft.com/en-us/resources/videos/configuration-and-app-settings-of-azure-web-sites)

Video

[Configure an App Service app in the Azure portal](https://docs.microsoft.com/en-us/azure/app-service/configure-common)

Link covered above.

[Buy a custom domain name for Azure App Service](https://docs.microsoft.com/en-us/azure/app-service/manage-custom-dns-buy-domain)

### Buy an App Service domain

For pricing information on App Service domains, visit the App Service Pricing page and scroll down to App Service Domain.

### Prepare the app

To map a custom DNS name to a web app, the web app's App Service plan must be a paid tier (Shared, Basic, Standard, Premium, or Consumption for Azure Functions). In this step, you make sure that the App Service app is in the supported pricing tier.

> Note
>
> App Service Free and Shared (preview) hosting plans are base tiers that run on the same Azure virtual machines as other App Service apps. Some apps might belong to other customers. These tiers are intended to be used only for development and testing purposes.

Check to make sure that the app is not in the F1 tier. Custom DNS is not supported in the F1 tier.

### Scale up the App Service plan

Select any of the non-free tiers (D1, B1, B2, B3, or any tier in the Production category). For additional options, click See additional options.

### Map App Service domain to your app

It's easy to map a hostname in your App Service domain to an App Service app, as long as it's in the same subscription. You map the App Service domain or any of its subdomain directly in your app, and Azure creates the necessary DNS records for you.

> Note
>
> If the domain and the app are in different subscriptions, you map the App Service domain to the app just like mapping an externally purchased domain. In this case, Azure DNS is the external domain provider, and you need to add the required DNS records manually.

### Manage custom DNS records

In Azure, DNS records for an App Service Domain are managed using Azure DNS. You can add, remove, and update DNS records, just like for an externally purchased domain. To manage custom DNS records:

## configure networking for an App Service

### Links

[Networking considerations for an App Service Environment](https://docs.microsoft.com/en-us/azure/app-service/environment/network-info)

Azure App Service Environment is a deployment of Azure App Service into a subnet in your Azure virtual network (VNet). There are two deployment types for an App Service environment (ASE):

- **External ASE**: Exposes the ASE-hosted apps on an internet-accessible IP address. For more information, see Create an External ASE.
- **ILB ASE**: Exposes the ASE-hosted apps on an IP address inside your VNet. The internal endpoint is an internal load balancer (ILB), which is why it's called an ILB ASE. For more information, see Create and use an ILB ASE.

All ASEs, External, and ILB, have a public VIP that is used for inbound management traffic and as the from address when making calls from the ASE to the internet. The calls from an ASE that go to the internet leave the VNet through the VIP assigned for the ASE. The public IP of this VIP is the source IP for all calls from the ASE that go to the internet. If the apps in your ASE make calls to resources in your VNet or across a VPN, the source IP is one of the IPs in the subnet used by your ASE. Because the ASE is within the VNet, it can also access resources within the VNet without any additional configuration. If the VNet is connected to your on-premises network, apps in your ASE also have access to resources there without additional configuration.

![External ASE](https://docs.microsoft.com/en-us/azure/app-service/environment/media/network_considerations_with_an_app_service_environment/networkase-overflow.png)

If you have an External ASE, the public VIP is also the endpoint that your ASE apps resolve to for:

- HTTP/S
- FTP/S
- Web deployment
- Remote debugging

![ILB ASE](https://docs.microsoft.com/en-us/azure/app-service/environment/media/network_considerations_with_an_app_service_environment/networkase-overflow2.png)

If you have an ILB ASE, the address of the ILB address is the endpoint for HTTP/S, FTP/S, web deployment, and remote debugging.

[App Service networking features](https://docs.microsoft.com/en-us/azure/app-service/networking-features)

*This article contains a lot of information about network integration options.*

You can deploy applications in Azure App Service in multiple ways. By default, apps hosted in App Service are accessible directly through the internet and can reach only internet-hosted endpoints. But for many applications, you need to control the inbound and outbound network traffic. There are several features in App Service to help you meet those needs. The challenge is knowing which feature to use to solve a given problem. This article will help you determine which feature to use, based on some example use cases.

There are two main deployment types for Azure App Service:

- The multitenant public service hosts App Service plans in the Free, Shared, Basic, Standard, Premium, PremiumV2, and PremiumV3 pricing SKUs.
- The single-tenant App Service Environment (ASE) hosts Isolated SKU App Service plans directly in your Azure virtual network.

The features you use will depend on whether you're in the multitenant service or in an ASE.

### Multitenant App Service networking features

Azure App Service is a distributed system. The roles that handle incoming HTTP or HTTPS requests are called front ends. The roles that host the customer workload are called workers. All the roles in an App Service deployment exist in a multitenant network. Because there are many different customers in the same App Service scale unit, you can't connect the App Service network directly to your network.

Instead of connecting the networks, you need features to handle the various aspects of application communication. The features that handle requests to your app can't be used to solve problems when you're making calls from your app. Likewise, the features that solve problems for calls from your app can't be used to solve problems to your app.

|Inbound features	|Outbound features|
|:--|:--|
|App-assigned address	|Hybrid Connections|
|Access restrictions	|Gateway-required VNet Integration|
|Service endpoints	|VNet Integration|
|Private endpoints	||

Other than noted exceptions, you can use all of these features together. You can mix the features to solve your problems.

### Default networking behavior

Azure App Service scale units support many customers in each deployment. The Free and Shared SKU plans host customer workloads on multitenant workers. The Basic and higher plans host customer workloads that are dedicated to only one App Service plan. If you have a Standard App Service plan, all the apps in that plan will run on the same worker. If you scale out the worker, all the apps in that App Service plan will be replicated on a new worker for each instance in your App Service plan.

### Outbound addresses

The worker VMs are broken down in large part by the App Service plans. The Free, Shared, Basic, Standard, and Premium plans all use the same worker VM type. The PremiumV2 plan uses another VM type. PremiumV3 uses yet another VM type. When you change the VM family, you get a different set of outbound addresses. If you scale from Standard to PremiumV2, your outbound addresses will change. If you scale from PremiumV2 to PremiumV3, your outbound addresses will change. In some older scale units, both the inbound and outbound addresses will change when you scale from Standard to PremiumV2.

There are a number of addresses that are used for outbound calls. The outbound addresses used by your app for making outbound calls are listed in the properties for your app. These addresses are shared by all the apps running on the same worker VM family in the App Service deployment. If you want to see all the addresses that your app might use in a scale unit, there's property called possibleOutboundAddresses that will list them.

### Private Endpoint

Private Endpoint is a network interface that connects you privately and securely to your Web App by Azure private link. Private Endpoint uses a private IP address from your virtual network, effectively bringing the web app into your virtual network. This feature is only for inbound flows to your web app. For more information, see Using private endpoints for Azure Web App.

Some use cases for this feature:

- Restrict access to your app from resources in a virtual network.
- Expose your app on a private IP in your virtual network.
- Protect your app with a WAF.

Private endpoints prevent data exfiltration because the only thing you can reach across the private endpoint is the app with which it's configured.

[Integrate your app with an Azure Virtual Network](https://docs.microsoft.com/en-us/azure/app-service/web-sites-integrate-with-vnet)

This article describes the Azure App Service VNet Integration feature and how to set it up with apps in Azure App Service. With Azure Virtual Network (VNets), you can place many of your Azure resources in a non-internet-routable network. The VNet Integration feature enables your apps to access resources in or through a VNet. VNet Integration doesn't enable your apps to be accessed privately.

Azure App Service has two variations on the VNet Integration feature:

- The multitenant systems that support the full range of pricing plans except Isolated.
- The App Service Environment, which deploys into your VNet and supports Isolated pricing plan apps.

The VNet Integration feature is used in multitenant apps. If your app is in App Service Environment, then it's already in a VNet and doesn't require use of the VNet Integration feature to reach resources in the same VNet. For more information on all of the networking features, see App Service networking features.

VNet Integration gives your app access to resources in your VNet, but it doesn't grant inbound private access to your app from the VNet. Private site access refers to making an app accessible only from a private network, such as from within an Azure virtual network. VNet Integration is used only to make outbound calls from your app into your VNet. The VNet Integration feature behaves differently when it's used with VNet in the same region and with VNet in other regions. The VNet Integration feature has two variations:

- Regional VNet Integration: When you connect to Azure Resource Manager virtual networks in the same region, you must have a dedicated subnet in the VNet you're integrating with.
- Gateway-required VNet Integration: When you connect to VNet in other regions or to a classic virtual network in the same region, you need an Azure Virtual Network gateway provisioned in the target VNet.

The VNet Integration features:

- Require a Standard, Premium, PremiumV2, PremiumV3, or Elastic Premium pricing plan.
- Support TCP and UDP.
- Work with Azure App Service apps and function apps.

There are some things that VNet Integration doesn't support, like:

- Mounting a drive.
- Active Directory integration.
- NetBIOS.

Gateway-required VNet Integration provides access to resources only in the target VNet or in networks connected to the target VNet with peering or VPNs. Gateway-required VNet Integration doesn't enable access to resources available across Azure ExpressRoute connections or works with service endpoints.

Regardless of the version used, VNet Integration gives your app access to resources in your VNet, but it doesn't grant inbound private access to your app from the VNet. Private site access refers to making your app accessible only from a private network, such as from within an Azure VNet. VNet Integration is only for making outbound calls from your app into your VNet.

### Regional VNet Integration

Using regional VNet Integration enables your app to access:

- Resources in a VNet in the same region as your app.
- Resources in VNets peered to the VNet your app is integrated with.
- Service endpoint secured services.
- Resources across Azure ExpressRoute connections.
- Resources in the VNet you're integrated with.
- Resources across peered connections, which include Azure ExpressRoute connections.
- Private endpoints

When you use VNet Integration with VNets in the same region, you can use the following Azure networking features:

- **Network security groups (NSGs)**: You can block outbound traffic with an NSG that's placed on your integration subnet. The inbound rules don't apply because you can't use VNet Integration to provide inbound access to your app.
- **Route tables (UDRs)**: You can place a route table on the integration subnet to send outbound traffic where you want.

> Note
>
> If you route all of your outbound traffic into your VNet, it's subject to the NSGs and UDRs that are applied to your integration subnet. When you route all of your outbound traffic into your VNet, your outbound addresses are still the outbound addresses that are listed in your app properties unless you provide routes to send the traffic elsewhere.

### Service endpoints

Regional VNet Integration enables you to use service endpoints. To use service endpoints with your app, use regional VNet Integration to connect to a selected VNet and then configure service endpoints with the destination service on the subnet you used for the integration. If you then wanted to access a service over service endpoints:

1. configure regional VNet Integration with your web app
2. go to the destination service and configure service endpoints against the subnet used for integration

### Azure DNS Private Zones

After your app integrates with your VNet, it uses the same DNS server that your VNet is configured with. You can override this behavior on your app by configuring the app setting WEBSITE_DNS_SERVER with the address of your desired DNS server. If you had a custom DNS server configured with your VNet but wanted to have your app use Azure DNS private zones, you should set WEBSITE_DNS_SERVER with value 168.63.129.16.

### Private endpoints

If you want to make calls to Private Endpoints, then you need to ensure that your DNS lookups will resolve to the private endpoint. To ensure that the DNS lookups from your app will point to your private endpoints you can:

- integrate with Azure DNS Private Zones. If your VNet doesn't have a custom DNS server, this will be automatic
- manage the private endpoint in the DNS server used by your app. To do this you need to know the private endpoint address and then point the endpoint you are trying to reach to that address with an A record.
- configure your own DNS server to forward to Azure DNS private zones

### How regional VNet Integration works

Apps in App Service are hosted on worker roles. The Basic and higher pricing plans are dedicated hosting plans where there are no other customers' workloads running on the same workers. Regional VNet Integration works by mounting virtual interfaces with addresses in the delegated subnet. Because the from address is in your VNet, it can access most things in or through your VNet like a VM in your VNet would. The networking implementation is different than running a VM in your VNet. That's why some networking features aren't yet available for this feature.

![How regional VNet Integration works](https://docs.microsoft.com/en-us/azure/app-service/media/web-sites-integrate-with-vnet/vnetint-regionalworks.png)

When regional VNet Integration is enabled, your app makes outbound calls to the internet through the same channels as normal. The outbound addresses that are listed in the app properties portal are the addresses still used by your app. What changes for your app are the calls to service endpoint secured services, or RFC 1918 addresses go into your VNet. If WEBSITE_VNET_ROUTE_ALL is set to 1, all outbound traffic can be sent into your VNet.

### Gateway-required VNet Integration

Gateway-required VNet Integration supports connecting to a VNet in another region or to a classic virtual network. Gateway-required VNet Integration:

- Enables an app to connect to only one VNet at a time.
- Enables up to five VNets to be integrated within an App Service plan.
- Allows the same VNet to be used by multiple apps in an App Service plan without affecting the total number that can be used by an App Service plan. If you have six apps using the same VNet in the same App Service plan, that counts as one VNet being used.
- Supports a 99.9% SLA due to the SLA on the gateway.
- Enables your apps to use the DNS that the VNet is configured with.
- Requires a Virtual Network route-based gateway configured with an SSTP point-to-site VPN before it can be connected to an app.

You can't use gateway-required VNet Integration:

- With a VNet connected with Azure ExpressRoute.
- From a Linux app.
- From a Windows container.
- To access service endpoint secured resources.
- With a coexistence gateway that supports both ExpressRoute and point-to-site or site-to-site VPNs.

### Pricing details

The regional VNet Integration feature has no additional charge for use beyond the App Service plan pricing tier charges.

Three charges are related to the use of the gateway-required VNet Integration feature:

- **App Service plan pricing tier charges**: Your apps need to be in a Standard, Premium, PremiumV2, or PremiumV3 App Service plan. For more information on those costs, see App Service pricing.
- **Data transfer costs**: There's a charge for data egress, even if the VNet is in the same datacenter. Those charges are described in Data Transfer pricing details.
- **VPN gateway costs**: There's a cost to the virtual network gateway that's required for the point-to-site VPN. For more information, see VPN gateway pricing.

## create and manage deployment slots

### Links

[Set up staging environments in Azure App Service](https://docs.microsoft.com/en-us/azure/app-service/deploy-staging-slots)

When you deploy your web app, web app on Linux, mobile back end, or API app to Azure App Service, you can use a separate deployment slot instead of the default production slot when you're running in the Standard, Premium, or Isolated App Service plan tier. Deployment slots are live apps with their own host names. App content and configurations elements can be swapped between two deployment slots, including the production slot.

Deploying your application to a non-production slot has the following benefits:

- You can validate app changes in a staging deployment slot before swapping it with the production slot.
- Deploying an app to a slot first and swapping it into production makes sure that all instances of the slot are warmed up before being swapped into production. This eliminates downtime when you deploy your app. The traffic redirection is seamless, and no requests are dropped because of swap operations. You can automate this entire workflow by configuring auto swap when pre-swap validation isn't needed.
- After a swap, the slot with previously staged app now has the previous production app. If the changes swapped into the production slot aren't as you expect, you can perform the same swap immediately to get your "last known good site" back.
Each App Service plan tier supports a different number of deployment slots. There's no additional charge for using deployment slots. To find out the number of slots your app's tier supports, see App Service limits.

To scale your app to a different tier, make sure that the target tier supports the number of slots your app already uses. For example, if your app has more than five slots, you can't scale it down to the **Standard** tier, because the **Standard** tier supports only five deployment slots.

### Add a slot

The app must be running in the **Standard**, **Premium**, or **Isolated** tier in order for you to enable multiple deployment slots.

The new deployment slot has no content, even if you clone the settings from a different slot. For example, you can publish to this slot with Git. You can deploy to the slot from a different repository branch or a different repository.

### What happens during a swap

#### Swap operation steps

When you swap two slots (usually from a staging slot into the production slot), App Service does the following to ensure that the target slot doesn't experience downtime:

1. Apply the following settings from the target slot (for example, the production slot) to all instances of the source slot:
  - Slot-specific app settings and connection strings, if applicable.
  - Continuous deployment settings, if enabled.
  - App Service authentication settings, if enabled.
  
    Any of these cases trigger all instances in the source slot to restart. During swap with preview, this marks the end of the first phase. The swap operation is paused, and you can validate that the source slot works correctly with the target slot's settings.
2. Wait for every instance in the source slot to complete its restart. If any instance fails to restart, the swap operation reverts all changes to the source slot and stops the operation.
3. If local cache is enabled, trigger local cache initialization by making an HTTP request to the application root ("/") on each instance of the source slot. Wait until each instance returns any HTTP response. Local cache initialization causes another restart on each instance.
4. If auto swap is enabled with custom warm-up, trigger Application Initiation by making an HTTP request to the application root ("/") on each instance of the source slot.

   If applicationInitialization isn't specified, trigger an HTTP request to the application root of the source slot on each instance.

   If an instance returns any HTTP response, it's considered to be warmed up.

5. If all instances on the source slot are warmed up successfully, swap the two slots by switching the routing rules for the two slots. After this step, the target slot (for example, the production slot) has the app that's previously warmed up in the source slot.
6. Now that the source slot has the pre-swap app previously in the target slot, perform the same operation by applying all settings and restarting the instances.

At any point of the swap operation, all work of initializing the swapped apps happens on the source slot. The target slot remains online while the source slot is being prepared and warmed up, regardless of where the swap succeeds or fails. To swap a staging slot with the production slot, make sure that the production slot is always the target slot. This way, the swap operation doesn't affect your production app.

### Which settings are swapped?

When you clone configuration from another deployment slot, the cloned configuration is editable. Some configuration elements follow the content across a swap (not slot specific), whereas other configuration elements stay in the same slot after a swap (slot specific). The following lists show the settings that change when you swap slots.

**Settings that are swapped**:

- General settings, such as framework version, 32/64-bit, web sockets
- App settings (can be configured to stick to a slot)
- Connection strings (can be configured to stick to a slot)
- Handler mappings
- Public certificates
- WebJobs content
- Hybrid connections *
- Virtual network integration *
- Service endpoints *
- Azure Content Delivery Network *

Features marked with an asterisk (*) are planned to be unswapped.

**Settings that aren't swapped**:

- Publishing endpoints
- Custom domain names
- Non-public certificates and TLS/SSL settings
- Scale settings
- WebJobs schedulers
- IP restrictions
- Always On
- Diagnostic settings
- Cross-origin resource sharing (CORS)

> Note
>
> Certain app settings that apply to unswapped settings are also not swapped. For example, since diagnostic settings are not swapped, related app settings like WEBSITE_HTTPLOGGING_RETENTION_DAYS and DIAGNOSTICS_AZUREBLOBRETENTIONDAYS are also not swapped, even if they don't show up as slot settings.

To configure an app setting or connection string to stick to a specific slot (not swapped), go to the **Configuration** page for that slot. Add or edit a setting, and then select **deployment slot setting**. Selecting this check box tells App Service that the setting is not swappable.

### Swap two slots

You can swap deployment slots on your app's Deployment slots page and the Overview page. For technical details on the slot swap, see What happens during swap.

> Important
>
> Before you swap an app from a deployment slot into production, make sure that production is your target slot and that all settings in the source slot are configured exactly as you want to have them in production.

### Swap with preview (multi-phase swap)

Before you swap into production as the target slot, validate that the app runs with the swapped settings. The source slot is also warmed up before the swap completion, which is desirable for mission-critical applications.

When you perform a swap with preview, App Service performs the same swap operation but pauses after the first step. You can then verify the result on the staging slot before completing the swap.

If you cancel the swap, App Service reapplies configuration elements to the source slot.

### Roll back a swap

If any errors occur in the target slot (for example, the production slot) after a slot swap, restore the slots to their pre-swap states by swapping the same two slots immediately.

### Route traffic

By default, all client requests to the app's production URL (`http://<app_name>.azurewebsites.net`) are routed to the production slot. You can route a portion of the traffic to another slot. This feature is useful if you need user feedback for a new update, but you're not ready to release it to production.

## implement Logic Apps

[Overview – What is Azure Logic Apps?](https://docs.microsoft.com/en-us/azure/logic-apps/logic-apps-overview)

Azure Logic Apps is a cloud service that helps you schedule, automate, and orchestrate tasks, business processes, and workflows when you need to integrate apps, data, systems, and services across enterprises or organizations. Logic Apps simplifies how you design and build scalable solutions for app integration, data integration, system integration, enterprise application integration (EAI), and business-to-business (B2B) communication, whether in the cloud, on premises, or both.

For example, here are just a few workloads you can automate with logic apps:

- Process and route orders across on-premises systems and cloud services.
- Send email notifications with Office 365 when events happen in various systems, apps, and services.
- Move uploaded files from an SFTP or FTP server to Azure Storage.
- Monitor tweets for a specific subject, analyze the sentiment, and create alerts or tasks for items that need review.

### How do logic apps work?

Every logic app workflow starts with a trigger, which fires when a specific event happens, or when new available data meets specific criteria. Many triggers provided by the connectors in Logic Apps include basic scheduling capabilities so that you can set up how regularly your workloads run. For more complex scheduling or advanced recurrences, you can use a Recurrence trigger as the first step in any workflow. Learn more about schedule-based workflows.

Each time that the trigger fires, the Logic Apps engine creates a logic app instance that runs the actions in the workflow. These actions can also include data conversions and workflow controls, such as conditional statements, switch statements, loops, and branching. For example, this logic app starts with a Dynamics 365 trigger with the built-in criteria "When a record is updated". If the trigger detects an event that matches this criteria, the trigger fires and runs the workflow's actions. Here, these actions include XML transformation, data updates, decision branching, and email notifications.

![Logic Apps Designer - example logic app](https://docs.microsoft.com/en-us/azure/logic-apps/media/logic-apps-overview/azure-logic-apps-designer.png)

You can build your logic apps visually with the Logic Apps Designer, which is available in the Azure portal through your browser and in Visual Studio. For more custom logic apps, you can create or edit logic app definitions in JavaScript Object Notation (JSON) by working in the "code view" editor. You can also use Azure PowerShell commands and Azure Resource Manager templates for select tasks. Logic apps deploy and run in the cloud on Azure. For a more detailed introduction, watch this video: Use Azure Enterprise Integration Services to run cloud apps at scale

### Access resources inside Azure virtual networks

Logic apps can access secured resources, such as virtual machines (VMs) and other systems or services, that are inside an Azure virtual network when you create an integration service environment (ISE). An ISE is a dedicated instance of the Logic Apps service that uses dedicated resources and runs separately from the "global" multi-tenant Logic Apps service.

### Pay only for what you use

Logic Apps uses consumption-based pricing and metering unless you have logic apps previously created with App Service plans.

Learn more about Logic Apps with these introductory videos:

- [Integration with Logic Apps - Go from zero to hero](https://channel9.msdn.com/Events/Build/2017/C9R17)
- [Enterprise integration with Microsoft Azure Logic Apps](https://channel9.msdn.com/Events/Ignite/Microsoft-Ignite-Orlando-2017/BRK2188)
- [Building advanced business processes with Logic Apps](https://channel9.msdn.com/Events/Ignite/Microsoft-Ignite-Orlando-2017/BRK3179)

### How does Logic Apps differ from Functions, WebJobs, and Power Automate?

All these services help you "glue" and connect disparate systems together. Each service has their advantages and benefits, so combining their capabilities is the best way to quickly build a scalable, full-featured integration system. For more information, see Choose between Logic Apps, Functions, WebJobs, and Power Automate.

### Key terms

- **Workflow**: Visualize, design, build, automate, and deploy business processes as series of steps.
- **Managed connectors**: Your logic apps need access to data, services, and systems. You can use prebuilt Microsoft-managed connectors that are designed to connect, access, and work with your data. See Connectors for Azure Logic Apps.
- **Triggers**: Many Microsoft-managed connectors provide triggers that fire when events or new data meet specified conditions. For example, an event might be getting an email or detecting changes in your Azure Storage account. Each time the trigger fires, the Logic Apps engine creates a new logic app instance that runs the workflow.
- **Actions**: Actions are all the steps that happen after the trigger. Each action usually maps to an operation that's defined by a managed connector, custom API, or custom connector.
- **Enterprise Integration Pack**: For more advanced integration scenarios, Logic Apps includes capabilities from BizTalk Server. The Enterprise Integration Pack provides connectors that help logic apps easily perform validation, transformation, and more.

### Links

- [Quickstart: Create your first workflow by using Azure Logic Apps – Azure portal](https://docs.microsoft.com/en-us/azure/logic-apps/quickstart-create-first-logic-app-workflow)
- [Quickstart: Create automated tasks, processes, and workflows with Azure Logic Apps – Visual Studio](https://docs.microsoft.com/en-us/azure/logic-apps/quickstart-create-logic-apps-with-visual-studio)
- [Quickstart: Create and manage logic app workflow definitions by using Visual Studio Code](https://docs.microsoft.com/en-us/azure/logic-apps/quickstart-create-logic-apps-visual-studio-code)
  
## implement Azure Functions

[An introduction to Azure Functions](https://docs.microsoft.com/en-us/azure/azure-functions/functions-overview)

Azure Functions is a serverless solution that allows you to write less code, maintain less infrastructure, and save on costs. Instead of worrying about deploying and maintaining servers, the cloud infrastructure provides all the up-to-date servers needed to keep your applications running.

You focus on the pieces of code that matter most to you, and Azure Functions handles the rest.

### Scenarios

In many cases, a function integrates with an array of cloud services to provide feature-rich implementations.

The following are a common, *but by no means exhaustive*, set of scenarios for Azure Functions.

|If you want to...	|then...|
|:--|:--|
|**Build a web API**	|Implement an endpoint for your web applications using the HTTP trigger|
|**Process file uploads**	|Run code when a file is uploaded or changed in blob storage|
|**Build a serverless workflow**	|Chain a series of functions together using durable functions|
|**Respond to database changes**	|Run custom logic when a document is created or updated in Cosmos DB|
|**Run scheduled tasks**	|Execute code at set times|
|**Create reliable message queue systems**|	Process message queues using Queue Storage, Service Bus, or Event Hubs|
|**Analyze IoT data streams**	|Collect and process data from IoT devices|
|**Process data in real time**	|Use Functions and SignalR to respond to data in the moment|

As you build your functions, you have the following options and resources available:

- **Use your preferred language**: Write functions in C#, Java, JavaScript, PowerShell, or Python, or use a custom handler to use virtually any other language.
- **Automate deployment**: From a tools-based approach to using external pipelines, there's a myriad of deployment options available.
- **Troubleshoot a function**: Use monitoring tools and testing strategies to gain insights into your apps.
- **Flexible pricing options**: With the Consumption plan, you only pay while your functions are running, while the Premium and App Service plans offer features for specialized needs.

### Links

- [Azure Functions triggers and bindings concepts](https://docs.microsoft.com/en-us/azure/azure-functions/functions-triggers-bindings)
- [Azure Functions trigger and binding example](https://docs.microsoft.com/en-us/azure/azure-functions/functions-bindings-example)
- [Azure Functions HTTP triggers and bindings overview](https://docs.microsoft.com/en-us/azure/azure-functions/functions-bindings-http-webhook)
- [What are Durable Functions?](https://docs.microsoft.com/en-us/azure/azure-functions/durable/durable-functions-overview)
