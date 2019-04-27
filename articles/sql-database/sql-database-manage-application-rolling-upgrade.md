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
ms.date: 02/13/2019
ms.openlocfilehash: 47fd6c1e2bb342bc1a31fb16a45a5ebc749dca69
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60702681"
---
# <a name="manage-rolling-upgrades-of-cloud-applications-by-using-sql-database-active-geo-replication"></a>Hantera löpande uppgraderingar av molnprogram med hjälp av SQL Database aktiv geo-replikering

Lär dig hur du använder [aktiv geo-replikering](sql-database-auto-failover-group.md) i Azure SQL Database för att möjliggöra löpande uppgraderingar av ditt molnprogram. Uppgraderingar är störande åtgärder, bör de vara en del av din kontinuitet för företag planering och design. I den här artikeln ska vi titta på två olika metoder för att samordna uppgraderingsprocessen och diskutera fördelar och nackdelar med varje alternativ. För den här artikeln refererar vi till ett program som består av en webbplats som är ansluten till en enda databas som sin datanivå. Vårt mål är att uppgradera version 1 (V1) av programmet för version 2 (V2) utan någon inverkan på användarupplevelsen.

När du utvärderar uppgraderingsalternativen, Tänk på följande:

* Påverkan på programmets tillgänglighet under uppgraderingar, till exempel hur länge programfunktionerna kan begränsas eller försämrad.
* Möjlighet att återställa databasen om uppgraderingen misslyckas.
* Säkerhetsproblem för programmet om en orelaterade, oåterkalleligt fel uppstår under uppgraderingen.
* Totalt antal dollar kostnad. Detta omfattar ytterligare databasredundans och kostnader för de tillfälliga komponenter som används av uppgraderingen.

## <a name="upgrade-applications-that-rely-on-database-backups-for-disaster-recovery"></a>Uppgradera program som förlitar sig på säkerhetskopiering av databaser för katastrofåterställning

Om ditt program är beroende av automatiska databassäkerhetskopieringar och använder geo-återställning för katastrofåterställning, distribueras den till en enda Azure-region. Skapa en mellanlagringsmiljö för att minimera störningar för användaren i den regionen med alla programkomponenter som är inblandade i uppgraderingen. Det första diagrammet illustrerar driftsmiljön innan uppgraderingen. Slutpunkten `contoso.azurewebsites.net` representerar en produktionsmiljö av webbappen. Om du vill kunna återställa uppgraderingen måste du skapa en mellanlagringsmiljö med en helt synkroniserad kopia av databasen. Följ dessa steg om du vill skapa en mellanlagringsmiljö för uppgraderingen:

1. Skapa en sekundär databas i samma Azure-region. Övervaka sekundärt för att se om seeding processen är klar (1).
2. Skapa en ny miljö för webbappen och anropa det ”mellanlagring”. Det kommer att registreras i Azure DNS med URL-Adressen `contoso-staging.azurewebsites.net` (2).

> [!NOTE]
> Dessa förberedelsesteg påverka inte produktionsmiljön, vilket kan fungera i full-åtkomstläge.

![SQL Database geo-replikering konfiguration för katastrofåterställning i molnet.](media/sql-database-manage-application-rolling-upgrade/option1-1.png)

När du är klar med steg för förberedelse är programmet redo för den faktiska uppgraderingen. I nästa diagram visas steg som ingår i uppgraderingen:

1. Ange den primära databasen till skrivskyddat läge (3). Det här läget garanterar att produktionsmiljön i webbappen (V1) är skrivskyddad under uppgraderingen, vilket gör dataavvikelser mellan databasinstanser V1 och V2.
2. Koppla från den sekundära databasen med hjälp av planerad avslutning-läget (4). Den här åtgärden skapar en helt synkroniserade, oberoende kopia av den primära databasen. Den här databasen kommer att uppgraderas.
3. Aktivera den sekundära databasen till läs-/ skrivläge och kör uppgraderingsskriptet (5).

![SQL Database geo-replikering konfiguration för katastrofåterställning i molnet.](media/sql-database-manage-application-rolling-upgrade/option1-2.png)

Om uppgraderingen är klar har är du nu redo att växla användare till den uppgraderade kopian programmet, vilket blir en produktionsmiljö. Växla omfattar några fler steg, enligt beskrivningen i nästa diagram:

1. Aktivera en växlingen mellan produktionsmiljöer och mellanlagringsmiljön webbappens (6). Den här åtgärden växlar URL: erna för de två miljöerna. Nu `contoso.azurewebsites.net` pekar till V2-versionen av webbplatsen och databasen (produktionsmiljö). 
2. Om du inte längre behöver den V1-versionen, vilket blev en mellanlagrings kopia efter växlingen kan du inaktivera mellanlagringsmiljön (7).

![SQL Database geo-replikering konfiguration för katastrofåterställning i molnet.](media/sql-database-manage-application-rolling-upgrade/option1-3.png)

Om uppgraderingen misslyckas (till exempel på grund av ett fel i uppgraderingsskriptet) kan du överväga att mellanlagringsmiljön äventyras. Om du vill återställa återställa programmet till tillståndet före uppgradering till programmet i produktionsmiljön för att fullständig åtkomst. I nästa diagram visas återgång stegen:

1. Ange databaskopian till läs-/ skrivläge (8). Den här åtgärden återställer alla V1-funktioner i produktion-kopia.
2. Utför analys av grundorsaken och inaktivera mellanlagringsmiljön (9).

Nu kan programmet är helt funktionella och du kan upprepa Uppgraderingsstegen.

> [!NOTE]
> Återställningen kräver inte DNS-ändringarna eftersom du inte ännu utför en växlingen.

![SQL Database geo-replikering konfiguration för katastrofåterställning i molnet.](media/sql-database-manage-application-rolling-upgrade/option1-4.png)

Den stora fördelen med det här alternativet är att du kan uppgradera ett program i en enskild region genom att följa en uppsättning enkla steg. Dollar kostnaden för uppgraderingen är relativt låg. 

Den huvudsakliga Nackdelen är att om ett oåterkalleligt fel inträffar under uppgraderingen, återställningen till tillståndet före uppgradering innebär att omdistribuera programmet i en annan region och återställa databasen från en säkerhetskopia med hjälp av geo-återställning. Den här processen resulterar i betydande driftavbrott.

## <a name="upgrade-applications-that-rely-on-database-geo-replication-for-disaster-recovery"></a>Uppgradera program som förlitar sig på database geo-replikering för haveriberedskap

Om programmet använder aktiv geo-replikering eller grupper för automatisk redundans för affärskontinuitet, distribueras den till minst två olika regioner. Det finns en aktiv, primär databas i en primär region och en skrivskyddad sekundär databas i en säkerhetskopiering region. Tillsammans med de faktorer som nämns i början av den här artikeln, måste även uppgradera garanterar att:

* Programmet förblir skyddat från kritiska fel vid alla tidpunkter under uppgraderingsprocessen.
* Geo-redundanta komponenter i programmet uppgraderas parallellt med de aktiva komponenterna.

För att uppnå dessa mål, förutom att använda Web Apps-miljöer, drar du nytta av Azure Traffic Manager med hjälp av en profil för redundans med en aktiv slutpunkt och en slutpunkt för säkerhetskopiering. I nästa diagram visas driftsmiljön före uppgraderingen. Webbplatserna `contoso-1.azurewebsites.net` och `contoso-dr.azurewebsites.net` representerar en produktionsmiljö av programmet med fullständig geografisk redundans. Produktionsmiljön innehåller följande komponenter:

* Produktionsmiljön webbappens `contoso-1.azurewebsites.net` i den primära regionen (1)
* Den primära databasen i den primära regionen (2)
* En standby instans av webbappen i regionen säkerhetskopiering (3)
* Geo-replikerad sekundär databas i regionen säkerhetskopiering (4)
* En Traffic Manager-prestanda-profil med en online-slutpunkt anropas `contoso-1.azurewebsites.net` och kallas för en offline-slutpunkt `contoso-dr.azurewebsites.net`

Om du vill göra det möjligt att återställa uppgraderingen, måste du skapa en mellanlagringsmiljö med en helt synkroniserad kopia av programmet. Eftersom du behöver se till att programmet kan snabbt återställa om ett oåterkalleligt fel inträffar under uppgraderingsprocessen måste mellanlagringsmiljön vara geo-redundant också. Följande steg krävs för att skapa en mellanlagringsmiljö för uppgraderingen:

1. Distribuera en mellanlagringsmiljö för webbappen i den primära regionen (6).
2. Skapa en sekundär databas i den primära Azure-regionen (7). Konfigurera mellanlagringsmiljön för web-app för att ansluta till den. 
3. Skapa en annan geo-redundant, sekundär databas i regionen säkerhetskopiering genom att replikera den sekundära databasen i den primära regionen. (Den här metoden anropas *kedjat geo-replikering*.) (8).
4. Distribuera en mellanlagringsmiljö för web app-instans i regionen säkerhetskopiering (9) och konfigurera den för att ansluta den geo-redundant sekundär databas som skapats enligt (8).

> [!NOTE]
> Dessa förberedelsesteg påverkar inte programmet i produktionsmiljön. Det kommer att finnas i läs-och skrivbehörighet.

![SQL Database geo-replikering konfiguration för katastrofåterställning i molnet.](media/sql-database-manage-application-rolling-upgrade/option2-1.png)

När du är klar med steg för förberedelse är mellanlagringsmiljön klar för uppgradering. I nästa diagram illustrerar stegen uppgraderingen:

1. Ange den primära databasen i produktionsmiljön till skrivskyddat läge (10). Det här läget garanterar att produktionsdatabasen (V1) inte ändras under uppgraderingen, vilket gör dataavvikelser mellan databasinstanser V1 och V2.

```sql
-- Set the production database to read-only mode
ALTER DATABASE <Prod_DB>
SET (ALLOW_CONNECTIONS = NO)
```

2. Avsluta geo-replikering genom att koppla från sekundärt (11). Den här åtgärden skapar en oberoende men helt synkroniserade kopia av produktionsdatabasen. Den här databasen kommer att uppgraderas. I följande exempel används Transact-SQL, men [PowerShell](/powershell/module/az.sql/remove-azsqldatabasesecondary?view=azps-1.5.0) är också tillgänglig. 

```sql
-- Disconnect the secondary, terminating geo-replication
ALTER DATABASE <Prod_DB>
REMOVE SECONDARY ON SERVER <Partner-Server>
```

3. Kör uppgraderingsskriptet mot `contoso-1-staging.azurewebsites.net`, `contoso-dr-staging.azurewebsites.net`, och den fristående primära databasen (12). Databasändringar replikeras automatiskt till den sekundära mellanlagringen.

![SQL Database geo-replikering konfiguration för katastrofåterställning i molnet.](media/sql-database-manage-application-rolling-upgrade/option2-2.png)

Om uppgraderingen har slutförts, är du nu redo att växla användare till V2-versionen av programmet. I nästa diagram visas steg som ingår:

1. Aktivera en växlingen mellan produktionsmiljöer och mellanlagringsmiljön webbappens i den primära regionen (13) och i regionen säkerhetskopiering (14). V2 av programmet blir en produktionsmiljö med en identisk kopia i regionen säkerhetskopiering.
2. Om du behöver inte längre V1-program (15 och 16) kan inaktivera du mellanlagringsmiljön.

![SQL Database geo-replikering konfiguration för katastrofåterställning i molnet.](media/sql-database-manage-application-rolling-upgrade/option2-3.png)

Om uppgraderingen misslyckas (till exempel på grund av ett fel i uppgraderingsskriptet) kan du överväga att mellanlagringsmiljön ska vara i ett inkonsekvent tillstånd. Om du vill återställa programmet till tillståndet före uppgraderingen återgå till att använda V1 av programmet i produktionsmiljön. På nästa diagram visas steg som krävs:

1. Ange den primära databaskopian i produktionsmiljön för att läs-/ skrivläge (17). Den här åtgärden återställer alla V1-funktioner i produktionsmiljön.
2. Utföra rotorsaksanalyser och reparera eller ta bort mellanlagringsmiljön (18 och 19).

Nu kan programmet är helt funktionella och du kan upprepa Uppgraderingsstegen.

> [!NOTE]
> Återställningen kräver inte DNS ändras eftersom du inte har vid ett byte.

![SQL Database geo-replikering konfiguration för katastrofåterställning i molnet.](media/sql-database-manage-application-rolling-upgrade/option2-4.png)

Den stora fördelen med det här alternativet är att du kan uppgradera både programmet och dess geo-redundant kopia parallellt utan att kompromissa med kontinuitet för företag under uppgraderingen.

Huvudsakliga Nackdelen är att det kräver dubbla redundans för varje programkomponent och därför medför högre dollar kostnad. Det innebär också en mer komplicerad arbetsflöde.

## <a name="summary"></a>Sammanfattning

De två uppgradera metoderna som beskrivs i artikel skiljer sig åt i komplexiteten och kostnaden för dollar, men de båda fokusera på att minimera hur länge användaren är begränsat till skrivskyddade åtgärder. Då definieras direkt av varaktigheten för uppgraderingsskriptet. Den beror inte på databasens storlek, tjänstnivå som du har valt, konfiguration för webbplats eller andra faktorer som du enkelt inte kan styra. Alla förberedelsesteg är fristående från Uppgraderingsstegen och påverkar inte programmet för produktion. Effektiviteten hos uppgraderingsskriptet är en nyckelfaktor som bestämmer användarupplevelsen under uppgraderingar. Därför är det bästa sättet att förbättra den att fokusera på att göra uppgraderingsskriptet så effektivt som möjligt.

## <a name="next-steps"></a>Nästa steg

* En översikt över affärskontinuitet och scenarier finns i [översikt över affärskontinuitet](sql-database-business-continuity.md).
* Läs om Azure SQL Database aktiv geo-replikering i [skapa läsbara sekundära databaser med aktiv geo-replikering](sql-database-active-geo-replication.md).
* Läs om Azure SQL Database automatisk redundans-grupper i [aktivera transparent och samordnad redundans för flera databaser med hjälp av automatisk redundans grupper](sql-database-auto-failover-group.md).
* Läs om mellanlagringsmiljöer i Azure App Service i [konfigurera mellanlagringsmiljöer i Azure App Service](../app-service/deploy-staging-slots.md).
* Läs om Azure Traffic Manager-profiler i [hantera en Azure Traffic Manager-profil](../traffic-manager/traffic-manager-manage-profiles.md).
