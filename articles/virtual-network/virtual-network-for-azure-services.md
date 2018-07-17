---
title: Virtuellt nätverk för Azure-tjänster | Microsoft Docs
description: Läs mer om fördelarna med att distribuera resurser till ett virtuellt nätverk. Resurser i virtuella nätverk kan kommunicera med varandra och lokala resurser, utan att trafiken passerar Internet.
services: virtual-network
documentationcenter: na
author: jimdial
manager: timlt
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/25/2017
ms.author: jdial
ms.openlocfilehash: c92a986d06deb9f7de10f0682fe46804e6ebb6e7
ms.sourcegitcommit: 0b05bdeb22a06c91823bd1933ac65b2e0c2d6553
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/17/2018
ms.locfileid: "39069884"
---
# <a name="virtual-network-integration-for-azure-services"></a>Virtual network-integration för Azure-tjänster

Integrera Azure-tjänster till ett Azure-nätverk kan privat åtkomst från instanser av en tjänst som distribuerats i det virtuella nätverket.

Du kan integrera Azure-tjänster med ditt virtuella nätverk med följande alternativ:
- Distribuera direkt dedikerade instanser av tjänsten till ett virtuellt nätverk. Dedikerade instanser av dessa tjänster kan nås privat i det virtuella nätverket och från lokala nätverk.
- Genom att utöka ett virtuellt nätverk till tjänsten via tjänstslutpunkter. Tjänstslutpunkter kan enskilda tjänstresurser skyddas till det virtuella nätverket.
 
## <a name="deploy-azure-services-into-virtual-networks"></a>Distribuera Azure-tjänster till virtuella nätverk

Du kan kommunicera med de flesta Azure-resurser via Internet via offentliga IP-adresser. När du distribuerar Azure-tjänster i en [virtuellt nätverk](virtual-networks-overview.md), du kan kommunicera med service-resurser via privata IP-adresser, privat.

![Tjänster som distribueras i ett virtuellt nätverk](./media/virtual-network-for-azure-services/deploy-service-into-vnet.png)

Distribuera tjänster inom ett virtuellt nätverk innehåller följande funktioner:

- Resurser i det virtuella nätverket kan kommunicera med varandra privat, via privata IP-adresser. Exempel direkt dataöverföring mellan HDInsight och SQL Server som körs på en virtuell dator i det virtuella nätverket.
- Lokala resurser kan komma åt resurser i ett virtuellt nätverk med hjälp av privata IP-adresser via en [VPN för plats-till-plats (VPN-Gateway)](../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fvirtual-network%2ftoc.json#s2smulti) eller [ExpressRoute](../expressroute/expressroute-introduction.md?toc=%2fazure%2fvirtual-network%2ftoc.json).
- Virtuella nätverk kan vara [peer-kopplade](virtual-network-peering-overview.md) om du vill aktivera resurser i de virtuella nätverken kan kommunicera med varandra med privata IP-adresser.
- Instanser av tjänsten i ett virtuellt nätverk hanteras fullständigt av Azure-tjänsten, att övervaka hälsotillståndet för instanserna och ange nödvändiga skala, baserat på belastning.
- Instanser av tjänsten distribueras i ett dedikerat undernät i ett virtuellt nätverk. Inkommande och utgående nätverksåtkomst måste öppnas via [nätverkssäkerhetsgrupper](security-overview.md#network-security-groups) undernät, enligt vägledning som tillhandahålls av tjänsterna.

### <a name="services-that-can-be-deployed-into-a-virtual-network"></a>Tjänster som kan distribueras till ett virtuellt nätverk

Alla tjänster som distribueras direkt till virtuellt nätverk har särskilda krav för Routning och vilka typer av trafik som måste tillåtas till och från undernät. Mer information finns i: 
 
- Virtuella datorer: [Linux](../virtual-machines/linux/infrastructure-networking-guidelines.md?toc=%2fazure%2fvirtual-network%2ftoc.json) eller [Windows](../virtual-machines/windows/infrastructure-networking-guidelines.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [Service fabric](../service-fabric/service-fabric-patterns-networking.md?toc=%2fazure%2fvirtual-network%2ftoc.json#existingvnet)
- [VM-skalningsuppsättningar](../virtual-machine-scale-sets/virtual-machine-scale-sets-mvss-existing-vnet.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [HDInsight](../hdinsight/hdinsight-extend-hadoop-virtual-network.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [App Service Environment](../app-service/web-sites-integrate-with-vnet.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [RedisCache](../redis-cache/cache-how-to-premium-vnet.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [API Management](../api-management/api-management-using-with-vnet.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [VPN Gateway](../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [Application Gateway (internt)](../application-gateway/application-gateway-ilb-arm.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [Azure Kubernetes Service (AKS)](../aks/networking-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [Azure Container Service Engine](https://github.com/Azure/acs-engine) med Azure-virtuella nätverk-CNI [plugin-programmet](https://github.com/Azure/acs-engine/tree/master/examples/vnet)
- [Azure Active Directory Domain Services](../active-directory-domain-services/active-directory-ds-getting-started-vnet.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [Azure Batch](../batch/batch-api-basics.md?toc=%2fazure%2fvirtual-network%2ftoc.json#virtual-network-vnet-and-firewall-configuration)
- [Azure SQL Database Managed Instance](../sql-database/sql-database-managed-instance-vnet-configuration.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [Molntjänster](https://msdn.microsoft.com/library/azure/jj156091): endast virtuella nätverk (klassisk)

Du kan distribuera en [intern Azure belastningsutjämnare](../load-balancer/load-balancer-internal-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) att läsa in balansera många av resurserna i listan ovan. I vissa fall kan tjänsten automatiskt skapar och distribuerar en belastningsutjämnare när du skapar en resurs.

## <a name="service-endpoints-for-azure-services"></a>Tjänsteslutpunkter för Azure-tjänster

Vissa Azure-tjänster kan inte distribueras i virtuella nätverk. Du kan begränsa åtkomsten till vissa av tjänstresurser till endast specifika virtuella undernätverk, om du väljer, genom att aktivera en tjänstslutpunkt för virtuellt nätverk. Läs mer om [virtuella nätverksslutpunkter](virtual-network-service-endpoints-overview.md), och de tjänster som slutpunkter kan aktiveras för.

## <a name="virtual-network-integration-across-multiple-azure-services"></a>Virtual network-integration mellan flera Azure-tjänster

Du kan distribuera en Azure-tjänst till ett undernät i ett virtuellt nätverk och säker kritiska-resurser till det undernätet. Du kan till exempel distribuera HDInsight i ditt virtuella nätverk och skydda ett lagringskonto till HDInsight-undernätet.





