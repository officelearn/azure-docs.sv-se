---
title: Virtuellt nätverk för Azure-tjänster | Microsoft Docs
description: Läs mer om fördelarna med att distribuera resurser i ett virtuellt nätverk. Resurser i virtuella nätverk kan kommunicera med varandra och lokala resurser, utan trafik som passerar genom Internet.
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
ms.openlocfilehash: eeb5fd765a6cad10be5706162566605feaa8af4c
ms.sourcegitcommit: b7290b2cede85db346bb88fe3a5b3b316620808d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/05/2018
ms.locfileid: "34802569"
---
# <a name="virtual-network-integration-for-azure-services"></a>Virtuell nätverksintegration för Azure-tjänster

Integrera Azure-tjänster till ett Azure virtual network kan privat åtkomst från instanser av en tjänst som distribuerats i det virtuella nätverket.

Du kan integrera Azure-tjänster med det virtuella nätverket med följande alternativ:
- Distribuera direkt dedikerade instanser av tjänsten till ett virtuellt nätverk. Dedikerad instanser av dessa tjänster kan användas privat i det virtuella nätverket och från lokala nätverk.
- Genom att utvidga ett virtuellt nätverk till tjänsten via slutpunkter. Slutpunkter kan enskilda serviceresurser som ska skyddas till det virtuella nätverket.
 
## <a name="deploy-azure-services-into-virtual-networks"></a>Distribuera Azure-tjänster till virtuella nätverk

Du kan kommunicera med de flesta Azure-resurser via Internet via offentliga IP-adresser. När du distribuerar Azure-tjänster i en [virtuellt nätverk](virtual-networks-overview.md), kan du kommunicera med tjänsten resurser privat, via privata IP-adresser.

![Tjänster som distribueras i ett virtuellt nätverk](./media/virtual-network-for-azure-services/deploy-service-into-vnet.png)

Distribuera tjänster inom ett virtuellt nätverk tillhandahåller följande funktioner:

- Resurser i det virtuella nätverket kan kommunicera med varandra privat, via privata IP-adresser. Exempelvis direkt överföra data mellan HDInsight och SQL Server som körs på en virtuell dator i det virtuella nätverket.
- Lokala resurser kan komma åt resurser i ett virtuellt nätverk med privata IP-adresser via en [plats-till-plats-VPN (VPN-Gateway)](../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fvirtual-network%2ftoc.json#s2smulti) eller [ExpressRoute](../expressroute/expressroute-introduction.md?toc=%2fazure%2fvirtual-network%2ftoc.json).
- Virtuella nätverk kan vara [peerkoppla](virtual-network-peering-overview.md) om du vill aktivera resurser i de virtuella nätverken för att kommunicera med varandra med privata IP-adresser.
- Instanser av tjänsten i ett virtuellt nätverk hanteras helt av Azure-tjänsten kan övervaka hälsotillståndet för instanserna och tillhandahålla nödvändiga skala, utifrån belastningen.
- Instanser av tjänsten har distribuerats till en dedikerad undernät i ett virtuellt nätverk. Inkommande och utgående nätverksåtkomst måste öppnas via [nätverkssäkerhetsgrupper](security-overview.md#network-security-groups) undernät, per riktlinjer som tillhandahålls av tjänsterna.

### <a name="services-that-can-be-deployed-into-a-virtual-network"></a>Tjänster som kan distribueras till ett virtuellt nätverk

Varje tjänst som direkt har distribuerats till virtuella nätverk har särskilda krav för Routning och vilka typer av trafik som får in och ut från undernät. Mer information finns i: 
 
- Virtuella datorer: [Linux](../virtual-machines/linux/infrastructure-networking-guidelines.md?toc=%2fazure%2fvirtual-network%2ftoc.json) eller [Windows](../virtual-machines/windows/infrastructure-networking-guidelines.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [Service fabric](../service-fabric/service-fabric-patterns-networking.md?toc=%2fazure%2fvirtual-network%2ftoc.json#existingvnet)
- [Skaluppsättningar för den virtuella datorn](../virtual-machine-scale-sets/virtual-machine-scale-sets-mvss-existing-vnet.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [HDInsight](../hdinsight/hdinsight-extend-hadoop-virtual-network.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [App Service Environment](../app-service/web-sites-integrate-with-vnet.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [RedisCache](../redis-cache/cache-how-to-premium-vnet.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [API Management](../api-management/api-management-using-with-vnet.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- 
  [VPN Gateway](../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [Programgateway (internt)](../application-gateway/application-gateway-ilb-arm.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [Azure Kubernetes-tjänsten (AKS)](../aks/networking-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [Azure Container Service Engine](https://github.com/Azure/acs-engine) med Azure Virtual Network CNI [plugin-program](https://github.com/Azure/acs-engine/tree/master/examples/vnet)
- [Azure Active Directory Domain Services](../active-directory-domain-services/active-directory-ds-getting-started-vnet.md?toc=%2fazure%2fvirtual-network%2ftoc.json): endast virtuella nätverk (klassisk)
- [Azure Batch](../batch/batch-api-basics.md?toc=%2fazure%2fvirtual-network%2ftoc.json#virtual-network-vnet-and-firewall-configuration)
- [Molntjänster](https://msdn.microsoft.com/library/azure/jj156091): endast virtuella nätverk (klassisk)

Du kan distribuera en [intern Azure belastningsutjämnare](../load-balancer/load-balancer-internal-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) att läsa in balansera många resurser i listan ovan. I vissa fall kan tjänsten automatiskt skapar och distribuerar en belastningsutjämnare när du skapar en resurs.

## <a name="service-endpoints-for-azure-services"></a>Slutpunkter för Azure-tjänster

Vissa Azure-tjänster kan distribueras i virtuella nätverk. Du kan begränsa åtkomsten till tjänsten resurser till specifika virtuellt undernät, om du väljer genom att aktivera en tjänstslutpunkt för virtuellt nätverk. Lär dig mer om [slutpunkter för virtuellt nätverk](virtual-network-service-endpoints-overview.md), och de tjänster som slutpunkter kan aktiveras för.

## <a name="virtual-network-integration-across-multiple-azure-services"></a>Virtuell nätverksintegration över flera Azure-tjänster

Du kan distribuera en Azure-tjänst till ett undernät i ett virtuellt nätverk och säker kritisk tjänst resurser till det undernätet. Du kan till exempel distribuera HDInsight till det virtuella nätverket och skydda ett lagringskonto till HDInsight-undernätet.





