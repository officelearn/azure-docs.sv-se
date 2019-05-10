---
title: Virtuellt nätverk för Azure-tjänster
titlesuffix: Azure Virtual Network
description: Läs mer om fördelarna med att distribuera resurser till ett virtuellt nätverk. Resurser i virtuella nätverk kan kommunicera med varandra och lokala resurser, utan att trafiken passerar Internet.
services: virtual-network
documentationcenter: na
author: malopMSFT
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/25/2017
ms.author: malop;kumud
ms.openlocfilehash: e5481b0e262021e28a398b72b5ad022673947609
ms.sourcegitcommit: 6f043a4da4454d5cb673377bb6c4ddd0ed30672d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/08/2019
ms.locfileid: "65409511"
---
# <a name="virtual-network-integration-for-azure-services"></a>Virtual network-integration för Azure-tjänster

Integrera Azure-tjänster till ett Azure-nätverk kan privat åtkomst till tjänsten från virtuella datorer eller beräkningsresurser i det virtuella nätverket.
Du kan integrera Azure-tjänster i ditt virtuella nätverk med följande alternativ:
- Distribuera dedikerade instanser av tjänsten till ett virtuellt nätverk. Tjänsterna kan sedan användas privat i det virtuella nätverket och från lokala nätverk.
- Utöka ett virtuellt nätverk till tjänsten via tjänstslutpunkter. Tjänstslutpunkter kan enskilda tjänstresurser skyddas till det virtuella nätverket.

Du kan kombinera en eller flera av mönster som ovan för att integrera flera Azure-tjänster till ditt virtuella nätverk. Du kan till exempel distribuera HDInsight i ditt virtuella nätverk och skydda ett lagringskonto till HDInsight-undernätet via tjänstslutpunkter.
 
## <a name="deploy-azure-services-into-virtual-networks"></a>Distribuera Azure-tjänster till virtuella nätverk

När du distribuerar dedikerade Azure-tjänster i en [virtuellt nätverk](virtual-networks-overview.md), du kan kommunicera med service-resurser via privata IP-adresser, privat.

![Tjänster som distribueras i ett virtuellt nätverk](./media/virtual-network-for-azure-services/deploy-service-into-vnet.png)

Distribuera tjänster inom ett virtuellt nätverk innehåller följande funktioner:

- Resurser i det virtuella nätverket kan kommunicera med varandra privat, via privata IP-adresser. Exempel direkt dataöverföring mellan HDInsight och SQL Server som körs på en virtuell dator i det virtuella nätverket.
- Lokala resurser kan komma åt resurser i ett virtuellt nätverk med hjälp av privata IP-adresser via en [VPN för plats-till-plats (VPN-Gateway)](../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fvirtual-network%2ftoc.json#s2smulti) eller [ExpressRoute](../expressroute/expressroute-introduction.md?toc=%2fazure%2fvirtual-network%2ftoc.json).
- Virtuella nätverk kan vara [peer-kopplade](virtual-network-peering-overview.md) om du vill aktivera resurser i de virtuella nätverken kan kommunicera med varandra med privata IP-adresser.
- Instanser av tjänsten i ett virtuellt nätverk vanligtvis hanteras fullständigt av Azure-tjänsten. Detta inkluderar att övervaka hälsotillståndet för resurserna och skala med belastning.
- Instanser av tjänsten har distribuerats till ett undernät i ett virtuellt nätverk. Inkommande och utgående nätverksåtkomst för undernätet måste öppnas via [nätverkssäkerhetsgrupper](security-overview.md#network-security-groups), enligt vägledning som tillhandahålls av tjänsten.
- Vissa tjänster också begränsa det undernät som de har distribuerats, begränsa tillämpningen av principer, vägar eller kombinera virtuella datorer och service-resurser inom samma undernät. Kontrollera med varje tjänst på de specifika begränsningarna som de kan ändras över tid. Exempel på sådana tjänster är Azure NetApp filer, dedikerad HSM, Azure Container Instances, App Service. 
- Du kan också tjänster kan kräva en [delegerad undernät](virtual-network-manage-subnet.md#add-a-subnet) explicit identifierare att ett undernät kan vara värd för en viss tjänst. Genom att delegera kan få tjänster explicit behörighet att skapa tjänstspecifika resurser i delegerade undernät.
- Se ett exempel på ett REST API-svar på en [virtuellt nätverk med en delegerad undernät](https://docs.microsoft.com/rest/api/virtualnetwork/virtualnetworks/get#get_virtual_network_with_a_delegated_subnet). En omfattande lista över tjänster som använder den delegerade undernät-modellen kan hämtas den [tillgängliga delegeringar](https://docs.microsoft.com/rest/api/virtualnetwork/availabledelegations/list) API.

### <a name="services-that-can-be-deployed-into-a-virtual-network"></a>Tjänster som kan distribueras till ett virtuellt nätverk

|Category|Tjänst| Dedicated¹ undernät
|-|-|-|
| Beräkna | Virtuella datorer: [Linux](../virtual-machines/linux/infrastructure-networking-guidelines.md?toc=%2fazure%2fvirtual-network%2ftoc.json) eller [Windows](../virtual-machines/windows/infrastructure-networking-guidelines.md?toc=%2fazure%2fvirtual-network%2ftoc.json) <br/>[VM-skalningsuppsättningar](../virtual-machine-scale-sets/virtual-machine-scale-sets-mvss-existing-vnet.md?toc=%2fazure%2fvirtual-network%2ftoc.json)<br/>[Cloud Service](https://msdn.microsoft.com/library/azure/jj156091): Endast virtuella nätverk (klassisk)<br/> [Azure Batch](../batch/batch-api-basics.md?toc=%2fazure%2fvirtual-network%2ftoc.json#virtual-network-vnet-and-firewall-configuration)| Nej <br/> Nej <br/> Nej <br/> No²
| Nätverk | [Programgateway - WAF](../application-gateway/application-gateway-ilb-arm.md?toc=%2fazure%2fvirtual-network%2ftoc.json)<br/>[VPN Gateway](../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fvirtual-network%2ftoc.json)<br/>[Azure-brandväggen](../firewall/overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) <br/>[Virtuella nätverksinstallationer](/windows-server/networking/sdn/manage/use-network-virtual-appliances-on-a-vn) | Ja <br/> Ja <br/> Ja <br/> Nej
|Data|[RedisCache](../azure-cache-for-redis/cache-how-to-premium-vnet.md?toc=%2fazure%2fvirtual-network%2ftoc.json)<br/>[Azure SQL Database Managed Instance](../sql-database/sql-database-managed-instance-connectivity-architecture.md?toc=%2fazure%2fvirtual-network%2ftoc.json)| Ja <br/> Ja <br/> 
|Analys | [Azure HDInsight](../hdinsight/hdinsight-extend-hadoop-virtual-network.md?toc=%2fazure%2fvirtual-network%2ftoc.json)<br/>[Azure Databricks](../azure-databricks/what-is-azure-databricks.md?toc=%2fazure%2fvirtual-network%2ftoc.json) |No² <br/> No² <br/> 
| Identitet | [Azure Active Directory Domain Services](../active-directory-domain-services/active-directory-ds-getting-started-vnet.md?toc=%2fazure%2fvirtual-network%2ftoc.json) |Nej <br/>
| Containrar | [Azure Kubernetes Service (AKS)](../aks/concepts-network.md?toc=%2fazure%2fvirtual-network%2ftoc.json)<br/>[Azure Container-instans (ACI)](https://www.aka.ms/acivnet)<br/>[Azure Container Service Engine](https://github.com/Azure/acs-engine) med Azure virtuella nätverk CNI [plugin-programmet](https://github.com/Azure/acs-engine/tree/master/examples/vnet)|No²<br/> Ja <br/><br/> Nej
| Webb | [API Management](../api-management/api-management-using-with-vnet.md?toc=%2fazure%2fvirtual-network%2ftoc.json)<br/>[App Service Environment](../app-service/web-sites-integrate-with-vnet.md?toc=%2fazure%2fvirtual-network%2ftoc.json)<br/>[Azure Logic Apps](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json)<br/>|Ja <br/> Ja <br/> Ja
| Värdbaserad | [Azure Dedicated HSM](../dedicated-hsm/index.yml?toc=%2fazure%2fvirtual-network%2ftoc.json)<br/>[Azure NetApp-filer](../azure-netapp-files/azure-netapp-files-introduction.md?toc=%2fazure%2fvirtual-network%2ftoc.json)<br/>|Ja <br/> Ja <br/>
| | |

¹ dedikerad innebär att endast specifika tjänstresurser kan distribueras i det här undernätet och kan inte kombineras med kunden VM/VMSSs <br/> ² rekommenderas, men inte ett obligatoriskt krav som införts av tjänsten.


## <a name="service-endpoints-for-azure-services"></a>Tjänsteslutpunkter för Azure-tjänster

Vissa Azure-tjänster kan inte distribueras i virtuella nätverk. Du kan begränsa åtkomsten till vissa av tjänstresurser till endast specifika virtuella undernätverk, om du väljer, genom att aktivera en tjänstslutpunkt för virtuellt nätverk.  Läs mer om [virtuella nätverksslutpunkter](virtual-network-service-endpoints-overview.md), och de tjänster som slutpunkter kan aktiveras för.
