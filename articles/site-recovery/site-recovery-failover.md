---
title: Redundans vid haveri beredskap med Azure Site Recovery | Microsoft Docs
description: Läs mer om att redundansväxla virtuella datorer och fysiska servrar under haveri beredskap med Azure Site Recovery-tjänsten.
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: article
ms.date: 06/30/2019
ms.author: raynew
ms.openlocfilehash: da55d83665792f6ea2f4c78aa2a6c3ca26c39233
ms.sourcegitcommit: 49c4b9c797c09c92632d7cedfec0ac1cf783631b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/05/2019
ms.locfileid: "70383191"
---
# <a name="fail-over-vms-and-physical-servers"></a>Redundansväxla virtuella datorer och fysiska servrar 

Den här artikeln beskriver hur du kan redundansväxla virtuella datorer och fysiska servrar som skyddas av Site Recovery.

## <a name="prerequisites"></a>Förutsättningar
1. Innan du gör en redundansväxling gör du ett [redundanstest för att se till att](site-recovery-test-failover-to-azure.md) allt fungerar som förväntat.
1. [Förbered nätverket](site-recovery-network-design.md) på mål platsen innan du gör en redundansväxling.  

Använd följande tabell för att veta om de alternativ för redundans som tillhandahålls av Azure Site Recovery. De här alternativen visas också för olika scenarier för växling vid fel.

| Scenario | Program återställnings krav | Arbets flöde för Hyper-V | Arbets flöde för VMware
|---|--|--|--|
|Planerad redundansväxling på grund av ett kommande Data Center avbrott| Ingen data förlust för programmet när en planerad aktivitet utförs| För Hyper-V replikerar ASR data med en kopierings frekvens som anges av användaren. Planerad redundans används för att åsidosätta frekvensen och replikera de slutliga ändringarna innan en redundansväxling initieras. <br/> <br/> 1. Planera en underhålls period enligt företagets ändrings hanterings process. <br/><br/> 2. Meddela användare om kommande stillestånds tid. <br/><br/> 3. Ta det användarspecifika programmet offline.<br/><br/>4. Initiera Planerad redundans med hjälp av ASR-portalen. Den lokala virtuella datorn stängs av automatiskt.<br/><br/>Effektiv program data förlust = 0 <br/><br/>En journal över återställnings punkter tillhandahålls också i ett kvarhållningsperiod för en användare som vill använda en äldre återställnings punkt. (24 timmar kvarhållning för Hyper-V). Om replikeringen har stoppats utanför tids perioden för kvarhållning fönstret kan kunder fortfarande redundansväxla med de senaste tillgängliga återställnings punkterna. | För VMware replikerar ASR data kontinuerligt med CDP. Redundansväxlingen ger användaren möjlighet att redundansväxla till senaste data (inklusive avstängnings programmet för inlägg)<br/><br/> 1. Planera en underhålls period enligt processen för ändrings hantering <br/><br/>2. meddela användare om kommande stillestånds tid <br/><br/>3. Ta det användarspecifika programmet offline.<br/><br/>4. Starta en planerad redundansväxling med hjälp av ASR-portalen till den senaste punkten när programmet är offline. Använd alternativet "Planerad redundans" på portalen och välj den senaste punkten för redundans. Den lokala virtuella datorn stängs av automatiskt.<br/><br/>Effektiv program data förlust = 0 <br/><br/>En journal över återställnings punkter i ett kvarhållningsperiod tillhandahålls för en kund som vill använda en äldre återställnings punkt. (72 timmar kvarhållning för VMware). Om replikeringen har stoppats utanför tids perioden för kvarhållning fönstret kan kunder fortfarande redundansväxla med de senaste tillgängliga återställnings punkterna.
|Redundansväxling på grund av ett oplanerat Data Center avbrott (naturlig eller IT-olycka) | Minimal data förlust för programmet | 1. Initiera organisationens BCP-plan <br/><br/>2. Initiera oplanerad redundansväxling med hjälp av ASR-portalen till den senaste eller en punkt från fönstret kvarhållning (journal).| 1. Initiera organisationens BCP-plan. <br/><br/>2. Initiera oplanerad redundansväxling med hjälp av ASR-portalen till den senaste eller en punkt från fönstret kvarhållning (journal).


## <a name="run-a-failover"></a>Köra en redundansväxling
Den här proceduren beskriver hur du kör en redundansväxling för en [återställnings plan](site-recovery-create-recovery-plans.md). Du kan också köra redundansväxlingen för en enskild virtuell dator eller fysisk server från sidan **replikerade objekt** enligt beskrivningen [här](vmware-azure-tutorial-failover-failback.md#run-a-failover-to-azure).


![Redundans](./media/site-recovery-failover/Failover.png)

1. Välj **återställnings planer** > *recoveryplan_name*. Klicka på **redundans**
2. På sidan **redundans** väljer du en **återställnings punkt** att redundansväxla till. Du kan välja något av följande alternativ:
   1. **Senaste**: Det här alternativet startar jobbet genom att först bearbeta alla data som har skickats till Site Recovery-tjänsten. Bearbetning av data skapar en återställnings punkt för varje virtuell dator. Den här återställnings punkten används av den virtuella datorn under redundansväxlingen. Det här alternativet ger det lägsta återställnings punkt målet (återställnings punkt målet) som den virtuella datorn som skapades efter redundansväxlingen har alla data som har repliker ATS till Site Recovery tjänsten när redundansväxlingen utlöstes.
   1. **Senaste bearbetade**: Det här alternativet växlar över alla virtuella datorer i återställnings planen till den senaste återställnings punkten som redan har bearbetats av Site Recoverys tjänsten. När du gör en redundanstest för en virtuell dator visas också tidsstämpeln för den senaste bearbetade återställnings punkten. Om du gör en återställning av en återställnings plan kan du gå till en enskild virtuell dator och titta på den **senaste återställnings punkt** panelen för att få den här informationen. Eftersom ingen tid har för att bearbeta obearbetade data ger det här alternativet ett failover-alternativ för låg RTO (återställnings tid).
   1. **Senaste appkonsekventa**: Det här alternativet växlar över alla virtuella datorer i återställnings planen till den senaste tillämpnings programmets konsekventa återställnings punkt som redan har bearbetats av Site Recoverys tjänsten. När du gör en redundanstest för en virtuell dator visas även tidsstämpeln för den senaste programkonsekventa återställnings punkten. Om du gör en återställning av en återställnings plan kan du gå till en enskild virtuell dator och titta på den **senaste återställnings punkt** panelen för att få den här informationen.
   1. **Senaste bearbetade multi-VM**: Det här alternativet är bara tillgängligt för återställnings planer som har minst en virtuell dator med konsekvens för flera virtuella datorer. Virtuella datorer som är en del av en redundansväxling av en replikeringsgrupp till den senaste vanliga återställnings punkten med flera virtuella datorer. Andra virtuella datorer redundansväxlas till den senaste bearbetade återställnings punkten.  
   1. **Senaste app med flera virtuella datorer – konsekvent**: Det här alternativet är bara tillgängligt för återställnings planer som har minst en virtuell dator med konsekvens för flera virtuella datorer. Virtuella datorer som är en del av en redundansväxling av en replikeringsgrupp till den senaste vanliga programkonsekventa multi-VM-programkonsekventa återställnings punkten. Andra virtuella datorer redundansväxlas till den senaste programkonsekventa återställnings punkten.
   1. **Anpassat**: Om du utför redundanstest för en virtuell dator kan du använda det här alternativet för att redundansväxla till en viss återställnings punkt.

      > [!NOTE]
      > Alternativet att välja en återställnings punkt är bara tillgängligt när du växlar över till Azure.
      >
      >


1. Om några av de virtuella datorerna i återställnings planen har redundansväxlats i en tidigare körning och nu är de virtuella datorerna aktiva på både käll-och mål platsen, kan du använda alternativet **ändra riktning** för att bestämma i vilken riktning redundansväxlingen ska ske.
1. Om du växlar över till Azure och data kryptering har Aktiver ATS för molnet (gäller endast när du har skyddade virtuella Hyper-v-datorer från en VMM-Server), i **krypterings nyckel** väljer du det certifikat som utfärdades när du aktiverade data kryptering under installations programmet på VMM-servern.
1. Välj **Stäng datorn innan du påbörjar redundans** om du vill att Site Recovery försöker stänga av virtuella käll datorer innan du utlöser redundansväxlingen. Redundansväxlingen fortsätter även om avstängningen Miss lyckas.  

    > [!NOTE]
    > Om virtuella Hyper-v-datorer är skyddade försöker alternativet att stänga av också att synkronisera de lokala data som ännu inte har skickats till tjänsten innan redundansväxlingen utlöses.
    >
    >

1. Du kan följa redundansförloppet på sidan **Jobb**. Även om fel uppstår under en oplanerad redundansväxling, körs återställnings planen tills den är klar.
1. Efter redundansväxlingen kontrollerar du den virtuella datorn genom att logga in på den. Om du vill växla till en annan återställnings punkt på den virtuella datorn kan du använda alternativet **ändra återställnings punkt** .
1. När du kontrollerat den redundansväxlade virtuella datorn kan du **Bekräfta** redundansväxlingen. **Commit tar bort alla återställnings punkter som är tillgängliga med tjänsten** och **ändra återställnings punkt** är inte längre tillgängliga.

## <a name="planned-failover"></a>Planerad redundans
Virtuella datorer/fysiska servrar som skyddas med Site Recovery också stöd för **planerad redundansväxling**. Planerad redundans är inget alternativ för redundans av data förlust. När en planerad redundansväxling utlöses stängs först de virtuella käll datorerna, den senaste informationen synkroniseras och sedan utlöses redundansväxlingen.

> [!NOTE]
> Vid redundansväxling av virtuella Hyper-v-datorer från en lokal plats till en annan lokal plats, för att komma tillbaka till den primära lokala platsen måste du först **Replikera** den virtuella datorn tillbaka till den primära platsen och sedan utlösa en redundansväxling. Om den primära virtuella datorn inte är tillgänglig måste du återställa den virtuella datorn från en säkerhets kopia innan du börjar **omvänd replikering** .   
 
 
## <a name="failover-job"></a>Redundansväxla jobb

![Redundans](./media/site-recovery-failover/FailoverJob.png)

När en redundansväxling utlöses innebär det följande steg:

1. Krav kontroll: Det här steget säkerställer att alla villkor som krävs för redundans är uppfyllda
1. ( Det här steget bearbetar data och gör det klart så att en virtuell Azure-dator kan skapas. Om du har valt den **senaste** återställnings punkten skapar det här steget en återställnings punkt från de data som har skickats till tjänsten.
1. Start: Det här steget skapar en virtuell Azure-dator med hjälp av de data som bearbetades i föregående steg.

> [!WARNING]
> **Avbryt inte en pågående redundansväxling**: Replikeringen av den virtuella datorn stoppas innan redundansväxlingen har startats. Om du **avbryter** ett pågående jobb stoppas redundansväxlingen, men den virtuella datorn kommer inte att börja replikera. Det går inte att starta replikeringen igen.
>
>

## <a name="time-taken-for-failover-to-azure"></a>Åtgången tid för redundans till Azure

I vissa fall kräver redundansväxlingen av virtuella datorer ett extra mellanliggande steg som vanligt vis tar cirka 8 till 10 minuter att slutföra. I följande fall är tiden som det tar att redundansväxla vara högre än vanligt:

* Virtuella VMware-datorer som använder mobilitets tjänsten av version äldre än 9,8
* Fysiska servrar
* Virtuella VMware Linux-datorer
* Virtuella Hyper-V-datorer som skyddas som fysiska servrar
* Virtuella VMware-datorer där följande driv rutiner inte finns som start driv rutiner
    * storvsc
    * vmbus
    * storflt
    * intelide
    * ATAPI
* Virtuella VMware-datorer som inte har DHCP-tjänster aktiverade oavsett om de använder DHCP eller statiska IP-adresser

I alla andra fall krävs inte det här mellanliggande steget och den tid det tar för redundansen är lägre.

## <a name="using-scripts-in-failover"></a>Använda skript i redundans
Du kanske vill automatisera vissa åtgärder när du gör en redundansväxling. Du kan använda skript eller [Azure Automation-runbooks](site-recovery-runbook-automation.md) i [återställnings planer](site-recovery-create-recovery-plans.md) för att göra det.

## <a name="post-failover-considerations"></a>Överväganden efter redundans
Efter redundans kanske du vill överväga följande rekommendationer:
### <a name="retaining-drive-letter-after-failover"></a>Behåll enhets beteckning efter redundans
Azure Site Recovery hanterar kvarhållning av enhets beteckningar. [Läs mer](vmware-azure-exclude-disk.md#example-1-exclude-the-sql-server-tempdb-disk) om hur det görs när du väljer att undanta vissa diskar.

## <a name="prepare-to-connect-to-azure-vms-after-failover"></a>Förbereda för att ansluta till virtuella Azure-datorer efter en redundansväxling

Om du vill ansluta till virtuella Azure-datorer med RDP/SSH efter en redundansväxling följer du kraven som sammanfattas i tabellen [här](site-recovery-test-failover-to-azure.md#prepare-to-connect-to-azure-vms-after-failover).

Följ stegen som beskrivs [här](site-recovery-failover-to-azure-troubleshoot.md) för att felsöka eventuella anslutningsproblem efter redundans.


## <a name="next-steps"></a>Nästa steg

> [!WARNING]
> När du har växlat över virtuella datorer och det lokala data centret är tillgängligt bör du [**skydda**](vmware-azure-reprotect.md) virtuella VMware-datorer igen till det lokala data centret.

Använd alternativet [**Planerad redundans**](hyper-v-azure-failback.md) för att återställa **virtuella Hyper-** v-datorer tillbaka till lokala enheter från Azure.

Om du har växlat över en virtuell Hyper-v-dator till ett annat lokalt Data Center som hanteras av en VMM-Server och det primära data centret är tillgängligt använder du alternativet **omvänd replikering** för att starta replikeringen tillbaka till det primära data centret.
