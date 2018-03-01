---
title: VMware till Azure replikeringsarkitektur i Azure Site Recovery | Microsoft Docs
description: "Den här artikeln innehåller en översikt över komponenter och används för att replikera lokala virtuella VMware-datorer till Azure med Azure Site Recovery-arkitektur"
author: rayne-wiselman
ms.service: site-recovery
ms.topic: article
ms.date: 01/15/2018
ms.author: raynew
ms.openlocfilehash: 4dd9d4f5f26d70f9130f48e2bf40ce71943a6c6d
ms.sourcegitcommit: 088a8788d69a63a8e1333ad272d4a299cb19316e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/27/2018
---
# <a name="vmware-to-azure-replication-architecture"></a>VMware till Azure replikeringsarkitektur

Den här artikeln beskriver arkitektur och processer som används när du replikera växla över och återställa virtuella VMware-datorer (VM) mellan en lokal VMware-platsen och Azure med hjälp av [Azure Site Recovery](site-recovery-overview.md).


## <a name="architectural-components"></a>Arkitekturkomponenter

Följande tabell och bild ger en övergripande bild av de komponenter som används för VMware-replikering till Azure.

**Komponent** | **Krav** | **Detaljer**
--- | --- | ---
**Azure** | Azure-prenumeration, Azure Storage-konto och Azure-nätverk. | Replikerade data från lokala virtuella datorer lagras i lagringskontot. Virtuella Azure-datorer skapas med replikerade data när du kör en växling vid fel från lokal till Azure. Virtuella Azure-datorer ansluter till det virtuella Azure-nätverket när de skapas.
**Configuration server-dator** | En enda lokal dator. Vi rekommenderar att du ska köra den som en VMware VM som kan distribueras från en nedladdad OVF-mall.<br/><br/> Datorn kör alla lokala Site Recovery-komponenter, bland annat konfigurationsservern, processervern och huvudmålservern. | **Konfigurationsservern**: samordnar kommunikationen mellan lokala och Azure och hanterar datareplikering.<br/><br/> **Processervern**: installeras som standard på konfigurationsservern. Den tar emot replikeringsdata; optimerar dem med cachelagring, komprimering och kryptering; och skickar den till Azure Storage. Processervern installeras också Azure Site Recovery Mobility-tjänsten på virtuella datorer du vill replikera, och utför automatisk identifiering av lokala datorer. Eftersom distributionen växer kan du lägga till ytterligare, separat servrar för att hantera större mängder replikeringstrafik.<br/><br/> **Huvudmålservern**: installeras som standard på konfigurationsservern. Den hanterar replikeringsdata vid återställning från Azure. Du kan lägga till en ytterligare, separat huvudmålservern för återställning efter fel för stora distributioner.
**VMware-servrar** | Virtuella VMware-datorer finns på lokala vSphere ESXi servrar. Vi rekommenderar en vCenter-server för att hantera värdar. | Under distributionen av Site Recovery kan du lägga till VMware-servrar till Recovery Services-valvet.
**Replikerade datorer** | Mobilitetstjänsten installeras på varje VMware VM som du vill replikera. | Vi rekommenderar att du automatisk installation från processervern. Du kan också installera tjänsten manuellt eller använda en automatisk distributionsmetod, till exempel System Center Configuration Manager.

**Arkitektur för VMware till Azure**

![Komponenter](./media/concepts-vmware-to-azure-architecture/arch-enhanced.png)

## <a name="replication-process"></a>Replikeringsprocessen

1. Förbered Azure-resurser och lokala komponenter.
2. Ange inställningar för datakälla replikering i Recovery Services-valvet. Som en del av den här processen kan du ställa in konfigurationsservern lokalt. Hämta en förberedd OVF-mall för att distribuera den här servern som en VMware VM, och importera dem till VMware för att skapa den virtuella datorn.
3. Ange mål replikeringsinställningarna, skapa en replikeringsprincip och aktivera replikering för din virtuella VMware-datorer.
4. Replikera datorer enligt replikeringsprinciper och en inledande kopia av VM-data replikeras till Azure Storage.
5. När replikeringen har slutförts börjar replikering av deltaändringar till Azure. Spårade ändringar för en dator lagras i en .hrl-fil.

    * Datorer kommunicerar med konfigurationsservern på port 443 för HTTPS inkommande för replikeringshantering av.

    * Datorer skicka replikeringsdata till processervern på port HTTPS 9443 inkommande (kan ändras).

    * Konfigurationsservern samordnar replikeringshantering med Azure via port HTTPS 443 utgående.

    * Processervern tar emot data från källdatorer, optimerar och krypteras det och skickar det till Azure Storage via port 443 utgående.

    * Om du aktiverar konsekvens för flera virtuella datorer kommunicerar datorer i replikeringsgruppen med varandra på port 20004. Multi-VM används om du grupperar flera datorer i replikeringsgrupper som delar kraschkonsekventa och app-konsekventa återställningspunkter när de växlar över vid fel. Den här metoden är användbar om datorer kör samma arbetsbelastning och måste vara konsekvent.

6. Trafik som replikeras till Azure storage offentliga slutpunkter på Internet. Du kan även använda Azure ExpressRoute [offentlig peering](../expressroute/expressroute-circuit-peerings.md#azure-public-peering). Replikera trafik via ett plats-till-plats virtuellt privat nätverk (VPN) från en lokal plats till Azure stöds inte.


**VMware till Azure replikeringen**

![Replikeringsprocessen](./media/concepts-vmware-to-azure-architecture/v2a-architecture-henry.png)

## <a name="failover-and-failback-process"></a>Processen för redundans och återställning efter fel

När replikeringen har ställts in och du kör en katastrof återställningsgranskning (testa redundans) för att kontrollera att allt fungerar som förväntat, kan du köra redundans och återställning som du behöver.

1. Du kan växla över en enskild dator eller skapa återställningsplaner för att växla över flera virtuella datorer.

2. När du kör en växling vid fel, skapas virtuella Azure-datorer från replikerade data i Azure-lagring.

3. Efter utlöser inledande redundans kan genomföra den för att starta åt arbetsbelastningen från den virtuella Azure-datorn.

När din primära lokala plats är tillgänglig igen, kan du återställa dit.
1. Du måste ställa in en infrastruktur för återställning efter fel, inklusive:

    * **Tillfällig processerver i Azure**: Om du vill växla tillbaka från Azure du ställer in en Azure VM att fungera som en processerver för att hantera replikering från Azure. Du kan ta bort den här virtuella datorn när återställningen är klar.

    * **VPN-anslutning**: för att växla tillbaka du behöver en VPN-anslutning (eller ExpressRoute) från Azure-nätverket till den lokala platsen.

    * **Separata huvudmålservern**: som standard som installerades med konfigurationsservern på lokal VMware VM huvudmålservern hanterar återställning efter fel. Om du måste växla tillbaka stora volymer av trafik, kan du ställa in en separat lokal huvudmålserver för detta ändamål.

    * **Återställningsprincip**: Om du vill replikera tillbaka till din lokala plats behöver du en återställningsprincip. Den här principen skapas automatiskt när du skapade din replikeringsprincip från lokal till Azure.
2. När komponenterna är på plats så utförs återställning efter fel i tre steg:

    a. Steg 1: Skapa nytt virtuella Azure-datorer så att de replikera från Azure till lokala VMwares virtuella datorer.

    b. Steg 2: Kör en redundansväxling till den lokala platsen.

    c. Steg 3: När arbetsbelastningar misslyckas igen kan du återaktivera replikering för de lokala virtuella datorerna.

**VMware återställning från Azure**

![Återställning efter fel](./media/concepts-vmware-to-azure-architecture/enhanced-failback.png)


## <a name="next-steps"></a>Nästa steg

Följ [självstudierna](tutorial-vmware-to-azure.md) att aktivera VMware till Azure-replikering.
