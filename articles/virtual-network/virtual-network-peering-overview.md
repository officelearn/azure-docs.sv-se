---
title: Azure Virtual Network-peering
titlesuffix: Azure Virtual Network
description: Lär dig mer om virtuell nätverkspeering i Azure.
services: virtual-network
documentationcenter: na
author: anavinahar
ms.service: virtual-network
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/15/2019
ms.author: anavin
ms.openlocfilehash: 59854d7d46f533510bea97a6845554fc0ce83dbb
ms.sourcegitcommit: b77e97709663c0c9f84d95c1f0578fcfcb3b2a6c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/22/2019
ms.locfileid: "74328354"
---
# <a name="virtual-network-peering"></a>VNET-peering

Virtual network peering enables you to seamlessly connect networks in [Azure Virtual Network](virtual-networks-overview.md). The virtual networks appear as one for connectivity purposes. The traffic between virtual machines uses the Microsoft backbone infrastructure. Like traffic between virtual machines in the same network, traffic is routed through Microsoft's *private* network only.

Azure supports the following types of peering:

* Virtual network peering: Connect virtual networks within the same Azure region.
* Global virtual network peering: Connecting virtual networks across Azure regions.

Fördelar med att använda VNET-peering (avsett om den är lokal eller global):

* En anslutning med korta svarstider och hög bandbredd mellan resurser i olika virtuella nätverk.
* The ability for resources in one virtual network to communicate with resources in a different virtual network.
* The ability to transfer data between virtual networks across Azure subscriptions, Azure Active Directory tenants, deployment models, and Azure regions.
* The ability to peer virtual networks created through the Azure Resource Manager.
* The ability to peer a virtual network created through Resource Manager to one created through the classic deployment model. Läs mer i avsnittet [om Azures distributionsmodeller](../azure-resource-manager/resource-manager-deployment-model.md?toc=%2fazure%2fvirtual-network%2ftoc.json).
* Inga driftstopp för resurser i de virtuella nätverken när du skapar peer-kopplingen eller när peer-kopplingen har skapats.

Nätverkstrafiken mellan peer-kopplade virtuella nätverk är privat. Trafiken mellan de virtuella nätverken finns i Microsoft-stamnätverket. Vid kommunikation mellan virtuella nätverk krävs inget offentligt Internet, inga gatewayer eller ingen kryptering.

## <a name="connectivity"></a>Anslutningsmöjlighet

For peered virtual networks, resources in either virtual network can directly connect with resources in the peered virtual network.

Nätverksfördröjningen mellan virtuella datorer i peer-kopplade virtuella nätverk i samma region är densamma som svarstiden inom ett enda virtuellt nätverk. Nätverkets genomflöde baseras på den bandbredd som tillåts för den virtuella datorn i proportion till dess storlek. Det finns inte några ytterligare begränsning vad gäller bandbredden inom peerkopplingen.

Trafiken mellan virtuella datorer i peer-kopplade virtuella nätverk dirigeras direkt genom Microsoft-stamnätsinfrastrukturen, inte via en gateway eller det offentliga Internet.

You can apply network security groups in either virtual network to block access to other virtual networks or subnets.
When configuring virtual network peering, either open or close the network security group rules between the virtual networks. If you open full connectivity between peered virtual networks, you can apply network security groups to block or deny specific access. Full connectivity is the default option. To learn more about network security groups, see [Security groups](security-overview.md).

## <a name="service-chaining"></a>Tjänstlänkning

Service chaining enables you to direct traffic from one virtual network to a virtual appliance or gateway in a peered network through user-defined routes.

To enable service chaining, configure user-defined routes that point to virtual machines in peered virtual networks as the *next hop* IP address. User-defined routes could also point to virtual network gateways to enable service chaining.

You can deploy hub-and-spoke networks, where the hub virtual network hosts infrastructure components such as a network virtual appliance or VPN gateway. Alla virtuella ekernätverk kan peer-kopplas till det virtuella navnätverket. Traffic flows through network virtual appliances or VPN gateways in the hub virtual network.

Peering för virtuellt nätverk gör att nästa hopp i den användardefinierade routningen kan vara IP-adressen för en virtuell dator i det peer-kopplade virtuella nätverket eller en VPN-gateway. You can't route between virtual networks with a user-defined route that specifies an Azure ExpressRoute gateway as the next hop type. Mer information om användardefinierade vägar finns i [översikten över användardefinierade vägar](virtual-networks-udr-overview.md#user-defined). To learn how to create a hub and spoke network topology, see [Hub-spoke network topology in Azure](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke?toc=%2fazure%2fvirtual-network%2ftoc.json).

## <a name="gateways-and-on-premises-connectivity"></a>Gateways och lokala anslutningar

Each virtual network, including a peered virtual network, can have its own gateway. A virtual network can use its gateway to connect to an on-premises network. You can also configure [virtual network-to-virtual network connections](../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md?toc=%2fazure%2fvirtual-network%2ftoc.json) by using gateways, even for peered virtual networks.

When you configure both options for virtual network interconnectivity, the traffic between the virtual networks flows through the peering configuration. The traffic uses the Azure backbone.

You can also configure the gateway in the peered virtual network as a transit point to an on-premises network. In this case, the virtual network that is using a remote gateway can't have its own gateway. A virtual network has only one gateway. The gateway is either a local or remote gateway in the peered virtual network, as shown in the following diagram:

![Överföring vid VNET-peering](./media/virtual-networks-peering-overview/local-or-remote-gateway-in-peered-virual-network.png)

Both virtual network peering and global virtual network peering support gateway transit.

Gateway transit between virtual networks created through different deployment models is supported. The gateway must be in the virtual network in the Resource Manager model. Mer information om hur du använder en gateway för överföring finns i [Configure a VPN gateway for transit in a virtual network peering](../vpn-gateway/vpn-gateway-peering-gateway-transit.md?toc=%2fazure%2fvirtual-network%2ftoc.json) (Konfigurera en VPN-gateway för överföring i peer-kopplade virtuella nätverk).

When you peer virtual networks that share a single Azure ExpressRoute connection, the traffic between them goes through the peering relationship. That traffic uses the Azure backbone network. Du kan fortfarande använda lokala gateways i varje virtuellt nätverk för att ansluta till den lokala kretsen. Otherwise, you can use a shared gateway and configure transit for on-premises connectivity.

## <a name="troubleshoot"></a>Felsökning

To confirm that virtual networks are peered, you can check effective routes. Check routes for a network interface in any subnet in a virtual network. Om peer-koppling för virtuellt nätverk finns har alla undernät i det virtuella nätverket vägar med nästa hopp-typ *VNet-peering* för varje adressutrymme i varje peer-kopplat virtuellt nätverk. For more information, see [Diagnose a virtual machine routing problem](diagnose-network-routing-problem.md).

You can also troubleshoot connectivity to a virtual machine in a peered virtual network using Azure Network Watcher. A connectivity check lets you see how traffic is routed from a source virtual machine's network interface to a destination virtual machine's network interface. For more information, see [Troubleshoot connections with Azure Network Watcher using the Azure portal](../network-watcher/network-watcher-connectivity-portal.md#check-connectivity-to-a-virtual-machine).

You can also try the [Troubleshoot virtual network peering issues](virtual-network-troubleshoot-peering-issues.md).

## Constraints for peered virtual networks<a name="requirements-and-constraints"></a>

Följande begränsningar gäller endast när virtuella nätverk peerkopplas globalt:

* Resources in one virtual network can't communicate with the front-end IP address of a Basic Internal Load Balancer (ILB)  in a globally peered virtual network.
* Some services that use a Basic load balancer don't work over global virtual network peering. For more information, see [What are the constraints related to Global VNet Peering and Load Balancers?](virtual-networks-faq.md#what-are-the-constraints-related-to-global-vnet-peering-and-load-balancers).

For more information, see [Requirements and constraints](virtual-network-manage-peering.md#requirements-and-constraints). To learn more about the supported number of peerings, see [Networking limits](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits).

## <a name="permissions"></a>Behörigheter

To learn about permissions required to create a virtual network peering, see [Permissions](virtual-network-manage-peering.md#permissions).

## <a name="pricing"></a>Prissättning

There's a nominal charge for ingress and egress traffic that uses a virtual network peering connection. For more information, see [Virtual Network pricing](https://azure.microsoft.com/pricing/details/virtual-network).

Gateway Transit is a peering property that enables a virtual network to utilize a VPN/ExpressRoute gateway in a peered virtual network. Gateway transit works for both cross premises and network-to-network connectivity. Traffic to the gateway (ingress or egress) in the peered virtual network incurs virtual network peering charges. For more information, see [VPN Gateway pricing](https://azure.microsoft.com/pricing/details/vpn-gateway/) for VPN gateway charges and ExpressRoute Gateway pricing for ExpressRoute gateway charges.

>[!NOTE]
> A previous version of this document stated that virtual network peering charges would not apply with Gateway Transit. It now reflects accurate pricing per the pricing page.

## <a name="next-steps"></a>Nästa steg

* You can create a peering between two virtual networks. The networks can belong to the same subscription, different deployment models in the same subscription, or different subscriptions. Genomför en självstudiekurs för något av följande scenarier:

    |Azure-distributionsmodell             | Prenumeration  |
    |---------                          |---------|
    |Båda Resource Manager              |[Samma](tutorial-connect-virtual-networks-portal.md)|
    |                                   |[Olika](create-peering-different-subscriptions.md)|
    |En Resource Manager, en klassisk  |[Samma](create-peering-different-deployment-models.md)|
    |                                   |[Olika](create-peering-different-deployment-models-subscriptions.md)|

* To learn how to create a hub and spoke network topology, see [Hub-spoke network topology in Azure](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke?toc=%2fazure%2fvirtual-network%2ftoc.json).
* To learn about all virtual network peering settings, see [Create, change, or delete a virtual network peering](virtual-network-manage-peering.md).
* For answers to common virtual network peering and global virtual network peering questions, see [VNet Peering](virtual-networks-faq.md#vnet-peering).
