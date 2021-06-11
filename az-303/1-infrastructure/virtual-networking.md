
# [Azure networking - Azure Docs](https://docs.microsoft.com/en-us/azure/networking/)

## implement VNet to VNet connections

[Configure a VNet-to-VNet VPN gateway connection by using the Azure portal](https://docs.microsoft.com/en-us/azure/vpn-gateway/vpn-gateway-howto-vnet-vnet-resource-manager-portal)

### About connecting VNets

The following sections describe the different ways to connect virtual networks.

#### VNet-to-VNet

Configuring a VNet-to-VNet connection is a simple way to connect VNets. When you connect a virtual network to another virtual network with a VNet-to-VNet connection type (VNet2VNet), it's similar to creating a Site-to-Site IPsec connection to an on-premises location. Both connection types use a VPN gateway to provide a secure tunnel with IPsec/IKE and function the same way when communicating. However, they differ in the way the local network gateway is configured.

When you create a VNet-to-VNet connection, the local network gateway address space is automatically created and populated. If you update the address space for one VNet, the other VNet automatically routes to the updated address space. It's typically faster and easier to create a VNet-to-VNet connection than a Site-to-Site connection.

#### Site-to-Site (IPsec)

If you're working with a complicated network configuration, you may prefer to connect your VNets by using a [Site-to-Site connection](https://docs.microsoft.com/en-us/azure/vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal) instead. When you follow the Site-to-Site IPsec steps, you create and configure the local network gateways manually. The local network gateway for each VNet treats the other VNet as a local site. These steps allow you to specify additional address spaces for the local network gateway to route traffic. If the address space for a VNet changes, you must manually update the corresponding local network gateway.

#### VNet peering

You can also connect your VNets by using VNet peering. VNet peering doesn't use a VPN gateway and has different constraints. Additionally, [VNet peering pricing](https://azure.microsoft.com/pricing/details/virtual-network) is calculated differently than VNet-to-VNet VPN Gateway pricing. For more information, see [VNet peering](https://docs.microsoft.com/en-us/azure/virtual-network/virtual-network-peering-overview).

### Why create a VNet-to-VNet connection?

You may want to connect virtual networks by using a VNet-to-VNet connection for the following reasons:

#### Cross region geo-redundancy and geo-presence

- You can set up your own geo-replication or synchronization with secure connectivity without going over internet-facing endpoints.
- With Azure Traffic Manager and Azure Load Balancer, you can set up highly available workload with geo-redundancy across multiple Azure regions. For example, you can set up SQL Server Always On availability groups across multiple Azure regions.

#### Regional multi-tier applications with isolation or administrative boundaries

- Within the same region, you can set up multi-tier applications with multiple virtual networks that are connected together because of isolation or administrative requirements.

VNet-to-VNet communication can be combined with multi-site configurations. These configurations lets you establish network topologies that combine cross-premises connectivity with inter-virtual network connectivity, as shown in the following diagram:

![VNet and Multi-Site](https://docs.microsoft.com/en-us/azure/vpn-gateway/media/vpn-gateway-howto-vnet-vnet-resource-manager-portal/aboutconnections.png)

### [Example Settings](https://docs.microsoft.com/en-us/azure/vpn-gateway/vpn-gateway-howto-vnet-vnet-resource-manager-portal#example-settings)

> Note
>
> When using a virtual network as part of a cross-premises architecture, be sure to coordinate with your on-premises network administrator to carve out an IP address range that you can use specifically for this virtual network. If a duplicate address range exists on both sides of the VPN connection, traffic will route in an unexpected way. Additionally, if you want to connect this virtual network to another virtual network, the address space cannot overlap with the other virtual network. Plan your network configuration accordingly.

### VNet-to-VNet FAQ

View the FAQ details for additional information about VNet-to-VNet connections.

The VNet-to-VNet FAQ applies to VPN gateway connections. For information about VNet peering, see Virtual network peering.

#### Does Azure charge for traffic between VNets?

VNet-to-VNet traffic within the same region is free for both directions when you use a VPN gateway connection. Cross-region VNet-to-VNet egress traffic is charged with the outbound inter-VNet data transfer rates based on the source regions. For more information, see VPN Gateway pricing page. If you're connecting your VNets by using VNet peering instead of a VPN gateway, see Virtual network pricing.

#### Does VNet-to-VNet traffic travel across the internet?

No. VNet-to-VNet traffic travels across the Microsoft Azure backbone, not the internet.

#### Can I establish a VNet-to-VNet connection across Azure Active Directory (AAD) tenants?

Yes, VNet-to-VNet connections that use Azure VPN gateways work across AAD tenants.

#### Is VNet-to-VNet traffic secure?

Yes, it's protected by IPsec/IKE encryption.

#### Do I need a VPN device to connect VNets together?

No. Connecting multiple Azure virtual networks together doesn't require a VPN device unless cross-premises connectivity is required.

#### Do my VNets need to be in the same region?

No. The virtual networks can be in the same or different Azure regions (locations).

#### If the VNets aren't in the same subscription, do the subscriptions need to be associated with the same Active Directory tenant?

No.

#### Can I use VNet-to-VNet to connect virtual networks in separate Azure instances?

No. VNet-to-VNet supports connecting virtual networks within the same Azure instance. For example, you can’t create a connection between global Azure and Chinese/German/US government Azure instances. Consider using a Site-to-Site VPN connection for these scenarios.

#### Can I use VNet-to-VNet along with multi-site connections?

Yes. Virtual network connectivity can be used simultaneously with multi-site VPNs.

#### How many on-premises sites and virtual networks can one virtual network connect to?

See the Gateway requirements table.

#### Can I use VNet-to-VNet to connect VMs or cloud services outside of a VNet?

No. VNet-to-VNet supports connecting virtual networks. It doesn't support connecting virtual machines or cloud services that aren't in a virtual network.

#### Can a cloud service or a load-balancing endpoint span VNets?

No. A cloud service or a load-balancing endpoint can't span across virtual networks, even if they're connected together.

#### Can I use a PolicyBased VPN type for VNet-to-VNet or Multi-Site connections?

No. VNet-to-VNet and Multi-Site connections require Azure VPN gateways with RouteBased (previously called dynamic routing) VPN types.

#### Can I connect a VNet with a RouteBased VPN Type to another VNet with a PolicyBased VPN type?

No, both virtual networks MUST use route-based (previously called dynamic routing) VPNs.

#### Do VPN tunnels share bandwidth?

Yes. All VPN tunnels of the virtual network share the available bandwidth on the Azure VPN gateway and the same VPN gateway uptime SLA in Azure.

#### Are redundant tunnels supported?

Redundant tunnels between a pair of virtual networks are supported when one virtual network gateway is configured as active-active.

#### Can I have overlapping address spaces for VNet-to-VNet configurations?

No. You can't have overlapping IP address ranges.

#### Can there be overlapping address spaces among connected virtual networks and on-premises local sites?

No. You can't have overlapping IP address ranges.

## implement VNet peering

### [Virtual network peering](https://docs.microsoft.com/en-us/azure/virtual-network/virtual-network-peering-overview)

Virtual network peering enables you to seamlessly connect two or more Virtual Networks in Azure. The virtual networks appear as one for connectivity purposes. The traffic between virtual machines in peered virtual networks uses the Microsoft backbone infrastructure. Like traffic between virtual machines in the same network, traffic is routed through Microsoft's private network only.

Azure supports the following types of peering:

- Virtual network peering: Connect virtual networks within the same Azure region.
- Global virtual network peering: Connecting virtual networks across Azure regions.

The benefits of using virtual network peering, whether local or global, include:

- A low-latency, high-bandwidth connection between resources in different virtual networks.
- The ability for resources in one virtual network to communicate with resources in a different virtual network.
- The ability to transfer data between virtual networks across Azure subscriptions, Azure Active Directory tenants, deployment models, and Azure regions.
- The ability to peer virtual networks created through the Azure Resource Manager.
- The ability to peer a virtual network created through Resource Manager to one created through the classic deployment model. To learn more about Azure deployment models, see [Understand Azure deployment models](https://docs.microsoft.com/en-us/azure/azure-resource-manager/management/deployment-models?toc=/azure/virtual-network/toc.json).
- No downtime to resources in either virtual network when creating the peering, or after the peering is created.
- Network traffic between peered virtual networks is private. Traffic between the virtual networks is kept on the Microsoft backbone network. No public Internet, gateways, or encryption is required in the communication between the virtual networks.

#### Connectivity

For peered virtual networks, resources in either virtual network can directly connect with resources in the peered virtual network.

The network latency between virtual machines in peered virtual networks in the same region is the same as the latency within a single virtual network. The network throughput is based on the bandwidth that's allowed for the virtual machine, proportionate to its size. There isn't any additional restriction on bandwidth within the peering.

The traffic between virtual machines in peered virtual networks is routed directly through the Microsoft backbone infrastructure, not through a gateway or over the public Internet.

You can apply network security groups in either virtual network to block access to other virtual networks or subnets. When configuring virtual network peering, either open or close the network security group rules between the virtual networks. If you open full connectivity between peered virtual networks, you can apply network security groups to block or deny specific access. Full connectivity is the default option. To learn more about network security groups, see Security groups.

#### Service chaining

Service chaining enables you to direct traffic from one virtual network to a virtual appliance or gateway in a peered network through user-defined routes.

To enable service chaining, configure user-defined routes that point to virtual machines in peered virtual networks as the next hop IP address. User-defined routes could also point to virtual network gateways to enable service chaining.

You can deploy hub-and-spoke networks, where the hub virtual network hosts infrastructure components such as a network virtual appliance or VPN gateway. All the spoke virtual networks can then peer with the hub virtual network. Traffic flows through network virtual appliances or VPN gateways in the hub virtual network.

Virtual network peering enables the next hop in a user-defined route to be the IP address of a virtual machine in the peered virtual network, or a VPN gateway. You can't route between virtual networks with a user-defined route that specifies an Azure ExpressRoute gateway as the next hop type. To learn more about user-defined routes, see [User-defined routes overview](https://docs.microsoft.com/en-us/azure/virtual-network/virtual-networks-udr-overview#user-defined). To learn how to create a hub and spoke network topology, see [Hub-spoke network topology in Azure](https://docs.microsoft.com/en-us/azure/architecture/reference-architectures/hybrid-networking/hub-spoke?toc=%2fazure%2fvirtual-network%2ftoc.json).

#### Gateways and on-premises connectivity

Each virtual network, including a peered virtual network, can have its own gateway. A virtual network can use its gateway to connect to an on-premises network. You can also configure virtual network-to-virtual network connections by using gateways, even for peered virtual networks.

When you configure both options for virtual network interconnectivity, the traffic between the virtual networks flows through the peering configuration. The traffic uses the Azure backbone.

You can also configure the gateway in the peered virtual network as a transit point to an on-premises network. In this case, the virtual network that is using a remote gateway can't have its own gateway. A virtual network has only one gateway. The gateway is either a local or remote gateway in the peered virtual network, as shown in the following diagram:

![Local or remote gateway in peered virtual network](https://docs.microsoft.com/en-us/azure/virtual-network/media/virtual-networks-peering-overview/local-or-remote-gateway-in-peered-virual-network.png)

Both virtual network peering and global virtual network peering support gateway transit.

Gateway transit between virtual networks created through different deployment models is supported. The gateway must be in the virtual network in the Resource Manager model. To learn more about using a gateway for transit, see Configure a VPN gateway for transit in a virtual network peering.

When you peer virtual networks that share a single Azure ExpressRoute connection, the traffic between them goes through the peering relationship. That traffic uses the Azure backbone network. You can still use local gateways in each virtual network to connect to the on-premises circuit. Otherwise, you can use a shared gateway and configure transit for on-premises connectivity.

#### Troubleshoot

To confirm that virtual networks are peered, you can check effective routes. Check routes for a network interface in any subnet in a virtual network. If a virtual network peering exists, all subnets within the virtual network have routes with next hop type VNet peering, for each address space in each peered virtual network. For more information, see Diagnose a virtual machine routing problem.

You can also troubleshoot connectivity to a virtual machine in a peered virtual network using Azure Network Watcher. A connectivity check lets you see how traffic is routed from a source virtual machine's network interface to a destination virtual machine's network interface. For more information, see Troubleshoot connections with Azure Network Watcher using the Azure portal.

You can also try the Troubleshoot virtual network peering issues.

#### Constraints for peered virtual networks

The following constraints apply only when virtual networks are globally peered:

- Resources in one virtual network can't communicate with the front-end IP address of a Basic Internal Load Balancer (ILB) in a globally peered virtual network.
- Some services that use a Basic load balancer don't work over global virtual network peering. For more information, see What are the constraints related to Global VNet Peering and Load Balancers?.

For more information, see Requirements and constraints. To learn more about the supported number of peerings, see Networking limits.

#### Pricing

- [Virtual Network Pricing](https://azure.microsoft.com/pricing/details/virtual-network)
- [VPN Gateway Pricing](https://azure.microsoft.com/pricing/details/vpn-gateway/)

There's a nominal charge for ingress and egress traffic that uses a virtual network peering connection. For more information, see [Virtual Network pricing](https://azure.microsoft.com/pricing/details/vpn-network/).

Gateway Transit is a peering property that enables a virtual network to utilize a VPN/ExpressRoute gateway in a peered virtual network. Gateway transit works for both cross premises and network-to-network connectivity. Traffic to the gateway (ingress or egress) in the peered virtual network incurs virtual network peering charges on the spoke VNet (or non-gateway VNet). For more information, see [VPN Gateway pricing](https://azure.microsoft.com/pricing/details/vpn-gateway/) for VPN gateway charges and ExpressRoute Gateway pricing for ExpressRoute gateway charges.

### [Create, change, or delete a virtual network peering](https://docs.microsoft.com/en-us/azure/virtual-network/virtual-network-manage-peering)

#### Create a virtual network peering

- **Allow virtual network access**: Select **Enabled** (default) if you want to enable communication between the two virtual networks. Enabling communication between virtual networks allows resources connected to either virtual network to communicate with each other with the same bandwidth and latency as if they were connected to the same virtual network. All communication between resources in the two virtual networks is over the Azure private network. The **VirtualNetwork** service tag for network security groups encompasses the virtual network and peered virtual network. To learn more about network security group service tags, see [Network security groups overview](https://docs.microsoft.com/en-us/azure/virtual-network/security-overview#service-tags). Select **Disabled** if you don't want traffic to flow to the peered virtual network. You might select **Disabled** if you've peered a virtual network with another virtual network, but occasionally want to disable traffic flow between the two virtual networks. You may find enabling/disabling is more convenient than deleting and re-creating peerings. When this setting is disabled, traffic doesn't flow between the peered virtual networks.

- **Allow forwarded traffic**: Check this box to allow traffic forwarded by a network virtual appliance in a virtual network (that didn't originate from the virtual network) to flow to this virtual network through a peering. For example, consider three virtual networks named Spoke1, Spoke2, and Hub. A peering exists between each spoke virtual network and the Hub virtual network, but peerings don't exist between the spoke virtual networks. A network virtual appliance is deployed in the Hub virtual network, and user-defined routes are applied to each spoke virtual network that route traffic between the subnets through the network virtual appliance. If this checkbox is not checked for the peering between each spoke virtual network and the hub virtual network, traffic doesn't flow between the spoke virtual networks because the hub is not forwarding the traffic between the virtual networks. While enabling this capability allows the forwarded traffic through the peering, it does not create any user-defined routes or network virtual appliances. User-defined routes and network virtual appliances are created separately. Learn about [user-defined routes](https://docs.microsoft.com/en-us/azure/virtual-network/virtual-networks-udr-overview#user-defined). You don't need to check this setting if traffic is forwarded between virtual networks through an Azure VPN Gateway.

- **Allow gateway transit**: Check this box if you have a virtual network gateway attached to this virtual network and want to allow traffic from the peered virtual network to flow through the gateway. For example, this virtual network may be attached to an on-premises network through a virtual network gateway. The gateway can be an ExpressRoute or VPN gateway. Checking this box allows traffic from the peered virtual network to flow through the gateway attached to this virtual network to the on-premises network. If you check this box, the peered virtual network cannot have a gateway configured. The peered virtual network must have the **Use remote gateways** checkbox checked when setting up the peering from the other virtual network to this virtual network. If you leave this box unchecked (default), traffic from the peered virtual network still flows to this virtual network, but cannot flow through a virtual network gateway attached to this virtual network. If the peering is between a virtual network (Resource Manager) and a virtual network (classic), the gateway must be in the virtual network (Resource Manager).

  In addition to forwarding traffic to an on-premises network, a VPN gateway can forward network traffic between virtual networks that are peered with the virtual network the gateway is in, without the virtual networks needing to be peered with each other. Using a VPN gateway to forward traffic is useful when you want to use a VPN gateway in a hub (see the hub and spoke example described for Allow forwarded traffic) virtual network to route traffic between spoke virtual networks that aren't peered with each other. To learn more about allowing use of a gateway for transit, see Configure a VPN gateway for transit in a virtual network peering. This scenario requires implementing user-defined routes that specify the virtual network gateway as the next hop type. Learn about user-defined routes. You can only specify a VPN gateway as a next hop type in a user-defined route, you cannot specify an ExpressRoute gateway as the next hop type in a user-defined route.

- **Use remote gateways**: Check this box to allow traffic from this virtual network to flow through a virtual network gateway attached to the virtual network you're peering with. For example, the virtual network you're peering with has a VPN gateway attached that enables communication to an on-premises network. Checking this box allows traffic from this virtual network to flow through the VPN gateway attached to the peered virtual network. If you check this box, the peered virtual network must have a virtual network gateway attached to it and must have the Allow gateway transit checkbox checked. If you leave this box unchecked (default), traffic from the peered virtual network can still flow to this virtual network, but cannot flow through a virtual network gateway attached to this virtual network.

Only one peering for this virtual network can have this setting enabled.

You can't use remote gateways if you already have a gateway configured in your virtual network. To learn more about using a gateway for transit, see Configure a VPN gateway for transit in a virtual network peering

##### Commands

- **Azure CLI**: `az network vnet peering create`
- **PowerShell**: `Add-AzVirtualNetworkPeering`

#### View or change peering settings

Before changing a peering, familiarize yourself with the requirements and constraints and [necessary permissions](https://docs.microsoft.com/en-us/azure/virtual-network/virtual-network-manage-peering#permissions).

1. In the search box at the top of the portal, enter virtual networks in the search box. When Virtual networks appear in the search results, select it. Do not select Virtual networks (classic) if it appears in the list, as you cannot create a peering from a virtual network deployed through the classic deployment model.
2. Select the virtual network in the list that you want to change peering settings for.
3. Under SETTINGS, select Peerings.
4. Select the peering you want to view or change settings for.
5. Change the appropriate setting. Read about the options for each setting in step 5 of Create a peering.
6. Select Save.

##### Commands

- **Azure CLI**: 
  - `az network vnet peering list` to list peerings for a virtual network
  - `az network vnet peering show` to show settings for a specific peering
  - `az network vnet peering update` to change peering settings
- **PowerShell**: `Get-AzVirtualNetworkPeering` to retrieve view peering settings and Set-AzVirtualNetworkPeering to change settings.

#### Delete a peering

Before deleting a peering, ensure your account has the necessary permissions.

When a peering is deleted, traffic from a virtual network no longer flows to the peered virtual network. When virtual networks deployed through Resource Manager are peered, each virtual network has a peering to the other virtual network. Though deleting the peering from one virtual network disables the communication between the virtual networks, it does not delete the peering from the other virtual network. The peering status for the peering that exists in the other virtual network is **Disconnected**. You cannot recreate the peering until you re-create the peering in the first virtual network and the peering status for both virtual networks changes to Connected.

If you want virtual networks to communicate sometimes, but not always, rather than deleting a peering, you can set the **Allow virtual network access** setting to **Disabled** instead. To learn how, read step 6 of the Create a peering section of this article. You may find disabling and enabling network access easier than deleting and recreating peerings.

1. In the search box at the top of the portal, enter virtual networks in the search box. When Virtual networks appear in the search results, select it. Do not select Virtual networks (classic) if it appears in the list, as you cannot create a peering from a virtual network deployed through the classic deployment model.
2. Select the virtual network in the list that you want to delete a peering for.
3. Under **SETTINGS**, select **Peerings**.
4. On the right side of the peering you want to delete, select ..., select Delete, then select Yes to delete the peering from the first virtual network.
5. Complete the previous steps to delete the peering from the other virtual network in the peering.

##### Commands

- **Azure CLI**: az `network vnet peering delete`
- **PowerShell**: `Remove-AzVirtualNetworkPeering`

#### Requirements and constraints

- You can peer virtual networks in the same region, or different regions. Peering virtual networks in different regions is also referred to as Global VNet Peering.

- When creating a global peering, the peered virtual networks can exist in any Azure public cloud region or China cloud regions or Government cloud regions. You cannot peer across clouds. For example, a VNet in Azure public cloud cannot be peered to a VNet in Azure China cloud.

- Resources in one virtual network cannot communicate with the front-end IP address of a Basic internal load balancer in a globally peered virtual network. Support for Basic Load Balancer only exists within the same region. Support for Standard Load Balancer exists for both, VNet Peering and Global VNet Peering. Services that use a Basic load balancer which will not work over Global VNet Peering are documented here.

- You can use remote gateways or allow gateway transit in globally peered virtual networks and locally peered virtual networks.

- The virtual networks can be in the same, or different subscriptions. When you peer virtual networks in different subscriptions, both subscriptions can be associated to the same or different Azure Active Directory tenant. If you don't already have an AD tenant, you can create one.

- The virtual networks you peer must have non-overlapping IP address spaces.

- You can't add address ranges to, or delete address ranges from a virtual network's address space once a virtual network is peered with another virtual network. To add or remove address ranges, delete the peering, add or remove the address ranges, then re-create the peering. To add address ranges to, or remove address ranges from virtual networks, see Manage virtual networks.

- You can peer two virtual networks deployed through Resource Manager or a virtual network deployed through Resource Manager with a virtual network deployed through the classic deployment model. You cannot peer two virtual networks created through the classic deployment model. If you're not familiar with Azure deployment models, read the Understand Azure deployment models article. You can use a VPN Gateway to connect two virtual networks created through the classic deployment model.

- When peering two virtual networks created through Resource Manager, a peering must be configured for each virtual network in the peering. You see one of the following types for peering status:
  - *Initiated*: When you create the peering to the second virtual network from the first virtual network, the peering status is Initiated.
  - *Connected*: When you create the peering from the second virtual network to the first virtual network, its peering status is Connected. If you view the peering status for the first virtual network, you see its status changed from Initiated to Connected. The peering is not successfully established until the peering status for both virtual network peerings is Connected.

- When peering a virtual network created through Resource Manager with a virtual network created through the classic deployment model, you only configure a peering for the virtual network deployed through Resource Manager. You cannot configure peering for a virtual network (classic), or between two virtual networks deployed through the classic deployment model. When you create the peering from the virtual network (Resource Manager) to the virtual network (Classic), the peering status is Updating, then shortly changes to Connected.

- A peering is established between two virtual networks. Peerings by itself are not transitive. If you create peerings between:
  - VirtualNetwork1 & VirtualNetwork2 - VirtualNetwork1 & VirtualNetwork2
  - VirtualNetwork2 & VirtualNetwork3 - VirtualNetwork2 & VirtualNetwork3
  
  There is no peering between VirtualNetwork1 and VirtualNetwork3 through VirtualNetwork2. If you want to create a virtual network peering between VirtualNetwork1 and VirtualNetwork3, you have to create a peering between VirtualNetwork1 and VirtualNetwork3. There is no peering between VirtualNetwork1 and VirtualNetwork3 through VirtualNetwork2. If you want VirtualNetwork1 and VirtualNetwork3 to directly communicate, you have to create an explicit peering between VirtualNetwork1 and VirtualNetwork3 or go through an NVA in the Hub network.

- You can't resolve names in peered virtual networks using default Azure name resolution. To resolve names in other virtual networks, you must use [Azure DNS for private domains](https://docs.microsoft.com/en-us/azure/dns/private-dns-overview?toc=/azure/virtual-network/toc.json) or a custom DNS server. To learn how to set up your own DNS server, see [Name resolution using your own DNS server](https://docs.microsoft.com/en-us/azure/virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances#name-resolution-that-uses-your-own-dns-server).

- Resources in peered virtual networks in the same region can communicate with each other with the same bandwidth and latency as if they were in the same virtual network. Each virtual machine size has its own maximum network bandwidth however. To learn more about maximum network bandwidth for different virtual machine sizes, see Windows or Linux virtual machine sizes.

- A virtual network can be peered to another virtual network, and also be connected to another virtual network with an Azure virtual network gateway. When virtual networks are connected through both peering and a gateway, traffic between the virtual networks flows through the peering configuration, rather than the gateway.

- Point-to-Site VPN clients must be downloaded again after virtual network peering has been successfully configured to ensure the new routes are downloaded to the client.

- There is a nominal charge for ingress and egress traffic that utilizes a virtual network peering. For more information, see the [pricing page](https://azure.microsoft.com/pricing/details/virtual-network).

#### Permissions

The accounts you use to work with virtual network peering must be assigned to the following roles:

- [Network Contributor](https://docs.microsoft.com/en-us/azure/role-based-access-control/built-in-roles?toc=/azure/virtual-network/toc.json#network-contributor): For a virtual network deployed through Resource Manager.
- [Classic Network Contributor](https://docs.microsoft.com/en-us/azure/role-based-access-control/built-in-roles?toc=/azure/virtual-network/toc.json#classic-network-contributor): For a virtual network deployed through the classic deployment model.

If your account is not assigned to one of the previous roles, it must be assigned to a custom role that is assigned the necessary actions from the following table:

|Action	|Name|
|:--|:--|
|Microsoft.Network/virtualNetworks/virtualNetworkPeerings/write	|Required to create a peering from virtual network A to virtual network B. Virtual network A must be a virtual network (Resource Manager)|
|Microsoft.Network/virtualNetworks/peer/action	|Required to create a peering from virtual network B (Resource Manager) to virtual network A|
|Microsoft.ClassicNetwork/virtualNetworks/peer/action	|Required to create a peering from virtual network B (classic) to virtual network A|
|Microsoft.Network/virtualNetworks/virtualNetworkPeerings/read	|Read a virtual network peering|
|Microsoft.Network/virtualNetworks/virtualNetworkPeerings/delete	|Delete a virtual network peering|
