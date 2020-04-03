---
title: Virtuellt nätverk för Azure-tjänster
titlesuffix: Azure Virtual Network
description: Läs mer om fördelarna med att distribuera resurser till ett virtuellt nätverk. Resurser i virtuella nätverk kan kommunicera med varandra och lokala resurser, utan att trafiken går via Internet.
services: virtual-network
documentationcenter: na
author: malopMSFT
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/25/2017
ms.author: malop
ms.reviewer: kumud
ms.openlocfilehash: b721857f2fa76dcee144521fb34b34ce48b7bd95
ms.sourcegitcommit: bc738d2986f9d9601921baf9dded778853489b16
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/02/2020
ms.locfileid: "80616909"
---
# <a name="virtual-network-integration-for-azure-services"></a>Integrering av virtuella nätverk för Azure-tjänster

Genom att integrera Azure-tjänster i ett virtuellt Azure-nätverk kan privat åtkomst till tjänsten från virtuella datorer eller beräkningsresurser i det virtuella nätverket.
Du kan integrera Azure-tjänster i ditt virtuella nätverk med följande alternativ:
- Distribuera dedikerade instanser av tjänsten till ett virtuellt nätverk. Tjänsterna kan sedan nås privat i det virtuella nätverket och från lokala nätverk.
- Använda [Privat länk](../private-link/private-link-overview.md) för att komma åt privat en specifik instans av tjänsten från ditt virtuella nätverk och från lokala nätverk.

Du kan också komma åt tjänsten med offentliga slutpunkter genom att utöka ett virtuellt nätverk till tjänsten, via [tjänstenslutpunkter](virtual-network-service-endpoints-overview.md). Tjänstslutpunkter gör att tjänstresurser kan skyddas till det virtuella nätverket.
 
## <a name="deploy-azure-services-into-virtual-networks"></a>Distribuera Azure-tjänster till virtuella nätverk

När du distribuerar dedikerade Azure-tjänster i ett [virtuellt nätverk](virtual-networks-overview.md)kan du kommunicera med tjänstresurserna privat, via privata IP-adresser.

![Tjänster som distribueras i ett virtuellt nätverk](./media/virtual-network-for-azure-services/deploy-service-into-vnet.png)

Distribuera tjänster inom ett virtuellt nätverk ger följande funktioner:

- Resurser inom det virtuella nätverket kan kommunicera med varandra privat, via privata IP-adresser. Exempel direkt överföra data mellan HDInsight och SQL Server som körs på en virtuell dator, i det virtuella nätverket.
- Lokala resurser kan komma åt resurser i ett virtuellt nätverk med privata IP-adresser via en [VPN-gateway (Site-to-Site VPN)](../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fvirtual-network%2ftoc.json#s2smulti) eller [ExpressRoute](../expressroute/expressroute-introduction.md?toc=%2fazure%2fvirtual-network%2ftoc.json).
- Virtuella nätverk kan [peered](virtual-network-peering-overview.md) för att göra det möjligt för resurser i de virtuella nätverken att kommunicera med varandra, med hjälp av privata IP-adresser.
- Tjänstinstanser i ett virtuellt nätverk hanteras vanligtvis helt av Azure-tjänsten. Detta inkluderar övervakning av hälsotillståndet för resurserna och skalning med belastning.
- Tjänstinstanser distribueras till ett undernät i ett virtuellt nätverk. Inkommande och utgående nätverksåtkomst för undernätet måste öppnas via [nätverkssäkerhetsgrupper](security-overview.md#network-security-groups), enligt vägledning från tjänsten.
- Vissa tjänster medför också begränsningar för det undernät som de distribueras i, vilket begränsar tillämpningen av principer, vägar eller kombinerar virtuella datorer och tjänstresurser inom samma undernät. Kontrollera med varje tjänst om de specifika begränsningarna eftersom de kan ändras med tiden. Exempel på sådana tjänster är Azure NetApp-filer, dedikerad HSM, Azure Container Instances, App Service. 
- Du kan också kräva ett [delegerat undernät](virtual-network-manage-subnet.md#add-a-subnet) som en explicit identifierare som ett undernät kan vara värd för en viss tjänst. Genom att delegera får tjänster explicita behörigheter för att skapa tjänstspecifika resurser i det delegerade undernätet.
- Se ett exempel på ett REST API-svar i ett [virtuellt nätverk med ett delegerat undernät](https://docs.microsoft.com/rest/api/virtualnetwork/virtualnetworks/get#get-virtual-network-with-a-delegated-subnet). En omfattande lista över tjänster som använder den delegerade undernätsmodellen kan erhållas via [API:et för tillgängliga delegationer.](https://docs.microsoft.com/rest/api/virtualnetwork/availabledelegations/list)

### <a name="services-that-can-be-deployed-into-a-virtual-network"></a>Tjänster som kan distribueras till ett virtuellt nätverk

|Kategori|Tjänst| Dedikerad¹ undernät
|-|-|-|
| Compute | Virtuella datorer: [Linux](../virtual-machines/linux/infrastructure-networking-guidelines.md?toc=%2fazure%2fvirtual-network%2ftoc.json) eller [Windows](../virtual-machines/windows/infrastructure-networking-guidelines.md?toc=%2fazure%2fvirtual-network%2ftoc.json) <br/>[Skalningsuppsättningar för virtuella datorer](../virtual-machine-scale-sets/virtual-machine-scale-sets-mvss-existing-vnet.md?toc=%2fazure%2fvirtual-network%2ftoc.json)<br/>[Molntjänst:](https://msdn.microsoft.com/library/azure/jj156091)Endast virtuellt nätverk (klassiskt)<br/> [Azure Batch](../batch/batch-api-basics.md?toc=%2fazure%2fvirtual-network%2ftoc.json#virtual-network-vnet-and-firewall-configuration)| Inga <br/> Inga <br/> Inga <br/> Ingen²
| Nätverk | [Application Gateway - WAF](../application-gateway/application-gateway-ilb-arm.md?toc=%2fazure%2fvirtual-network%2ftoc.json)<br/>[VPN Gateway](../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fvirtual-network%2ftoc.json)<br/>[Azure Firewall](../firewall/overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) <br/>[Virtuella nätverksenheter](/windows-server/networking/sdn/manage/use-network-virtual-appliances-on-a-vn) | Ja <br/> Ja <br/> Ja <br/> Inga
|Data|[RedisCache](../azure-cache-for-redis/cache-how-to-premium-vnet.md?toc=%2fazure%2fvirtual-network%2ftoc.json)<br/>[Hanterad Azure SQL Database-instans](../sql-database/sql-database-managed-instance-connectivity-architecture.md?toc=%2fazure%2fvirtual-network%2ftoc.json)| Ja <br/> Ja <br/> 
|Analys | [Azure HDInsight](../hdinsight/hdinsight-extend-hadoop-virtual-network.md?toc=%2fazure%2fvirtual-network%2ftoc.json)<br/>[Azure Databricks](../azure-databricks/what-is-azure-databricks.md?toc=%2fazure%2fvirtual-network%2ftoc.json) |Ingen² <br/> Ingen² <br/> 
| Identitet | [Azure Active Directory Domain Services](../active-directory-domain-services/active-directory-ds-getting-started-vnet.md?toc=%2fazure%2fvirtual-network%2ftoc.json) |Inga <br/>
| Containrar | [Azure Kubernetes Service (AKS)](../aks/concepts-network.md?toc=%2fazure%2fvirtual-network%2ftoc.json)<br/>[Azure Container Instance (ACI)](https://www.aka.ms/acivnet)<br/>[Azure Container Service Engine](https://github.com/Azure/acs-engine) med Azure Virtual Network CNI [plug-in](https://github.com/Azure/acs-engine/tree/master/examples/vnet)<br/>[Azure Functions](../azure-functions/functions-networking-options.md#virtual-network-integration) |Ingen²<br/> Ja <br/><br/> Inga <br/> Ja
| Webb | [API Management](../api-management/api-management-using-with-vnet.md?toc=%2fazure%2fvirtual-network%2ftoc.json)<br/>[Web Apps](../app-service/web-sites-integrate-with-vnet.md?toc=%2fazure%2fvirtual-network%2ftoc.json)<br/>[App Service-miljön](../app-service/web-sites-integrate-with-vnet.md?toc=%2fazure%2fvirtual-network%2ftoc.json)<br/>[Azure Logic Apps](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json)<br/>|Ja <br/> Ja <br/> Ja <br/> Ja
| Värd | [Dedikerad HSM i Azure](../dedicated-hsm/index.yml?toc=%2fazure%2fvirtual-network%2ftoc.json)<br/>[Azure NetApp Files](../azure-netapp-files/azure-netapp-files-introduction.md?toc=%2fazure%2fvirtual-network%2ftoc.json)<br/>|Ja <br/> Ja <br/>
| | |

¹ "Dedikerad" innebär att endast tjänstspecifika resurser kan distribueras i det här undernätet och inte kan kombineras med kundens VM/VMSSs <br/> ² Det rekommenderas som bästa praxis att ha dessa tjänster i ett särskilt undernät, men inte ett obligatoriskt krav som ställs av tjänsten.
