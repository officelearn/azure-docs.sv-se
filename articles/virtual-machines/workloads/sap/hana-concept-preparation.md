---
title: Principer för katastrofåterställning och förberedelse på SAP HANA på Azure (stora instanser) | Microsoft-dokument
description: Principer för haveriberedskap och förberedelse på SAP HANA på Azure (stora instanser)
services: virtual-machines-linux
documentationcenter: ''
author: saghorpa
manager: gwallace
editor: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 09/10/2018
ms.author: saghorpa
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 33d52f871de75a7f7d34016b040e44d6f1623fd8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "70101267"
---
# <a name="disaster-recovery-principles"></a>Principer för katastrofåterställning

STORA HANA-instanser erbjuder en funktioner för haveriberedskap mellan HANA Large Instance-stämplar i olika Azure-regioner. Om du till exempel distribuerar STORA HANA-instansenheter i regionen USA west i Azure kan du använda HANA-enheter för stora instanser i regionen ÖSTRA USA som enheter för haveriberedskap. Som tidigare nämnts är haveriberedskap inte konfigurerad automatiskt, eftersom det kräver att du betalar för en annan HANA-enhet för stor instans i DR-regionen. Inställningen för haveriberedskap fungerar för uppskalning och utskalningsinställningar. 

I de scenarier som har distribuerats hittills använder kunderna enheten i DR-regionen för att köra icke-produktionssystem som använder en installerad HANA-instans. Hana-enheten för stora instans måste vara av samma SKU som SKU som används för produktionsändamål. Följande avbildning visar hur diskkonfigurationen mellan serverenheten i Azure-produktionsregionen och katastrofåterställningsregionen ser ut:

![DR-inställningskonfiguration från disksynpunkt](./media/hana-overview-high-availability-disaster-recovery/disaster_recovery_setup.PNG)

Som visas i den här översiktsbilden måste du sedan beställa en andra uppsättning diskvolymer. Måldiskvolymerna har samma storlek som produktionsvolymerna för produktionsinstansen i katastrofåterställningsenheterna. Dessa diskvolymer är associerade med HANA Large Instance-serverenheten på haveriberedskapsplatsen. Följande volymer replikeras från produktionsregionen till DR-platsen:

- /hana/data
- /hana/logbackups /hana/logbackups /hana/logbackups /han 
- /hana/shared (inkluderar /usr/sap)

/hana/log-volymen replikeras inte eftersom SAP HANA-transaktionsloggen inte behövs på det sätt som återställningen från dessa volymer görs. 

Grunden för den funktionalitet för haveriberedskap som erbjuds är den lagringsreplikeringsfunktion som erbjuds av hana-infrastrukturen för stora instans. Funktionen som används på lagringssidan är inte en konstant ström av ändringar som replikeras på ett asynkront sätt när ändringar sker i lagringsvolymen. I stället är det en mekanism som bygger på det faktum att ögonblicksbilder av dessa volymer skapas regelbundet. Deltat mellan en redan replikerad ögonblicksbild och en ny ögonblicksbild som ännu inte replikerats överförs sedan till katastrofåterställningsplatsen till måldiskvolymer.  Dessa ögonblicksbilder lagras på volymerna och, om det finns en redundans för haveriberedskap, måste återställas på dessa volymer.  

Den första överföringen av hela data av volymen bör vara innan mängden data blir mindre än deltan mellan ögonblicksbilder. Därför innehåller volymerna på DR-platsen var och en av de volymögonblicksbilder som utförs på produktionsplatsen. Så småningom kan du använda det DR-systemet för att komma till en tidigare status för att återställa förlorade data, utan att återställa produktionssystemet.

Om det finns en MCOD-distribution med flera oberoende SAP HANA-instanser på en HANA-enhet för stor instans, förväntas det att alla SAP HANA-instanser får lagring replikerad till DR-sidan.

I de fall där du använder HANA System Replication som funktioner med hög tillgänglighet på produktionsplatsen och använder lagringsbaserad replikering för DR-platsen replikeras volymerna för båda noderna från primär plats till DR-instansen. Du måste köpa ytterligare lagringsutrymme (samma storlek som för den primära noden) på DR-platsen för att hantera replikering från både primär och sekundär till DR. 



>[!NOTE]
>Funktionen för lagringsreplikering av HANA-instansen spegling och replikerande ögonblicksbilder av lagring. Om du inte utför ögonblicksbilder av lagring som introducerades i avsnittet Säkerhetskopiering och återställning i den här artikeln kan det inte finnas någon replikering till platsen för haveriberedskap. Lagringsögonblicksbild är en förutsättning för lagringsreplikering till haveriberedskapsplatsen.



## <a name="preparation-of-the-disaster-recovery-scenario"></a>Förberedelse av katastrofåterställningsscenariot
I det här fallet har du ett produktionssystem som körs på HANA Stora instanser i regionen produktion Azure. För de steg som följer, låt oss anta att SID för att HANA-systemet är "PRD", och att du har ett icke-produktionssystem som körs på HANA stora instanser i REGIONEN DR Azure. För det senare, låt oss anta att dess SID är "TST." Följande bild visar den här konfigurationen:

![Start av DR-installation](./media/hana-overview-high-availability-disaster-recovery/disaster_recovery_start1.PNG)

Om serverinstansen inte redan har beställts med den extra lagringsvolymuppsättningen, bifogar SAP HANA på Azure Service Management ytterligare uppsättning volymer som ett mål för produktionsrepliken till den STORA HANA-instansenheten där du kör TST HANA-exempel. För detta ändamål måste du ange SID för din produktion HANA-instans. När SAP HANA på Azure Service Management har bekräftat bilagan till dessa volymer måste du montera dessa volymer till HANA Large Instance-enheten.

![DR-inställning nästa steg](./media/hana-overview-high-availability-disaster-recovery/disaster_recovery_start2.PNG)

Nästa steg är att du installerar den andra SAP HANA-instansen på HANA Large Instance-enheten i DR Azure-regionen, där du kör TST HANA-instansen. Den nyligen installerade SAP HANA-instansen måste ha samma SID. De användare som skapas måste ha samma UID och grupp-ID som produktionsinstansen har. Läs [Säkerhetskopiering och återställning](hana-backup-restore.md) för mer information. Om installationen lyckades måste du:

- Kör steg 2 i förberedelsen för ögonblicksbild av lagring som beskrivs i [Säkerhetskopiering och återställning](hana-backup-restore.md).
- Skapa en offentlig nyckel för DR-enheten för HANA Large Instance-enheten om du ännu inte har gjort det. Se steg 3 i förberedelsen för ögonblicksbild av lagring som beskrivs i [Säkerhetskopiering och återställning](hana-backup-restore.md).
- Underhåll *HANABackupCustomerDetails.txt* med den nya HANA-instansen och testa om anslutningen till lagring fungerar korrekt.  
- Stoppa den nyligen installerade SAP HANA-instansen på HANA Large Instance-enheten i REGIONEN DR Azure.
- Avmontera dessa PRD-volymer och kontakta SAP HANA på Azure Service Management. Volymerna kan inte vara monterade på enheten eftersom de inte kan vara tillgängliga när de fungerar som mål för lagringsreplikering.  

![DR-inställningssteg innan replikering upprättas](./media/hana-overview-high-availability-disaster-recovery/disaster_recovery_start3.PNG)

Operationsteamet upprättar replikeringsrelationen mellan PRD-volymerna i azure-regionen för produktion och PRD-volymerna i DR Azure-regionen.

>[!IMPORTANT]
>/hana/log-volymen replikeras inte eftersom det inte är nödvändigt att återställa den replikerade SAP HANA-databasen till ett konsekvent tillstånd på haveriberedskapsplatsen.

Konfigurera sedan eller justera säkerhetskopieringsschemat för säkerhetskopiering av ögonblicksbilder för lagring för att komma till din RTO och RPO i katastroffallet. Om du vill minimera återställningspunktsmålet anger du följande replikeringsintervall i tjänsten HANA Large Instance:
- För de volymer som omfattas av den kombinerade ögonblicksbilden (ögonblicksbildtyp **hana**) anges att replikera var 15:e minut till motsvarande lagringsvolymmål på katastrofåterställningsplatsen.
- För säkerhetskopieringsvolymen för **transaktionsloggen (ögonblicksbildtyploggar**) anges att replikera var tredje minut till motsvarande lagringsvolymmål på katastrofåterställningsplatsen.

Om du vill minimera målsättningen för återställningspunkten ställer du in följande:
- Utför en ögonblicksbild av **hana-typlagring** (se "Steg 7: Utför ögonblicksbilder") var 30:e minut till 1 timme.
- Utför SAP HANA transaktionslogg säkerhetskopior var 5 minuter.
- Utför en **loggtyp** lagring ögonblicksbild var 5-15 minuter. Med denna intervallperiod uppnår du en RPO på cirka 15-25 minuter.

Med den här inställningen kan sekvensen av säkerhetskopior av transaktionsloggen, ögonblicksbilder av lagring och replikeringen av HANA-transaktionsloggens säkerhetskopieringsvolym och /hana/data och /hana/shared (inkluderar /usr/sap) se ut som de data som visas i den här bilden:

 ![Relation mellan en ögonblicksbild av säkerhetskopiering av transaktionslogg och en fästspegel på en tidsaxel](./media/hana-overview-high-availability-disaster-recovery/snapmirror.PNG)

För att uppnå en ännu bättre RPO i haveriberedskapsfallet kan du kopiera HANA-transaktionsloggsäkerhetskopior från SAP HANA på Azure (stora instanser) till den andra Azure-regionen. För att uppnå denna ytterligare RPO-minskning utför du följande steg:

1. Säkerhetskopiera HANA-transaktionsloggen så ofta som möjligt till /hana/logbackups.
1. Använd rsync för att kopiera säkerhetskopior av transaktionsloggen till de virtuella NFS-resursbaserade Virtuella Azure-datorerna. De virtuella datorerna finns i virtuella Azure-nätverk i Azure-produktionsregionen och i DR-regionerna. Du måste ansluta båda virtuella Azure-nätverken till kretsen som ansluter produktionen av HANA-stora instanser till Azure. Se grafiken i avsnittet [Nätverksöverväganden för haveriberedskap med avsnittet Stora HANA-instanser.](#Network-considerations-for-disaster recovery-with-HANA-Large-Instances) 
1. Behåll säkerhetskopior av transaktionsloggen i regionen i den virtuella datorn som är kopplad till den NFS-exporterade lagringen.
1. I ett katastrofväxlingsfall kan du komplettera säkerhetskopiorna för transaktionsloggen som du hittar på volymen /hana/logbackups med mer nyligen tagna transaktionsloggsäkerhetskopior på NFS-resursen på katastrofåterställningsplatsen. 
1. Starta en säkerhetskopiering av transaktionsloggen för att återställa till den senaste säkerhetskopian som kan sparas över till DR-regionen.

När HANA Large Instance-åtgärder bekräftar replikeringsrelationsinställningarna och du startar säkerhetskopiering av körningslagringslagring, börjar datareplikeringen.

![DR-inställningssteg innan replikering upprättas](./media/hana-overview-high-availability-disaster-recovery/disaster_recovery_start4.PNG)

När replikeringen fortskrider återställs inte ögonblicksbilderna på PRD-volymerna i DR Azure-regionerna. De lagras bara. Om volymerna är monterade i ett sådant tillstånd representerar de det tillstånd där du avmonterade dessa volymer efter att PRD SAP HANA-instansen installerades i serverenheten i REGIONEN DR Azure. De representerar också lagringssäkerhetskopior som ännu inte har återställts.

Om det finns en redundans kan du också välja att återställa till en äldre ögonblicksbild av lagring i stället för den senaste lagringsögonblicksbilden.

## <a name="next-steps"></a>Nästa steg

- Se [Felningsövervikelseproceduren för haverining](hana-failover-procedure.md).
