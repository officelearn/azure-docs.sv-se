---
title: Hana redundansprocedur till en katastrofplats för SAP HANA på Azure (Stora instanser) | Microsoft-dokument
description: Så här utför du redundans till en haveriberedskapsplats för SAP HANA på Azure (stora instanser)
services: virtual-machines-linux
documentationcenter: ''
author: saghorpa
manager: juergent
editor: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 04/22/2019
ms.author: saghorpa
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 3fe3ee79318ab9fdc9f2c0e9585051439b76b5cf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "77617141"
---
# <a name="disaster-recovery-failover-procedure"></a>Redundans för haveriberedskap


>[!IMPORTANT]
>Den här artikeln ersätter inte SAP HANA-administrationsdokumentationen eller SAP Notes. Vi förväntar oss att du har en gedigen förståelse för och expertis inom SAP HANA administration och drift, särskilt för säkerhetskopiering, återställning, hög tillgänglighet och katastrofåterställning (DR). I den här artikeln visas skärmdumpar från SAP HANA Studio. Innehåll, struktur och arten av skärmarna av SAP administrationsverktyg och verktygen själva kan ändras från SAP HANA release till release.

Det finns två fall att tänka på när du växlar över till en DR-plats:

- Du behöver SAP HANA-databasen för att gå tillbaka till den senaste statusen för data. I det här fallet finns det ett självbetjäningsskript som du kan utföra redundansen med utan att behöva kontakta Microsoft. För återställningen av återställningen måste du arbeta med Microsoft.
- Du vill återställa till en ögonblicksbild av lagring som inte är den senaste replikerade ögonblicksbilden. I det här fallet måste du arbeta med Microsoft. 

>[!NOTE]
>Följande steg måste göras på HANA Large Instance-enheten, som representerar DR-enheten. 
 
Om du vill återställa till de senaste replikerade lagringsögonblicksbilderna följer du stegen i "Utför fullständig DR-redundans – azure_hana_dr_failover" i [Microsofts ögonblicksbildverktyg för SAP HANA på Azure](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/snapshot_tools_v4.2/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20v4.2.1.pdf). 

Om du vill att flera SAP HANA-instanser ska kunna gå över kör du kommandot azure_hana_dr_failover flera gånger. Ange det SAP HANA SID som du vill växla över och återställa på begäran. 


Du kan testa DR-redundansen också utan att påverka den faktiska replikeringsrelationen. Om du vill utföra en testväxling följer du stegen i "Utför en testdr-redundans – azure_hana_test_dr_failover" i [Microsofts snapshot-verktyg för SAP HANA på Azure](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/snapshot_tools_v4.2/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20v4.2.1.pdf). 

>[!IMPORTANT]
>Kör *inga* produktionstransaktioner på den instans som du skapade på DR-platsen genom att **testa en redundans.** Kommandot azure_hana_test_dr_failover skapar en uppsättning volymer som inte har någon relation till den primära platsen. Därför är synkronisering tillbaka till den primära platsen *inte* möjlig. 

Om du vill att flera SAP HANA-instanser ska testas kör du skriptet flera gånger. Ange SAP HANA SID för den instans som du vill testa för redundans när det begärs. 

>[!NOTE]
>Om du behöver växla över till DR-platsen för att rädda vissa data som togs bort för flera timmar sedan och behöver DR-volymerna för att ställas in på en tidigare ögonblicksbild, gäller den här proceduren. 

1. Stäng av icke-produktionsinstansen för HANA på haveriberedskapsenheten för STORA HANA-instanser som du kör. En vilande HANA-produktionsinstans är förinstallerad.
1. Kontrollera att inga SAP HANA-processer körs. Använd följande kommando för den här kontrollen:

      `/usr/sap/hostctrl/exe/sapcontrol –nr <HANA instance number> - function GetProcessList`.

      Utdata ska visa **dig hdbdaemonprocessen** i ett stoppat tillstånd och inga andra HANA-processer i kör eller startat tillstånd.
1. Ta reda på vilket användarnamn eller SAP HANA-säkerhetskopierings-ID du vill att platsen för haveriberedskap ska återställas. I verkliga katastrofåterställningsfall är den här ögonblicksbilden vanligtvis den senaste ögonblicksbilden. Om du behöver återställa förlorade data väljer du en tidigare ögonblicksbild.
1. Kontakta Azure Support via en supportbegäran med hög prioritet. Be om återställning av ögonblicksbilden med namn och datum för ögonblicksbilden eller HANA-säkerhetskopierings-ID på DR-platsen. Standard är att operationssidan återställer /hana/datavolymen endast. Om du vill ha volymerna /hana/logbackups också måste du ange det specifikt. *Återställ inte volymen /hana/shared.* Välj i stället specifika filer som global.ini från **.snapshot-katalogen** och dess underkataloger när du har återmontera volymen /hana/shared för PRD. 

   På verksamhetssidan inträffar följande steg:

   a. Replikeringen av ögonblicksbilder från produktionsvolymen till katastrofåterställningsvolymerna stoppas. Den här störningen kan redan ha inträffat om ett avbrott på produktionsplatsen är orsaken till att du behöver utföra haveriberedskapsproceduren.
   
   b. Namnet på ögonblicksbilden för lagring eller ögonblicksbild med det säkerhetskopierings-ID som du valde återställs på katastrofåterställningsvolymerna.
   
   c. Efter återställningen är katastrofåterställningsvolymerna tillgängliga för att monteras på HANA-enheter för stora instans i katastrofåterställningsregionen.
      
1. Montera katastrofåterställningsvolymerna till HANA Large Instance-enheten på haveriberedskapsplatsen. 
1. Starta den vilande SAP HANA-produktionsinstansen.
1. Om du väljer att kopiera säkerhetskopieringsloggar för transaktionsloggar för att minska RPO-tiden sammanfogar du säkerhetskopiorna för transaktionsloggen till den nyligen monterade KATALOGEN DR /hana/logbackups. Skriv inte över befintliga säkerhetskopior. Kopiera nyare säkerhetskopior som inte replikerades med den senaste replikeringen av en ögonblicksbild av lagring.
1. Du kan också återställa enskilda filer från ögonblicksbilder som inte replikerades till volymen /hana/shared/PRD i REGIONEN DR Azure.

Följande steg visar hur du återställer SAP HANA-produktionsinstansen baserat på den återställda lagringsögonblicksbilden och säkerhetskopiering av transaktionsloggen som är tillgängliga.

1. Ändra säkerhetskopieringsplatsen till **/hana/logbackups** med hjälp av SAP HANA Studio.

   ![Ändra säkerhetskopieringsplatsen för DR-återställning](./media/hana-overview-high-availability-disaster-recovery/change_backup_location_dr1.png)

1. SAP HANA söker igenom säkerhetskopieringsfilplatserna och föreslår den senaste säkerhetskopieringen av transaktionsloggen att återställa till. Genomsökningen kan ta några minuter tills en skärm som följande visas:

   ![Lista över säkerhetskopior av transaktionsloggen för DR-återställning](./media/hana-overview-high-availability-disaster-recovery/backup_list_dr2.PNG)

1. Justera några av standardinställningarna:

      - Rensa **Använd Delta-säkerhetskopior**.
      - Välj **Initiera loggområde**.

   ![Ange initieringsloggområdet](./media/hana-overview-high-availability-disaster-recovery/initialize_log_dr3.PNG)

1. Välj **Slutför**.

   ![Avsluta DR-återställningen](./media/hana-overview-high-availability-disaster-recovery/finish_dr4.PNG)

Ett förloppsfönster, som det som visas här, ska visas. Tänk på att exemplet är en återställning av haveriberedskap för en UTSKALNINGS-SAP HANA-konfiguration med tre nod.

![Återställ förloppet](./media/hana-overview-high-availability-disaster-recovery/restore_progress_dr5.PNG)

Om återställningen slutar svara på skärmen **Slutför** och inte visar förloppsskärmen bekräftar du att alla SAP HANA-instanser på arbetsnoderna körs. Starta SAP HANA-instanserna manuellt om det behövs.


## <a name="failback-from-a-dr-to-a-production-site"></a>Återställning från en DR till en produktionsanläggning
Du kan växla tillbaka från en DR till en produktionsplats. Låt oss titta på ett scenario där redundansen till platsen för haveriberedskap orsakades av problem i azure-regionen för produktion och inte av ditt behov av att återställa förlorade data. 

Du har kört din SAP-produktionsarbetsbelastning ett tag på haveriberedskapsplatsen. När problemen på produktionsplatsen har lösts vill du växla tillbaka till produktionsplatsen. Eftersom du inte kan förlora data innebär steget tillbaka till produktionsplatsen flera steg och nära samarbete med SAP HANA i Azure-operationsteamet. Det är upp till dig att utlösa operationsteamet för att börja synkronisera tillbaka till produktionsplatsen när problemen har lösts.

Följ de här stegen:

1. SAP HANA på Azure-driftteamet hämtar utlösaren för att synkronisera produktionslagringsvolymerna från lagringsvolymerna för haveriberedskap, som nu representerar produktionstillståndet. I det här läget stängs hana-enheten för stora instans på produktionsplatsen.
1. SAP HANA på Azure-driftteamet övervakar replikeringen och ser till att den fångas upp innan de informerar dig.
1. Du stänger av program som använder produktions-HANA-instansen på haveriberedskapsplatsen. Du utför sedan en HANA-transaktionsloggsäkerhetskopiering. Därefter stoppar du HANA-instansen som körs på HANA-enheter för stora instanser på haveriberedskapsplatsen.
1. När HANA-instansen som körs i HANA-enheten för stor instans på haveriberedskapsplatsen har stängts av synkroniseras diskvolymerna manuellt igen.
1. SAP HANA i Azure-driftteamet startar HANA Large Instance-enheten på produktionsplatsen igen. De lämnar över den till dig. Du kontrollerar att SAP HANA-instansen är i ett avstängningstillstånd vid starttiden för HANA Large Instance-enheten.
1. Du utför samma steg för återställning av databaser som du gjorde när du tidigare misslyckades med att återställa haveri.

## <a name="monitor-disaster-recovery-replication"></a>Övervaka replikering av haveriberedskap

Om du vill övervaka statusen för `azure_hana_replication_status`lagringsreplikeringsförloppet kör du skriptet . Det här kommandot måste köras från en enhet som körs på haveriberedskapsplatsen för att fungera som förväntat. Kommandot fungerar oavsett om replikeringen är aktiv. Kommandot kan köras för varje HANA-stor instansenhet för din klient på platsen för haveriberedskap. Den kan inte användas för att få information om startvolymen. 

Mer information om kommandot och dess utdata finns i "Hämta DR-replikeringsstatus - azure_hana_replication_status" i [Microsofts ögonblicksbildverktyg för SAP HANA på Azure](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/snapshot_tools_v4.2/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20v4.2.1.pdf).


## <a name="next-steps"></a>Nästa steg
- Se [Övervaka och felsöka från HANA-sidan](hana-monitor-troubleshoot.md).
