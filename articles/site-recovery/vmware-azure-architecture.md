---
title: VMware till Azure disaster recovery-arkitekturen i Azure Site Recovery | Microsoft Docs
description: Den här artikeln innehåller en översikt över komponenter och arkitektur som används när du konfigurerar haveriberedskap för lokala virtuella VMware-datorer till Azure med Azure Site Recovery
author: rayne-wiselman
ms.service: site-recovery
ms.topic: conceptual
ms.date: 11/27/2018
ms.author: raynew
ms.openlocfilehash: 962ced808f97dd1fea3805fa8c953e6d7563cd17
ms.sourcegitcommit: b0f39746412c93a48317f985a8365743e5fe1596
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/04/2018
ms.locfileid: "52871752"
---
# <a name="vmware-to-azure-disaster-recovery-architecture"></a>VMware till Azure disaster recovery-arkitekturen

Den här artikeln beskrivs arkitekturen och processer som används när du distribuerar disaster recovery-replikering, redundans och återställning av virtuella VMware-datorer (VM) mellan en lokal VMware-platsen och Azure med hjälp av den [Azure Site Recovery](site-recovery-overview.md) servuce.


## <a name="architectural-components"></a>Arkitekturkomponenter

Följande tabell och bild ger en översikt över de komponenter som används för haveriberedskap för VMware till Azure.

**Komponent** | **Krav** | **Detaljer**
--- | --- | ---
**Azure** | En Azure-prenumeration, Azure Storage-konto och Azure-nätverk. | Replikerade data från lokala virtuella datorer lagras i lagringskontot. Virtuella Azure-datorer skapas med replikerad data när du kör en redundans från lokalt till Azure. Virtuella Azure-datorer ansluter till det virtuella Azure-nätverket när de skapas.
**Configuration server-dator** | En enda lokal dator. Vi rekommenderar att du ska köra den som en VMware-VM som kan distribueras från en nedladdade OVF-mall.<br/><br/> Datorn kör alla lokala Site Recovery-komponenter, bland annat konfigurationsservern, processervern och huvudmålservern. | **Konfigurationsservern**: samordnar kommunikationen mellan lokala och Azure och hanterar datareplikering.<br/><br/> **Processervern**: installeras som standard på konfigurationsservern. Den tar emot replikeringsdata; optimerar dem med cachelagring, komprimering och kryptering och skickar dem till Azure Storage. Processervern installerar också Azure Site Recovery-Mobilitetstjänsten på virtuella datorer du vill replikera, samt utför automatisk identifiering av lokala datorer. Allt eftersom distributionen växer kan du lägga till ytterligare, separat processervrar för att hantera större mängder replikeringstrafik.<br/><br/> **Huvudmålservern**: installeras som standard på konfigurationsservern. Den hanterar replikeringsdata vid återställning från Azure. För stora distributioner, kan du lägga till en ytterligare, separat huvudmålserver för återställning efter fel.
**VMware-servrar** | Virtuella VMware-datorer finns på den lokala vSphere ESXi-servrar. Vi rekommenderar en vCenter-server hanterar värdarna. | Under distributionen av Site Recovery måste du lägga till VMware-servrar till Recovery Services-valvet.
**Replikerade datorer** | Mobilitetstjänsten installeras på varje VMware VM som du replikerar. | Vi rekommenderar att du tillåter automatisk installation från processervern. Du kan också installera tjänsten manuellt eller använda en metod för automatisk distribution, till exempel System Center Configuration Manager.

**Arkitektur för VMware till Azure**

![Komponenter](./media/vmware-azure-architecture/arch-enhanced.png)



## <a name="replication-process"></a>Replikeringsprocessen

1. När du aktiverar replikering för en virtuell dator, börjar den inledande replikeringen till Azure storage med hjälp av den angivna replikeringsprincipen. Observera följande:
    - För virtuella VMware-datorer är replikering på blocknivå, nästan kontinuerlig, med hjälp av mobilitetstjänstagenten som körs på den virtuella datorn.
    - Alla replikeringsprincipens inställningar tillämpas:
        - **Tröskelvärde för Replikeringspunktmål**. Den här inställningen påverkar inte replikering. Det hjälper dig med övervakning. En händelse inträffar och eventuellt ett e-postmeddelande skickas om aktuellt RPO överskrider tröskelvärdet gränsen som du anger.
        - **Kvarhållning av återställningspunkt**. Den här inställningen anger hur långt tillbaka i tiden som du vill gå när avbrott uppstår. Maximal kvarhållning på premium storage är 24 timmar. Det är 72 timmar med standardlagring. 
        - **Appkonsekventa ögonblicksbilder**. Appkompatibel ögonblicksbild kan ta varje 1 till 12 timmar, beroende på behoven för dina appar. Ögonblicksbilder är standard Azure blob-ögonblicksbilder. Mobilitetsagenten som körs på en virtuell dator begär en VSS-ögonblicksbild i enlighet med den här inställningen och bokmärken som point-in-time som ett program som är konsekvent pekar i dataströmmen för replikering.

2. Trafik som replikeras till Azure storage-offentliga slutpunkter för via internet. Alternativt kan du använda Azure ExpressRoute med [offentlig peering](../expressroute/expressroute-circuit-peerings.md#publicpeering). Replikering av trafik via ett plats-till-plats virtuellt privat nätverk (VPN) från en lokal plats till Azure stöds inte.
3. När den inledande replikeringen är klar börjar replikeringen av deltaändringar till Azure. Spårade ändringar för en dator skickas till processervern.
4. Kommunikation händer följande:

    - Virtuella datorer kommunicera med den lokala konfigurationsservern på port HTTPS 443 inkommande, för replikeringshantering.
    - Konfigurationsservern samordnar replikeringen med Azure via port HTTPS 443 utgående.
    - Virtuella datorer skickar replikeringsdata till processervern (som körs på configuration server-datorn) på port HTTPS 9443 inkommande. Den här porten kan ändras.
    - Processervern tar emot replikeringsdata, optimerar och krypterar dem och skickar dem till Azure storage över port 443 utgående.




**VMware till Azure replikeringsprocessen**

![Replikeringsprocessen](./media/vmware-azure-architecture/v2a-architecture-henry.png)

## <a name="failover-and-failback-process"></a>Processen för redundans och återställning efter fel

När replikering har ställts in och du kör ett programåterställningstest (testa redundans) om du vill kontrollera att allt fungerar som förväntat kan köra du redundans och återställning efter fel som du behöver.

1. Du kör misslyckas för en enskild dator eller skapa en återställning tillsammans att växla över flera virtuella datorer på samma gång. Fördelen med en återställningsplan snarare än en enda dator redundans inkluderar:
    - Du kan utforma appen beroenden genom att inkludera alla virtuella datorer i appen i en enda återställningsplan.
    - Du kan lägga till skript, Azure-runbooks och pausa för manuella åtgärder.
2. Du etablerar den för att få åtkomst till arbetsbelastningen från den virtuella Azure-datorer efter första redundansen.
3. När din primära lokala plats är tillgänglig igen, kan du förbereda för återställning vid fel. För att kunna återställa dit du behöva ställa in en infrastruktur för återställning efter fel, inklusive:

    * **Tillfällig processerver i Azure**: Om du vill redundansväxla från Azure, du har konfigurerat en Azure-dator så att den fungerar som en processerver för att hantera replikering från Azure. Du kan ta bort den här virtuella datorn när återställningen är klar.
    * **VPN-anslutningen**: för att återställa, du behöver en VPN-anslutning (eller ExpressRoute) från Azure-nätverket till den lokala platsen.
    * **Separat huvudmålserver**: som standard som installerades med konfigurationsservern på en lokal VMware VM huvudmålservern hanterar återställning efter fel. Om du vill växla tillbaka stora mängder trafik kan du konfigurera en separat lokal huvudmålserver för detta ändamål.
    * **Återställningsprincip**: Om du vill replikera tillbaka till din lokala plats behöver du en återställningsprincip. Den här principen skapas automatiskt när du skapar en replikeringsprincip från en lokal plats till Azure.
4. När komponenterna är på plats så utförs återställning efter fel i tre åtgärder:

    - Steg 1: Återaktivera skyddet av virtuella Azure-datorer så att de replikera från Azure till lokala VMware-datorer.
    -  Steg 2: Kör en redundansväxling till den lokala platsen.
    - Steg 3: När arbetsbelastningar har återställts du återaktivera replikering för de lokala virtuella datorerna.
    
 
**VMware återställning efter fel från Azure**

![Återställning efter fel](./media/vmware-azure-architecture/enhanced-failback.png)


## <a name="next-steps"></a>Nästa steg

Följ [den här självstudien](vmware-azure-tutorial.md) att aktivera VMware till Azure-replikering.
