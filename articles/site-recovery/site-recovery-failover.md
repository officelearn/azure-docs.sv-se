---
title: Redundans i Site Recovery | Microsoft Docs
description: Azure Site Recovery samordnar replikering, redundans och återställning av virtuella datorer och fysiska servrar. Läs mer om redundans till Azure eller ett sekundärt datacenter.
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: article
ms.date: 05/02/2018
ms.author: ponatara
ms.openlocfilehash: 40f35cde2b55da0763f6ee65b065f5dd8a55b9c6
ms.sourcegitcommit: 870d372785ffa8ca46346f4dfe215f245931dae1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/08/2018
---
# <a name="failover-in-site-recovery"></a>Redundans i Site Recovery
Den här artikeln beskriver hur till redundans virtuella datorer och fysiska servrar som skyddas av Site Recovery.

## <a name="prerequisites"></a>Förutsättningar
1. Innan du gör en redundansväxling gör en [redundanstestningen](site-recovery-test-failover-to-azure.md) så att allt fungerar som förväntat.
1. [Förbered nätverket](site-recovery-network-design.md) på målplatsen innan du gör en redundansväxling.  

Använd följande tabell om du vill ha information om alternativen redundans från Azure Site Recovery. Dessa alternativ visas också olika redundans scenarier.

| Scenario | Återställning av programkrav | Arbetsflöde för Hyper-V | Arbetsflöde för VMware
|---|--|--|--|
|Planerad växling vid fel på grund av ett avbrott för kommande datacenter| Noll dataförlust för programmet när en planerad aktivitet utförs| För Hyper-V replikerar ASR data på en kopieringsfrekvens som anges av användaren. Planerad växling vid fel används för att åsidosätta frekvensen och replikera de slutliga ändringarna innan en redundansväxling initieras. <br/> <br/> 1.    Planera en underhållsperiod enligt företagets processen för ändringshantering. <br/><br/> 2. meddela användare om kommande driftstopp. <br/><br/> 3. Koppla användarinriktad programmet.<br/><br/>4 initiera planerad växling vid fel med hjälp av ASR-portalen. Den lokala virtuella datorn är automatiskt avställning.<br/><br/>Effektiva dataförlust = 0 <br/><br/>En journal återställningspunkter finns också i en kvarhållningsperiod för en användare som vill använda en tidigare återställningspunkt. (24 timmar kvarhållning för Hyper-V).| För VMware replikerar ASR data kontinuerligt med CDP. Redundanskluster ger användaren möjlighet att redundans till den senaste informationen (inklusive efter programmet avställning)<br/><br/> 1. Planera en underhållsperiod enligt av processen för ändringshantering <br/><br/>2. meddela användare om kommande driftstopp <br/><br/>3.    Koppla användarinriktad programmet. <br/><br/>4.  Starta en planerad redundans med hjälp av ASR portal till den senaste tidpunkten när programmet är offline. Använd alternativet ”oplanerad växling” på portalen och väljer den senaste tidpunkten för redundans. Den lokala virtuella datorn är automatiskt avställning.<br/><br/>Effektiva dataförlust = 0 <br/><br/>En journal av återställningspunkter i en kvarhållningsperiod anges för en kund som vill använda en tidigare återställningspunkt. (72 timmar för kvarhållning för VMware).
|Växling vid fel på grund av en oplanerad datacenter avbrottstid (naturliga eller IT-katastrof) | Minimal dataförlust för programmet | 1. initiera organisationens BCP plan <br/><br/>2. Initiera oplanerad växling vid fel med hjälp av ASR portal till senast eller en punkt från kvarhållningsperiod (journal).| 1. Initiera organisationens BCP plan. <br/><br/>2.  Initiera oplanerad växling vid fel med hjälp av ASR portal till senast eller en punkt från kvarhållningsperiod (journal).


## <a name="run-a-failover"></a>Köra en redundansväxling
Den här proceduren beskriver hur du kör en redundansväxling för en [återställningsplanen](site-recovery-create-recovery-plans.md). Du kan också köra redundans för en enskild virtuell dator eller fysisk server från den **replikerade objekt** sidan


![Redundans](./media/site-recovery-failover/Failover.png)

1. Välj **Återställningsplaner** > *recoveryplan_name*. Klicka på **växling vid fel**
2. På den **redundans** väljer en **återställningspunkt** ska gå över till. Du kan välja något av följande alternativ:
    1.  **Senaste** (standard): det här alternativet startar jobbet första behandlar alla data som har skickats till Site Recovery-tjänsten. Bearbetningen av informationen skapar en återställningspunkt för varje virtuell dator. Den här återställningspunkten används av den virtuella datorn under växling vid fel. Det här alternativet ger den lägsta RPO (mål för återställningspunkt) som den virtuella datorn skapas efter växling vid fel har alla data som har replikerats till Site Recovery-tjänsten när redundans utlöstes.
    1.  **Senaste bearbetas**: det här alternativet flyttas över alla virtuella datorer i återställningsplanen så att den senaste återställningspunkten som redan har behandlats av Site Recovery-tjänsten. När du gör testa redundans för en virtuell dator visas också tidsstämpeln för den senaste bearbetade återställningspunkten. Om du gör redundans för en återställningsplan går du till en enskild virtuell dator och titta på **senaste återställningspunkter** rutan för att hämta informationen. Ingen tid för att bearbeta obearbetade data, ger det här alternativet ett alternativ för låga RTO (mål) växling vid fel.
    1.  **Senaste programkonsekventa**: det här alternativet flyttas över alla virtuella datorer i återställningsplanen så att den senaste programkonsekvent återställningspunkt som redan har behandlats av Site Recovery-tjänsten. När du gör testa redundans för en virtuell dator visas också tidsstämpeln för den senaste programkonsekventa återställningspunkten. Om du gör redundans för en återställningsplan går du till en enskild virtuell dator och titta på **senaste återställningspunkter** rutan för att hämta informationen.
    1.  **Senaste multi-VM bearbetas**: det här alternativet är endast tillgängligt för återställningsplaner som har minst en virtuell dator med konsekvens för flera på. Virtuella datorer som är en del av en replikering redundansväxla till den senaste vanliga multi-VM konsekventa återställningspunkten punkt. Andra virtuella datorer växling till sina senaste bearbetade återställningspunkten.  
    1.  **Senaste multi-VM programkonsekventa**: det här alternativet är endast tillgängligt för återställningsplaner som har minst en virtuell dator med multi-VM konsekvenskontroll på. Virtuella datorer som är en del av en replikering redundansväxla till den senaste vanliga multi-VM programkonsekventa återställningspunkten punkt. Andra virtuella datorer redundans till sina senaste programkonsekventa återställningspunkten.
    1.  **Anpassad**: Om du gör testa redundans för en virtuell dator, så du kan använda det här alternativet ska gå över till en viss återställningspunkt.

    > [!NOTE]
    > Möjlighet att välja en återställningspunkt är endast tillgänglig när du redundansväxlar till Azure.
    >
    >


1. Om några av de virtuella datorerna i återställningsplanen har redundansväxlats i en tidigare körning och nu virtuella datorer är aktiva på både käll- och plats, kan du använda **ändra riktning** alternativet för att avgöra i vilken riktning på växling vid fel ska inträffa.
1. Om du växlar över till Azure och datakryptering är aktiverat för molnet (gäller endast när du har skyddat Hyper-v virtuella datorer från en VMM-Server) i **krypteringsnyckeln** Välj det certifikat som utfärdades när du har aktiverat datakryptering under installationen på VMM-servern.
1. Välj **avställning datorn innan du påbörjar redundans** om du vill använda Site Recovery att göra en avstängning av virtuella källdatorer innan växling vid fel. Redundans fortsätter även om avställning misslyckas.  

    > [!NOTE]
    > Om Hyper-v virtuella datorer är skyddade, försöker alternativet att avställning också synkroniserar lokala data som inte har ännu har skickats till tjänsten innan växling vid fel.
    >
    >

1. Du kan följa redundansförloppet på sidan **Jobb**. Även om fel uppstår under en oplanerad redundans körs återställningsplanen tills den är klar.
1. Verifiera den virtuella datorn efter växling vid fel, genom att logga in till den. Om du vill växla till en annan återställningspunkt för den virtuella datorn så att du kan använda **ändra återställningspunkt** alternativet.
1. När du kontrollerat den redundansväxlade virtuella datorn kan du **Bekräfta** redundansväxlingen. **Commit tar bort alla återställningspunkter som är tillgängliga med tjänsten** och **ändra återställningspunkt** alternativet är inte längre tillgänglig.

## <a name="planned-failover"></a>Planerad redundans
Virtuella datorer/fysiska servrar som skyddas med Site Recovery även stöd **planerad redundans**. Planerad redundans är ett noll data går förlorade redundans alternativ. När en planerad redundansväxling initieras först virtuella källdatorer avställning, senaste data synkroniseras och sedan en växling vid fel utlöses.

> [!NOTE]
> Under redundansväxlingen av Hyper-v virtuella datorer från en lokal plats till en annan lokal plats till gå tillbaka till webbplatsen för primär lokal måste du första **omvänd replikering** den virtuella datorn tillbaka till primär plats och sedan Utlös en växling vid fel. Om den primära virtuella datorn inte är tillgängliga innan från att **omvänd replikering** du måste återställa den virtuella datorn från en säkerhetskopia.   
>
>
## <a name="failover-job"></a>Beställningsjobbet

![Redundans](./media/site-recovery-failover/FailoverJob.png)

När en växling vid fel utlöses omfattar följande steg:

1. Kravkontroll: det här steget säkerställer att alla krav som gäller för växling vid fel är uppfyllda.
1. Redundans: Det här steget bearbetar data och gör den redo så att en virtuell Azure-dator kan skapas ur den. Om du har valt **senaste** återställningspunkten det här steget skapar en återställningspunkt från data som har skickats till tjänsten.
1. Starta: Det här steget skapar en virtuell Azure-dator med hjälp av data som bearbetas i föregående steg.

> [!WARNING]
> **Inte avbryta en pågående redundans**: innan redundans startas replikeringen för den virtuella datorn har stoppats. Om du **Avbryt** ett jobb pågår, redundans stoppas, men den virtuella datorn startar inte replikeras. Att det går inte starta replikering igen.
>
>

## <a name="time-taken-for-failover-to-azure"></a>Tidsåtgång för redundans till Azure

I vissa fall kräver redundans för virtuella datorer ett extra steg som tar vanligtvis cirka 8 till 10 minuter för att slutföra. Den tid det tar att redundans kommer vara högre än vanliga i följande fall:

* Med hjälp av mobilitetstjänsten version som är äldre än 9.8 virtuella VMware-datorer
* Fysiska servrar
* VMware Linux virtuella datorer
* Hyper-V virtuella datorer som skyddas som fysiska servrar
* VMware-datorer där följande drivrutiner inte finns som startdrivrutiner
    * storvsc
    * VMBus
    * storflt
    * Intelide
    * ATAPI
* Virtuella VMware-datorer som inte har DHCP-tjänsten aktiveras oavsett om de använder DHCP eller statiska IP-adresser

Detta steg krävs inte i alla andra fall, och den tid det tar för växling vid fel är lägre.





## <a name="using-scripts-in-failover"></a>Med hjälp av skript i redundanskluster
Du kanske vill automatisera vissa åtgärder, medan en växling vid fel. Du kan använda skript eller [Azure automation-runbooks](site-recovery-runbook-automation.md) i [återställningsplaner](site-recovery-create-recovery-plans.md) du gör.

## <a name="post-failover-considerations"></a>Överväganden för efter växling vid fel
Efter växling vid fel som du kanske vill Tänk på följande:
### <a name="retaining-drive-letter-after-failover"></a>Behålla enhetsbeteckning efter växling vid fel
Om du vill behålla enhetsbeteckning på virtuella datorer efter redundans kan du ange den **SAN-princip** för den virtuella datorn till **OnlineAll**. [Läs mer](https://support.microsoft.com/en-us/help/3031135/how-to-preserve-the-drive-letter-for-protected-virtual-machines-that-are-failed-over-or-migrated-to-azure).



## <a name="next-steps"></a>Nästa steg

> [!WARNING]
> När du redundansväxlade virtuella datorer och lokala Datacenter är tillgängligt, bör du [ **skyddar** ](vmware-azure-reprotect.md) virtuella VMware-datorer tillbaka till lokala datacenter.

Använd [ **planerad redundans** ](hyper-v-azure-failback.md) att **återställning** Hyper-v virtuella datorer till lokala från Azure.

Om du har inte över en Hyper-v virtuell dator till en annan lokal datacenter som hanteras av en VMM-server och primära Datacenter är tillgänglig, sedan **omvänd replikering** alternativet för att starta replikering tillbaka till primära data Center.
