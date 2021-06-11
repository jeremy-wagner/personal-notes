# Implement load balancing and network security

Azure Load Balancer Family

- Public Load Balancer
  - OSI Layer 4 (the transport layer)
  - Internet facing (needs a public IP) 
    - Should be connected to 1 or more (preferably 2 or more VMs)
  - Offers two traffic distribution modes
- Internal Load Balancer
  - OSI Layer 4
  - Similar to the Public Load Balancer, except for internal traffic (only traffic within a VNet)
    - Good for applying load balancing to n-tier application services (database)
- Application Gateway
  - OSI Layer 7 (the application layer)
  - Application Delivery Controller (ADC) as a Service
  - SSL Offload
  - Web Application Firewall (WAF)
- Traffic Manager
  - OSI Layer 7
  - DNS-level geographical load balancing
  - Offers different routing methods

## implement Azure Load Balancer

### Azure Public Load Balancer

- Basic SKU is not zone-redundant
- Load Balancer is not a firewall, you should be using an NSG, and this is required by the Standard Public LB. 

### SKUs

|Basic|Standard|
|---|---|
|Backend up to 100 instances|Backend up to 1,000 instances|
|Backend pool of a single availability set or scale set|Backend pool of any blend of VMs, availability sets, or scale sets|
|Frontend is not zone-redundant|Frontend is zone-redundant|
|Multiple inbound frontend configurations|Multiple inbound *and* outbound frontend configurations|
|NSG optional|NSG required|
|60-90 seconds for mangement operations|< 30 seconds for mangagement operations|

### Azure Load Balancer Resource Components

- Frontend IP configuration
  - List of Public or Private IPs that the load balancer is listening on 
- Backend pool 
  - The VMs that you are load balancing across
- Health probe
  - Probe to determine which VMs in the pool are healthy
  - There is a default NSG for whitelisting Health probe traffic
- Load balancing rules
  - To determine which types of traffic get handled by the load balancer
- Inbound NAT rules
  - Bind 4444 to port 3389 on a specific VM so that you can RDP to it
  - Could also be considered a thin layer of security by obscurity (shouldn't rely on this)

### Traffic Distribution Modes

#### Hash-based affinity

Hash-based affinity creates a 5-tuple hash to determine which VM to route the traffic to.
If the port changes the hash does and the traffic could go to another node.

#### Source IP affinity

Just uses Source IP and Destination IP for the hash, so won't change if port changes.
You need to use this for RD Gateway (RDS)

### Summary

- Keep in mind that network virtual appliances (NVAs) are available in the Azure Marketplace
  - Deploy them as virtual machines
  - Customers may want to use what they have on-prem (they can share configs)

### Links

- [Create an internal load balancer by using the Azure PowerShell module](https://docs.microsoft.com/en-us/azure/load-balancer/load-balancer-get-started-ilb-arm-ps)
- [Quickstart: Create a Load Balancer to load balance VMs using the Azure portal](https://docs.microsoft.com/en-us/azure/load-balancer/quickstart-load-balancer-standard-public-portal)
  - Similar to the internal traffic load balancer, but Bastion isn't used

### [Tutorial: Balance internal traffic load with a Basic load balancer in the Azure portal](https://docs.microsoft.com/en-us/azure/load-balancer/tutorial-load-balancer-basic-internal-portal)

> Note
>
> Standard SKU load balancer is recommended for production workloads. For more information about skus, see Azure Load Balancer SKUs.

In this section, you create a load balancer that load balances virtual machines.

When you create an internal load balancer, a virtual network is configured as the network for the load balancer.

A private IP address in the virtual network is configured as the frontend (named as LoadBalancerFrontend by default) for the load balancer.

The frontend IP address can be Static or Dynamic.

### Create the virtual network

In this section, you'll create a virtual network and subnet.

- A backend subnet is created
- A BastionHost is created, with an address space for its subnet

### Create load balancer resources

In this section, you configure:

- Load balancer settings for a backend address pool.
- A health probe.
- A load balancer rule.

### Create a backend pool

A backend address pool contains the IP addresses of the virtual (NICs) connected to the load balancer.

### Create a health probe

The load balancer monitors the status of your app with a health probe.

The health probe adds or removes VMs from the load balancer based on their response to health checks.

### Create a load balancer rule

A load balancer rule is used to define how traffic is distributed to the VMs. You define the frontend IP configuration for the incoming traffic and the backend IP pool to receive the traffic. The source and destination port are defined in the rule.

In this section, you'll create a load balancer rule:

- Named myHTTPRule.
- In the frontend named LoadBalancerFrontEnd.
- Listening on Port 80.
- Directs load balanced traffic to the backend named myBackendPool on Port 80.

### Create backend servers

In this section, you:

- Create two virtual machines for the backend pool of the load balancer.
- Install IIS on the virtual machines to test the load balancer.

### Create virtual machines

In this section, you'll create two VMs (myVM1 and myVM2).

These VMs are added to the backend pool of the load balancer that was created earlier.

### Next steps

In this quickstart, you:

- Created an Azure standard or basic internal load balancer
- Attached 2 VMs to the load balancer.
- Configured the load balancer traffic rule, health probe, and then tested the load balancer.

## implement an application gateway

### Azure Application Gateway

- A software defined networking device - a web traffic load balancer
- If you want a public load balancer but you're not doing web traffic (HTTP/HTTPS) then you don't need the Application gateway
  - Optimised for HTTP(S) workloads
- Operates at OSI Level 7
  - URL based routing (/images, /video) - you might have one host that has all the videos
- SSL offloading
  - HTTP to HTTPS redirection
- Mutli-site hosting
- WAF (Web Application Firewall)
  - OWASP core rule sets 3.0 or 2.2.9 - so it protects against most common vulnerabilities

The Azure Quickstart Template Gallery has templates for architectures: https://azure.microsoft.com/en-us/resources/templates/

- In Configuration you can change the Tier to WAF to get the OWASP WAF
  - There are two modes - Prevention or Detection
  - You can choose your OWASP ruleset
  - You can also tick individual rules
- The backend pools can be a VM, scale set, IP, or domain name
- There is a HTTP Settings section, to enable Cookie Based Affinity and the Probe
- You define Listeners and associate Rules with them
  - You can add a Path-based Rule

### Links

[Application Gateway configuration overview](https://docs.microsoft.com/en-us/azure/application-gateway/configuration-overview)

Azure Application Gateway consists of several components that you can configure in various ways for different scenarios. This article shows you how to configure each component.

![Application Gateway components flow chart](https://docs.microsoft.com/en-us/azure/application-gateway/media/configuration-overview/configuration-overview1.png)

This image illustrates an application that has three listeners. The first two are multi-site listeners for `http://acme.com/*` and `http://fabrikam.com/*`, respectively. Both listen on port 80. The third is a basic listener that has end-to-end Transport Layer Security (TLS) termination, previously known as Secure Sockets Layer (SSL) termination.

### Infrastructure

The Application Gateway infrastructure includes the virtual network, subnets, network security groups, and user defined routes.

For more information, see Application Gateway infrastructure configuration.

### Front-end IP address

You can configure the application gateway to have a public IP address, a private IP address, or both. A public IP is required when you host a back end that clients must access over the Internet via an Internet-facing virtual IP (VIP).

For more information, see Application Gateway front-end IP address configuration.

### Listeners

A listener is a logical entity that checks for incoming connection requests by using the port, protocol, host, and IP address. When you configure the listener, you must enter values for these that match the corresponding values in the incoming request on the gateway.

For more information, see Application Gateway listener configuration.

### Request routing rules

When you create an application gateway by using the Azure portal, you create a default rule (rule1). This rule binds the default listener (appGatewayHttpListener) with the default back-end pool (appGatewayBackendPool) and the default back-end HTTP settings (appGatewayBackendHttpSettings). After you create the gateway, you can edit the settings of the default rule or create new rules.

For more information, see Application Gateway request routing rules.

### HTTP settings

The application gateway routes traffic to the back-end servers by using the configuration that you specify here. After you create an HTTP setting, you must associate it with one or more request-routing rules.

For more information, see Application Gateway HTTP settings configuration.

### Back-end pool
You can point a back-end pool to four types of backend members: a specific virtual machine, a virtual machine scale set, an IP address/FQDN, or an app service.

After you create a back-end pool, you must associate it with one or more request-routing rules. You must also configure health probes for each back-end pool on your application gateway. When a request-routing rule condition is met, the application gateway forwards the traffic to the healthy servers (as determined by the health probes) in the corresponding back-end pool.

### Health probes

An application gateway monitors the health of all resources in its back end by default. But we strongly recommend that you create a custom probe for each back-end HTTP setting to get greater control over health monitoring. To learn how to configure a custom probe, see Custom health probe settings.

> Note
>
> After you create a custom health probe, you need to associate it to a back-end HTTP setting. A custom probe won't monitor the health of the back-end pool unless the corresponding HTTP setting is explicitly associated with a listener using a rule.

## implement a Web Application Firewall

### Links

- [Azure Web Application Firewall on Azure Application Gateway](https://docs.microsoft.com/en-us/azure/web-application-firewall/ag/ag-overview)

Azure Web Application Firewall (WAF) on Azure Application Gateway provides centralized protection of your web applications from common exploits and vulnerabilities. Web applications are increasingly targeted by malicious attacks that exploit commonly known vulnerabilities. SQL injection and cross-site scripting are among the most common attacks.

WAF on Application Gateway is based on Core Rule Set (CRS) 3.1, 3.0, or 2.2.9 from the Open Web Application Security Project (OWASP). The WAF automatically updates to include protection against new vulnerabilities, with no additional configuration needed.

All of the WAF features listed below exist inside of a WAF Policy. You can create multiple policies, and they can be associated with an Application Gateway, to individual listeners, or to path-based routing rules on an Application Gateway. This way, you can have separate policies for each site behind your Application Gateway if needed. For more information on WAF Policies, see Create a WAF Policy.

![Application Gateway WAF diagram](https://docs.microsoft.com/en-us/azure/web-application-firewall/media/ag-overview/waf1.png)

Application Gateway operates as an application delivery controller (ADC). It offers Transport Layer Security (TLS), previously known as Secure Sockets Layer (SSL), termination, cookie-based session affinity, round-robin load distribution, content-based routing, ability to host multiple websites, and security enhancements.

Application Gateway security enhancements include TLS policy management and end-to-end TLS support. Application security is strengthened by WAF integration into Application Gateway. The combination protects your web applications against common vulnerabilities. And it provides an easy-to-configure central location to manage.

### WAF modes

The Application Gateway WAF can be configured to run in the following two modes:

- **Detection mode**: Monitors and logs all threat alerts. You turn on logging diagnostics for Application Gateway in the Diagnostics section. You must also make sure that the WAF log is selected and turned on. Web application firewall doesn't block incoming requests when it's operating in Detection mode.
- **Prevention mode**: Blocks intrusions and attacks that the rules detect. The attacker receives a "403 unauthorized access" exception, and the connection is closed. Prevention mode records such attacks in the WAF logs.
 
> Note
>
> It is recommended that you run a newly deployed WAF in Detection mode for a short period of time in a production environment. This provides the opportunity to obtain firewall logs and update any exceptions or custom rules prior to transition to Prevention mode. This can help reduce the occurrence of unexpected blocked traffic.

### Protection

- Protect your web applications from web vulnerabilities and attacks without modification to back-end code.
- Protect multiple web applications at the same time. An instance of Application Gateway can host up to 40 websites that are protected by a web application firewall.
- Create custom WAF policies for different sites behind the same WAF
- Protect your web applications from malicious bots with the IP Reputation ruleset (preview)

### Monitoring

- Monitor attacks against your web applications by using a real-time WAF log. The log is integrated with Azure Monitor to track WAF alerts and easily monitor trends.
- The Application Gateway WAF is integrated with Azure Security Center. Security Center provides a central view of the security state of all your Azure resources.

### Customization

- Customize WAF rules and rule groups to suit your application requirements and eliminate false positives.
- Associate a WAF Policy for each site behind your WAF to allow for site-specific configuration
- Create custom rules to suit the needs of your application

### Features

- SQL-injection protection.
- Cross-site scripting protection.
- Protection against other common web attacks, such as command injection, HTTP request smuggling, HTTP response splitting, and remote file inclusion.
- Protection against HTTP protocol violations.
- Protection against HTTP protocol anomalies, such as missing host user-agent and accept headers.
- Protection against crawlers and scanners.
- Detection of common application misconfigurations (for example, Apache and IIS).
- Configurable request size limits with lower and upper bounds.
- Exclusion lists let you omit certain request attributes from a WAF evaluation. A common example is Active Directory-inserted tokens that are used for authentication or password fields.
- Create custom rules to suit the specific needs of your applications.
- Geo-filter traffic to allow or block certain countries/regions from gaining access to your applications. (preview)
- Protect your applications from bots with the bot mitigation ruleset. (preview)
- Inspect JSON and XML in the request body

### WAF policy and rules

To enable a Web Application Firewall on Application Gateway, you must create a WAF policy. This policy is where all of the managed rules, custom rules, exclusions, and other customizations such as file upload limit exist.

You can configure a WAF policy and associate that policy to one or more application gateways for protection. A WAF policy consists of two types of security rules:

- Custom rules that you create
- Managed rule sets that are a collection of Azure-managed pre-configured set of rules

When both are present, custom rules are processed before processing the rules in a managed rule set. A rule is made of a match condition, a priority, and an action. Action types supported are: ALLOW, BLOCK, and LOG. You can create a fully customized policy that meets your specific application protection requirements by combining managed and custom rules.

Rules within a policy are processed in a priority order. Priority is a unique integer that defines the order of rules to process. Smaller integer value denotes a higher priority and those rules are evaluated before rules with a higher integer value. Once a rule is matched, the corresponding action that was defined in the rule is applied to the request. Once such a match is processed, rules with lower priorities aren't processed further.

A web application delivered by Application Gateway can have a WAF policy associated to it at the global level, at a per-site level, or at a per-URI level.

### WAF monitoring

Monitoring the health of your application gateway is important. Monitoring the health of your WAF and the applications that it protects are supported by integration with Azure Security Center, Azure Monitor, and Azure Monitor logs.

![Diagram of Application Gateway WAF diagnostics](https://docs.microsoft.com/en-us/azure/web-application-firewall/media/ag-overview/diagnostics.png)

## implement Azure Firewall

### Links

[Tutorial: Deploy and configure Azure Firewall using the Azure portal](https://docs.microsoft.com/en-us/azure/firewall/tutorial-firewall-deploy-portal)

Controlling outbound network access is an important part of an overall network security plan. For example, you may want to limit access to web sites. Or, you may want to limit the outbound IP addresses and ports that can be accessed.

One way you can control outbound network access from an Azure subnet is with Azure Firewall. With Azure Firewall, you can configure:

- Application rules that define fully qualified domain names (FQDNs) that can be accessed from a subnet.
- Network rules that define source address, protocol, destination port, and destination address.

Network traffic is subjected to the configured firewall rules when you route your network traffic to the firewall as the subnet default gateway.

For this tutorial, you create a simplified single VNet with two subnets for easy deployment.

For production deployments, a [hub and spoke model](https://docs.microsoft.com/en-us/azure/architecture/reference-architectures/hybrid-networking/hub-spoke) is recommended, where the firewall is in its own VNet. The workload servers are in peered VNets in the same region with one or more subnets.

- AzureFirewallSubnet - the firewall is in this subnet.
- Workload-SN - the workload server is in this subnet. This subnet's network traffic goes through the firewall.

![Tutorial network infrastructure](https://docs.microsoft.com/en-us/azure/firewall/media/tutorial-firewall-deploy-portal/tutorial-network.png)

In this tutorial, you learn how to:

- Set up a test network environment
- Deploy a firewall
- Create a default route
- Configure an application rule to allow access to www.google.com
- Configure a network rule to allow access to external DNS servers
- Configure a NAT rule to allow a remote desktop to the test server
- Test the firewall

### Create a default route

For the Workload-SN subnet, configure the outbound default route to go through the firewall.

17. For Next hop type, select Virtual appliance.

Azure Firewall is actually a managed service, but virtual appliance works in this situation.

### Configure an application rule

This is the application rule that allows outbound access to `www.google.com`.

### Configure a network rule

This is the network rule that allows outbound access to two IP addresses at port 53 (DNS).

### Configure a DNAT rule

This rule allows you to connect a remote desktop to the Srv-Work virtual machine through the firewall.

## implement the Azure Front Door Service

### Links

- [What is Azure Front Door Service?](https://docs.microsoft.com/en-us/azure/frontdoor/front-door-overview)

Azure Front Door is a global, scalable entry-point that uses the Microsoft global edge network to create fast, secure, and widely scalable web applications. With Front Door, you can transform your global consumer and enterprise applications into robust, high-performing personalized modern applications with contents that reach a global audience through Azure.

![Front Door architecture](https://docs.microsoft.com/en-us/azure/frontdoor/media/front-door-overview/front-door-visual-diagram.png)

Front Door works at Layer 7 (HTTP/HTTPS layer) using anycast protocol with split TCP and Microsoft's global network to improve global connectivity. Based on your routing method you can ensure that Front Door will route your client requests to the fastest and most available application backend. An application backend is any Internet-facing service hosted inside or outside of Azure. Front Door provides a range of traffic-routing methods and backend health monitoring options to suit different application needs and automatic failover scenarios. Similar to Traffic Manager, Front Door is resilient to failures, including failures to an entire Azure region.

> Note
>
> Azure provides a suite of fully managed load-balancing solutions for your scenarios.
>
> - If you are looking to do DNS based global routing and do not have requirements for Transport Layer Security (TLS) protocol termination ("SSL offload"), per-HTTP/HTTPS request or application-layer processing, review [Traffic Manager](https://docs.microsoft.com/en-us/azure/traffic-manager/traffic-manager-overview).
> - If you want to load balance between your servers in a region at the application layer, review [Application Gateway](https://docs.microsoft.com/en-us/azure/application-gateway/overview)
> - To do network layer load balancing, review [Load Balancer](https://docs.microsoft.com/en-us/azure/load-balancer/load-balancer-overview).
>
> Your end-to-end scenarios may benefit from combining these solutions as needed. For an Azure load-balancing options comparison, see Overview of load-balancing options in Azure.

### Why use Azure Front Door?

With Front Door you can build, operate, and scale out your dynamic web application and static content. Front Door enables you to define, manage, and monitor the global routing for your web traffic by optimizing for top-tier end-user performance and reliability through quick global failover.

Key features included with Front Door:

- Accelerated application performance by using split TCP-based anycast protocol.
- Intelligent health probe monitoring for backend resources.
- URL-path based routing for requests.
- Enables hosting of multiple websites for efficient application infrastructure.
- Cookie-based session affinity.
- SSL offloading and certificate management.
- Define your own custom domain.
- Application security with integrated Web Application Firewall (WAF).
- Redirect HTTP traffic to HTTPS with URL redirect.
- Custom forwarding path with URL rewrite.
- Native support of end-to-end IPv6 connectivity and HTTP/2 protocol.

### Pricing

For pricing information, see [Front Door Pricing](https://azure.microsoft.com/pricing/details/frontdoor/). See SLA for Azure Front Door.

[Quickstart: Create a Front Door for a highly available global web application](https://docs.microsoft.com/en-us/azure/frontdoor/quickstart-create-front-door)

- Create two instances of a web app
  - This quickstart requires two instances of a web application that run in different Azure regions. Both the web application instances run in Active/Active mode, so either one can take traffic. This configuration differs from an Active/Stand-By configuration, where one acts as a failover.
- Create a Front Door for your application
  - Configure Azure Front Door to direct user traffic based on lowest latency between the two web apps servers. To begin, add a frontend host for Azure Front Door.
  - Next, create a backend pool that contains your two web apps.
  - Finally, add a routing rule. A routing rule maps your frontend host to the backend pool. The rule forwards a request for contoso-frontend.azurefd.net to myBackendPool.

## implement Azure Traffic Manager

### Azure Traffic Manager

It uses DNS to refer the client to an IP. Here is points to different Application Gateways in different regions:
Allows you to geo-distribute your application around the world.

### Links

[What is Traffic Manager?](https://docs.microsoft.com/en-us/azure/traffic-manager/traffic-manager-overview)

Azure Traffic Manager is a DNS-based traffic load balancer that enables you to distribute traffic optimally to services across global Azure regions, while providing high availability and responsiveness.

Traffic Manager uses DNS to direct client requests to the most appropriate service endpoint based on a traffic-routing method and the health of the endpoints. An endpoint is any Internet-facing service hosted inside or outside of Azure. Traffic Manager provides a range of traffic-routing methods and endpoint monitoring options to suit different application needs and automatic failover models. Traffic Manager is resilient to failure, including the failure of an entire Azure region.

> Note
>
> Azure provides a suite of fully managed load-balancing solutions for your scenarios. If you are looking for Transport Layer Security (TLS) protocol termination ("SSL offload") or per-HTTP/HTTPS request, application-layer processing, review Application Gateway. If you are looking for regional load balancing, review Load Balancer. Your end-to-end scenarios might benefit from combining these solutions as needed.
>
> For an Azure load-balancing options comparison, see Overview of load-balancing options in Azure.

Traffic Manager offers the following features:

### Increase application availability

Traffic Manager delivers high availability for your critical applications by monitoring your endpoints and providing automatic failover when an endpoint goes down.

### Improve application performance

Azure allows you to run cloud services or websites in datacenters located around the world. Traffic Manager improves application responsiveness by directing traffic to the endpoint with the lowest network latency for the client.

### Perform service maintenance without downtime

You can perform planned maintenance operations on your applications without downtime. Traffic Manager can direct traffic to alternative endpoints while the maintenance is in progress.

### Combine hybrid applications

Traffic Manager supports external, non-Azure endpoints enabling it to be used with hybrid cloud and on-premises deployments, including the "burst-to-cloud," "migrate-to-cloud," and "failover-to-cloud" scenarios.

### Distribute traffic for complex deployments

Using nested Traffic Manager profiles, multiple traffic-routing methods can be combined to create sophisticated and flexible rules to scale to the needs of larger, more complex deployments.

[Quickstart: Create a Traffic Manager profile using the Azure portal](https://docs.microsoft.com/en-us/azure/traffic-manager/quickstart-create-traffic-manager-profile)

This quickstart describes how to create a Traffic Manager profile that delivers high availability for your web application.

In this quickstart, you'll read about two instances of a web application. Each of them is running in a different Azure region. You'll create a Traffic Manager profile based on endpoint priority. The profile directs user traffic to the primary site running the web application. Traffic Manager continuously monitors the web application. If the primary site is unavailable, it provides automatic failover to the backup site.

For this quickstart, you'll need two instances of a web application deployed in two different Azure regions (East US and West Europe). Each will serve as primary and failover endpoints for Traffic Manager.

### Create a Traffic Manager profile

Create a Traffic Manager profile that directs user traffic based on endpoint priority.

### Add Traffic Manager endpoints

Add the website in the East US as primary endpoint to route all the user traffic. Add the website in West Europe as a failover endpoint. When the primary endpoint is unavailable, traffic automatically routes to the failover endpoint.

## implement Network Security Groups and Application Security Groups

### Links

[Security groups](https://docs.microsoft.com/en-us/azure/virtual-network/security-overview)

You can use an Azure network security group to filter network traffic to and from Azure resources in an Azure virtual network. A network security group contains security rules that allow or deny inbound network traffic to, or outbound network traffic from, several types of Azure resources. For each rule, you can specify source and destination, port, and protocol.

This article describes properties of a network security group rule, the default security rules that are applied, and the rule properties that you can modify to create an augmented security rule.

### Security rules

A network security group contains zero, or as many rules as desired, within Azure subscription limits. Each rule specifies the following properties:

|Property	|Explanation|
|:--|:--|
|Name	|A unique name within the network security group.|
|Priority	|A number between 100 and 4096. Rules are processed in priority order, with lower numbers processed before higher numbers, because lower numbers have higher priority. Once traffic matches a rule, processing stops. As a result, any rules that exist with lower priorities (higher numbers) that have the same attributes as rules with higher priorities are not processed.|
|Source or destination	|Any, or an individual IP address, classless inter-domain routing (CIDR) block (10.0.0.0/24, for example), service tag, or application security group. If you specify an address for an Azure resource, specify the private IP address assigned to the resource. Network security groups are processed after Azure translates a public IP address to a private IP address for inbound traffic, and before Azure translates a private IP address to a public IP address for outbound traffic. . Specifying a range, a service tag, or application security group, enables you to create fewer security rules. The ability to specify multiple individual IP addresses and ranges (you cannot specify multiple service tags or application groups) in a rule is referred to as augmented security rules. Augmented security rules can only be created in network security groups created through the Resource Manager deployment model. You cannot specify multiple IP addresses and IP address ranges in network security groups created through the classic deployment model.|
|Protocol	|TCP, UDP, ICMP or Any.|
|Direction	|Whether the rule applies to inbound, or outbound traffic.|
|Port range	|You can specify an individual or range of ports. For example, you could specify 80 or 10000-10005. Specifying ranges enables you to create fewer security rules. Augmented security rules can only be created in network security groups created through the Resource Manager deployment model. You cannot specify multiple ports or port ranges in the same security rule in network security groups created through the classic deployment model.|
|Action	|Allow or deny|

Network security group security rules are evaluated by priority using the 5-tuple information (source, source port, destination, destination port, and protocol) to allow or deny the traffic. You may not create two security rules with the same priority and direction. A flow record is created for existing connections. Communication is allowed or denied based on the connection state of the flow record. The flow record allows a network security group to be stateful. If you specify an outbound security rule to any address over port 80, for example, it's not necessary to specify an inbound security rule for the response to the outbound traffic. You only need to specify an inbound security rule if communication is initiated externally. The opposite is also true. If inbound traffic is allowed over a port, it's not necessary to specify an outbound security rule to respond to traffic over the port.

Existing connections may not be interrupted when you remove a security rule that enabled the flow. Traffic flows are interrupted when connections are stopped and no traffic is flowing in either direction, for at least a few minutes.

There are limits to the number of security rules you can create in a network security group. For details, see [Azure limits](https://docs.microsoft.com/en-us/azure/azure-resource-manager/management/azure-subscription-service-limits?toc=/azure/virtual-network/toc.json#azure-resource-manager-virtual-networking-limits).

### [Default security rules](https://docs.microsoft.com/en-us/azure/virtual-network/network-security-groups-overview#default-security-rules)

Azure creates the following default rules in each network security group that you create (click link)

In the Source and Destination columns, *VirtualNetwork*, *AzureLoadBalancer*, and *Internet* are service tags, rather than IP addresses. In the protocol column, Any encompasses TCP, UDP, and ICMP. When creating a rule, you can specify TCP, UDP, ICMP or Any. 0.0.0.0/0 in the Source and Destination columns represents all addresses. Clients like Azure portal, Azure CLI, or PowerShell can use * or any for this expression.

You cannot remove the default rules, but you can override them by creating rules with higher priorities.

### Augmented security rules

Augmented security rules simplify security definition for virtual networks, allowing you to define larger and complex network security policies, with fewer rules. You can combine multiple ports and multiple explicit IP addresses and ranges into a single, easily understood security rule. Use augmented rules in the source, destination, and port fields of a rule. To simplify maintenance of your security rule definition, combine augmented security rules with service tags or application security groups. There are limits to the number of addresses, ranges, and ports that you can specify in a rule. For details, see Azure limits.

### Service tags

A service tag represents a group of IP address prefixes from a given Azure service. It helps to minimize the complexity of frequent updates on network security rules.

For more information, see [Azure service tags](https://docs.microsoft.com/en-us/azure/virtual-network/service-tags-overview). For an example on how to use the Storage service tag to restrict network access, see Restrict network access to PaaS resources.

### Application security groups

Application security groups enable you to configure network security as a natural extension of an application's structure, allowing you to group virtual machines and define network security policies based on those groups. You can reuse your security policy at scale without manual maintenance of explicit IP addresses. To learn more, see [Application security groups](https://docs.microsoft.com/en-us/azure/virtual-network/application-security-groups).

Application security groups enable you to configure network security as a natural extension of an application's structure, allowing you to group virtual machines and define network security policies based on those groups. You can reuse your security policy at scale without manual maintenance of explicit IP addresses. The platform handles the complexity of explicit IP addresses and multiple rule sets, allowing you to focus on your business logic. To better understand application security groups, consider the following example:

![Application security groups](https://docs.microsoft.com/en-us/azure/virtual-network/media/security-groups/application-security-groups.png)

In the previous picture, NIC1 and NIC2 are members of the AsgWeb application security group. NIC3 is a member of the AsgLogic application security group. NIC4 is a member of the AsgDb application security group. Though each network interface in this example is a member of only one network security group, a network interface can be a member of multiple application security groups, up to the Azure limits. None of the network interfaces have an associated network security group. NSG1 is associated to both subnets and contains the following rules:

#### Allow-HTTP-Inbound-Internet
This rule is needed to allow traffic from the internet to the web servers. Because inbound traffic from the internet is denied by the DenyAllInbound default security rule, no additional rule is needed for the AsgLogic or AsgDb application security groups.

|Priority	|Source	|Source ports	|Destination	|Destination ports	|Protocol	|Access|
|:--|:--|:--|:--|:--|:--|:--|
|100	|Internet|	*|	AsgWeb|	80|	TCP	|Allow|

#### Deny-Database-All

Because the AllowVNetInBound default security rule allows all communication between resources in the same virtual network, this rule is needed to deny traffic from all resources.

|Priority	|Source	|Source ports|	Destination	|Destination ports	|Protocol	|Access|
|:--|:--|:--|:--|:--|:--|:--|
|120	|*	|*	|AsgDb	|1433	|Any	|Deny|

#### Allow-Database-BusinessLogic

This rule allows traffic from the AsgLogic application security group to the AsgDb application security group. The priority for this rule is higher than the priority for the Deny-Database-All rule. As a result, this rule is processed before the Deny-Database-All rule, so traffic from the AsgLogic application security group is allowed, whereas all other traffic is blocked.

|Priority	|Source|	Source ports	|Destination	|Destination ports	|Protocol	|Access|
|:--|:--|:--|:--|:--|:--|:--|
|110	|AsgLogic	|*	|AsgDb	|1433	|TCP|	Allow|

The rules that specify an application security group as the source or destination are only applied to the network interfaces that are members of the application security group. If the network interface is not a member of an application security group, the rule is not applied to the network interface, even though the network security group is associated to the subnet.

Application security groups have the following constraints:

- There are limits to the number of application security groups you can have in a subscription, as well as other limits related to application security groups. For details, see Azure limits.
- You can specify one application security group as the source and destination in a security rule. You cannot specify multiple application security groups in the source or destination.
- All network interfaces assigned to an application security group have to exist in the same virtual network that the first network interface assigned to the application security group is in. For example, if the first network interface assigned to an application security group named AsgWeb is in the virtual network named VNet1, then all subsequent network interfaces assigned to ASGWeb must exist in VNet1. You cannot add network interfaces from different virtual networks to the same application security group.
- If you specify an application security group as the source and destination in a security rule, the network interfaces in both application security groups must exist in the same virtual network. For example, if AsgLogic contained network interfaces from VNet1, and AsgDb contained network interfaces from VNet2, you could not assign AsgLogic as the source and AsgDb as the destination in a rule. All network interfaces for both the source and destination application security groups need to exist in the same virtual network.

> Tip
>
> To minimize the number of security rules you need, and the need to change the rules, plan out the application security groups you need and create rules using service tags or application security groups, rather than individual IP addresses, or ranges of IP addresses, whenever possible.

[Create, change, or delete a network security group](https://docs.microsoft.com/en-us/azure/virtual-network/manage-network-security-group)

### Work with network security groups

You can create, view all, view details of, change, and delete a network security group. You can also associate or dissociate a network security group from a network interface or subnet.

### [Create a network security group](https://docs.microsoft.com/en-us/azure/virtual-network/manage-network-security-group#create-a-network-security-group)

### View all network security groups

Go to the Azure portal to view your network security groups. Search for and select Network security groups. The list of network security groups appears for your subscription.

### Change a network security group

1. Go to the Azure portal to view your network security groups. Search for and select Network security groups.
2. Select the name of the network security group you want to change.

The most common changes are to add a security rule, remove a rule, and associate or dissociate a network security group to or from a subnet or network interface.

### Associate or dissociate a network security group to or from a subnet or network interface

To associate a network security group to, or dissociate a network security group from a network interface, see Associate a network security group to, or dissociate a network security group from a network interface. To associate a network security group to, or dissociate a network security group from a subnet, see Change subnet settings.

### Delete a network security group

If a network security group is associated to any subnets or network interfaces, it can't be deleted. Dissociate a network security group from all subnets and network interfaces before attempting to delete it.

1. Go to the Azure portal to view your network security groups. Search for and select Network security groups.
2. Select the name of the network security group you want to delete.
3. In the network security group's toolbar, select Delete. Then select Yes in the confirmation dialog box.

### Work with security rules

A network security group contains zero or more security rules. You can create, view all, view details of, change, and delete a security rule.

### [Create a security rule](https://docs.microsoft.com/en-us/azure/virtual-network/manage-network-security-group#create-a-security-rule)

### View all security rules

A network security group contains zero or more rules. To learn more about the information listed when viewing rules, see Network security group overview.

1. Go to the Azure portal to view the rules of a network security group. Search for and select Network security groups.
2. Select the name of the network security group that you want to view the rules for.
3. In the network security group's menu bar, choose Inbound security rules or Outbound security rules.

The list contains any rules you've created and the network security group's [default security rules](https://docs.microsoft.com/en-us/azure/virtual-network/security-overview#default-security-rules).

### Work with application security groups

An application security group contains zero or more network interfaces. To learn more, see application security groups. All network interfaces in an application security group must exist in the same virtual network. To learn how to add a network interface to an application security group, see Add a network interface to an application security group.

### [Create an application security group](https://docs.microsoft.com/en-us/azure/virtual-network/manage-network-security-group#create-an-application-security-group)

### Permissions

To do tasks on network security groups, security rules, and application security groups, your account must be assigned to the Network contributor role or to a Custom role that's assigned the appropriate permissions as listed in the following tables:

### Network security group

|Action	|Name|
|:--|:--|
|Microsoft.Network/networkSecurityGroups/read	|Get network security group|
|Microsoft.Network/networkSecurityGroups/write	|Create or update network security group|
|Microsoft.Network/networkSecurityGroups/delete	|Delete network security group|
|Microsoft.Network/networkSecurityGroups/join/action	|Associate a network security group to a subnet or network interface|

### Network security group rule

|Action	|Name|
|:--|:--|
|Microsoft.Network/networkSecurityGroups/securityRules/read	|Get rule|
|Microsoft.Network/networkSecurityGroups/securityRules/write	|Create or update rule|
|Microsoft.Network/networkSecurityGroups/securityRules/delete|	Delete rule|

### Application security group

|Action	|Name|
|:--|:--|
|Microsoft.Network/applicationSecurityGroups/joinIpConfiguration/action	|Join an IP configuration to an application security group|
|Microsoft.Network/applicationSecurityGroups/joinNetworkSecurityRule/action	|Join a security rule to an application security group|
|Microsoft.Network/applicationSecurityGroups/read	|Get an application security group|
|Microsoft.Network/applicationSecurityGroups/write	|Create or update an application security group|
|Microsoft.Network/applicationSecurityGroups/delete|	Delete an application security group|

## implement Bastion

### Links

[What is Azure Bastion?](https://docs.microsoft.com/en-us/azure/bastion/bastion-overview)

Azure Bastion is a service you deploy that lets you connect to a virtual machine using your browser and the Azure portal. The Azure Bastion service is a fully platform-managed PaaS service that you provision inside your virtual network. It provides secure and seamless RDP/SSH connectivity to your virtual machines directly from the Azure portal over TLS. When you connect via Azure Bastion, your virtual machines do not need a public IP address, agent, or special client software.

Bastion provides secure RDP and SSH connectivity to all of the VMs in the virtual network in which it is provisioned. Using Azure Bastion protects your virtual machines from exposing RDP/SSH ports to the outside world, while still providing secure access using RDP/SSH.

### Architecture

Azure Bastion deployment is per virtual network, not per subscription/account or virtual machine. Once you provision an Azure Bastion service in your virtual network, the RDP/SSH experience is available to all your VMs in the same virtual network.

RDP and SSH are some of the fundamental means through which you can connect to your workloads running in Azure. Exposing RDP/SSH ports over the Internet isn't desired and is seen as a significant threat surface. This is often due to protocol vulnerabilities. To contain this threat surface, you can deploy bastion hosts (also known as jump-servers) at the public side of your perimeter network. Bastion host servers are designed and configured to withstand attacks. Bastion servers also provide RDP and SSH connectivity to the workloads sitting behind the bastion, as well as further inside the network.

![Azure Bastion Architecture](https://docs.microsoft.com/en-us/azure/bastion/media/bastion-overview/architecture.png)

This figure shows the architecture of an Azure Bastion deployment. In this diagram:

- The Bastion host is deployed in the virtual network.
- The user connects to the Azure portal using any HTML5 browser.
- The user selects the virtual machine to connect to.
- With a single click, the RDP/SSH session opens in the browser.
- No public IP is required on the Azure VM.

### Key features

The following features are available:

- **RDP and SSH directly in Azure portal**: You can directly get to the RDP and SSH session directly in the Azure portal using a single click seamless experience.
- **Remote Session over TLS and firewall traversal for RDP/SSH**: Azure Bastion uses an HTML5 based web client that is automatically streamed to your local device, so that you get your RDP/SSH session over TLS on port 443 enabling you to traverse corporate firewalls securely.
- **No Public IP required on the Azure VM**: Azure Bastion opens the RDP/SSH connection to your Azure virtual machine using private IP on your VM. You don't need a public IP on your virtual machine.
- **No hassle of managing NSGs**: Azure Bastion is a fully managed platform PaaS service from Azure that is hardened internally to provide you secure RDP/SSH connectivity. You don't need to apply any NSGs on Azure Bastion subnet. Because Azure Bastion connects to your virtual machines over private IP, you can configure your NSGs to allow RDP/SSH from Azure Bastion only. This removes the hassle of managing NSGs each time you need to securely connect to your virtual machines.
- **Protection against port scanning**: Because you do not need to expose your virtual machines to public Internet, your VMs are protected against port scanning by rogue and malicious users located outside your virtual network.
- **Protect against zero-day exploits. Hardening in one place only**: Azure Bastion is a fully platform-managed PaaS service. Because it sits at the perimeter of your virtual network, you don’t need to worry about hardening each of the virtual machines in your virtual network. The Azure platform protects against zero-day exploits by keeping the Azure Bastion hardened and always up to date for you.


[Create an Azure Bastion host](https://docs.microsoft.com/en-us/azure/bastion/bastion-create-host-portal)

This tutorial shows you how to connect to a virtual machine through your browser using Azure Bastion and the Azure portal. In the Azure portal, you deploy Bastion to your virtual network. After deploying Bastion, you connect to a VM via its private IP address using the Azure portal. Your VM does not need a public IP address or special software. Once the service is provisioned, the RDP/SSH experience is available to all of the virtual machines in the same virtual network. For more information about Azure Bastion, see [What is Azure Bastion?](https://docs.microsoft.com/en-us/azure/bastion/bastion-overview).

### Prerequisites

- A virtual network.
- A Windows virtual machine in the virtual network.
- The following required roles:
  - Reader role on the virtual machine.
  - Reader role on the NIC with private IP of the virtual machine.
  - Reader role on the Azure Bastion resource.
- Ports: To connect to the Windows VM, you must have the following ports open on your Windows VM:
  - Inbound ports: RDP (3389)

### Create a bastion host

This section helps you create the bastion object in your VNet. This is required in order to create a secure connection to a VM in the VNet.

1. From the Home page, select + Create a resource.
2. On the New page, in the Search box, type Bastion, then select Enter to get to the search results. On the result for Bastion, verify that the publisher is Microsoft.
3. Select Create.
4. On the Create a Bastion page, configure a new Bastion resource.
  - Subscription: The Azure subscription you want to use to create a new Bastion resource.
  - Resource Group: The Azure resource group in which the new Bastion resource will be created. If you don't have an existing resource group, you can create a new one.
  - Name: The name of the new Bastion resource.
  - Region: The Azure public region that the resource will be created in.
  - Virtual network: The virtual network in which the Bastion resource will be created. You can create a new virtual network in the portal during this process, or use an existing virtual network. If you are using an existing virtual network, make sure the existing virtual network has enough free address space to accommodate the Bastion subnet requirements. If you don't see your virtual network from the dropdown, make sure you have selected the correct Resource Group.
  - Subnet: Once you create or select a virtual network, the subnet field will appear. The subnet in your virtual network where the new Bastion host will be deployed. The subnet will be dedicated to the Bastion host. Select Manage subnet configuration and create the Azure Bastion subnet. Select +Subnet and create a subnet using the following guidelines:
    - The subnet must be named AzureBastionSubnet.
    - The subnet must be at least /27 or larger.
    
    You don't need to fill out additional fields. Select OK and then, at the top of the page, select Create a Bastion to return to the Bastion configuration page.
  - Public IP address: The public IP of the Bastion resource on which RDP/SSH will be accessed (over port 443). Create a new public IP. The public IP address must be in the same region as the Bastion resource you are creating. This is IP address does not have anything to do with any of the VMs that you want to connect to. It's the public IP for the Bastion host resource.
  - Public IP address name: The name of the public IP address resource. For this tutorial, you can leave the default.
  - Public IP address SKU: This setting is prepopulated by default to Standard. Azure Bastion uses/supports only the Standard Public IP SKU.
  - Assignment: This setting is prepopulated by default to Static.
5. When you have finished specifying the settings, select Review + Create. This validates the values. Once validation passes, you can create the Bastion resource.
6. Select Create.
