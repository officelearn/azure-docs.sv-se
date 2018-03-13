---
title: Fysisk server till Azure replikeringsarkitektur i Azure Site Recovery | Microsoft Docs
description: "Den här artikeln innehåller en översikt över komponenter och arkitektur som används vid replikering av lokala fysiska servrar till Azure med Azure Site Recovery-tjänsten"
author: rayne-wiselman
ms.service: site-recovery
ms.topic: article
ms.date: 03/09/2018
ms.author: raynew
ms.openlocfilehash: a8af2ee4a32925603d24aee2403ab504a0ca05a8
ms.sourcegitcommit: a0be2dc237d30b7f79914e8adfb85299571374ec
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/12/2018
---
# <a name="physical-server-to-azure-replication-architecture"></a>Fysisk server till Azure replikeringsarkitektur

Den här artikeln beskriver arkitektur och processer som används när du replikera växla över och återställa fysiska Windows- och Linux-servrar mellan en lokal plats och Azure, med hjälp av den [Azure Site Recovery](site-recovery-overview.md) service.


## <a name="architectural-components"></a>Arkitekturkomponenter

Följande tabell och bild ger en övergripande bild av de komponenter som används för fysisk serverreplikering till Azure.  

**Komponent** | **Krav** | **Detaljer**
--- | --- | ---
**Azure** | Azure-prenumeration, Azure storage-konto och Azure-nätverk. | Replikerade data från lokala virtuella datorer lagras i lagringskontot. Virtuella Azure-datorer skapas med replikerade data när du kör en misslyckas från lokal till Azure. Virtuella Azure-datorer ansluter till det virtuella Azure-nätverket när de skapas.
**Konfigurationsserver** | En enda lokal fysisk dator eller VMware VM distribueras för att köra alla lokal Site Recovery-komponenter. Den virtuella datorn körs konfigurationsservern, processervern och huvudmålservern. | Konfigurationsservern samordnar kommunikationen mellan den lokala miljön och Azure och hanterar datareplikering.
 **Processerver**:  | Installeras som standard tillsammans med konfigurationsservern. | Fungerar som en replikeringsgateway. Den tar emot replikeringsdata, optimerar dem med cachelagring, komprimering och kryptering och skickar dem till Azure Storage.<br/><br/> Processervern installerar också mobilitetstjänsten på servrar som du vill replikera.<br/><br/> Eftersom distributionen växer kan du lägga till ytterligare, separat servrar för att hantera större mängder replikeringstrafik.
 **Huvudmålservern** | Installeras som standard tillsammans med konfigurationsservern. | Hanterar replikeringsdata vid återställning efter fel från Azure.<br/><br/> Du kan lägga till en ytterligare, separat huvudmålservern för återställning efter fel för stora distributioner.
**Replikerade servrar** | Mobilitetstjänsten installeras på varje server som du vill replikera. | Vi rekommenderar att du tillåta automatisk installation från processervern. Du kan också installera tjänsten manuellt eller använda en automatisk distributionsmetod som System Center Configuration Manager.

**Fysisk till Azure-arkitektur**

![Komponenter](./media/physical-azure-architecture/arch-enhanced.png)

## <a name="replication-process"></a>Replikeringsprocessen

1. Du kan konfigurera distribution, inklusive lokalt och Azure-komponenter. I Recovery Services-valvet anger du replikeringskälla och mål, ställa in konfigurationsservern och skapa en replikeringsprincip och aktivera replikering.
2. Replikera datorer enligt replikeringsprinciper och en inledande kopia av server-data replikeras till Azure-lagring.
3. När replikeringen har slutförts börjar replikering av deltaändringar till Azure. Spårade ändringar för en dator lagras i en .hrl-fil.
    - Datorer kommunicerar med konfigurationsservern på port 443 för HTTPS inkommande för replikeringshantering av.
    - Datorer skicka replikeringsdata till processervern på port HTTPS 9443 inkommande (kan ändras).
    - Konfigurationsservern samordnar replikeringshantering med Azure via port HTTPS 443 utgående.
    - Processervern tar emot data från källdatorer, optimerar och krypterar den och skickar den till Azure Storage över port 443 utgående.
    - Om du aktiverar konsekvens för flera virtuella datorer kommunicerar datorer i replikeringsgruppen med varandra på port 20004. Multi-VM används om du grupperar flera datorer i replikeringsgrupper som delar kraschkonsekventa och app-konsekventa återställningspunkter när de växlar över vid fel. Detta är användbart om datorerna kör samma arbetsbelastning och måste överensstämma.
4. Trafik replikeras till offentliga Azure Storage-slutpunkter, över Internet. Du kan även använda Azure ExpressRoute [offentlig peering](../expressroute/expressroute-circuit-peerings.md#azure-public-peering). Replikering av trafik via en plats-till-plats-VPN från en lokal plats till Azure stöds inte.


**Fysisk till Azure replikeringen**

![Replikeringsprocessen](./media/physical-azure-architecture/v2a-architecture-henry.png)

## <a name="failover-and-failback-process"></a>Processen för redundans och återställning efter fel

När replikeringen har ställts in och du har kört en katastrof återställningsgranskning (testa redundans) för att kontrollera att allt fungerar som förväntat, kan du köra redundans och återställning som du behöver. Tänk på följande:

- Planerad redundans stöds inte.
- Du måste växla tillbaka till en lokal VMware VM. Det innebär att du behöver en lokal VMware-infrastruktur, även när du replikera lokala fysiska servrar till Azure.
- Du växlar över en enskild dator eller skapa återställningsplaner för att växla över flera datorer tillsammans.
- När du kör en växling vid fel, skapas virtuella Azure-datorer från replikerade data i Azure-lagring.
- Efter utlöser inledande redundans kan genomföra den för att starta åt arbetsbelastningen från den virtuella Azure-datorn.
- När din primära lokala plats är tillgänglig igen, kan du återställa dit.
- Du måste ställa in en infrastruktur för återställning efter fel, inklusive:
    - **Tillfällig processerver i Azure**: Om du vill växla tillbaka från Azure du ställer in en Azure VM att fungera som en processerver för att hantera replikering från Azure. Du kan ta bort den här virtuella datorn när återställningen är klar.
    - **VPN-anslutning**: för att växla tillbaka du behöver en VPN-anslutning (eller Azure ExpressRoute) från Azure-nätverket till den lokala platsen.
    - **Separata huvudmålservern**: som standard som installerades med konfigurationsservern på lokal VMware VM, huvudmålservern hanterar återställning efter fel. Men om du behöver växla tillbaka stora volymer trafik bör du ställa in en separat lokal huvudmålserver för detta ändamål.
    - **Återställningsprincip**: Om du vill replikera tillbaka till din lokala plats behöver du en återställningsprincip. Detta skapas automatiskt när du skapade din replikeringsprincip från lokal till Azure.
    - **VMware-infrastrukturen**: du behöver en VMware-infrastrukturen för återställning efter fel. Du kan inte växla tillbaka till en fysisk server.
- När komponenterna är på plats så utförs återställning efter fel i tre steg:
    - Steg 1: Skapa nytt virtuella Azure-datorer så att de replikera från Azure till lokala VMwares virtuella datorer.
    - Steg 2: Kör en redundansväxling till den lokala platsen.
    - Steg 3: När arbetsbelastningar misslyckas igen kan du återaktivera replikering.

**VMware återställning från Azure**

![Återställning efter fel](./media/physical-azure-architecture/enhanced-failback.png)


## <a name="next-steps"></a>Nästa steg

Följ [självstudierna](physical-azure-disaster-recovery.md) att aktivera fysisk server till Azure-replikering.
