---
title: HANA redundans proceduren till katastrofåterställningsplatsen för SAP HANA på Azure (stora instanser) | Microsoft Docs
description: Hur du utför redundans till plats för katastrofåterställning för SAP HANA på Azure (stora instanser)
services: virtual-machines-linux
documentationcenter: ''
author: saghorpa
manager: jeconnoc
editor: ''
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 04/22/2019
ms.author: saghorpa
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 76d8bb816bdf229d13a49fa61337899a8bf29ecd
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2019
ms.locfileid: "64685110"
---
# <a name="disaster-recovery-failover-procedure"></a>Redundans för haveriberedskap


>[!IMPORTANT]
>Den här dokumentationen är ingen ersättning av dokumentationen för administration av SAP HANA eller SAP Notes. Det förväntas att läsaren har en djupare förståelse av och expertis i SAP HANA-administration och åtgärder, särskilt med ämnen för säkerhetskopiering, återställning, hög tillgänglighet och katastrofåterställning. I den här dokumentationen visas skärmbilder från SAP HANA-Studio. Innehåll, struktur och arten av de olika stegen i SAP-verktyg för fjärrserveradministration och verktyg själva ändras från SAP HANA version till version.

Det finns två fall att tänka på när redundansväxla till DR-plats:

- Du måste SAP HANA-databas för att gå tillbaka till den senaste statusen för data. Det finns i det här fallet ett självbetjäning skript som du kan utföra redundans utan att behöva kontakta Microsoft. Men för återställning efter fel behöver du arbetar med Microsoft.
- Du vill återställa till en storage-ögonblicksbilder som inte är den senaste replikerade ögonblicksbilden. I det här fallet behöver du arbetar med Microsoft. 

>[!NOTE]
>Följande steg måste utföras på enheten stora HANA-instansen, som representerar den DR-enheten. 
 
Om du vill återställa till de senaste replikerad lagring ögonblicksbilderna, utföra stegen som anges i avsnittet **'Utföra fullständig DR-redundans - azure_hana_dr_failover'** dokumentets [Microsoft ögonblicksbild verktyg för SAP HANA på Azure ](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/snapshot_tools_v4.0/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20v4.0.pdf). 

Om du vill ha flera SAP HANA-instanser som redundansväxlas måste köra kommandot azure_hana_dr_failover flera gånger. När begäran Skriv i SAP HANA SID du vill redundansväxla och återställa. 


Du kan också testa DR-redundans utan att påverka faktiska replikeringens relation. För att genomföra ett redundanstest, följer du stegen i **'Utföra ett test DR-redundans - azure_hana_test_dr_failover'** dokumentets [Microsoft ögonblicksbild verktyg för SAP HANA på Azure](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/snapshot_tools_v4.0/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20v4.0.pdf). 

>[!IMPORTANT]
>Gör *inte* kör alla transaktioner i produktions på den instans som du skapade i DR-plats genom processen att **en redundanstestning**. Den azure_hana_test_dr_failover kommandot skapar en uppsättning volymer som inte har någon relation till den primära platsen. Därför kan synkroniseringen tillbaka till den primära platsen är *inte* möjligt. 

Om du vill ha flera SAP HANA-instanser för att testa måste att köra skriptet flera gånger. När begäran Skriv i SAP HANA SID för den instans som du vill testa för redundans. 

>[!NOTE]
>Den här proceduren gäller om du vill växla över till DR-plats att rädda vissa data skedde timmar sedan och behöver DR-volymer anges till en tidigare ögonblicksbild. 

1. Stänga av icke-produktion-instansen för HANA på stora HANA-instanser som du kör disaster recovery-enheten. Det beror på att det finns en vilande HANA produktion instans som redan är installerat.
1. Se till att inga SAP HANA-processer körs. Använd följande kommando för den här kontrollen: `/usr/sap/hostctrl/exe/sapcontrol –nr <HANA instance number> - function GetProcessList`. Utdata ska visa den **hdbdaemon** bearbeta i ett stoppat tillstånd och inga andra HANA processer som körs eller igång nu.
1. Avgör vilken ögonblicksbild namn eller SAP HANA säkerhetskopie-ID som du vill ha katastrofåterställningsplatsen återställs. Den här ögonblicksbilden är vanligtvis den senaste ögonblicksbilden i verkliga disaster recovery fall. Om du vill återställa förlorade data kan du välja en tidigare ögonblicksbild.
1. Kontakta supporten för Azure genom en supportförfrågan med hög prioritet. Be om att återställa säkerhetskopian av den ögonblicksbilden (med namn och datum för ögonblicksbilden) eller HANA säkerhetskopie-ID på DR-plats. Standardvärdet är att operations-sida återställer/hana/datavolymen endast. Om du vill ha/hana/logbackups volymerna samt måste du särskilt tillstånd som. *Återställ inte /hana/shared volymen.* I stället bör du välja specifika filer som global.ini av den **.snapshot** mappen och dess undermappar när du återansluta/hana/klusterdelad volym för PRD. 

   På sidan åtgärder utförs följande steg:

   a. Replikering av ögonblicksbilder från produktionsvolymen till disaster recovery volymerna har stoppats. Den här avbrott kan ha hänt redan om ett avbrott på produktionsplatsen är därför du måste utföra katastrofåterställning.
   
   b. Lagringen ögonblicksbild namn eller skapa en ögonblicksbild med säkerhetskopiering-ID du har valt har återställts på disaster recovery-volymer.
   
   c. Efter återställningen, disaster recovery-volymer som är tillgängliga som ska monteras till stora HANA-instansen enheter i regionen disaster recovery.
      
1. Montera disaster recovery-volymer till stora HANA-instansen-enhet i katastrofåterställningsplatsen. 
1. Starta den vilande SAP HANA-instansen för produktion.
1. Om du väljer att kopiera loggen säkerhetskopiering transaktionsloggar för att minska tid som RPO, måste du koppla dessa säkerhetskopieringar av transaktionsloggen till den nyligen monterade DR/hana/logbackups katalogen. Skriv inte över befintliga säkerhetskopior. Kopiera nyare säkerhetskopieringar som inte har replikerats med den senaste replikeringen av storage-ögonblicksbilder.
1. Du kan även återställa enskilda filer bort ögonblicksbilder som har replikerats till /hana/shared/PRD volymen i DR-Azure-region.

Nästa sekvens med steg omfattar återställa SAP HANA produktionsinstansen baserat på den återställda storage-ögonblicksbilden och de säkerhetskopieringar av transaktionsloggen som är tillgängliga:

1. Ändra platsen för säkerhetskopian till **/hana/logbackups** med hjälp av SAP HANA-Studio.
   ![Ändra platsen för säkerhetskopian för DR-återställning](./media/hana-overview-high-availability-disaster-recovery/change_backup_location_dr1.png)

1. SAP HANA skannar hela säkerhetskopian-platser och ger förslag på den senaste transaction log säkerhetskopian för att återställa till. Genomsökningen kan ta några minuter tills en skärm som visas följande: ![Listan över säkerhetskopieringar av transaktionsloggen för DR-återställning](./media/hana-overview-high-availability-disaster-recovery/backup_list_dr2.PNG)

1. Justera några av standardinställningarna:

      - Rensa **Använd Delta säkerhetskopior**.
      - Välj **initiera loggen området**.

   ![Ange området initiera loggen](./media/hana-overview-high-availability-disaster-recovery/initialize_log_dr3.PNG)

1. Välj **Slutför**.

   ![Slut DR-återställning](./media/hana-overview-high-availability-disaster-recovery/finish_dr4.PNG)

Ett fönster för förlopp, som den visas här, bör visas. Tänk på att exemplet är av en disaster recovery-återställning av en konfiguration med tre noder skala ut SAP HANA.

![Återställning pågår](./media/hana-overview-high-availability-disaster-recovery/restore_progress_dr5.PNG)

Om det verkar som om återställningen svarar inte på den **Slutför** skärmen och inte visa förloppsskärm, kontrollera att alla SAP HANA-instanser på arbetsnoderna körs. Om det behövs kan du starta SAP HANA-instanser manuellt.


## <a name="failback-from-a-dr-to-a-production-site"></a>Återställning efter fel från en DR till en produktionsplats
Du kan återställa från en DR till en produktionsplats. Låt oss titta på ett scenario där redundans till katastrofåterställningsplatsen orsakades av problem i Azure-region för produktion, inte av din behovet av att återställa förlorade data. Du har körts din SAP-produktionsarbetsbelastningar ett tag i katastrofåterställningsplatsen. När problemen vid produktionsplatsen löses som du vill växla tillbaka till din produktionsplats. Eftersom du inte förlorar data kan omfattar steg tillbaka till produktionsplatsen flera steg och nära samarbete med SAP HANA på Azure operativa team. Det är upp till dig att utlösa driftsteamet att starta synkroniseringen tillbaka till produktionsplatsen när problemen är lösta.

Det här är de steg du ska vidta:

1. SAP HANA på Azure driftsteamet hämtar utlösaren att synkronisera produktion lagringsvolymer från disaster recovery lagringsvolymer, som nu representerar tillståndet för produktion. I det här tillståndet stängs stora HANA-instansen-enhet i produktionsplatsen av.
1. SAP HANA på Azure driftsteamet övervakar replikeringen och ser till att den har fått allt innan informerar dig.
1. Du kan stänga av de program som använder produktion HANA-instans på katastrofåterställningsplatsen. Sedan gör du en säkerhetskopia av transaktionsloggen HANA. Därefter måste stoppa du HANA-instans som körs på enheter för stora HANA-instansen i katastrofåterställningsplatsen.
1. När den HANA-instansen som körs i stora HANA-instansen-enhet i katastrofåterställningsplatsen är avstängd synkroniserar driftsteamet manuellt diskvolymer igen.
1. SAP HANA på Azure driftsteamet startar enheten stora HANA-instansen i produktionsplatsen igen och lämnar det till dig. Du se till att SAP HANA-instans är i en avstängning vid starten av den stora HANA-instansen-enheten.
1. Du kan utföra samma steg för återställning av databasen som du gjorde när tidigare redundansväxla till platsen för katastrofåterställning.

## <a name="monitor-disaster-recovery-replication"></a>Övervaka disaster recovery-replikering

Du kan övervaka statusen för din lagring replikeringens förlopp genom att köra skriptet `azure_hana_replication_status`. Det här kommandot måste köras från en enhet som körs i disaster recovery-platsen ska fungera som förväntat. Kommandot fungerar oavsett om replikering är aktiv. Kommandot kan köras för varje enhet för stora HANA-instansen för din klient på disaster recovery-plats. Det kan inte användas för att få information om startvolymen. Mer information om kommandot och dess utdata finns **”Get replikeringsstatus för DR - azure_hana_replication_status”** dokumentets [Microsoft ögonblicksbild verktyg för SAP HANA på Azure](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/snapshot_tools_v4.0/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20v4.0.pdf).


## <a name="next-steps"></a>Nästa steg
- Referera till [övervakning och felsökning från HANA sida](hana-monitor-troubleshoot.md).
