---
title: Katastrof återställnings arkitektur för VMware VM i Azure Site Recovery
description: Den här artikeln innehåller en översikt över komponenter och arkitektur som används när du konfigurerar haveri beredskap för lokala virtuella VMware-datorer till Azure med Azure Site Recovery
author: rayne-wiselman
ms.service: site-recovery
services: site-recovery
ms.topic: conceptual
ms.date: 11/06/2019
ms.author: raynew
ms.openlocfilehash: ccf258594aa68fc9b5d0189c9ada640078e0ba6f
ms.sourcegitcommit: 38b11501526a7997cfe1c7980d57e772b1f3169b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/22/2020
ms.locfileid: "76514875"
---
# <a name="vmware-to-azure-disaster-recovery-architecture"></a>Katastrof återställnings arkitektur för VMware till Azure

Den här artikeln beskriver arkitekturen och processerna som används när du distribuerar haveri beredskap, redundans och återställning av virtuella VMware-datorer mellan en lokal VMware-plats och Azure med hjälp av tjänsten [Azure Site Recovery](site-recovery-overview.md) .


## <a name="architectural-components"></a>Arkitekturkomponenter

Följande tabell och grafik ger en övergripande bild av de komponenter som används för VMware haveri beredskap till Azure.

**Komponent** | **Krav** | **Detaljer**
--- | --- | ---
**Azure** | En Azure-prenumeration, Azure Storage konto för cache, hanterad disk och Azure-nätverk. | Replikerade data från lokala virtuella datorer lagras i Azure Storage. Virtuella Azure-datorer skapas med replikerade data när du kör en redundansväxling från en lokal plats till Azure. Virtuella Azure-datorer ansluter till det virtuella Azure-nätverket när de skapas.
**Konfigurations Server dator** | En enda lokal dator. Vi rekommenderar att du kör det som en virtuell VMware-dator som kan distribueras från en Hämtad OVF-mall.<br/><br/> Datorn kör alla lokala Site Recovery-komponenter som innehåller konfigurations servern, processervern och huvud mål servern. | **Konfigurations Server**: samordnar kommunikationen mellan både lokalt och Azure och hanterar datareplikering.<br/><br/> **Processerver**: installeras som standard på konfigurations servern. Den tar emot replikeringsdata; optimerar den med cachelagring, komprimering och kryptering; och skickar den till Azure Storage. Processervern installerar också mobilitetstjänsten Azure Site Recovery på de virtuella datorer du vill replikera, samt utför automatisk identifiering av lokala virtuella VMware-datorer. När distributionen växer kan du lägga till ytterligare separata process servrar för att hantera större volymer av replikeringstrafiken.<br/><br/> **Huvud mål server**: installeras som standard på konfigurations servern. Den hanterar replikeringsdata under återställning efter fel från Azure. För stora distributioner kan du lägga till ytterligare en separat huvud mål server för återställning efter fel.
**VMware-servrar** | Virtuella VMware-datorer finns på lokala vSphere ESXi-servrar. Vi rekommenderar att en vCenter-Server hanterar värdarna. | När du Site Recovery distribution lägger du till VMware-servrar i Recovery Services-valvet.
**Replikerade datorer** | Mobilitets tjänsten är installerad på varje virtuell VMware-dator som du replikerar. | Vi rekommenderar att du tillåter automatisk installation från processervern. Alternativt kan du installera tjänsten manuellt eller använda en automatiserad distributions metod, till exempel Configuration Manager.

**Arkitektur för VMware till Azure**

![Komponenter](./media/vmware-azure-architecture/arch-enhanced.png)



## <a name="replication-process"></a>Replikeringsprocessen

1. När du aktiverar replikering för en virtuell dator börjar den inledande replikeringen till Azure Storage med den angivna replikeringsprincipen. Observera följande:
    - För virtuella VMware-datorer är replikeringen block-level, nästan kontinuerlig, med mobilitets tjänst agenten som körs på den virtuella datorn.
    - Princip inställningar för replikering tillämpas:
        - Återställnings **tröskel**. Den här inställningen påverkar inte replikeringen. Det hjälper till med övervakning. En händelse höjs och eventuellt ett e-postmeddelande som skickas, om den aktuella återställningen överskrider den tröskel gräns som du anger.
        - **Kvarhållning av återställnings punkt**. Den här inställningen anger hur långt bakåt i tiden du vill gå när ett avbrott inträffar. Maximal kvarhållning i Premium Storage är 24 timmar. På standard lagring är det 72 timmar. 
        - **Programkonsekventa ögonblicks bilder**. Programkonsekventa ögonblicks bilder kan ta var 1 till 12 timmar, beroende på ditt programs behov. Ögonblicks bilder är standardiserade Azure Blob-ögonblicksbilder. Mobilitets agenten som körs på en virtuell dator begär en VSS-ögonblicksbild i enlighet med den här inställningen och bok märken som är punkt-i tid som en programkonsekvent punkt i replik data strömmen.

2. Trafiken replikeras till offentliga slut punkter i Azure Storage via Internet. Alternativt kan du använda Azure-ExpressRoute med [Microsoft-peering](../expressroute/expressroute-circuit-peerings.md#microsoftpeering). Det finns inte stöd för att replikera trafik över ett virtuellt privat nätverk (VPN) från plats till plats från en lokal plats till Azure.
3. När den inledande replikeringen har slutförts börjar replikeringen av delta ändringar till Azure. Spårade ändringar för en dator skickas till processervern.
4. Kommunikationen sker på följande sätt:

    - Virtuella datorer kommunicerar med den lokala konfigurations servern på port HTTPS 443 inkommande, för hantering av replikering.
    - Konfigurations servern dirigerar replikeringen med Azure via port HTTPS 443 utgående.
    - Virtuella datorer skickar replikeringsdata till processervern (körs på konfigurations serverdatorn) på port HTTPS 9443 inkommande. Den här porten kan ändras.
    - Processervern tar emot replikeringsdata, optimerar och krypterar den och skickar den till Azure Storage via port 443 utgående.
5. Replikeringsdata loggar första marken i ett cache Storage-konto i Azure. Dessa loggar bearbetas och data lagras på en Azure-hanterad disk (kallas för automatisk start disk). Återställnings punkterna skapas på den här disken.




**Replikering av VMware till Azure-replikering**

![Replikeringsprocessen](./media/vmware-azure-architecture/v2a-architecture-henry.png)

## <a name="failover-and-failback-process"></a>Processen för redundans och återställning efter fel

När replikeringen har kon figurer ATS och du kör en haveri beredskap (testa redundans) för att kontrol lera att allt fungerar som förväntat, kan du köra redundans och återställning efter fel när du behöver.

1. Det går inte att köra en enskild dator eller så kan du skapa en återställnings plan för att redundansväxla flera virtuella datorer på samma gång. Fördelen med en återställnings plan i stället för en enskild dators redundans är:
    - Du kan utforma program beroenden genom att inkludera alla virtuella datorer i appen i en enda återställnings plan.
    - Du kan lägga till skript, Azure-Runbooks och pausa för manuella åtgärder.
2. När du har utlöst den inledande redundansväxlingen genomför du den för att börja komma åt arbets belastningen från den virtuella Azure-datorn.
3. När din primära lokala plats är tillgänglig igen, kan du förbereda för att återställa. För att återställa måste du konfigurera en infrastruktur för återställning efter fel, inklusive:

    * **Tillfällig processerver i Azure**: om du vill återställa från Azure konfigurerar du en virtuell Azure-dator så att den fungerar som en processerver för att hantera replikering från Azure. Du kan ta bort den här virtuella datorn när återställningen är klar.
    * **VPN-anslutning**: om du vill återställa behöver du en VPN-anslutning (eller ExpressRoute) från Azure-nätverket till den lokala platsen.
    * **Separat huvud mål server**: som standard hanterar huvud mål servern som installerades med konfigurations servern på den lokala virtuella VMware-datorn återställning efter fel. Om du behöver återställa stora mängder trafik måste du konfigurera en separat lokal huvud mål server för det här ändamålet.
    * **Återställningsprincip**: Om du vill replikera tillbaka till din lokala plats behöver du en återställningsprincip. Den här principen skapas automatiskt när du skapar en replikeringsprincip från lokal plats till Azure.
4. Efter att komponenterna är på plats sker återställning efter fel i tre åtgärder:

    - Steg 1: skydda virtuella datorer i Azure så att de replikeras från Azure tillbaka till lokala virtuella VMware-datorer.
    -  Steg 2: kör en redundansväxling till den lokala platsen.
    - Steg 3: när arbets belastningarna har misslyckats igen återaktiverar du replikeringen för de lokala virtuella datorerna.
    
 
**VMware-återställning från Azure**

![Återställning efter fel](./media/vmware-azure-architecture/enhanced-failback.png)


## <a name="next-steps"></a>Nästa steg

Följ [den här självstudien](vmware-azure-tutorial.md) för att aktivera VMware till Azure-replikering.
