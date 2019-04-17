---
title: Arkitekturen för haveriberedskap för fysiska servrar till Azure med hjälp av Azure Site Recovery | Microsoft Docs
description: Den här artikeln innehåller en översikt över komponenter och arkitektur som används under haveriberedskap för lokala fysiska servrar till Azure med Azure Site Recovery-tjänsten.
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 04/16/2019
ms.author: raynew
ms.openlocfilehash: 99aec3be893693e523dffefbb3c422222ac19a2e
ms.sourcegitcommit: fec96500757e55e7716892ddff9a187f61ae81f7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2019
ms.locfileid: "59616874"
---
# <a name="physical-server-to-azure-disaster-recovery-architecture"></a>Fysisk server till Azure disaster recovery-arkitekturen

Den här artikeln beskrivs arkitekturen och processer som används när du replikera, redundansväxla och återställa fysiska Windows och Linux-servrar mellan en lokal plats och Azure, med hjälp av den [Azure Site Recovery](site-recovery-overview.md) service.


## <a name="architectural-components"></a>Arkitekturkomponenter

Följande tabell och bild ger en översikt över de komponenter som används för fysisk serverreplikering till Azure.  

**Komponent** | **Krav** | **Detaljer**
--- | --- | ---
**Azure** | En Azure-prenumeration och ett Azure-nätverk. | Replikerade data från lokala fysiska datorer lagras i Azure-hanterade diskar. Virtuella Azure-datorer skapas med replikerad data när du kör en växling vid fel över från lokal plats till Azure. Virtuella Azure-datorer ansluter till det virtuella Azure-nätverket när de skapas.
**Konfigurationsserver** | En enda lokal fysisk dator eller VMware VM distribueras för att köra alla lokalerna Site Recovery-komponenter. Den virtuella datorn körs på konfigurationsservern, processervern och huvudmålservern. | Konfigurationsservern samordnar kommunikationen mellan den lokala miljön och Azure och hanterar datareplikering.
 **Processerver**:  | Installeras som standard tillsammans med konfigurationsservern. | Fungerar som en replikeringsgateway. Den tar emot replikeringsdata, optimerar dem med cachelagring, komprimering och kryptering och skickar dem till Azure Storage.<br/><br/> Processervern installerar också mobilitetstjänsten på servrar som du vill replikera.<br/><br/> Allt eftersom distributionen växer kan du lägga till ytterligare, separat processervrar för att hantera större mängder replikeringstrafik.
 **Huvudmålservern** | Installeras som standard tillsammans med konfigurationsservern. | Hanterar replikeringsdata vid återställning efter fel från Azure.<br/><br/> För stora distributioner, kan du lägga till en ytterligare, separat huvudmålserver för återställning efter fel.
**Replikerade servrar** | Mobilitetstjänsten installeras på varje server som du vill replikera. | Vi rekommenderar att du tillåter automatisk installation från processervern. Du kan också installera tjänsten manuellt eller använda en automatisk distributionsmetod som System Center Configuration Manager.

**Fysisk till Azure-arkitektur**

![Komponenter](./media/physical-azure-architecture/arch-enhanced.png)

## <a name="replication-process"></a>Replikeringsprocessen

1. Du ställer in distributionen, inklusive lokala och Azure-komponenter. I Recovery Services-valvet anger du replikeringskälla och mål, ställer in konfigurationsservern, skapa en replikeringsprincip och aktiverar replikering.
2. Datorer replikera enligt replikeringsprincipen och en inledande kopia av server-data replikeras till Azure storage.
3. När den inledande replikeringen är klar börjar replikeringen av deltaändringar till Azure. Spårade ändringar för en dator lagras i en .hrl-fil.
    - Datorer kommunicerar med konfigurationsservern på port HTTPS 443 inkommande, för replikeringshantering.
    - Datorer skickar replikeringsdata till processervern på port HTTPS 9443 inkommande (kan ändras).
    - Konfigurationsservern samordnar replikeringshantering med Azure via port HTTPS 443 utgående.
    - Processervern tar emot data från källdatorer, optimerar och krypterar den och skickar den till Azure Storage över port 443 utgående.
    - Om du aktiverar konsekvens för flera virtuella datorer kommunicerar datorer i replikeringsgruppen med varandra på port 20004. Multi-VM används om du grupperar flera datorer i replikeringsgrupper som delar kraschkonsekventa och app-konsekventa återställningspunkter när de växlar över vid fel. Detta är användbart om datorerna kör samma arbetsbelastning och måste överensstämma.
4. Trafik replikeras till offentliga Azure Storage-slutpunkter, över Internet. Du kan även använda Azure ExpressRoute [offentlig peering](../expressroute/expressroute-circuit-peerings.md#publicpeering). Replikering av trafik via en plats-till-plats-VPN från en lokal plats till Azure stöds inte.


**Fysisk till Azure replikeringsprocessen**

![Replikeringsprocessen](./media/physical-azure-architecture/v2a-architecture-henry.png)

## <a name="failover-and-failback-process"></a>Processen för redundans och återställning efter fel

När replikering har ställts in och du har kört ett programåterställningstest (testa redundans) om du vill kontrollera att allt fungerar som förväntat kan köra du redundans och återställning efter fel som du behöver. Tänk på följande:

- Planerad redundans stöds inte.
- Du måste växla tillbaka till en lokal VMware VM. Det innebär att du behöver en lokal VMware-infrastruktur, även när du replikerar lokala fysiska servrar till Azure.
- Du växlar över en enskild dator eller skapa återställningsplaner för att växla över flera datorer tillsammans.
- När du kör en redundans skapas virtuella Azure-datorer från replikerade data i Azure storage.
- Du etablerar den för att få åtkomst till arbetsbelastningen från den virtuella Azure-datorer efter första redundansen.
- När din primära lokala plats är tillgänglig igen, kan du återställa dit.
- Du måste konfigurera en infrastruktur för återställning efter fel, inklusive:
    - **Tillfällig processerver i Azure**: Om du vill redundansväxla från Azure ställa du in en Azure-dator så att den fungerar som en processerver för att hantera replikering från Azure. Du kan ta bort den här virtuella datorn när återställningen är klar.
    - **VPN-anslutningen**: För att återställa, behöver du en VPN-anslutning (eller Azure ExpressRoute) från Azure-nätverket till den lokala platsen.
    - **Separat huvudmålserver**: Som standard hanterar som installerades med konfigurationsservern på en lokal VMware VM, huvudmålservern återställning efter fel. Men om du vill växla tillbaka stora mängder trafik kan bör du ställa in en separat lokal huvudmålserver för detta ändamål.
    - **Återställningsprincip**: Om du vill replikera tillbaka till din lokala plats behöver du en princip för återställning efter fel. Detta skapades automatiskt när du skapade din replikeringsprincip från en lokal plats till Azure.
    - **VMware-infrastruktur**: Du behöver en VMware-infrastruktur för återställning efter fel. Du kan inte växla tillbaka till en fysisk server.
- När komponenterna är på plats så utförs återställning efter fel i tre steg:
    - Steg 1: Återaktivera skyddet av virtuella Azure-datorer så att de replikera från Azure till lokala VMware-datorer.
    - Steg 2: Kör en redundansväxling till den lokala platsen.
    - Steg 3: När arbetsbelastningar har återställts återaktivera replikering.

**VMware återställning efter fel från Azure**

![Återställning efter fel](./media/physical-azure-architecture/enhanced-failback.png)


## <a name="next-steps"></a>Nästa steg

Följ [den här självstudien](physical-azure-disaster-recovery.md) att aktivera fysisk server till Azure-replikering.
