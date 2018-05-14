---
title: Azure ExpressRoute med Azure Site Recovery | Microsoft Docs
description: Beskriver hur du använder Azure ExpressRoute med Azure Site Recovery för migrering och katastrofåterställning
services: site-recovery
documentationcenter: ''
author: mayanknayar
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 05/11/2018
ms.author: manayar
ms.openlocfilehash: 7cc4c84ebae7ade4169f8d85a2d5cc11f1df6f87
ms.sourcegitcommit: c52123364e2ba086722bc860f2972642115316ef
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/11/2018
---
# <a name="azure-expressroute-with-azure-site-recovery"></a>Azure ExpressRoute med Azure Site Recovery

Microsoft Azure ExpressRoute låter dig utöka ditt lokala nätverk till Microsoft-molnet över en privat anslutning med hjälp av en anslutningsprovider. Med ExpressRoute kan du upprätta anslutningar till Microsofts molntjänster, till exempel Microsoft Azure, Office 365 och Dynamics 365.

Den här artikeln beskriver hur du kan använda Azure ExpressRoute med Azure Site Recovery för migrering och katastrofåterställning.

## <a name="expressroute-circuits"></a>ExpressRoute-kretsar

En ExpressRoute-krets representerar en logisk anslutning mellan din lokala infrastruktur och dina Microsoft-molntjänster med en anslutning. Du kan ordna flera ExpressRoute-kretsar. Varje krets kan vara i samma eller olika regioner och kan vara ansluten till din lokala via olika anslutningen leverantörer. Mer information om ExpressRoute-kretsar [här](../expressroute/expressroute-circuit-peerings.md).

## <a name="expressroute-routing-domains"></a>ExpressRoute-routningsdomäner

En ExpressRoute-krets har flera routningsdomäner som är kopplade till den:
-   [Privat Azure-peering](../expressroute/expressroute-circuit-peerings.md#azure-private-peering) – Azure compute tjänster, nämligen virtuella datorer (IaaS), och molntjänster (PaaS) som har distribuerats i ett virtuellt nätverk kan vara ansluten till domänen för privat peering. Privat peering domänen betraktas som en betrodd förlängning av Kärnnätverket i Microsoft Azure.
-   [Offentlig Azure-peering](../expressroute/expressroute-circuit-peerings.md#azure-public-peering) -tjänster som Azure Storage, SQL-databaser och webbplatser erbjuds på offentliga IP-adresser. Privat kan du ansluta till tjänster som ligger på den offentliga IP-adresser, inklusive VIP av dina molntjänster genom offentlig peering routningsdomän. Offentlig peering har ersatts för nya skapande och Microsoft-Peering ska användas i stället för Azure PaaS-tjänster.
-   [Microsoft-peering](../expressroute/expressroute-circuit-peerings.md#microsoft-peering) -anslutning till Microsoft online services (Office 365, Dynamics 365 och Azure PaaS services) är via Microsoft-peering. Microsoft-peering är den rekommenderade routning domänen att ansluta till Azure PaaS-tjänster.

Lär dig mer om och jämför ExpressRoute routningsdomänerna [här](../expressroute/expressroute-circuit-peerings.md#routing-domain-comparison).

## <a name="on-premises-to-azure-replication-with-expressroute"></a>Lokal Azure replikering med ExpressRoute

Azure Site Recovery kan katastrofåterställning och migreringen till Azure för lokalt [Hyper-V-datorer](hyper-v-azure-architecture.md), [virtuella VMware-datorer](vmware-azure-architecture.md), och [fysiska servrar](physical-azure-architecture.md). För alla lokala scenarier, Azure, replikeringsdata skickas till och lagras i ett Azure Storage-konto. Vid replikering betala inte du alla avgifter för virtuell dator. När du kör en redundansväxling till Azure skapar Site Recovery automatiskt virtuella Azure IaaS-datorer.

Site Recovery replikerar data till ett Azure Storage-konto via en offentlig slutpunkt. Om du vill använda ExpressRoute för Site Recovery replikering, kan du använda [offentlig peering](../expressroute/expressroute-circuit-peerings.md#azure-public-peering) eller [Microsoft-peering](../expressroute/expressroute-circuit-peerings.md#microsoft-peering). Microsoft-peering är den rekommenderade routningsdomän för replikering. När virtuella datorer eller servrar kan du växla över till Azure-nätverk, du kan komma åt dem med hjälp av [privat peering](../expressroute/expressroute-circuit-peerings.md#azure-private-peering). Replikering stöds inte över privat peering.

Det kombinerade scenariot representeras i följande diagram: ![på-lokal-till-Azure med ExpressRoute](./media/concepts-expressroute-with-site-recovery/site-recovery-with-expressroute.png)

## <a name="azure-to-azure-replication-with-expressroute"></a>Replikering i Azure till Azure med ExpressRoute

Azure Site Recovery gör det möjligt för katastrofåterställning av [virtuella Azure-datorer](azure-to-azure-architecture.md). Beroende på om din Azure virtuella datorer används [Azure hanterade diskar](../virtual-machines/windows/managed-disks-overview.md), replikeringsdata som skickas till ett Azure Storage-konto eller replik hanterade disken på målet Azure-region. Även om replikering slutpunkter är offentliga replikeringstrafik för Virtuella Azure-replikering som standard inte sker via Internet, oavsett vilket Azure-region som det virtuella källnätverket finns i. Du kan åsidosätta Azures system standardvägen för adressprefixet 0.0.0.0/0 med en [anpassade väg](../virtual-network/virtual-networks-udr-overview.md#custom-routes) och minska VM-trafiken till en lokal virtuell nätverksenhet (NVA), men den här konfigurationen rekommenderas inte för Site Recovery replikering. Om du använder anpassade vägar, bör du [skapa ett virtuellt nätverk tjänstslutpunkten](azure-to-azure-about-networking.md#create-network-service-endpoint-for-storage) i ditt virtuella nätverk för ”lagring” så att replikeringstrafiken inte lämnar Azure gränsen.

För Virtuella Azure-katastrofåterställning krävs som standard ExpressRoute inte för replikering. När virtuella datorer växla över till målet Azure-region, du kan komma åt dem med hjälp av [privat peering](../expressroute/expressroute-circuit-peerings.md#azure-private-peering).

Om du redan använder ExpressRoute för att ansluta från ditt lokala datacenter till virtuella Azure-datorer på den käll-regionen, kan du planera för återställning av ExpressRoute-anslutning på målregionen växling vid fel. Du kan använda samma ExpressRoute-krets att ansluta till målregionen via en virtuell nätverksanslutning eller använda en separat ExpressRoute-krets och anslutningen för katastrofåterställning. Olika möjliga scenarier beskrivs [här](azure-vm-disaster-recovery-with-expressroute.md#failover-models-with-expressroute).

Du kan replikera virtuella Azure-datorer till någon Azure-region inom samma geografiska kluster som detaljerad [här](../site-recovery/azure-to-azure-support-matrix.md#region-support). Om det valda målet Azure-regionen inte är inom samma geopolitiska region som källa, kan du behöva aktivera ExpressRoute Premium. Mer information [ExpressRoute platser](../expressroute/expressroute-locations.md#azure-regions-to-expressroute-locations-within-a-geopolitical-region) och [ExpressRoute priser](https://azure.microsoft.com/pricing/details/expressroute/).

## <a name="next-steps"></a>Nästa steg
- Lär dig mer om [ExpressRoute-kretsar](../expressroute/expressroute-circuit-peerings.md).
- Lär dig mer om [ExpressRoute routningsdomänerna](../expressroute/expressroute-circuit-peerings.md#expressroute-routing-domains).
- Lär dig mer om [ExpressRoute platser](../expressroute/expressroute-locations.md).
- Mer information om återställning av [virtuella Azure-datorer med ExpressRoute ](azure-vm-disaster-recovery-with-expressroute.md).
