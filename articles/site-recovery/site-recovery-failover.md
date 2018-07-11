---
title: Redundans i Site Recovery | Microsoft Docs
description: Azure Site Recovery samordnar replikering, redundans och återställning av virtuella datorer och fysiska servrar. Läs mer om redundansväxling till Azure eller ett sekundärt datacenter.
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: article
ms.date: 07/06/2018
ms.author: ponatara
ms.openlocfilehash: 3ef52030f694b0f9ccf2bd10545918a4fae9f2ee
ms.sourcegitcommit: a06c4177068aafc8387ddcd54e3071099faf659d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/09/2018
ms.locfileid: "37918313"
---
# <a name="failover-in-site-recovery"></a>Redundans i Site Recovery
Den här artikeln beskrivs hur du redundansväxling för virtuella datorer och fysiska servrar som skyddas av Site Recovery.

## <a name="prerequisites"></a>Förutsättningar
1. Innan du utför en redundansväxling gör en [redundanstest](site-recovery-test-failover-to-azure.md) så att allt fungerar som förväntat.
1. [Förbered nätverket](site-recovery-network-design.md) på målplatsen innan du gör en redundansväxling.  

Använd följande tabell om du vill veta om alternativen för redundans av Azure Site Recovery. Dessa alternativ visas också för olika redundansscenarier.

| Scenario | Återställning av programkrav | Arbetsflöde för Hyper-V | Arbetsflöde för VMware
|---|--|--|--|
|Planerad redundans på grund av en kommande datacenter-avbrott| Ingen dataförlust för programmet när en planerad aktivitet utförs| För Hyper-V replikerar ASR data på en kopieringsfrekvens som anges av användaren. Planerad redundans används för att åsidosätta frekvensen och replikera de slutliga förändringar innan redundans initieras. <br/> <br/> 1.    Planera en underhållsperiod enligt din verksamhet av processen för ändringshantering. <br/><br/> 2. meddela användare om kommande driftstopp. <br/><br/> 3. Koppla från användarinriktade-programmet.<br/><br/>4. initiera planerad redundans med hjälp av ASR-portalen. Den lokala virtuella datorn är automatiskt avstängningstillstånd.<br/><br/>Effektiva dataförlust = 0 <br/><br/>En journal återställningspunkter ges också i en kvarhållningsperiod för en användare som vill använda en tidigare återställningspunkt. (24 timmar kvarhållning för Hyper-V).| För VMware replikerar ASR data kontinuerligt med hjälp av CDP. Redundans ger användaren möjlighet att redundans till den senaste informationen (inklusive post programmet avställning)<br/><br/> 1. Planera en underhållsperiod enligt av processen för ändringshantering <br/><br/>2. meddela användare om kommande driftstopp <br/><br/>3.    Koppla från användarinriktade-programmet. <br/><br/>4.  Starta en planerad redundans med hjälp av ASR-portalen till den senaste tidpunkten när programmet är offline. Använd alternativet ”oplanerad redundans” på portalen och välj den senaste tidpunkten för redundans. Den lokala virtuella datorn är automatiskt avstängningstillstånd.<br/><br/>Effektiva dataförlust = 0 <br/><br/>En journal återställningspunkter i en kvarhållningsperiod har angetts för en kund som vill använda en tidigare återställningspunkt. (72 timmars kvarhållning för VMware).
|Växling vid fel på grund av en oplanerad datacenter-avbrott (naturligt eller IT-haveri) | Minimal dataförlust för programmet | 1. initiera organisationens BCP-plan <br/><br/>2. Initiera oplanerad redundans med hjälp av ASR-portalen till senast eller en tidpunkt från kvarhållningsperiod (journal).| 1. Initiera organisationens BCP-plan. <br/><br/>2.  Initiera oplanerad redundans med hjälp av ASR-portalen till senast eller en tidpunkt från kvarhållningsperiod (journal).


## <a name="run-a-failover"></a>Köra en redundansväxling
Den här proceduren beskriver hur du kör en redundansväxling för en [återställningsplanen](site-recovery-create-recovery-plans.md). Du kan också köra redundans för en enskild virtuell dator eller fysisk server från den **replikerade objekt** sidan


![Redundans](./media/site-recovery-failover/Failover.png)

1. Välj **Återställningsplaner** > *recoveryplan_name*. Klicka på **redundans**
2. På den **redundans** väljer en **återställningspunkt** att redundansväxla till. Du kan välja något av följande alternativ:
    1.  **Senaste** (standard): det här alternativet startar jobbet genom att första bearbeta alla data som har skickats till Site Recovery-tjänsten. Bearbeta data skapar en återställningspunkt för varje virtuell dator. Den här återställningspunkten används av den virtuella datorn under redundansväxlingen. Det här alternativet ger det lägsta MÅLET (mål för återställningspunkt) som den virtuella datorn skapas efter att redundansen har fått alla data som har replikerats till Site Recovery-tjänsten när redundansen utlöstes.
    1.  **Senaste bearbetade**: det här alternativet redundansväxlar alla virtuella datorer i återställningsplanen till den senaste återställningspunkten som redan har bearbetats av Site Recovery-tjänsten. När du gör redundanstest för en virtuell dator, visas också tidsstämpeln för den senaste bearbetade återställningspunkten. Om du genomför redundans för en återställningsplan går du till en enskild virtuell dator och titta på **senaste återställningspunkter** panelen för att få den här informationen. Ingen tid på för att bearbeta obearbetade data, ger det här alternativet ett lågt mål för Återställningstid (RTO) redundans alternativ.
    1.  **Senaste appkonsekventa**: det här alternativet redundansväxlar alla virtuella datorer i återställningsplanen till den senaste programkonsekvent återställningspunkt som redan har bearbetats av Site Recovery-tjänsten. När du gör redundanstest för en virtuell dator, visas också tidsstämpeln för den senaste appkonsekventa återställningspunkten. Om du genomför redundans för en återställningsplan går du till en enskild virtuell dator och titta på **senaste återställningspunkter** panelen för att få den här informationen.
    1.  **Senaste multi-VM bearbetas**: det här alternativet är bara tillgängligt för återställningsplaner som har minst en virtuell dator med konsekvens på. Virtuella datorer som är en del av en replikering gruppväxling till den senaste vanliga Konsekvens programkonsekvent återställningspunkten för programkatalog. Andra virtuella datorer redundans till sina senaste bearbetade återställningspunkten.  
    1.  **Senaste multi-VM appkonsekvent**: det här alternativet är bara tillgängligt för återställningsplaner som har minst en virtuell dator med flera virtuella datorer konsekvens vidare. Virtuella datorer som ingår i en grupp replikeringsredundansen till den senaste vanliga Konsekvens programkonsekventa återställningspunkten för programkatalog. Andra virtuella datorer redundans till sina senaste programkonsekventa återställningspunkten.
    1.  **Anpassad**: Om du genomför testning av redundans för en virtuell dator så kan du använda det här alternativet ska gå över till en specifik återställningspunkt.

    > [!NOTE]
    > Möjlighet att välja en återställningspunkt är endast tillgänglig när du redundansväxla till Azure.
    >
    >


1. Om några av de virtuella datorerna i återställningsplanen som växlades över i en tidigare körning och nu virtuella datorer är aktiva på både käll- och plats, kan du använda **ändra riktning** alternativet för att bestämma vilken riktning där den redundans ska inträffa.
1. Om du växlar över till Azure och datakryptering är aktiverat för molnet (gäller endast när du har skyddat Hyper-v-datorer från en VMM-Server) i **krypteringsnyckeln** välja det certifikat som utfärdades när du har aktiverat datakryptering under installationen på VMM-servern.
1. Välj **avställning datorn innan du påbörjar redundans** om du vill använda Site Recovery för att stänga av virtuella källdatorer innan du utlöser redundansväxlingen. Redundansväxlingen fortsätter även om avställning misslyckas.  

    > [!NOTE]
    > Om Hyper-v-datorer skyddas, försöker avställning också kan synkronisera lokala data som inte har ännu har skickats till tjänsten innan du utlöser redundansväxlingen.
    >
    >

1. Du kan följa redundansförloppet på sidan **Jobb**. Även om fel inträffar under en oplanerad redundans, körs återställningsplanen tills den är klar.
1. Efter redundansväxlingen verifierar du den virtuella datorn genom att logga in på den. Om du vill växla till en annan återställningspunkt för den virtuella datorn så du kan använda **ändra återställningspunkt** alternativet.
1. När du kontrollerat den redundansväxlade virtuella datorn kan du **Bekräfta** redundansväxlingen. **Commit tar bort alla återställningspunkter som är tillgängliga med tjänsten** och **ändra återställningspunkt** alternativet är inte längre tillgänglig.

## <a name="planned-failover"></a>Planerad redundans
Virtuella datorer/fysiska servrar som skyddas med Site Recovery också stöd för **planerad redundans**. Planerad redundans är ett noll dataförlust redundans alternativ. När en planerad redundans utlöses först virtuella källdatorer är avställning, senaste data synkroniseras och sedan en redundansväxling har utlösts.

> [!NOTE]
> Under en redundansväxling av Hyper-v-datorer från en lokal plats till en annan lokal plats till gå tillbaka till den primära lokala platsen måste du först **omvänd replikering** den virtuella datorn tillbaka till primär plats och sedan utlösa redundans. Om den primära virtuella datorn inte är tillgängligt sedan innan du startar till **omvänd replikering** du måste återställa den virtuella datorn från en säkerhetskopia.   
>
>
## <a name="failover-job"></a>Redundansjobbet skapades

![Redundans](./media/site-recovery-failover/FailoverJob.png)

När en redundansväxling har utlösts omfattar följande steg:

1. Kravkontroll: det här steget säkerställer att alla villkor som krävs för redundans är uppfyllda
1. Redundans: Det här steget bearbetar data och gör det är redo så att du kan skapa en Azure virtuell dator ut från den. Om du har valt **senaste** återställningspunkt, det här steget skapar en återställningspunkt från data som har skickats till tjänsten.
1. Start: Det här steget skapar en Azure virtuell dator med hjälp av data som behandlas i föregående steg.

> [!WARNING]
> **Avbryt inte en pågående redundans**: innan redundansen startas så stoppas replikeringen för den virtuella datorn. Om du **Avbryt** ett jobb pågår, redundans så stoppas, men den virtuella datorn startar inte att replikera. Det går inte att starta replikering igen.
>
>

## <a name="time-taken-for-failover-to-azure"></a>Tidsåtgång för redundans till Azure

I vissa fall kräver redundans för virtuella datorer ett extra steg som vanligtvis tar cirka 8 till 10 minuter för att slutföra. I följande fall den tid det tar att redundans kommer att vara högre än vanligt:

* VMware-datorer med hjälp av mobilitetstjänsten version som är äldre än 9.8
* Fysiska servrar
* VMware Linux-datorer
* Hyper-V virtuella datorer som skyddas som fysiska servrar
* VMware-datorer där följande drivrutiner inte finns som startdrivrutiner
    * storvsc
    * VMBus
    * storflt
    * Intelide
    * ATAPI
* VMware-datorer som inte har DHCP-tjänsten aktiveras oavsett om de använder DHCP eller statiska IP-adresser

Den här mellanliggande steg krävs inte i alla andra fall, och den tid det tar för redundans är lägre.





## <a name="using-scripts-in-failover"></a>Med hjälp av skript i redundanskluster
Du kanske vill automatisera vissa åtgärder när du gör en redundansväxling. Du kan använda skript eller [Azure automation-runbooks](site-recovery-runbook-automation.md) i [återställningsplaner](site-recovery-create-recovery-plans.md) du gör.

## <a name="post-failover-considerations"></a>Publicera tänka på vid
Efter redundans du beakta följande rekommendationer:
### <a name="retaining-drive-letter-after-failover"></a>Behåller enhetsbeteckningen efter redundans
Om du vill behålla enhetsbeteckning på virtuella datorer efter en redundansväxling kan du ställa in den **SAN-princip** för den virtuella datorn till **OnlineAll**. [Läs mer](https://support.microsoft.com/en-us/help/3031135/how-to-preserve-the-drive-letter-for-protected-virtual-machines-that-are-failed-over-or-migrated-to-azure).



## <a name="next-steps"></a>Nästa steg

> [!WARNING]
> När du har redundansväxlat virtuella datorer och lokala Datacenter är tillgängligt, bör du [ **återaktivera skyddet av** ](vmware-azure-reprotect.md) virtuella VMware-datorer tillbaka till det lokala datacentralen.

Använd [ **planerad redundans** ](hyper-v-azure-failback.md) alternativet att **återställning efter fel** Hyper-v-datorer till en lokal från Azure.

Om du inte har över ett Hyper-v virtuell dator till en annan lokala datacenter som hanteras av en VMM-server och det primära datacentralen är tillgänglig kan sedan använda **omvänd replikering** möjlighet att starta replikering tillbaka till den primära data Center.
