---
title: Om att använda ExpressRoute med Azure Site Recovery
description: Beskriver hur du använder Azure ExpressRoute med Azure Site Recovery-tjänsten för haveriberedskap och migrering.
services: site-recovery
author: mayurigupta13
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 10/13/2019
ms.author: mayg
ms.openlocfilehash: e4525bdc6165e8e736db5f539c764d25250cb248
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79258010"
---
# <a name="azure-expressroute-with-azure-site-recovery"></a>Azure ExpressRoute med Azure Site Recovery

Microsoft Azure ExpressRoute låter dig utöka ditt lokala nätverk till Microsoft-molnet över en privat anslutning med hjälp av en anslutningsprovider. Med ExpressRoute kan du upprätta anslutningar till Microsofts molntjänster, till exempel Microsoft Azure, Office 365 och Dynamics 365.

I den här artikeln beskrivs hur du kan använda Azure ExpressRoute med Azure Site Recovery för haveriberedskap och migrering.

## <a name="expressroute-circuits"></a>ExpressRoute-kretsar

En ExpressRoute-krets representerar en logisk anslutning mellan din lokala infrastruktur och Microsofts molntjänster via en anslutningsleverantör. Du kan beställa flera ExpressRoute-kretsar. Varje krets kan vara i samma eller olika regioner och kan anslutas till dina lokaler via olika anslutningsleverantörer. Läs mer om ExpressRoute-kretsar [här](../expressroute/expressroute-circuit-peerings.md).

En ExpressRoute-krets har flera routningsdomäner associerade med sig. Läs mer om och jämför Routningsdomäner för ExpressRoute [här](../expressroute/expressroute-circuit-peerings.md#peeringcompare).

## <a name="on-premises-to-azure-replication-with-expressroute"></a>Lokalt till Azure-replikering med ExpressRoute

Azure Site Recovery möjliggör haveriberedskap och migrering till Azure för lokala [virtuella Hyper-V-datorer,](hyper-v-azure-architecture.md) [virtuella VMware-datorer](vmware-azure-architecture.md)och [fysiska servrar](physical-azure-architecture.md). För alla lokala till Azure-scenarier skickas replikeringsdata till och lagras i ett Azure Storage-konto. Under replikering betalar du inga avgifter för virtuella datorer. När du kör en redundans till Azure skapar Site Recovery automatiskt virtuella Azure IaaS-datorer.

Site Recovery replikerar data till ett Azure Storage-konto eller replikhanterad disk på Azure-regionen för målet över en offentlig slutpunkt. Om du vill använda ExpressRoute för replikeringstrafik för webbplatsåterställning kan du använda [Microsoft-peering](../expressroute/expressroute-circuit-peerings.md#microsoftpeering) eller en befintlig [offentlig peering](../expressroute/about-public-peering.md) (föråldrad för nya skapelser). Microsoft-peering är den rekommenderade routningsdomänen för replikering. Observera att replikering inte stöds via privat peering.

Kontrollera att [nätverkskraven](vmware-azure-configuration-server-requirements.md#network-requirements) för konfigurationsservern också uppfylls. Anslutning till specifika url:er krävs av Configuration Server för orkestrering av site recovery-replikering. ExpressRoute kan inte användas för den här anslutningen. 

Om du använder proxy lokalt och vill använda ExpressRoute för replikeringstrafik måste du konfigurera listan Proxy bypass på konfigurationsserver- och processservrarna. Följ stegen nedan:

- Ladda ner PsExec verktyg [härifrån](https://aka.ms/PsExec) för att komma åt System användarkontext.
- Öppna Internet Explorer i systemanvändarkontext genom att köra följande kommandorad psexec -s -i %programfiler%\Internet Explorer\iexplore.exe"
- Lägga till proxyinställningar i IE
- Lägg till Azure-lagrings-URL:en *.blob.core.windows.net

Detta säkerställer att endast replikeringstrafik flödar via ExpressRoute medan kommunikationen kan gå via proxy.

När virtuella datorer eller servrar har växlat över till ett virtuellt Azure-nätverk kan du komma åt dem med hjälp av [privat peering](../expressroute/expressroute-circuit-peerings.md#privatepeering). 

Det kombinerade scenariot finns representerat ![i följande diagram: Lokalt-till-Azure med ExpressRoute](./media/concepts-expressroute-with-site-recovery/site-recovery-with-expressroute.png)

## <a name="azure-to-azure-replication-with-expressroute"></a>Azure till Azure-replikering med ExpressRoute

Azure Site Recovery möjliggör haveriberedskap för [virtuella Azure-datorer](azure-to-azure-architecture.md). Beroende på om dina virtuella Azure-datorer använder [Azure Managed Disks](../virtual-machines/windows/managed-disks-overview.md)skickas replikeringsdata till ett Azure Storage-konto eller replikhanterad disk på azure-regionen för målet. Även om replikeringsslutpunkterna är offentliga, går replikeringstrafik för Azure VM-replikering, som standard, inte över Internet, oavsett vilken Azure-region källvirtosnätverket finns i. Du kan åsidosätta Azures standardsystemväg för 0.0.0.0/0-adressprefixet med en [anpassad väg](../virtual-network/virtual-networks-udr-overview.md#custom-routes) och vidarekoppla VM-trafik till en lokal virtuell nätverksinstallation (NVA), men den här konfigurationen rekommenderas inte för site recovery-replikering. Om du använder anpassade vägar bör du skapa en slutpunkt för [en virtuell nätverkstjänst](azure-to-azure-about-networking.md#create-network-service-endpoint-for-storage) i det virtuella nätverket för "Lagring" så att replikeringstrafiken inte lämnar Azure-gränsen.

För Azure VM-haveriberedskap krävs som standard inte ExpressRoute för replikering. När virtuella datorer har växlat över till Azure-regionen för mål kan du komma åt dem med hjälp av [privat peering](../expressroute/expressroute-circuit-peerings.md#privatepeering). Observera att dataöverföringspriser gäller oavsett hur datareplikeringssättet ska gälla i Azure-regioner.

Om du redan använder ExpressRoute för att ansluta från ditt lokala datacenter till virtuella Azure-datorer i källregionen kan du planera för att återupprätta ExpressRoute-anslutningen i målregionen redundans. Du kan använda samma ExpressRoute-krets för att ansluta till målregionen via en ny virtuell nätverksanslutning eller använda en separat ExpressRoute-krets och anslutning för haveriberedskap. De olika möjliga scenarierna beskrivs [här](azure-vm-disaster-recovery-with-expressroute.md#fail-over-azure-vms-when-using-expressroute).

Du kan replikera virtuella Azure-datorer till alla Azure-regioner inom samma geografiska kluster som beskrivs [här](../site-recovery/azure-to-azure-support-matrix.md#region-support). Om den valda Azure-regionen inte ligger inom samma geopolitiska region som källan kan du behöva aktivera ExpressRoute Premium. Mer information finns i [ExpressRoute-platser](../expressroute/expressroute-locations.md) och [ExpressRoute-priser](https://azure.microsoft.com/pricing/details/expressroute/).

## <a name="next-steps"></a>Nästa steg
- Läs mer om [ExpressRoute-kretsar](../expressroute/expressroute-circuit-peerings.md).
- Läs mer om [Routningsdomäner för ExpressRoute](../expressroute/expressroute-circuit-peerings.md#peeringcompare).
- Läs mer om [ExpressRoute-platser](../expressroute/expressroute-locations.md).
- Läs mer om haveriberedskap av [virtuella Azure-datorer med ExpressRoute](azure-vm-disaster-recovery-with-expressroute.md).
