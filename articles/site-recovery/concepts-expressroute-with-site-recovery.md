---
title: Om hur du använder Azure ExpressRoute med Azure Site Recovery för katastrofåterställning och migrering | Microsoft Docs
description: Beskriver hur du använder Azure ExpressRoute med Azure Site Recovery-tjänsten för katastrofåterställning och migrering.
services: site-recovery
author: mayurigupta13
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 6/27/2019
ms.author: mayg
ms.openlocfilehash: 35fa26112a6026ab05bd59b38621de7ee802c715
ms.sourcegitcommit: ac1cfe497341429cf62eb934e87f3b5f3c79948e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/01/2019
ms.locfileid: "67491901"
---
# <a name="azure-expressroute-with-azure-site-recovery"></a>Med ExpressRoute i Azure med Azure Site Recovery

Microsoft Azure ExpressRoute låter dig utöka ditt lokala nätverk till Microsoft-molnet över en privat anslutning med hjälp av en anslutningsprovider. Med ExpressRoute kan du upprätta anslutningar till Microsofts molntjänster, till exempel Microsoft Azure, Office 365 och Dynamics 365.

Den här artikeln beskrivs hur du kan använda Azure ExpressRoute med Azure Site Recovery för katastrofåterställning och migrering.

## <a name="expressroute-circuits"></a>ExpressRoute-kretsar

En ExpressRoute-krets representerar en logisk anslutning mellan din lokala infrastruktur och Microsofts molntjänster via en anslutningsleverantör. Du kan beställa flera ExpressRoute-kretsar. Varje krets kan finnas i samma eller olika regioner och kan vara ansluten till ditt lokala nätverk via olika anslutningsleverantörer. Mer information om ExpressRoute-kretsar [här](../expressroute/expressroute-circuit-peerings.md).

En ExpressRoute-krets har flera routningsdomäner som är kopplade till den. Läs mer om att jämföra ExpressRoute-routningsdomäner [här](../expressroute/expressroute-circuit-peerings.md#peeringcompare).

## <a name="on-premises-to-azure-replication-with-expressroute"></a>Lokalt till Azure-replikering med ExpressRoute

Azure Site Recovery kan haveriberedskap och migrering till Azure för lokala [Hyper-V-datorer](hyper-v-azure-architecture.md), [virtuella VMware-datorer](vmware-azure-architecture.md), och [fysiska servrar](physical-azure-architecture.md). För alla lokala till Azure-scenarier, replikeringsdata skickas till och lagras i ett Azure Storage-konto. Du behöver inte betala några avgifter för virtuella datorer under replikeringen. När du kör en redundansväxling till Azure skapar Site Recovery automatiskt Azure IaaS-datorer.

Site Recovery replikerar data till en Azure Storage-konto eller repliken på mål-Azure-region med hanterade diskar via en offentlig slutpunkt. Du kan använda för att använda ExpressRoute för Site Recovery-replikeringstrafik [Microsoft-peering](../expressroute/expressroute-circuit-peerings.md#microsoftpeering) eller en befintlig [offentlig peering](../expressroute/expressroute-circuit-peerings.md#publicpeering) (inaktuellt för nya skapande). Microsoft-peering är den rekommenderade routningsdomän för replikering. Observera att replikering inte stöds över privat peering.

Se till att den [nätverk krav](vmware-azure-configuration-server-requirements.md#network-requirements) för konfigurationsservern också är uppfyllda. Anslutningen till specifika URL: er krävs av konfigurationsservern för dirigering av Site Recovery-replikering. ExpressRoute kan inte användas för den här anslutningen. 

Om du använder proxy på den lokala och vill använda ExpressRoute för replikeringstrafik, måste du konfigurera listan över kringgå Proxy på konfigurationsservern och Processervrar. Följ stegen nedan:

- Ladda ned PsExec-verktyg från [här](https://aka.ms/PsExec) att komma åt användaren systemkontexten.
- Öppna Internet Explorer i systemkontexten för användare genom att köra följande kommandorad psexec -s -i ”%programfiles%\Internet Explorer\iexplore.exe”
- Lägg till proxyinställningarna i Internet Explorer
- Lägg till Azure storage-URL i listan över kringgå *. blob.core.windows.net

Det säkerställer att endast replikeringstrafik flödar genom ExpressRoute medan kommunikationen kan gå igenom proxy.

När virtuella datorer eller servrar kan du växla över till en Azure-nätverk, du kan komma åt dem med hjälp av [privat peering](../expressroute/expressroute-circuit-peerings.md#privatepeering). 

Det kombinerade scenariot visas i följande diagram: ![På-lokalt till Azure med ExpressRoute](./media/concepts-expressroute-with-site-recovery/site-recovery-with-expressroute.png)

## <a name="azure-to-azure-replication-with-expressroute"></a>Replikering från Azure till Azure med ExpressRoute

Azure Site Recovery kan haveriberedskap för [Azure-datorer](azure-to-azure-architecture.md). Beroende på om din Azure virtuella datorer använda [Azure Managed Disks](../virtual-machines/windows/managed-disks-overview.md), replikeringsdata som skickas till ett Azure Storage-konto eller repliken på mål-Azure-region med hanterade diskar. Även om replikering slutpunkterna är offentliga sker replikeringstrafik för Virtuella Azure-replikering, som standard inte via Internet, oavsett vilket Azure-region det virtuella nätverket i källan finns i. Du kan åsidosätta Azures standardsystemväg för adressprefixet 0.0.0.0/0 med en [anpassad väg](../virtual-network/virtual-networks-udr-overview.md#custom-routes) och använda ett annat VM-trafik till en lokal virtuell nätverksinstallation (NVA), men den här konfigurationen rekommenderas inte för Site Recovery replikering. Om du använder anpassade vägar, bör du [skapa en tjänstslutpunkt för virtuellt nätverk](azure-to-azure-about-networking.md#create-network-service-endpoint-for-storage) i ditt virtuella nätverk för ”Storage” så att replikeringstrafiken inte lämnar Azure gränsen.

För haveriberedskap för virtuella Azure-datorer krävs som standard ExpressRoute inte för replikering. När virtuella datorer som växlar över till mål-Azure-region, du kan komma åt dem med hjälp av [privat peering](../expressroute/expressroute-circuit-peerings.md#privatepeering).

Om du redan använder ExpressRoute för att ansluta från ditt lokala datacenter till Azure virtuella datorer på källregionen, kan du planera för återupprättar ExpressRoute-anslutningen vid redundans mål-region. Du kan använda samma ExpressRoute-krets för att ansluta till målregion via en ny virtuell nätverksanslutning eller använda en separat ExpressRoute-krets och anslutningen för katastrofåterställning. Olika möjliga scenarier beskrivs [här](azure-vm-disaster-recovery-with-expressroute.md#fail-over-azure-vms-when-using-expressroute).

Du kan replikera virtuella Azure-datorer till valfri Azure-region inom samma geografiska klustret enligt anvisningarna [här](../site-recovery/azure-to-azure-support-matrix.md#region-support). Om den valda Azure-målregion inte ligger inom samma geopolitiska region som källa, kan du behöva aktivera ExpressRoute Premium. Mer information hittar du [ExpressRoute-platser](../expressroute/expressroute-locations.md#azure-regions-to-expressroute-locations-within-a-geopolitical-region) och [prisinformation för ExpressRoute](https://azure.microsoft.com/pricing/details/expressroute/).

## <a name="next-steps"></a>Nästa steg
- Läs mer om [ExpressRoute-kretsar](../expressroute/expressroute-circuit-peerings.md).
- Läs mer om [ExpressRoute-routningsdomäner](../expressroute/expressroute-circuit-peerings.md#peeringcompare).
- Läs mer om [ExpressRoute-platser](../expressroute/expressroute-locations.md).
- Mer information om haveriberedskap för [Azure-datorer med ExpressRoute](azure-vm-disaster-recovery-with-expressroute.md).
