---
title: Med ExpressRoute i Azure med Azure Site Recovery | Microsoft Docs
description: Beskriver hur du använder Azure ExpressRoute med Azure Site Recovery för katastrofåterställning och migrering
services: site-recovery
documentationcenter: ''
author: mayanknayar
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 07/06/2018
ms.author: manayar
ms.openlocfilehash: eb28a3d165bcb6cc5e63162053029ce14b0d7f8f
ms.sourcegitcommit: 4b1083fa9c78cd03633f11abb7a69fdbc740afd1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/10/2018
ms.locfileid: "49078145"
---
# <a name="azure-expressroute-with-azure-site-recovery"></a>Med ExpressRoute i Azure med Azure Site Recovery

Microsoft Azure ExpressRoute låter dig utöka ditt lokala nätverk till Microsoft-molnet över en privat anslutning med hjälp av en anslutningsprovider. Med ExpressRoute kan du upprätta anslutningar till Microsofts molntjänster, till exempel Microsoft Azure, Office 365 och Dynamics 365.

Den här artikeln beskrivs hur du kan använda Azure ExpressRoute med Azure Site Recovery för katastrofåterställning och migrering.

## <a name="expressroute-circuits"></a>ExpressRoute-kretsar

En ExpressRoute-krets representerar en logisk anslutning mellan din lokala infrastruktur och Microsofts molntjänster via en anslutningsleverantör. Du kan beställa flera ExpressRoute-kretsar. Varje krets kan finnas i samma eller olika regioner och kan vara ansluten till ditt lokala nätverk via olika anslutningsleverantörer. Mer information om ExpressRoute-kretsar [här](../expressroute/expressroute-circuit-peerings.md).

## <a name="expressroute-routing-domains"></a>ExpressRoute-routningsdomäner

En ExpressRoute-krets har flera routningsdomäner som är kopplade till den:
-   [Azures privata peering](../expressroute/expressroute-circuit-peerings.md#azure-private-peering) – Azure-Beräkningstjänster, nämligen virtuella datorer (IaaS), och molntjänster (PaaS) som har distribuerats i ett virtuellt nätverk kan anslutas via privata peering domänen. Privat peering domänen betraktas som en betrodd utökning av ditt kärnnätverk i Microsoft Azure.
-   [Azures offentliga peering](../expressroute/expressroute-circuit-peerings.md#azure-public-peering) -tjänster som Azure Storage, SQL-databaser och webbplatser som erbjuds på offentliga IP-adresser. Privat kan du ansluta till tjänster som finns i den offentliga IP-adresser, inklusive virtuella IP-adresser för cloud services, via offentlig peering routningsdomän. Offentlig peering är inaktuellt för nya skapande och Microsoft-Peering ska användas i stället för Azure PaaS-tjänster.
-   [Microsoft-peering](../expressroute/expressroute-circuit-peerings.md#microsoft-peering) -anslutningar till Microsofts onlinetjänster (Office 365, Dynamics 365 och Azure PaaS-tjänster) är via Microsoft-peering. Microsoft-peering är den rekommendera routning domänen att ansluta till Azure PaaS-tjänster.

Läs mer om att jämföra ExpressRoute-routningsdomäner [här](../expressroute/expressroute-circuit-peerings.md#routing-domain-comparison).

## <a name="on-premises-to-azure-replication-with-expressroute"></a>Lokalt till Azure-replikering med ExpressRoute

Azure Site Recovery kan haveriberedskap och migrering till Azure för lokala [Hyper-V-datorer](hyper-v-azure-architecture.md), [virtuella VMware-datorer](vmware-azure-architecture.md), och [fysiska servrar](physical-azure-architecture.md). För alla lokala till Azure-scenarier, replikeringsdata skickas till och lagras i ett Azure Storage-konto. Du behöver inte betala några avgifter för virtuella datorer under replikeringen. När du kör en redundansväxling till Azure skapar Site Recovery automatiskt Azure IaaS-datorer.

Site Recovery replikerar data till ett Azure Storage-konto via en offentlig slutpunkt. Om du vill använda ExpressRoute för Site Recovery-replikering, kan du använda [offentlig peering](../expressroute/expressroute-circuit-peerings.md#azure-public-peering) eller [Microsoft-peering](../expressroute/expressroute-circuit-peerings.md#microsoft-peering). Microsoft-peering är den rekommenderade routningsdomän för replikering. När virtuella datorer eller servrar kan du växla över till en Azure-nätverk, du kan komma åt dem med hjälp av [privat peering](../expressroute/expressroute-circuit-peerings.md#azure-private-peering). Replikering stöds inte över privat peering.

Det kombinerade scenariot visas i följande diagram: ![på plats-till Azure med ExpressRoute](./media/concepts-expressroute-with-site-recovery/site-recovery-with-expressroute.png)

## <a name="azure-to-azure-replication-with-expressroute"></a>Replikering från Azure till Azure med ExpressRoute

Azure Site Recovery kan haveriberedskap för [Azure-datorer](azure-to-azure-architecture.md). Beroende på om din Azure virtuella datorer använda [Azure Managed Disks](../virtual-machines/windows/managed-disks-overview.md), replikeringsdata som skickas till ett Azure Storage-konto eller repliken på mål-Azure-region med hanterade diskar. Även om replikering slutpunkterna är offentliga sker replikeringstrafik för Virtuella Azure-replikering, som standard inte via Internet, oavsett vilket Azure-region det virtuella nätverket i källan finns i. Du kan åsidosätta Azures standardsystemväg för adressprefixet 0.0.0.0/0 med en [anpassad väg](../virtual-network/virtual-networks-udr-overview.md#custom-routes) och använda ett annat VM-trafik till en lokal virtuell nätverksinstallation (NVA), men den här konfigurationen rekommenderas inte för Site Recovery replikering. Om du använder anpassade vägar, bör du [skapa en tjänstslutpunkt för virtuellt nätverk](azure-to-azure-about-networking.md#create-network-service-endpoint-for-storage) i ditt virtuella nätverk för ”Storage” så att replikeringstrafiken inte lämnar Azure gränsen.

För haveriberedskap för virtuella Azure-datorer krävs som standard ExpressRoute inte för replikering. När virtuella datorer som växlar över till mål-Azure-region, du kan komma åt dem med hjälp av [privat peering](../expressroute/expressroute-circuit-peerings.md#azure-private-peering).

Om du redan använder ExpressRoute för att ansluta från ditt lokala datacenter till Azure virtuella datorer på källregionen, kan du planera för återupprättar ExpressRoute-anslutningen vid redundans mål-region. Du kan använda samma ExpressRoute-krets för att ansluta till målregion via en ny virtuell nätverksanslutning eller använda en separat ExpressRoute-krets och anslutningen för katastrofåterställning. Olika möjliga scenarier beskrivs [här](azure-vm-disaster-recovery-with-expressroute.md#fail-over-azure-vms-when-using-expressroute).

Du kan replikera virtuella Azure-datorer till valfri Azure-region inom samma geografiska klustret enligt anvisningarna [här](../site-recovery/azure-to-azure-support-matrix.md#region-support). Om den valda Azure-målregion inte ligger inom samma geopolitiska region som källa, kan du behöva aktivera ExpressRoute Premium. Mer information hittar du [ExpressRoute-platser](../expressroute/expressroute-locations.md#azure-regions-to-expressroute-locations-within-a-geopolitical-region) och [prisinformation för ExpressRoute](https://azure.microsoft.com/pricing/details/expressroute/).

## <a name="next-steps"></a>Nästa steg
- Läs mer om [ExpressRoute-kretsar](../expressroute/expressroute-circuit-peerings.md).
- Läs mer om [ExpressRoute-routningsdomäner](../expressroute/expressroute-circuit-peerings.md#expressroute-routing-domains).
- Läs mer om [ExpressRoute-platser](../expressroute/expressroute-locations.md).
- Mer information om haveriberedskap för [Azure-datorer med ExpressRoute ](azure-vm-disaster-recovery-with-expressroute.md).
