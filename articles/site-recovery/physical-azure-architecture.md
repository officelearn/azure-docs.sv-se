---
title: Katastrof återställnings arkitektur för fysiska servrar i Azure Site Recovery
description: Den här artikeln innehåller en översikt över komponenter och arkitektur som används vid haveri beredskap för lokala fysiska servrar till Azure med tjänsten Azure Site Recovery.
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 11/14/2019
ms.author: raynew
ms.openlocfilehash: 72f21babd4d12e69cd346d8693e5ed4fe9117134
ms.sourcegitcommit: 38b11501526a7997cfe1c7980d57e772b1f3169b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/22/2020
ms.locfileid: "76513957"
---
# <a name="physical-server-to-azure-disaster-recovery-architecture"></a>Fysisk server till Azure Disaster Recovery-arkitektur

Den här artikeln beskriver arkitekturen och processerna som används när du replikerar, växlar över och återställer fysiska Windows-och Linux-servrar mellan en lokal plats och Azure med hjälp av tjänsten [Azure Site Recovery](site-recovery-overview.md) .


## <a name="architectural-components"></a>Arkitekturkomponenter

Följande tabell och grafik ger en övergripande bild av de komponenter som används för replikering av fysiska servrar till Azure.  

**Komponent** | **Krav** | **Detaljer**
--- | --- | ---
**Azure** | En Azure-prenumeration och ett Azure-nätverk. | Replikerade data från lokala fysiska datorer lagras på Azure Managed disks. Virtuella Azure-datorer skapas med replikerade data när du kör en redundans från en lokal plats till Azure. Virtuella Azure-datorer ansluter till det virtuella Azure-nätverket när de skapas.
**Konfigurationsserver** | En enda lokal fysisk dator eller virtuell VMware-dator distribueras för att köra alla lokala Site Recovery-komponenter. Den virtuella datorn kör konfigurations servern, processervern och huvud mål servern. | Konfigurationsservern samordnar kommunikationen mellan den lokala miljön och Azure och hanterar datareplikering.
 **Processerver**:  | Installeras som standard tillsammans med konfigurations servern. | Fungerar som en replikeringsgateway. Den tar emot replikeringsdata, optimerar dem med cachelagring, komprimering och kryptering och skickar dem till Azure Storage.<br/><br/> Processervern installerar också mobilitets tjänsten på de servrar som du vill replikera.<br/><br/> När distributionen växer kan du lägga till ytterligare separata process servrar för att hantera större volymer av replikeringstrafiken.
 **Huvudmålservern** | Installeras som standard tillsammans med konfigurations servern. | Hanterar replikeringsdata vid återställning efter fel från Azure.<br/><br/> För stora distributioner kan du lägga till ytterligare en separat huvud mål server för återställning efter fel.
**Replikerade servrar** | Mobilitets tjänsten är installerad på varje server som du replikerar. | Vi rekommenderar att du tillåter automatisk installation från processervern. Alternativt kan du installera tjänsten manuellt eller använda en automatiserad distributions metod som Configuration Manager.

**Arkitektur för fysisk till Azure**

![Komponenter](./media/physical-azure-architecture/arch-enhanced.png)

## <a name="replication-process"></a>Replikeringsprocessen

1. Du konfigurerar distributionen, inklusive lokala och Azure-komponenter. I Recovery Services valvet anger du replikeringens källa och mål, konfigurerar konfigurations servern, skapar en replikeringsprincip och aktiverar replikering.
2. Datorer replikeras i enlighet med replikeringsprincipen och en ursprunglig kopia av Server data replikeras till Azure Storage.
3. När den inledande replikeringen har slutförts börjar replikeringen av delta ändringar till Azure. Spårade ändringar för en dator lagras i en .hrl-fil.
    - Datorer kommunicerar med konfigurations servern på port HTTPS 443 inkommande, för hantering av replikering.
    - Datorer skickar replikeringsdata till processervern på port HTTPS 9443 inkommande (kan ändras).
    - Konfigurationsservern samordnar replikeringshantering med Azure via port HTTPS 443 utgående.
    - Processervern tar emot data från källdatorer, optimerar och krypterar den och skickar den till Azure Storage över port 443 utgående.
    - Om du aktiverar konsekvens för flera virtuella datorer kommunicerar datorer i replikeringsgruppen med varandra på port 20004. Multi-VM används om du grupperar flera datorer i replikeringsgrupper som delar kraschkonsekventa och app-konsekventa återställningspunkter när de växlar över vid fel. Detta är användbart om datorerna kör samma arbetsbelastning och måste överensstämma.
4. Trafik replikeras till offentliga Azure Storage-slutpunkter, över Internet. Du kan även använda Azure ExpressRoute [offentlig peering](../expressroute/about-public-peering.md). Replikering av trafik via en plats-till-plats-VPN från en lokal plats till Azure stöds inte.


**Process för fysisk till Azure-replikering**

![Replikeringsprocessen](./media/physical-azure-architecture/v2a-architecture-henry.png)

## <a name="failover-and-failback-process"></a>Processen för redundans och återställning efter fel

När replikeringen har kon figurer ATS och du har kört en haveri beredskap (testa redundans) för att kontrol lera att allt fungerar som förväntat, kan du köra redundans och återställning efter fel när du behöver. Tänk på följande:

- Planerad redundans stöds inte.
- Du måste växla tillbaka till en lokal virtuell VMware-dator. Det innebär att du behöver en lokal VMware-infrastruktur, även när du replikerar lokala fysiska servrar till Azure.
- Du växlar över en enskild dator eller skapar återställnings planer för att växla över flera datorer tillsammans.
- När du kör en redundansväxling skapas virtuella Azure-datorer från replikerade data i Azure Storage.
- När du har utlöst den inledande redundansväxlingen genomför du den för att börja komma åt arbets belastningen från den virtuella Azure-datorn.
- När din primära lokala plats är tillgänglig igen, kan du återställa dit.
- Du måste konfigurera en infrastruktur för återställning efter fel, inklusive:
    - **Tillfällig processerver i Azure**: om du vill återställa från Azure konfigurerar du en virtuell Azure-dator så att den fungerar som en processerver för att hantera replikering från Azure. Du kan ta bort den här virtuella datorn när återställningen är klar.
    - **VPN-anslutning**: om du vill återställa behöver du en VPN-anslutning (eller Azure-ExpressRoute) från Azure-nätverket till den lokala platsen.
    - **Separat huvud mål server**: som standard är huvud mål servern som installerades med konfigurations servern, på den lokala virtuella VMware-datorn, hanterar återställning efter fel. Men om du behöver återställa stora mängder trafik måste du konfigurera en separat lokal huvud mål server för det här ändamålet.
    - **Återställningsprincip**: Om du vill replikera tillbaka till din lokala plats behöver du en återställningsprincip. Detta skapades automatiskt när du skapade replikeringsprincipen från lokal plats till Azure.
    - **VMware-infrastruktur**: du behöver en VMware-infrastruktur för återställning efter fel. Du kan inte växla tillbaka till en fysisk server.
- Efter att komponenterna är på plats sker återställning i tre steg:
    - Steg 1: skydda virtuella datorer i Azure så att de replikeras från Azure tillbaka till lokala virtuella VMware-datorer.
    - Steg 2: kör en redundansväxling till den lokala platsen.
    - Steg 3: när arbets belastningarna har misslyckats igen återaktiverar du replikeringen.

**VMware-återställning från Azure**

![Återställning efter fel](./media/physical-azure-architecture/enhanced-failback.png)


## <a name="next-steps"></a>Nästa steg

Följ [den här självstudien](physical-azure-disaster-recovery.md) för att aktivera fysisk server till Azure-replikering.
