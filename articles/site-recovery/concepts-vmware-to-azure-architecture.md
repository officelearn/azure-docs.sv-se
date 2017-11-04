---
title: "Granska arkitektur för VMware-replikering till Azure | Microsoft Docs"
description: "Den här artikeln innehåller en översikt över komponenter och arkitektur som används för att replikera lokala virtuella VMware-datorer till Azure med Azure Site Recovery-tjänsten"
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: d03d2dd3-2455-4ca8-a942-a342030ee6ce
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/10/2017
ms.author: raynew
ms.openlocfilehash: ac1151d15a88650f5845cb879cd210e9f7cba0fd
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="vmware-to-azure-replication-architecture"></a>VMware till Azure replikeringsarkitektur

Den här artikeln beskriver arkitektur och processer som används när du replikera växla över och återställa virtuella VMware-datorer (VM) mellan en lokal VMware-platsen och Azure, med hjälp av den [Azure Site Recovery](site-recovery-overview.md) service.


## <a name="architectural-components"></a>Arkitekturkomponenter

Följande tabell och bild ger en övergripande bild av de komponenter som används för VMware-replikering till Azure.  

**Komponent** | **Krav** | **Detaljer**
--- | --- | ---
**Azure** | Azure-prenumeration, Azure storage-konto och Azure-nätverk. | Replikerade data från lokala virtuella datorer lagras i lagringskontot. Virtuella Azure-datorer skapas med replikerade data när du kör en misslyckas från lokal till Azure. Virtuella Azure-datorer ansluter till det virtuella Azure-nätverket när de skapas.
**Konfigurationsserver** | En enda lokal VMware VM distribueras för att köra alla lokal Site Recovery-komponenter. Den virtuella datorn körs konfigurationsservern, processervern och huvudmålservern. | Konfigurationsservern samordnar kommunikationen mellan den lokala miljön och Azure och hanterar datareplikering.
 **Processerver**:  | Installeras som standard tillsammans med konfigurationsservern. | Fungerar som en replikeringsgateway. Den tar emot replikeringsdata, optimerar dem med cachelagring, komprimering och kryptering och skickar dem till Azure Storage.<br/><br/> Processervern installeras också mobilitetstjänsten på virtuella datorer du vill replikera, och utför automatisk identifiering av virtuella datorer på lokal VMware-servrar.<br/><br/> Eftersom distributionen växer kan du lägga till ytterligare, separat servrar för att hantera större mängder replikeringstrafik.
 **Huvudmålservern** | Installeras som standard tillsammans med konfigurationsservern. | Hanterar replikeringsdata vid återställning efter fel från Azure.<br/><br/> Du kan lägga till en ytterligare, separat huvudmålservern för återställning efter fel för stora distributioner.
**VMware-servrar** | Virtuella VMware-datorer finns på lokala vSphere ESXi servrar. Vi rekommenderar en vCenter-server för att hantera värdar. | Under distributionen av Site Recovery kan du lägga till VMware-servrar till Recovery Services-valvet.
**Replikerade datorer** | Mobilitetstjänsten installeras på varje VMware VM som du vill replikera. | Vi rekommenderar att du tillåta automatisk installation från processervern. Du kan också installera tjänsten manuellt eller använda en automatisk distributionsmetod som System Center Configuration Manager. 

**Arkitektur för VMware till Azure**

![Komponenter](./media/concepts-vmware-to-azure-architecture/arch-enhanced.png)

## <a name="replication-process"></a>Replikeringsprocessen

1. Du kan konfigurera distribution, inklusive lokalt och Azure-komponenter. I Recovery Services-valvet anger du replikeringskälla och mål, ställa in konfigurationsservern och skapa en replikeringsprincip och aktivera replikering.
2. Replikera datorer enligt replikeringsprinciper och en inledande kopia av VM-data replikeras till Azure-lagring.
3. När replikeringen har slutförts börjar replikering av deltaändringar till Azure. Spårade ändringar för en dator lagras i en .hrl-fil.
    - Datorer kommunicerar med konfigurationsservern på port 443 för HTTPS inkommande för replikeringshantering av.
    - Datorer skicka replikeringsdata till processervern på port HTTPS 9443 inkommande (kan ändras).
    - Konfigurationsservern samordnar replikeringshantering med Azure via port HTTPS 443 utgående.
    - Processervern tar emot data från källdatorer, optimerar och krypterar den och skickar den till Azure Storage över port 443 utgående.
    - Om du aktiverar konsekvens för flera kommunicerar datorer i replikeringsgruppen med varandra via port 20004. Multi-VM används om du grupperar flera datorer i replikeringsgrupper som delar kraschkonsekventa och app-konsekventa återställningspunkter när de växlar över vid fel. Detta är användbart om datorerna kör samma arbetsbelastning och måste överensstämma.
4. Trafik replikeras till offentliga Azure Storage-slutpunkter, över Internet. Du kan även använda Azure ExpressRoute [offentlig peering](../expressroute/expressroute-circuit-peerings.md#azure-public-peering). Replikering av trafik via en plats-till-plats-VPN från en lokal plats till Azure stöds inte.


**VMware till Azure replikeringen**

![Replikeringsprocessen](./media/concepts-vmware-to-azure-architecture/v2a-architecture-henry.png)

## <a name="failover-and-failback-process"></a>Processen för redundans och återställning efter fel

När replikeringen har ställts in och du har kört en katastrof återställningsgranskning (testa redundans) för att kontrollera att allt fungerar som förväntat, kan du köra redundans och återställning som du behöver.

1. Du kan växla över en enskild dator eller skapa återställningsplaner för att växla över flera virtuella datorer.
2. När du kör en växling vid fel, skapas virtuella Azure-datorer från replikerade data i Azure-lagring.
3. Efter utlöser inledande redundans kan genomföra den för att starta åt arbetsbelastningen från den virtuella Azure-datorn.

När din primära lokala plats är tillgänglig igen, kan du återställa dit.
1. Du måste ställa in en infrastruktur för återställning efter fel, inklusive:
    - **Tillfällig processerver i Azure**: Om du vill växla tillbaka från Azure du ställer in en Azure VM att fungera som en processerver för att hantera replikering från Azure. Du kan ta bort den här virtuella datorn när återställningen är klar.
    - **VPN-anslutning**: för att växla tillbaka du behöver en VPN-anslutning (eller Azure ExpressRoute) från Azure-nätverket till den lokala platsen.
    - **Separata huvudmålservern**: som standard som installerades med konfigurationsservern på lokal VMware VM, huvudmålservern hanterar återställning efter fel. Men om du behöver växla tillbaka stora volymer trafik bör du ställa in en separat lokal huvudmålserver för detta ändamål.
    - **Återställningsprincip**: Om du vill replikera tillbaka till din lokala plats behöver du en återställningsprincip. Detta skapas automatiskt när du skapade din replikeringsprincip från lokal till Azure.
2. När komponenterna är på plats så utförs återställning efter fel i tre steg:
    - Steg 1: Skapa nytt virtuella Azure-datorer så att de replikera från Azure till lokala VMwares virtuella datorer.
    - Steg 2: Kör en redundansväxling till den lokala platsen.
    - Steg 3: När arbetsbelastningar misslyckas igen kan du återaktivera replikering för de lokala virtuella datorerna.

**VMware återställning från Azure**

![Återställning efter fel](./media/concepts-vmware-to-azure-architecture/enhanced-failback.png)


## <a name="next-steps"></a>Nästa steg

Granska supportmatrisen Följ guiden för att aktivera VMware till Azure-replikering.
Kör en redundans och återställning efter fel.