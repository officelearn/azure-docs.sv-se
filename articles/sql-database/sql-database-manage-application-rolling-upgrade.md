---
title: Rullande programuppgraderingar - Azure SQL Database | Microsoft Docs
description: Lär dig hur du använder Azure SQL Database geo-replikering för att stödja online uppgraderingar av ditt program i molnet.
services: sql-database
author: anosov1960
manager: craigg
ms.service: sql-database
ms.custom: business continuity
ms.topic: article
ms.date: 04/01/2018
ms.author: sashan
ms.openlocfilehash: d1799befb2b5f59f1794bbca2a1daa2a13990882
ms.sourcegitcommit: 3a4ebcb58192f5bf7969482393090cb356294399
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/06/2018
---
# <a name="managing-rolling-upgrades-of-cloud-applications-using-sql-database-active-geo-replication"></a>Hantera löpande uppgraderingar av molnprogram som använder SQL-databas aktiv geo-replikering
> [!NOTE]
> [Aktiv geo-replikering](sql-database-geo-replication-overview.md) är nu tillgänglig för alla databaser i alla nivåer.
> 

Lär dig hur du använder [georeplikering](sql-database-geo-replication-overview.md) i SQL-databasen för att aktivera löpande uppgraderingar av ditt program i molnet. Uppgraderingen är en störande åtgärd, bör den vara en del av ditt företag kontinuitet planerings- och. I den här artikeln vi titta på två olika metoder för att samordna uppgraderingsprocessen och diskutera fördelar och avvägningarna med varje alternativ. Vid tillämpningen av den här artikeln ska vi använda ett enkelt program som består av en webbplats som är ansluten till en databas som sin datanivå. Vårt mål är att uppgradera version 1 av programmet till version 2 utan betydande påverkan på slutanvändarens upplevelse. 

När du utvärderar alternativen uppgraderingen bör du följande faktorer:

* Inverkan på tillgänglighet under uppgraderingar. Hur länge funktionen programmet begränsad eller vara försämrad.
* Möjlighet att återställa om ett fel uppstår på uppgradera.
* Säkerhetsproblem för programmet om en orelaterade katastrofalt fel inträffar under uppgraderingen.
* Totalt antal dollar kostnad.  Detta omfattar ytterligare redundans och kostnader för de tillfälliga komponenter som används av uppgraderingen. 

## <a name="upgrading-applications-that-rely-on-database-backups-for-disaster-recovery"></a>Uppgradera program som förlitar sig på säkerhetskopiering av databaser för katastrofåterställning
Om ditt program använder automatiska säkerhetskopieringar och använder geo-återställning för katastrofåterställning, distribueras den vanligtvis till en enda Azure-region. I det här fallet innebär uppgraderingen att du skapar en säkerhetskopiering distribution av alla programkomponenter ingår i uppgraderingen. För att minimera störningar för slutanvändaren ska du använda Azure Traffic Manager (WATM) med failover-profilen.  Följande diagram illustrerar driftsmiljön före uppgraderingen. Slutpunkten <i>contoso 1.azurewebsites.net</i> representerar en produktionsplatsen för program som behöver uppgraderas. Om du vill aktivera möjligheten att återställa uppgraderingen, måste du skapa en steg-plats med en helt synkroniserad kopia av programmet. Följande steg krävs för att förbereda program för uppgraderingen:

1. Skapa en plats för fas för uppgraderingen. Att göra det skapar en sekundär databas (1) och distribuerar en identisk webbplats i samma Azure-region. Övervaka sekundärt för att se om seeding processen är klar.
2. Skapa en profil för växling vid fel i WATM med <i>contoso 1.azurewebsites.net</i> som online slutpunkt och <i>contoso 2.azurewebsites.net</i> som offline. 

> [!NOTE]
> Observera de förberedande stegen påverkar inte programmet i produktionsplatsen och den kan fungera i full åtkomst-läge.
>  

![Konfiguration av SQL-databasen gå-replikeringen. Katastrofåterställning i molnet.](media/sql-database-manage-application-rolling-upgrade/Option1-1.png)

Programmet är redo för den faktiska uppgraderingen när de förberedande stegen har slutförts. Följande diagram illustrerar steg som ingår i uppgraderingen. 

1. Ange den primära databasen i produktionsplatsen till skrivskyddat läge (3). Detta garanterar att produktions-instans av programmet (V1) förblir skrivskyddad under uppgraderingen, vilket gör dataavvikelser mellan databasinstanser V1 och V2.  
2. Koppla från den sekundära databasen med hjälp av planerad avslutning (4). Den skapar en helt synkroniserad oberoende kopia av den primära databasen. Den här databasen kommer att uppgraderas.
3. Aktivera den primära databasen till läsa / skriva-läge och kör uppgraderingsskriptet i steget uttag (5).     

![Konfiguration av SQL Database geo-replikering. Katastrofåterställning i molnet.](media/sql-database-manage-application-rolling-upgrade/Option1-2.png)

Om uppgraderingen slutförts är du nu redo att växla slutanvändarna mellanlagrade kopian programmet. Det blir nu produktionsplatsen av programmet.  Detta omfattar några fler steg som visas i följande diagram.

1. Växla online slutpunkten i profilen WATM som <i>contoso 2.azurewebsites.net</i>, som pekar på version 2 av webbplatsen (6). Nu blir produktionsplatsen med V2-programmet och slutanvändarens trafik dirigeras till den.  
2. Om du inte längre behöver V1 programkomponenterna så du kan ta bort dem (7).   

![Konfiguration av SQL Database geo-replikering. Katastrofåterställning i molnet.](media/sql-database-manage-application-rolling-upgrade/Option1-3.png)

Om uppgraderingen misslyckas, till exempel på grund av ett fel i uppgraderingsskript, steget facket bör övervägas komprometteras. Om du vill återställa programmet till tillståndet före uppgradering återgå du helt enkelt till programmet i produktionsplatsen fullständig behörighet till. Stegen visas i nästa diagrammet.    

1. Ange databaskopian läsa / skriva-läge (8). Detta återställer fullständig V1 funktionellt i produktionsplatsen.
2. Utför Rotorsaksanalys och ta bort avslöjade komponenterna i steget uttag (9). 

Nu programmet fungerar fullt ut och Uppgraderingsstegen kan upprepas.

> [!NOTE]
> Återställningen inte kräver ändringar i WATM profilen eftersom den redan pekar på <i>contoso 1.azurewebsites.net</i> som aktiv slutpunkt.
> 
> 

![Konfiguration av SQL Database geo-replikering. Katastrofåterställning i molnet.](media/sql-database-manage-application-rolling-upgrade/Option1-4.png)

Nyckeln **nytta** för det här alternativet är att du kan uppgradera ett program i en region med en uppsättning enkla steg. Dollar kostnaden för uppgraderingen är relativt låg. Huvudsakliga **förhållandet** är att återställningen till tillståndet före uppgraderingen kommer innebär ny distribution av programmet i en annan region och återställa databasen från säkerhetskopiering med geo-återställning om ett oåterkalleligt fel uppstår under uppgraderingen. Den här processen medför betydande driftstopp.   

## <a name="upgrading-applications-that-rely-on-database-geo-replication-for-disaster-recovery"></a>Uppgradera program som förlitar sig på database geo-replikering för katastrofåterställning
Om ditt program utnyttjar geo-replikering för företagskontinuitet, har den distribuerats till minst två olika regioner med en aktiv distribution i primär region och en vänteläge distribution i säkerhetskopiering region. Utöver de faktorer som tidigare nämnts måste uppgraderingsprocessen garantera att:

* Programmet förblir skyddat från allvarligt fel vid alla tidpunkter under uppgraderingen
* Geo-redundant komponenter för programmet uppgraderas parallellt med aktiva komponenter

För att uppnå dessa mål ska du använda Azure Traffic Manager (WATM) med hjälp av failover-profil med en aktiv och tre säkerhetskopierade slutpunkter.  Följande diagram illustrerar driftsmiljön före uppgraderingen. Webbplatserna <i>contoso 1.azurewebsites.net</i> och <i>contoso dr.azurewebsites.net</i> motsvarar en produktionsplatsen av programmet med fullständig geografisk redundans. Om du vill aktivera möjligheten att återställa uppgraderingen, måste du skapa en steg-plats med en helt synkroniserad kopia av programmet. Eftersom du behöver säkerställa att programmet kan snabbt återställa om ett oåterkalleligt fel uppstår under uppgraderingsprocessen måste vara geo-redundant samt steget plats. Följande steg krävs för att förbereda program för uppgraderingen:

1. Skapa en plats för fas för uppgraderingen. Att göra det skapar en sekundär databas (1) och distribuerar en identisk kopia av webbplatsen i samma Azure-region. Övervaka sekundärt för att se om seeding processen är klar.
2. Skapa en sekundär geo-redundant databas i steget uttag av geo-replikering den sekundära databasen till säkerhetskopiering region (Detta kallas ”härledda geo-replikering”). Övervaka säkerhetskopieringen sekundär om seeding processen är slutförd (3).
3. Skapa en reservexemplaret för webbplatsen i området för säkerhetskopiering och länka det till den geo-redundant sekundärt (4).  
4. Lägga till ytterligare slutpunkter <i>contoso 2.azurewebsites.net</i> och <i>contoso 3.azurewebsites.net</i> för failover-profilen i WATM som offline slutpunkter (5). 

> [!NOTE]
> Observera de förberedande stegen påverkar inte programmet i produktionsplatsen och den kan fungera i full åtkomst-läge.
> 
> 

![Konfiguration av SQL Database geo-replikering. Katastrofåterställning i molnet.](media/sql-database-manage-application-rolling-upgrade/Option2-1.png)

När de förberedande stegen har slutförts, är steget facket redo för uppgraderingen. Följande diagram illustrerar Uppgraderingsstegen.

1. Ange den primära databasen i produktionsplatsen till skrivskyddat läge (6). Detta garanterar att produktions-instans av programmet (V1) förblir skrivskyddad under uppgraderingen, vilket gör dataavvikelser mellan databasinstanser V1 och V2.  
2. Koppla från den sekundära databasen i samma region använder planerad avslutning-läge (7). Den skapar en helt synkroniserad oberoende kopia av den primära databasen blir automatiskt till en primär när avslutades. Den här databasen kommer att uppgraderas.
3. Aktivera den primära databasen i steget-fack för att läsa / skriva-läge och kör uppgraderingsskriptet (8).    

![Konfiguration av SQL Database geo-replikering. Katastrofåterställning i molnet.](media/sql-database-manage-application-rolling-upgrade/Option2-2.png)

Om uppgraderingen har slutförts du är nu redo att växla slutanvändarna till version 2 av programmet. Följande diagram illustrerar steg som ingår.

1. Växla aktiv slutpunkt i profilen WATM som <i>contoso 2.azurewebsites.net</i>, som nu pekar på version 2 av webbplatsen (9). Nu blir den en produktionsplatsen med V2-programmet och slutanvändarens trafik dirigeras till den. 
2. Om du inte längre behöver V1-programmet så att du kan på ett säkert sätt kan du ta bort den (10 och 11).  

![Konfiguration av SQL Database geo-replikering. Katastrofåterställning i molnet.](media/sql-database-manage-application-rolling-upgrade/Option2-3.png)

Om uppgraderingen misslyckas, till exempel på grund av ett fel i uppgraderingsskript, steget facket bör övervägas komprometteras. Om du vill återställa programmet till tillståndet före uppgradering återgå du helt enkelt till att använda programmet på produktionsplatsen med fullständig åtkomst. Stegen visas i nästa diagrammet.    

1. Ange den primära databaskopian i produktionsplatsen till läsa / skriva-läge (12). Detta återställer fullständig V1 funktionellt i produktionsplatsen.
2. Utför Rotorsaksanalys och ta bort avslöjade komponenterna i steget uttag (13 och 14). 

Nu programmet fungerar fullt ut och Uppgraderingsstegen kan upprepas.

> [!NOTE]
> Återställningen inte kräver ändringar i WATM profilen eftersom den redan pekar på <i>contoso 1.azurewebsites.net</i> som aktiv slutpunkt.
> 
> 

![Konfiguration av SQL Database geo-replikering. Katastrofåterställning i molnet.](media/sql-database-manage-application-rolling-upgrade/Option2-4.png)

Nyckeln **nytta** för det här alternativet är att du kan uppgradera både programmet och dess geo-redundant kopia parallellt utan att kompromissa med din företagskontinuitet under uppgraderingen. Huvudsakliga **förhållandet** är att den kräver dubbla redundans för varje PROGRAMKOMPONENTEN och därför högre dollar kostnad uppkommer för. Det innebär också en mer komplicerad arbetsflöde. 

## <a name="summary"></a>Sammanfattning
De två uppgradera metoderna som beskrivs i artikel skiljer sig åt i komplexitet och dollar kostnad, men de båda fokusera på att minimera den tid när slutanvändaren är begränsad till skrivskyddade åtgärder. Då definieras direkt av uppgraderingsskriptet varaktighet. Det är inte beroende av databasens storlek, tjänstnivån som du har valt, webbplatskonfigurationen och andra faktorer som du kan enkelt styra. Detta beror på att alla förberedelsesteg frikopplat från Uppgraderingsstegen och kan göras utan att påverka programmet för produktion. Uppgraderingsskriptet effektivitet är avgörande som anger slutanvändarens upplevelse under uppgraderingar. Därför är det bästa sättet du kan förbättra den genom att fokusera arbetet på gör uppgraderingsskriptet så effektivt som möjligt.  

## <a name="next-steps"></a>Nästa steg
* En översikt över verksamhetskontinuitet och scenarier finns [översikt över verksamhetskontinuitet](sql-database-business-continuity.md).
* Lär dig mer om Azure SQL Database automatiserad säkerhetskopieringar, se [SQL-databas automatisk säkerhetskopiering](sql-database-automated-backups.md).
* Läs om hur du använder automatisk säkerhetskopiering för återställning i [återställa en databas från automatisk säkerhetskopiering](sql-database-recovery-using-backups.md).
* Mer information om alternativ för snabbare återställning, se [aktiv geo-replikering](sql-database-geo-replication-overview.md).


