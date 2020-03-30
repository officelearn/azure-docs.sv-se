---
title: Löpande programuppgraderingar
description: Lär dig hur du använder Azure SQL Database geo-replikering för att stödja online uppgraderingar av ditt molnprogram.
services: sql-database
ms.service: sql-database
ms.subservice: high-availability
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: anosov1960
ms.author: sashan
ms.reviewer: mathoma, carlrab
ms.date: 02/13/2019
ms.openlocfilehash: 9c627c3e597fdcd3859ce02ea208fc7a8b5d612b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "73822860"
---
# <a name="manage-rolling-upgrades-of-cloud-applications-by-using-sql-database-active-geo-replication"></a>Hantera rullande uppgraderingar av molnprogram med hjälp av ACTIVE GEO-replication i SQL Database

Lär dig hur du använder [aktiv geo-replikering](sql-database-auto-failover-group.md) i Azure SQL Database för att aktivera rullande uppgraderingar av ditt molnprogram. Eftersom uppgraderingar är störande åtgärder, bör de vara en del av din verksamhet-kontinuitet planering och design. I den här artikeln tittar vi på två olika metoder för att iscensätta uppgraderingsprocessen och diskutera fördelarna och kompromisserna med varje alternativ. I den här artikeln hänvisar vi till ett program som består av en webbplats som är ansluten till en enda databas som sin datanivå. Vårt mål är att uppgradera version 1 (V1) av programmet till version 2 (V2) utan någon större inverkan på användarupplevelsen.

När du utvärderar uppgraderingsalternativ bör du tänka på följande:

* Inverkan på programmets tillgänglighet under uppgraderingar, till exempel hur länge programfunktionerna kan vara begränsade eller försämrade.
* Möjlighet att återställa om uppgraderingen misslyckas.
* Säkerhetsproblem för programmet om ett orelaterat, oåterkalleligt fel inträffar under uppgraderingen.
* Total dollarkostnad. Den här faktorn omfattar ytterligare databasredundans och inkrementella kostnader för de temporära komponenter som används av uppgraderingsprocessen.

## <a name="upgrade-applications-that-rely-on-database-backups-for-disaster-recovery"></a>Uppgradera program som är beroende av säkerhetskopiering av databaser för haveriberedskap

Om ditt program förlitar sig på automatiska säkerhetskopieringar av databaser och använder geoåterställning för haveriberedskap distribueras det till en enda Azure-region. För att minimera användaravbrott skapar du en mellanlagringsmiljö i den regionen med alla programkomponenter som ingår i uppgraderingen. Det första diagrammet illustrerar driftmiljön före uppgraderingsprocessen. Slutpunkten `contoso.azurewebsites.net` representerar en produktionsmiljö för webbappen. Om du vill kunna återställa uppgraderingen måste du skapa en mellanlagringsmiljö med en fullständigt synkroniserad kopia av databasen. Så här skapar du en mellanlagringsmiljö för uppgraderingen:

1. Skapa en sekundär databas i samma Azure-region. Övervaka sekundärt för att se om såddprocessen är klar (1).
2. Skapa en ny miljö för din webbapp och kalla den "Förproduktion". Den registreras i Azure DNS `contoso-staging.azurewebsites.net` med URL:en (2).

> [!NOTE]
> De här förberedelsestegen påverkar inte produktionsmiljön, som kan fungera i fullåtkomstläge.

![GEO-replikeringskonfiguration för SQL Database för molnkatastrofåterställning.](media/sql-database-manage-application-rolling-upgrade/option1-1.png)

När förberedelsestegen är klara är programmet klart för den faktiska uppgraderingen. Nästa diagram illustrerar de steg som ingår i uppgraderingsprocessen:

1. Ställ in den primära databasen på skrivskyddat läge (3). Det här läget garanterar att webbappens (V1) produktionsmiljö förblir skrivskyddad under uppgraderingen, vilket förhindrar dataskillnader mellan V1- och V2-databasinstanserna.
2. Koppla bort den sekundära databasen med hjälp av det planerade avslutningsläget (4). Den här åtgärden skapar en fullständigt synkroniserad, oberoende kopia av den primära databasen. Databasen kommer att uppgraderas.
3. Vrid den sekundära databasen till läs-skriv-läge och kör uppgraderingsskriptet (5).

![GEO-replikeringskonfiguration för SQL Database för molnkatastrofåterställning.](media/sql-database-manage-application-rolling-upgrade/option1-2.png)

Om uppgraderingen har slutförts är du nu redo att byta användare till den uppgraderade kopian av programmet, som blir en produktionsmiljö. Växling innebär några steg till, vilket illustreras i nästa diagram:

1. Aktivera en växlingsåtgärd mellan produktions- och mellanlagringsmiljöer i webbappen (6). Den här åtgärden växlar url:erna för de två miljöerna. Nu `contoso.azurewebsites.net` pekar på V2-versionen av webbplatsen och databasen (produktionsmiljö). 
2. Om du inte längre behöver V1-versionen, som blev en mellanlagringskopia efter växlingen, kan du inaktivera mellanlagringsmiljön (7).

![GEO-replikeringskonfiguration för SQL Database för molnkatastrofåterställning.](media/sql-database-manage-application-rolling-upgrade/option1-3.png)

Om uppgraderingsprocessen misslyckas (till exempel på grund av ett fel i uppgraderingsskriptet) anser du att mellanlagringsmiljön ska komprometteras. Om du vill återställa programmet till tillståndet före uppgraderingen återställer du programmet i produktionsmiljön till fullständig åtkomst. I nästa diagram visas omversionsstegen:

1. Ställ in databaskopian på läs- och skrivläge (8). Den här åtgärden återställer hela V1-funktionen för produktionskopian.
2. Utför grundorsaksanalysen och inaktivera mellanlagringsmiljön (9).

Nu är programmet fullt fungerande och du kan upprepa uppgraderingsstegen.

> [!NOTE]
> Återställningen kräver inte DNS-ändringar eftersom du ännu inte har utföra en växlingsåtgärd.

![GEO-replikeringskonfiguration för SQL Database för molnkatastrofåterställning.](media/sql-database-manage-application-rolling-upgrade/option1-4.png)

Den största fördelen med det här alternativet är att du kan uppgradera ett program i en enda region genom att följa en uppsättning enkla steg. Dollarn kostnaden för uppgraderingen är relativt låg. 

Den huvudsakliga kompromissen är att om ett oåterkalleligt fel inträffar under uppgraderingen innebär återställningen till tillståndet före uppgraderingen att programmet distribueras i en annan region och återställa databasen från säkerhetskopiering med hjälp av geo-återställning. Den här processen resulterar i betydande driftstopp.

## <a name="upgrade-applications-that-rely-on-database-geo-replication-for-disaster-recovery"></a>Uppgradera program som är beroende av databasgeo-replikering för haveriberedskap

Om programmet använder aktiva geo-replikerings- eller automatisk redundansgrupper för affärskontinuitet distribueras det till minst två olika regioner. Det finns en aktiv, primär databas i en primär region och en skrivskyddad, sekundär databas i en säkerhetskopieringsregion. Tillsammans med de faktorer som nämns i början av denna artikel, måste uppgraderingsprocessen också garantera att:

* Programmet förblir skyddat från katastrofala fel hela tiden under uppgraderingsprocessen.
* De geo-redundanta komponenterna i programmet uppgraderas parallellt med de aktiva komponenterna.

För att uppnå dessa mål, förutom att använda Web Apps-miljöerna, kommer du att dra nytta av Azure Traffic Manager genom att använda en redundansprofil med en aktiv slutpunkt och en slutpunkt för säkerhetskopiering. Nästa diagram illustrerar driftmiljön före uppgraderingsprocessen. Webbplatserna `contoso-1.azurewebsites.net` och `contoso-dr.azurewebsites.net` representerar en produktionsmiljö för programmet med fullständig geografisk redundans. Produktionsmiljön omfattar följande komponenter:

* Webbappens `contoso-1.azurewebsites.net` produktionsmiljö i den primära regionen (1)
* Den primära databasen i den primära regionen (2)
* En standby-instans av webbappen i säkerhetskopieringsregionen (3)
* Den geo-replikerade sekundära databasen i säkerhetskopieringsregionen (4)
* En Traffic Manager-prestandaprofil med `contoso-1.azurewebsites.net` en onlineslutpunkt anropad och en offlineslutpunkt som kallas`contoso-dr.azurewebsites.net`

Om du vill göra det möjligt att återställa uppgraderingen måste du skapa en mellanlagringsmiljö med en fullständigt synkroniserad kopia av programmet. Eftersom du måste se till att programmet snabbt kan återställas om ett oåterkalleligt fel inträffar under uppgraderingsprocessen, måste mellanlagringsmiljön också vara geosagundant. Följande steg krävs för att skapa en mellanlagringsmiljö för uppgraderingen:

1. Distribuera en mellanlagringsmiljö för webbappen i den primära regionen (6).
2. Skapa en sekundär databas i den primära Azure-regionen (7). Konfigurera mellanlagringsmiljön för webbappen för att ansluta till den. 
3. Skapa en annan geo-redundant, sekundär databas i säkerhetskopieringsområdet genom att replikera den sekundära databasen i den primära regionen. (Den här metoden kallas *kedjad geo-replikering*.) (8).
4. Distribuera en mellanlagringsmiljö för webbappinstansen i säkerhetskopieringsregionen (9) och konfigurera den för att ansluta den geouppsagbara sekundära databasen som skapats vid (8).

> [!NOTE]
> De här förberedelsestegen påverkar inte programmet i produktionsmiljön. Det kommer att förbli fullt fungerande i läs-skriv-läge.

![GEO-replikeringskonfiguration för SQL Database för molnkatastrofåterställning.](media/sql-database-manage-application-rolling-upgrade/option2-1.png)

När förberedelsestegen är klara är mellanlagringsmiljön redo för uppgraderingen. I nästa diagram visas följande uppgraderingssteg:

1. Ställ in den primära databasen i produktionsmiljön på skrivskyddat läge (10). Det här läget garanterar att produktionsdatabasen (V1) inte ändras under uppgraderingen, vilket förhindrar dataskillnader mellan V1- och V2-databasinstanserna.

```sql
-- Set the production database to read-only mode
ALTER DATABASE <Prod_DB>
SET (ALLOW_CONNECTIONS = NO)
```

2. Avsluta georeplikering genom att koppla från den sekundära (11). Den här åtgärden skapar en oberoende men fullständigt synkroniserad kopia av produktionsdatabasen. Databasen kommer att uppgraderas. I följande exempel används Transact-SQL men [PowerShell](/powershell/module/az.sql/remove-azsqldatabasesecondary?view=azps-1.5.0) är också tillgängligt. 

```sql
-- Disconnect the secondary, terminating geo-replication
ALTER DATABASE <Prod_DB>
REMOVE SECONDARY ON SERVER <Partner-Server>
```

3. Kör uppgraderingsskriptet mot `contoso-1-staging.azurewebsites.net`, `contoso-dr-staging.azurewebsites.net`och mellanlagringsprimärdatabasen (12). Databasändringarna replikeras automatiskt till mellanlagrings sekundära.

![GEO-replikeringskonfiguration för SQL Database för molnkatastrofåterställning.](media/sql-database-manage-application-rolling-upgrade/option2-2.png)

Om uppgraderingen har slutförts är du nu redo att byta användare till V2-versionen av programmet. Nästa diagram illustrerar de steg som ingår:

1. Aktivera en växlingsåtgärd mellan produktions- och mellanlagringsmiljöer för webbappen i den primära regionen (13) och i säkerhetskopieringsregionen (14). V2 i programmet blir nu en produktionsmiljö, med en redundant kopia i säkerhetskopieringsregionen.
2. Om du inte längre behöver V1-programmet (15 och 16) kan du inaktivera mellanlagringsmiljön.

![GEO-replikeringskonfiguration för SQL Database för molnkatastrofåterställning.](media/sql-database-manage-application-rolling-upgrade/option2-3.png)

Om uppgraderingsprocessen misslyckas (till exempel på grund av ett fel i uppgraderingsskriptet) anser du att mellanlagringsmiljön är i ett inkonsekvent tillstånd. Om du vill återställa programmet till tillståndet före uppgraderingen återgår du till att använda V1 av programmet i produktionsmiljön. De steg som krävs visas i nästa diagram:

1. Ange att den primära databaskopian i produktionsmiljön ska läsas i läsläge (17). Den här åtgärden återställer fullständiga V1-funktioner i produktionsmiljön.
2. Utför rotorsaksanalysen och reparera eller ta bort mellanlagringsmiljön (18 och 19).

Nu är programmet fullt fungerande och du kan upprepa uppgraderingsstegen.

> [!NOTE]
> Återställningen kräver inte DNS-ändringar eftersom du inte utförde en växlingsåtgärd.

![GEO-replikeringskonfiguration för SQL Database för molnkatastrofåterställning.](media/sql-database-manage-application-rolling-upgrade/option2-4.png)

Den största fördelen med det här alternativet är att du kan uppgradera både programmet och dess geo-redundanta kopia parallellt utan att kompromissa med din affärskontinuitet under uppgraderingen.

Den viktigaste kompromissen är att det kräver dubbel redundans för varje applikationskomponent och därför medför högre dollarkostnad. Det innebär också ett mer komplicerat arbetsflöde.

## <a name="summary"></a>Sammanfattning

De två uppgraderingsmetoderna som beskrivs i artikeln skiljer sig åt i komplexitet och dollarkostnad, men de fokuserar båda på att minimera hur länge användaren är begränsad till skrivskyddade åtgärder. Den tiden definieras direkt av uppgraderingsskriptets varaktighet. Det beror inte på databasens storlek, vilken tjänstnivå du har valt, webbplatskonfigurationen eller andra faktorer som du inte enkelt kan kontrollera. Alla förberedelsesteg frikopplas från uppgraderingsstegen och påverkar inte produktionsprogrammet. Uppgraderingsskriptets effektivitet är en viktig faktor som avgör användarupplevelsen under uppgraderingar. Så det bästa sättet att förbättra den upplevelsen är att fokusera dina ansträngningar på att göra uppgraderingsskriptet så effektivt som möjligt.

## <a name="next-steps"></a>Nästa steg

* En översikt över affärskontinuitet och scenarier finns i [Översikt över affärskontinuitet](sql-database-business-continuity.md).
* Mer information om Azure SQL Database active geo-replication finns i [Skapa läsbara sekundära databaser med aktiv geo-replikering](sql-database-active-geo-replication.md).
* Mer information om Azure SQL Database grupper [Use auto-failover groups to enable transparent and coordinated failover of multiple databases](sql-database-auto-failover-group.md)för automatisk redundans i Azureds.
* Mer information om mellanlagringsmiljöer i Azure App Service finns [i Konfigurera mellanlagringsmiljöer i Azure App Service](../app-service/deploy-staging-slots.md).
* Mer information om Azure Traffic Manager-profiler finns i [Hantera en Azure Traffic Manager-profil](../traffic-manager/traffic-manager-manage-profiles.md).
