---
title: Löpande programuppgraderingar – Azure SQL Database | Microsoft Docs
description: Lär dig använda Azure SQL Database geo-replikering för att stödja online uppgraderingar av ditt molnprogram.
services: sql-database
ms.service: sql-database
ms.subservice: high-availability
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: anosov1960
ms.author: sashan
ms.reviewer: mathoma, carlrab
manager: craigg
ms.date: 01/29/2019
ms.openlocfilehash: 1aa3960e3a974703cfecec2bd28fc41f74f7df96
ms.sourcegitcommit: 898b2936e3d6d3a8366cfcccc0fccfdb0fc781b4
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/30/2019
ms.locfileid: "55238415"
---
# <a name="managing-rolling-upgrades-of-cloud-applications-using-sql-database-active-geo-replication"></a>Hantera löpande uppgraderingar av molnprogram med SQL Database aktiv geo-replikering

Lär dig hur du använder [aktiv geo-replikering](sql-database-auto-failover-group.md) i SQL-databasen för att möjliggöra löpande uppgraderingar av ditt molnprogram. Eftersom uppgraderingen är en störande åtgärd, bör det vara en del av ditt företag affärskontinuitet planering och design. I den här artikeln vi titta på två olika metoder för att samordna uppgraderingsprocessen och diskutera fördelarna och nackdelarna med varje alternativ. För den här artikeln använder vi ett program som består av en webbplats som är anslutna till en enkel databas som sin datanivå. Vårt mål är att uppgradera version 1 av programmet till version 2 utan några betydande påverkan på slutanvändarens upplevelse.

När du utvärderar uppgraderingsalternativen, bör du tänka på följande faktorer:

* Påverkan på programmets tillgänglighet under uppgraderingar. Hur länge funktionen programmet kan vara begränsad eller försämrad.
* Möjlighet att återställa om uppgraderingen misslyckas.
* Säkerhetsrisken i ett program om en orelaterade allvarligt fel skulle uppstå under uppgraderingen.
* Totalt antal dollar kostnad.  Detta omfattar ytterligare redundans och kostnader för de tillfälliga komponenter som används av uppgraderingen.

## <a name="upgrading-applications-that-rely-on-database-backups-for-disaster-recovery"></a>Uppgradera program som förlitar sig på säkerhetskopiering av databaser för katastrofåterställning

Om ditt program är beroende av automatiska databassäkerhetskopieringar och använder geo-återställning för katastrofåterställning, distribueras den till en enda Azure-region. För att minimera störningar för slutanvändaren, skapar du en mellanlagringsmiljö i den regionen med alla programkomponenter som är inblandade i uppgraderingen. Följande diagram illustrerar driftsmiljön före uppgraderingen. Slutpunkten `contoso.azurewebsites.net` representerar en produktionsplatsen på webbprogrammet. Om du vill göra möjligheten att återställa uppgraderingen, måste du skapa en mellanlagringsplats med en helt synkroniserad kopia av databasen. Följande steg skapar en mellanlagringsmiljö för uppgraderingen:

1. Skapa en sekundär databas i samma Azure-region. Övervaka sekundärt för att se om seeding processen är slutförd (1).
2. Skapa en ny distributionsplats för ditt webbprogram som kallas ”mellanlagring”. Det kommer att registreras i DNS med URL: en `contoso-staging.azurewebsites.net` (2).

> [!NOTE]
> Observera de förberedande stegen påverkar inte produktionsplatsen och den kan fungera i full åtkomst-läge.
>  

![Konfiguration av SQL Database Go-replikering. Katastrofåterställning i molnet.](media/sql-database-manage-application-rolling-upgrade/option1-1.png)

Programmet är redo för den faktiska uppgraderingen när förberedelsesteg har slutförts. Följande diagram illustrerar stegen som ingår i uppgraderingsprocessen.

1. Ange den primära databasen till skrivskyddat läge (3). Det här läget kommer garanterar att produktionsplatsen för webbprogram (V1) förblir skrivskyddat läge under uppgraderingen, vilket gör dataavvikelser mellan databasinstanser V1 och V2.  
2. Koppla från den sekundära databasen med hjälp av planerad avslutning-läget (4). Den skapar en helt synkroniserad oberoende kopia av den primära databasen. Den här databasen kommer att uppgraderas.
3. Aktivera den primära databasen till läs-/ skrivläge och kör uppgraderingsskriptet (5).

![Konfiguration för SQL Database geo-replikering. Katastrofåterställning i molnet.](media/sql-database-manage-application-rolling-upgrade/option1-2.png)

Om uppgraderingen har slutförts, är du nu redo att växla slutanvändarna till den uppgraderade kopian programmet. Nu blir det en produktionsplatsen.  Växling innebär några fler steg som illustreras i följande diagram.

1. Aktivera en växlingen mellan produktions- och mellanlagringsplatser för webbprogram (6). Det växlar URL: er av två platserna. Nu `contoso.azurewebsites.net` pekar till V2-versionen av webbplatsen och databasen (produktionsmiljö).  
2. Om du inte längre behöver den V1-versionen, vilket blev en mellanlagrings kopia efter växlingen inaktivera du mellanlagring envoronment (7).

![Konfiguration för SQL Database geo-replikering. Katastrofåterställning i molnet.](media/sql-database-manage-application-rolling-upgrade/option1-3.png)

Om uppgraderingen misslyckas, till exempel på grund av ett uppgraderingsskript, steg facket bör övervägas äventyras. Att återställa programmet till förberedande tillstånd, kan du återställa programmet på produktionsplatsen till fullständig åtkomst. Stegen som ingår visas på i nästa diagram.

1. Ange databaskopian till läs-/ skrivläge (8). Den återställer fullständig V1 funktionellt på produktion kopian.
2. Utför Rotorsaksanalys och ställa mellanlagringsmiljön (9).

I det här läget programmet är helt funktionella och Uppgraderingsstegen kan upprepas.

> [!NOTE]
> Återställningen kräver inte DNS-ändringarna när du inte ännu utför en växlingen.

![Konfiguration för SQL Database geo-replikering. Katastrofåterställning i molnet.](media/sql-database-manage-application-rolling-upgrade/option1-4.png)

Nyckeln **nytta** för det här alternativet är att du kan uppgradera ett program i en enda region med en uppsättning enkla steg. Dollar kostnaden för uppgraderingen är relativt låg. Huvudsakliga **kompromiss** är att om ett oåterkalleligt fel inträffar under uppgraderingen kommer återställningen till tillståndet före uppgradering innebär omdistribution av programmet i en annan region och återställa databasen från säkerhetskopiering med GEO-återställning. Den här processen resulterar i betydande driftavbrott.

## <a name="upgrading-applications-that-rely-on-database-geo-replication-for-disaster-recovery"></a>Uppgradera program som förlitar sig på database geo-replikering för haveriberedskap

Om ditt program använder aktiv geo-replikering eller redundansgrupper för affärskontinuitet, distribueras den till minst två olika regioner med en aktiv primär databas i den primära regionen och en skrivskyddad sekundär databas i Backup-region. Förutom de faktorer som vi nämnde tidigare garanterar uppgraderingsprocessen att:

* Programmet förblir skyddat från kritiska fel vid alla tidpunkter under uppgraderingsprocessen
* Geo-redundanta komponenter i programmet uppgraderas parallellt med de aktiva komponenterna

För att uppnå dessa mål, förutom att använda Web App-distributionsfack, ska du använda Azure Traffic Manager (ATM) med en profil för redundans med en aktiv och en säkerhetskopiering slutpunkter.  Följande diagram illustrerar driftsmiljön före uppgraderingen. Webbplatserna `contoso-1.azurewebsites.net` och `contoso-dr.azurewebsites.net` representerar en produktionsmiljö av programmet med fullständig geografisk redundans. Produktionsmiljön innehåller följande komponenter:

1. Produktionsplatsen på webbprogrammet `contoso-1.azurewebsites.net` i den primära regionen (1)
2. Primära databasen i den primära regionen (2) 
3. En vänteläge instans av webbappen i regionen säkerhetskopiering (3)
4. GEO-replikerad sekundär databas i regionen säkerhetskopiering (4)
5. Azure traffic manager prestanda-profil med online-slutpunkt `contoso-1.azurewebsites.net` och offline-slutpunkt `contoso-dr.azurewebsites.net`

Om du vill göra möjligheten att återställa uppgraderingen, måste du skapa en mellanlagringsmiljö med en helt synkroniserad kopia av programmet. Eftersom du behöver se till att programmet kan snabbt återställa om ett oåterkalleligt fel inträffar under uppgraderingsprocessen måste vara geo-redundant samt mellanlagringsmiljön. Följande steg krävs för att skapa en mellanlagringsmiljö för uppgraderingen:

1. Distribuera en mellanlagringsplats för webbprogram i den primära regionen (6)
2. Skapa en sekundär databas i den primära Azure-regionen (7). Konfigurera mellanlagringsplats för webbprogram för att ansluta till den. 
3. Skapa en annan geo-redundant sekundär databas i regionen säkerhetskopiering genom att replikera den sekundära databasen i den primära regionen (kallas ”härledda geo-replikering”) (8).
3. Distribuera en mellanlagringsplats för web application-instans i regionen säkerhetskopiering (9) och konfigurera den för att ansluta den geo-sekundära som skapades i steg (9).


> [!NOTE]
> Observera de förberedande stegen påverkar inte programmet i produktionsplatsen och förblir fullt funktionell i skrivskyddad läge.

![Konfiguration för SQL Database geo-replikering. Katastrofåterställning i molnet.](media/sql-database-manage-application-rolling-upgrade/option2-1.png)

När de förberedande stegen har slutförts, är mellanlagringsmiljön kan uppgraderas. Följande diagram illustrerar Uppgraderingsstegen.

1. Ange den primära databasen i produktionsplatsen till skrivskyddat läge (10). Det här läget kommer garanterar produktionsdatabasen (V1) inte ändras under uppgraderingen, vilket gör dataavvikelser mellan databasinstanser V1 och V2.  
2. Koppla från den sekundära databasen i samma region använder planerad avslutning-läge (11). En oberoende men helt synkroniserade kopia av produktionsdatabasen skapas. Den här databasen kommer att uppgraderas.
3. Kör uppgraderingsskriptet mot `contoso-1-staging.azurewebsites.net`, `contoso-dr-staging.azurewebsites.net` och tillfälliga primära databasen (12). Databasändringar replikeras automatiskt till den sekundära mellanlagringen 

![Konfiguration för SQL Database geo-replikering. Katastrofåterställning i molnet.](media/sql-database-manage-application-rolling-upgrade/option2-2.png)

Om uppgraderingen har slutförts, är du nu redo att växla slutanvändarna till V2-versionen av programmet. Följande diagram illustrerar stegen som ingår.

1. Aktivera en växlingen mellan produktions- och mellanlagringsplatser för webbprogram i den primära regionen (13) och i regionen säkerhetskopiering (14). V2 av programmet blir en produktionsplatsen med en identisk kopia i regionen säkerhetskopiering.
2. Du kan inaktivera den mellanlagringsmiljön om du inte längre behöver V1-program (15 och 16).  

![Konfiguration för SQL Database geo-replikering. Katastrofåterställning i molnet.](media/sql-database-manage-application-rolling-upgrade/option2-3.png)

Om uppgraderingen misslyckas, till exempel på grund av ett uppgraderingsskript, betraktas mellanlagringsmiljön i inkonsekvent tillstånd. Att återställa programmet till tillståndet före uppgraderingen återgå till att använda V1 av programmet i produktionsmiljö. Steg som krävs visas på i nästa diagram.

1. Ange den primära databaskopian i produktionsplatsen till läs-/ skrivläge (17). Den återställer fullständig V1 funktionellt i produktionsplatsen.
2. Utföra Rotorsaksanalys och reparera eller ta bort mellanlagringsmiljön (18 och 19).

Nu kan programmet är helt funktionella och Uppgraderingsstegen kan upprepas.

> [!NOTE]
> Återställningen kräver inte DNS-ändringarna eftersom du inte utförde en växlingen.

![Konfiguration för SQL Database geo-replikering. Katastrofåterställning i molnet.](media/sql-database-manage-application-rolling-upgrade/option2-4.png)

Nyckeln **nytta** för det här alternativet är att du kan uppgradera både programmet och dess geo-redundant kopia parallellt utan att kompromissa med kontinuitet för företag under uppgraderingen. Huvudsakliga **kompromiss** är att den kräver dubbla redundans för varje programkomponent och därför medför högre dollar kostnad. Det innebär också en mer komplicerad arbetsflöde.

## <a name="summary"></a>Sammanfattning

De två uppgradera metoderna som beskrivs i artikel skiljer sig åt i komplexitet och dollar kostnad, men de båda fokusera på att minimera tiden när användaren är begränsat till skrivskyddade åtgärder. Då definieras direkt av varaktigheten för uppgraderingsskriptet. Den inte är beroende på databasens storlek, tjänstnivå som du har valt, webbplatskonfigurationen och andra faktorer som du enkelt inte kan styra. Alla förberedelsesteg är fristående från Uppgraderingsstegen och påverkar inte programmet för produktion. Effektiviteten hos uppgraderingsskriptet är en viktig faktor som anger slutanvändarens upplevelse under uppgraderingar. Så är det bästa sättet som du kan förbättra det genom att fokusera på att göra uppgraderingsskriptet så effektivt som möjligt.  

## <a name="next-steps"></a>Nästa steg

* En översikt över affärskontinuitet och scenarier finns i [översikt över affärskontinuitet](sql-database-business-continuity.md).
* Läs om Azure SQL Database aktiv geo-replikering i [skapa läsbara sekundära databaser med aktiv geo-replikering](sql-database-active-geo-replication.md).
* Läs om redundans för Azure SQL Database-grupper i [aktivera transparent och samordnad redundans för flera databaser med hjälp av automatisk redundans grupper](sql-database-auto-failover-group.md).
* Mer information om distributionsplatser och mellanlagringsmiljön i Azure App Service, se [konfigurera mellanlagringsmiljöer i Azure App Service](../app-service/deploy-staging-slots.md).  
* Läs Azure traffic manager-profiler i [hantera en Azure Traffic Manager-profil](../traffic-manager/traffic-manager-manage-profiles.md).  


