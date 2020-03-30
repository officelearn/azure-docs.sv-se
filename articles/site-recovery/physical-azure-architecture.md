---
title: Arkitektur för återställning av fysisk serverkatastrof i Azure Site Recovery
description: Den här artikeln innehåller en översikt över komponenter och arkitektur som används vid haveriberedskap av lokala fysiska servrar till Azure med Azure Site Recovery-tjänsten.
ms.topic: conceptual
ms.date: 02/11/2020
ms.openlocfilehash: 089d981284986a2b6eb0ee7f1dbd401fc7ce4fcd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77162845"
---
# <a name="physical-server-to-azure-disaster-recovery-architecture"></a>Haveriberedskapsarkitektur för fysisk server till Azure

I den här artikeln beskrivs arkitekturen och processerna som används när du replikerar, växlar över och återställer fysiska Windows- och Linux-servrar mellan en lokal plats och Azure med hjälp av [Azure Site Recovery-tjänsten.](site-recovery-overview.md)

## <a name="architectural-components"></a>Arkitekturkomponenter

Följande tabell och grafik ger en vy på hög nivå av de komponenter som används för fysisk serverreplikering till Azure.

| **Komponent** | **Krav** | **Detaljer** |
| --- | --- | --- |
| **Azure** | En Azure-prenumeration och ett Azure-nätverk. | Replikerade data från lokala fysiska datorer lagras i Azure-hanterade diskar. Virtuella Azure-datorer skapas med replikerade data när du kör en redundans från lokalt till Azure. Virtuella Azure-datorer ansluter till det virtuella Azure-nätverket när de skapas. |
| **Bearbeta server** | Installeras som standard tillsammans med konfigurationsservern. | Fungerar som en replikeringsgateway. Den tar emot replikeringsdata, optimerar dem med cachelagring, komprimering och kryptering och skickar dem till Azure Storage.<br/><br/> Processservern installerar också mobilitetstjänsten på servrar som du vill replikera.<br/><br/> När distributionen växer kan du lägga till ytterligare, separata processservrar för att hantera större volymer replikeringstrafik. |
| **Huvudmålservern** | Installeras som standard tillsammans med konfigurationsservern. | Hanterar replikeringsdata under återställningen från Azure.<br/><br/> För stora distributioner kan du lägga till ytterligare en separat huvudmålserver för återställning efter fel. |
| **Replikerade servrar** | Mobilitetstjänsten är installerad på varje server som du replikerar. | Vi rekommenderar att du tillåter automatisk installation från processservern. Du kan också installera tjänsten manuellt eller använda en automatiserad distributionsmetod, till exempel Configuration Manager. |

**Arkitektur för fysisk till Azure**

![Komponenter](./media/physical-azure-architecture/arch-enhanced.png)

## <a name="replication-process"></a>Replikeringsprocessen

1. Du ställer in distributionen, inklusive lokala och Azure-komponenter. I valvet för återställningstjänster anger du replikeringskällan och replikeringsmålet, ställer in konfigurationsservern, skapar en replikeringsprincip och aktiverar replikering.
1. Datorer replikeras med replikeringsprincipen och en första kopia av serverdata replikeras till Azure-lagring.
1. När den första replikeringen är klar börjar replikering av deltaändringar till Azure. Spårade ändringar för en dator lagras i en fil med _.hrl-tillägget._
   - Datorer kommunicerar med konfigurationsservern på HTTPS-port 443 inkommande, för replikeringshantering.
   - Datorer skickar replikeringsdata till processservern på inkommande HTTPS-port 9443 (kan ändras).
   - Konfigurationsservern dirigerar replikeringshantering med Azure via UTGÅENDE HTTPS-port 443.
   - Processservern tar emot data från källdatorer, optimerar och krypterar den och skickar den till Azure-lagring via UTgående HTTPS-port 443.
   - Om du aktiverar konsekvens för flera virtuella datorer kommunicerar datorer i replikeringsgruppen med varandra på port 20004. Multi-VM används om du grupperar flera datorer i replikeringsgrupper som delar kraschkonsekventa och app-konsekventa återställningspunkter när de växlar över vid fel. Dessa grupper är användbara om datorer kör samma arbetsbelastning och måste vara konsekventa.
1. Trafik replikeras till offentliga Azure Storage-slutpunkter, över Internet. Du kan även använda Azure ExpressRoute [offentlig peering](../expressroute/about-public-peering.md).

   > [!NOTE]
   > Replikering stöds inte via ett VPN från plats till plats från en lokal plats eller Azure [ExpressRoute-privat peering](concepts-expressroute-with-site-recovery.md#on-premises-to-azure-replication-with-expressroute).

**Fysisk till Azure-replikeringsprocess**

![Replikeringsprocessen](./media/physical-azure-architecture/v2a-architecture-henry.png)

## <a name="failover-and-failback-process"></a>Processen för redundans och återställning efter fel

När replikeringen har konfigurerats kan du köra en borrmaskin för haveriberedskap (test redundans) för att kontrollera att allt fungerar som förväntat. Sedan kan du växla över och växla tillbaka efter behov. Tänk på följande:

- Planerad redundans stöds inte.
- Misslyckas tillbaka till en lokal virtuell dator med VMware är nödvändig. Du behöver en lokal VMware-infrastruktur, även när du replikerar lokala fysiska servrar till Azure.
- Du växlar över en enda dator, eller skapar återställningsplaner, för att växla över flera datorer tillsammans.
- När du kör en redundans skapas virtuella Azure-datorer från replikerade data i Azure-lagring.
- När den första redundansen har utlösts, begår du den för att börja komma åt arbetsbelastningen från Den virtuella Azure-datorn.
- När din primära lokala plats är tillgänglig igen, kan du återställa dit.
- Konfigurera en infrastruktur för återställning efter fel som innehåller:
  - **Tillfällig processserver i Azure:** För att växla tillbaka från Azure konfigurerar du en Virtuell Azure-dator för att fungera som en processserver för att hantera replikering från Azure. Du kan ta bort den här virtuella datorn när återställningen har slutförts.
  - **VPN-anslutning:** För att växla tillbaka behöver du en VPN-anslutning (eller Azure ExpressRoute) från Azure-nätverket till den lokala platsen.
  - **Separat huvudmålserver:** Som standard hanteras återställningen av återställningen av huvudmålservern som installerades med konfigurationsservern på den lokala virtuella datorn VMware. Om du behöver växla tillbaka stora trafikvolymer bör du ställa in en separat lokal huvudmålserver.
  - **Återställningsprincip**: Om du vill replikera tillbaka till din lokala plats behöver du en återställningsprincip. Principen skapades automatiskt när du skapade replikeringsprincipen från lokalt till Azure.
  - **VMware infrastruktur:** För att misslyckas tillbaka, behöver du en VMware infrastruktur. Du kan inte växla tillbaka till en fysisk server.
- När komponenterna är på plats sker fel tillbaka i tre steg:
  - **Steg 1:** Rotera om Azure-virtuella datorer så att de replikeras från Azure tillbaka till de lokala virtuella datorerna för VMware.
  - **Steg 2**: Kör en redundans till den lokala platsen.
  - **Steg 3:** När arbetsbelastningar har misslyckats tillbaka kan du återor kunna replikering.

**VMware-återställning från Azure**

![Återställning efter fel](./media/physical-azure-architecture/enhanced-failback.png)

## <a name="next-steps"></a>Nästa steg

Information om hur du konfigurerar haveriberedskap för fysiska servrar i Azure finns i [guiden för hur du kan styra](physical-azure-disaster-recovery.md).
