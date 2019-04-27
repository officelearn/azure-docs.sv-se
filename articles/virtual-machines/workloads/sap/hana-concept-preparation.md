---
title: Disaster recovery principer och förberedelse för SAP HANA på Azure (stora instanser) | Microsoft Docs
description: Disaster recovery principer och förberedelse för SAP HANA på Azure (stora instanser)
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
ms.date: 09/10/2018
ms.author: saghorpa
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 73643cd6954932f933e200baad09e4301300aac2
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60794822"
---
# <a name="disaster-recovery-principles"></a>Disaster Recovery-principer

Stora HANA-instanser erbjuder en disaster recovery-funktionen mellan stora HANA-instansen tidsstämplar i olika Azure-regioner. Om du distribuerar enheter för stora HANA-instansen i västra USA-regionen för Azure kan kan du exempelvis använda stora HANA-instansen-enheter i regionen USA, Öst som disaster recovery-enheter. Såsom nämnts tidigare har haveriberedskap inte konfigurerats automatiskt, eftersom den kräver att du betalar för en annan enhet för stora HANA-instans i regionen för Haveriberedskap. Installationsprogrammet för disaster recovery fungerar för skala upp samt skalbar inställningar. 

I de scenarier som hittills distribueras, använda kunder enhet i regionen för Haveriberedskap för att köra icke-produktionssystem som använder en installerad HANA-instans. Den stora HANA-instansen enheten måste vara av samma SKU som SKU: N som används i produktionen. Följande bild visar vilka diskkonfigurationen mellan server-enhet i produktion i Azure-region och regionen disaster recovery ser ut:

![DR-installationskonfiguration från disk synsätt](./media/hana-overview-high-availability-disaster-recovery/disaster_recovery_setup.PNG)

I den här bilden översikt visas sedan måste du ordna en andra uppsättningen av diskvolymer. Diskvolymer mål är samma storlek som volymerna för produktion för produktion-instans i disaster recovery-enheter. Dessa volymer på diskar som är associerade med stora HANA-instansen server-enhet i katastrofåterställningsplatsen. Följande volymer replikeras från produktionsregionen som till DR-plats:

- / hana/data
- / hana/logbackups 
- /Hana/Shared (inklusive/usr/sap)

/Hana/log volymen inte har replikerats eftersom transaktionsloggen för SAP HANA inte krävs på det sätt som återställningen från dessa volymer är klar. 

Grunden för disaster recovery-funktionerna som erbjuds erbjuds lagringsfunktioner för replikering av infrastrukturen som stora HANA-instansen. De funktioner som används på lagringssidan är inte en konstant ström av ändringar som replikerar på ett asynkront sätt som dataändringar görs lagringsvolymen. Det är en mekanism som bygger på det faktum att skapa ögonblicksbilder av volymerna regelbundet. Delta mellan en redan replikerade ögonblicksbild och en ny ögonblicksbild som ännu inte har replikerats överförs sedan till katastrofåterställningsplatsen till volymer på diskar.  Dessa ögonblicksbilder lagras på volymerna och om det finns en katastrof redundansväxling, måste återställas på dessa volymer.  

Den första överföringen av fullständiga data på volymen ska vara innan mängden data som blir mindre än deltan mellan ögonblicksbilder. Därför kan innehåller volymerna som i DR-plats olika ögonblicksbilderna av volymen utförs i produktionsplatsen. Slutligen kan du använda DR systemet för att komma till en tidigare status att återställa förlorade data utan att återställa i produktionssystemet.

Om det finns en MCOD distribution med flera oberoende SAP HANA-instanser på en enhet för stora HANA-instansen, förväntas det att alla SAP HANA-instanser får lagringen ska replikeras till haveriberedskapssidan.

I fall där du använder HANA System Replication när funktioner för hög tillgänglighet i din produktionsplats och Använd storage-baserad replikering för DR-plats kan replikeras volymer av båda noderna från primär plats till DR-instans. Du måste köpa ytterligare lagringsutrymme (samma storlek från och med primära noden) på DR-plats för replikering från både primära och sekundära till DR. 



>[!NOTE]
>Replikering för stora HANA-instansen lagringsfunktioner spegling och replikering av ögonblicksbilder av lagring. Om du inte gör ögonblicksbilder av lagring som den införts i avsnittet säkerhetskopiering och återställning av den här artikeln, får inte det finnas några replikering till platsen för katastrofåterställning. Storage ögonblicksbildskörning krävs för att storage-replikering till platsen för katastrofåterställning.



## <a name="preparation-of-the-disaster-recovery-scenario"></a>Förberedelsen av katastrofåterställning
I det här scenariot har du ett produktionssystem som körs på stora HANA-instanser i Azure-region för produktion. I de steg som följer antar vi att SID för det HANA-systemet är ”PRD” och att du har ett icke-produktionssystem som körs på stora HANA-instanser i regionen DR Azure. I det senare antar att dess SID är ”TST”. Följande bild visar den här konfigurationen:

![Början av DR-konfiguration](./media/hana-overview-high-availability-disaster-recovery/disaster_recovery_start1.PNG)

Om server-instansen inte har redan beställts med ytterligare lagringsutrymme volym, SAP HANA på Azure Service Management bifogar ytterligare uppsättning volymer som mål för produktion repliken till den stora HANA-instansen enheten där du kör TST HANA-instans. Du måste ange SID för din produktion HANA-instans för detta ändamål. När SAP HANA på Azure Service Management bekräftar den bifogade filen volymer, måste du montera volymerna till stora HANA-instansen-enhet.

![Nästa steg för DR-konfiguration](./media/hana-overview-high-availability-disaster-recovery/disaster_recovery_start2.PNG)

Nästa steg är att installera den andra instansen av SAP HANA på stora HANA-instansen-enhet i DR-Azure-regionen, där du kör den TST HANA-instansen. Den nyligen installerade SAP HANA-instansen måste ha samma SID. Användare som skapas måste ha samma UID och grupp-ID som produktionsinstansen har. Läs [säkerhetskopiering och återställning](hana-backup-restore.md) mer information. Om installationen lyckades, måste du:

- Kör du steg 2 av den ögonblicksbild förberedelserna för lagringen som beskrivs i [säkerhetskopiering och återställning](hana-backup-restore.md).
- Skapa en offentlig nyckel för DR-enhet med stora HANA-instansen enhet om du inte ännu har gjort. Se steg 3 i den ögonblicksbild förberedelserna för lagringen som beskrivs i [säkerhetskopiering och återställning](hana-backup-restore.md).
- Underhålla den *HANABackupCustomerDetails.txt* med den nya HANA-instansen och testa om anslutning till storage fungerar korrekt.  
- Stoppa den nyligen installerade SAP HANA-instansen på den stora HANA-instansen enheten i DR-Azure-region.
- Avmontera volymerna PRD och kontakta SAP HANA på Azure Service Management. Volymerna kan inte förblir monterade till enheten eftersom de kan inte nås samtidigt som fungerar som lagringsmål för replikering.  

![DR-installationssteget innan du upprättar replikering](./media/hana-overview-high-availability-disaster-recovery/disaster_recovery_start3.PNG)

Driftsteamet upprättar replikeringsrelationen mellan PRD volymer i Azure-region för produktion och PRD volymer i DR-Azure-region.

>[!IMPORTANT]
>Volymen /hana/log replikeras inte eftersom det inte är nödvändigt att återställa den replikerade SAP HANA-databasen till ett konsekvent tillstånd på katastrofåterställningsplatsen.

Därefter konfigurera eller ändra storage ögonblicksbild Säkerhetskopieringsschemat att komma till din RTO och RPO om haveriberedskap. Ange följande replikeringsintervall i tjänsten stora HANA-instansen för att minimera mål för återställningspunkt:
- För volymer som omfattas av den kombinerade ögonblicksbilden (typ av ögonblicksbild **hana**), ange att replikera var femtonde minut till motsvarande storage volym mål i katastrofåterställningsplatsen.
- För säkerhetskopiering transaktionsvolymer log (typ av ögonblicksbild **loggar**), ange att replikera var 3: e minut på vissa målenheter volym motsvarande lagring i katastrofåterställningsplatsen.

För att minimera återställningspunktmålet, ställer du in följande:
- Utföra en **hana** typ av storage-ögonblicksbilder (finns i ”steg 7: Utföra ögonblicksbilder ”) var 30: e minut till 1 timme.
- Utföra säkerhetskopieringar av transaktionsloggen SAP HANA var femte minut.
- Utföra en **loggar** skriver storage ögonblicksbild var 5 till 15 minuter. Med den här perioden intervall uppnå ett Återställningspunktmål på cirka 15-25 minuter.

Med den här installationen, serie säkerhetskopieringar av transaktionsloggen, ögonblicksbilder av lagring och replikering av HANA-transaktionen logga volym och/hana/säkerhetskopieringsdata och /hana/shared (inklusive/usr/sap) kan se ut de data som visas i den här bilden:

 ![Förhållandet mellan en transaktion log ögonblicksbild och en snapin-spegling på en tidsaxeln](./media/hana-overview-high-availability-disaster-recovery/snapmirror.PNG)

För att uppnå en ännu bättre RPO disaster recovery om kan kopiera du säkerhetskopieringarna av transaktionsloggen HANA från SAP HANA på Azure (stora instanser) till andra Azure-region. Utför följande steg för att uppnå den här ytterligare RPO minskning:

1. Logga in så ofta som möjligt /hana/logbackups säkerhetskopiera HANA-transaktionen.
1. Använd rsync för att kopiera säkerhetskopieringarna av transaktionsloggen till de NFS resurs Azure virtuella datorerna. De virtuella datorerna är i Azure-nätverk i produktionsregionen Azure och DR-regioner. Du behöver ansluta båda virtuella Azure-nätverk till kretsen ansluta produktion stora HANA-instanser till Azure. Se bilderna i det [Network överväganden för haveriberedskap med stora HANA-instanser](#Network-considerations-for-disaster recovery-with-HANA-Large-Instances) avsnittet. 
1. Behåll säkerhetskopieringarna av transaktionsloggen i regionen i den virtuella datorn som är kopplade till NFS exporteras lagring.
1. Komplettera de säkerhetskopieringar av transaktionsloggen du hitta på /hana/logbackups volymen med mer nyligen tagit säkerhetskopieringar av transaktionsloggen på NFS dela på katastrofåterställningsplatsen i haveriberedskap redundans fall. 
1. Starta en säkerhetskopiering av transaktionsloggen att återställa till den senaste säkerhetskopian som kan sparas under DR-regionen.

När stora HANA-instansen operations bekräfta replikering relation installationen och säkerhetskopior av körningen storage ögonblicksbilder startas, startar replikering av data.

![DR-installationssteget innan du upprättar replikering](./media/hana-overview-high-availability-disaster-recovery/disaster_recovery_start4.PNG)

Eftersom replikering pågår, ögonblicksbilder på PRD volymer i DR-Azure-regionerna inte återställs. De lagras endast. Om volymerna är monterade i ett sådant tillstånd, representerar de tillståndet som demontera volymerna när PRD SAP HANA-instansen har installerats i server-enhet i DR-Azure-region. De kan också representera storage säkerhetskopieringar som inte har återställts.

Om det finns en redundans, kan du också välja att återställa till en äldre lagring ögonblicksbild i stället för den senaste storage-ögonblicksbilden.

## <a name="next-steps"></a>Nästa steg

- Se [redundans för katastrofåterställning](hana-failover-procedure.md).
