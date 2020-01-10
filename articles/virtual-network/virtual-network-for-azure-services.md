---
title: Virtuellt nätverk för Azure-tjänster
titlesuffix: Azure Virtual Network
description: Lär dig mer om fördelarna med att distribuera resurser i ett virtuellt nätverk. Resurser i virtuella nätverk kan kommunicera med varandra, och lokala resurser, utan trafik som passerar Internet.
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
ms.openlocfilehash: d343f09320a95265ac5e84c80941f454a268ffd4
ms.sourcegitcommit: aee08b05a4e72b192a6e62a8fb581a7b08b9c02a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/09/2020
ms.locfileid: "75772184"
---
# <a name="virtual-network-integration-for-azure-services"></a>Integrering av virtuella nätverk för Azure-tjänster

Integrering av Azure-tjänster på ett virtuellt Azure-nätverk möjliggör privat åtkomst till tjänsten från virtuella datorer eller beräknings resurser i det virtuella nätverket.
Du kan integrera Azure-tjänster i ditt virtuella nätverk med följande alternativ:
- Distribuera dedikerade instanser av tjänsten till ett virtuellt nätverk. Tjänsterna kan sedan nås privat i det virtuella nätverket och från lokala nätverk.
- Använd [privat länk](../private-link/private-link-overview.md) för att få åtkomst till privat en speciell instans av tjänsten från det virtuella nätverket och från lokala nätverk.

Du kan också komma åt tjänsten med offentliga slut punkter genom att utöka ett virtuellt nätverk till tjänsten via [tjänstens slut punkter](virtual-network-service-endpoints-overview.md). Med tjänst slut punkter kan tjänst resurser skyddas till det virtuella nätverket.
 
## <a name="deploy-azure-services-into-virtual-networks"></a>Distribuera Azure-tjänster till virtuella nätverk

När du distribuerar dedikerade Azure-tjänster i ett [virtuellt nätverk](virtual-networks-overview.md)kan du kommunicera med tjänst resurserna privat via privata IP-adresser.

![Tjänster som distribueras i ett virtuellt nätverk](./media/virtual-network-for-azure-services/deploy-service-into-vnet.png)

Det finns följande funktioner för att distribuera tjänster i ett virtuellt nätverk:

- Resurser i det virtuella nätverket kan kommunicera med varandra privat via privata IP-adresser. Exempel: direkt överföring av data mellan HDInsight och SQL Server som körs på en virtuell dator i det virtuella nätverket.
- Lokala resurser kan komma åt resurser i ett virtuellt nätverk med hjälp av privata IP-adresser via en [plats-till-plats-VPN (VPN gateway)](../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fvirtual-network%2ftoc.json#s2smulti) eller [ExpressRoute](../expressroute/expressroute-introduction.md?toc=%2fazure%2fvirtual-network%2ftoc.json).
- Virtuella nätverk kan [peer](virtual-network-peering-overview.md) -kopplas för att göra det möjligt för resurser i de virtuella nätverken att kommunicera med varandra, med hjälp av privata IP-adresser.
- Tjänst instanser i ett virtuellt nätverk hanteras vanligt vis helt av Azure-tjänsten. Detta omfattar övervakning av hälso tillståndet för resurser och skalning med belastningen.
- Tjänst instanser distribueras till ett undernät i ett virtuellt nätverk. Inkommande och utgående nätverks åtkomst för under nätet måste öppnas via [nätverks säkerhets grupper](security-overview.md#network-security-groups), enligt de rikt linjer som tillhandahålls av tjänsten.
- Vissa tjänster har också begränsningar för det undernät som de distribueras i, vilket begränsar tillämpningen av principer, vägar eller kombinerar virtuella datorer och tjänst resurser i samma undernät. Sök efter varje tjänst enligt de begränsningar som de kan ändra med tiden. Exempel på sådana tjänster är Azure NetApp Files, dedikerad HSM, Azure Container Instances App Service. 
- Om du vill kan tjänsterna kräva ett [delegerat undernät](virtual-network-manage-subnet.md#add-a-subnet) som en explicit identifierare som ett undernät kan vara värd för en viss tjänst. Genom att delegera, får tjänster explicita behörigheter att skapa tjänstespecifika resurser i det delegerade under nätet.
- Se ett exempel på ett REST API-svar i ett [virtuellt nätverk med ett delegerat undernät](https://docs.microsoft.com/rest/api/virtualnetwork/virtualnetworks/get#get-virtual-network-with-a-delegated-subnet). En omfattande lista över tjänster som använder den delegerade under nät modellen kan hämtas via [tillgängliga Delegerings](https://docs.microsoft.com/rest/api/virtualnetwork/availabledelegations/list) -API: er.

### <a name="services-that-can-be-deployed-into-a-virtual-network"></a>Tjänster som kan distribueras till ett virtuellt nätverk

|Kategori|Tjänst| Dedikerat ¹ undernät
|-|-|-|
| Databearbetning | Virtuella datorer: [Linux](../virtual-machines/linux/infrastructure-networking-guidelines.md?toc=%2fazure%2fvirtual-network%2ftoc.json) eller [Windows](../virtual-machines/windows/infrastructure-networking-guidelines.md?toc=%2fazure%2fvirtual-network%2ftoc.json) <br/>[Skaluppsättningar för virtuella datorer](../virtual-machine-scale-sets/virtual-machine-scale-sets-mvss-existing-vnet.md?toc=%2fazure%2fvirtual-network%2ftoc.json)<br/>[Moln tjänst](https://msdn.microsoft.com/library/azure/jj156091): endast virtuellt nätverk (klassiskt)<br/> [Azure Batch](../batch/batch-api-basics.md?toc=%2fazure%2fvirtual-network%2ftoc.json#virtual-network-vnet-and-firewall-configuration)| Inga <br/> Inga <br/> Inga <br/> Nr ²
| Nätverk | [Application Gateway-WAF](../application-gateway/application-gateway-ilb-arm.md?toc=%2fazure%2fvirtual-network%2ftoc.json)<br/>[VPN Gateway](../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fvirtual-network%2ftoc.json)<br/>[Azure Firewall](../firewall/overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) <br/>[Virtuella nätverks enheter](/windows-server/networking/sdn/manage/use-network-virtual-appliances-on-a-vn) | Ja <br/> Ja <br/> Ja <br/> Inga
|Data|[RedisCache](../azure-cache-for-redis/cache-how-to-premium-vnet.md?toc=%2fazure%2fvirtual-network%2ftoc.json)<br/>[Azure SQL Database Managed Instance](../sql-database/sql-database-managed-instance-connectivity-architecture.md?toc=%2fazure%2fvirtual-network%2ftoc.json)| Ja <br/> Ja <br/> 
|Analyser | [Azure HDInsight](../hdinsight/hdinsight-extend-hadoop-virtual-network.md?toc=%2fazure%2fvirtual-network%2ftoc.json)<br/>[Azure Databricks](../azure-databricks/what-is-azure-databricks.md?toc=%2fazure%2fvirtual-network%2ftoc.json) |Nr ² <br/> Nr ² <br/> 
| Identitet | [Azure Active Directory Domain Services](../active-directory-domain-services/active-directory-ds-getting-started-vnet.md?toc=%2fazure%2fvirtual-network%2ftoc.json) |Inga <br/>
| Containers | [Azure Kubernetes Service (AKS)](../aks/concepts-network.md?toc=%2fazure%2fvirtual-network%2ftoc.json)<br/>[Azure Container Instance (ACI)](https://www.aka.ms/acivnet)<br/>[Azure Container Service motor](https://github.com/Azure/acs-engine) med [plugin-programmet](https://github.com/Azure/acs-engine/tree/master/examples/vnet) Azure Virtual Network cni|Nr ²<br/> Ja <br/><br/> Inga
| Webb | [API Management](../api-management/api-management-using-with-vnet.md?toc=%2fazure%2fvirtual-network%2ftoc.json)<br/>[App Service Environment](../app-service/web-sites-integrate-with-vnet.md?toc=%2fazure%2fvirtual-network%2ftoc.json)<br/>[Azure Logic Apps](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json)<br/>|Ja <br/> Ja <br/> Ja
| Nås | [Azure Dedicated HSM](../dedicated-hsm/index.yml?toc=%2fazure%2fvirtual-network%2ftoc.json)<br/>[Azure NetApp Files](../azure-netapp-files/azure-netapp-files-introduction.md?toc=%2fazure%2fvirtual-network%2ftoc.json)<br/>|Ja <br/> Ja <br/>
| | |

¹ "dedikerad" innebär att endast tjänstespecifika resurser kan distribueras i det här under nätet och inte kan kombineras med Customer VM/VMSSs <br/> ² rekommenderas, men inte ett obligatoriskt krav som angetts av tjänsten.
