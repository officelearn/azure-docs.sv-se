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
ms.reviewer: carlrab
manager: craigg
ms.date: 08/23/2018
ms.openlocfilehash: 7031617dea4b7eb45309fd003242a8ee32d797ed
ms.sourcegitcommit: 7fd404885ecab8ed0c942d81cb889f69ed69a146
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/12/2018
ms.locfileid: "53272022"
---
# <a name="managing-rolling-upgrades-of-cloud-applications-using-sql-database-active-geo-replication"></a>Hantera löpande uppgraderingar av molnprogram med SQL Database aktiv geo-replikering

Lär dig hur du använder [aktiv geo-replikering](sql-database-auto-failover-group.md) i SQL-databasen för att möjliggöra löpande uppgraderingar av ditt molnprogram. Eftersom uppgraderingen är en störande åtgärd, bör det vara en del av ditt företag affärskontinuitet planering och design. I den här artikeln vi titta på två olika metoder för att samordna uppgraderingsprocessen och diskutera fördelarna och nackdelarna med varje alternativ. För den här artikeln använder vi ett enkelt program som består av en webbplats som är anslutna till en enkel databas som sin datanivå. Vårt mål är att uppgradera version 1 av programmet till version 2 utan några betydande påverkan på slutanvändarens upplevelse.

När du utvärderar alternativen uppgraderingen bör du tänka på följande faktorer:

* Påverkan på programmets tillgänglighet under uppgraderingar. Hur länge funktionen programmet kan vara begränsad eller försämrad.
* Möjlighet att återställa om en uppgraderingen skulle misslyckas.
* Säkerhetsrisken i ett program om en orelaterade allvarligt fel skulle uppstå under uppgraderingen.
* Totalt antal dollar kostnad.  Detta omfattar ytterligare redundans och kostnader för de tillfälliga komponenter som används av uppgraderingen.

## <a name="upgrading-applications-that-rely-on-database-backups-for-disaster-recovery"></a>Uppgradera program som förlitar sig på säkerhetskopiering av databaser för katastrofåterställning

Om ditt program är beroende av automatiska databassäkerhetskopieringar och använder geo-återställning för katastrofåterställning, är det vanligtvis distribueras till en enda Azure-region. Uppgraderingen innebär att du i det här fallet skapar du en säkerhetskopiering distribution av alla programkomponenter som ingår i uppgraderingen. För att minimera störningar för slutanvändaren ska du använda Azure Traffic Manager (ATM) med redundans-profilen.  Följande diagram illustrerar driftsmiljön före uppgraderingen. Slutpunkten `contoso-1.azurewebsites.net` representerar en produktionsplatsen för det program som måste uppgraderas. Om du vill göra möjligheten att återställa uppgraderingen måste du skapa en steg-plats med en helt synkroniserad kopia av programmet. Följande steg krävs för att förbereda programmet för uppgraderingen:

1. Skapa en steg-plats för uppgraderingen. Gör detta genom att skapa en sekundär databas (1) och distribuera en identisk webbplats i samma Azure-region. Övervaka sekundärt för att se om seeding processen är klar.
2. Skapa en profil för växling vid fel i ATM med `contoso-1.azurewebsites.net` som online slutpunkt och `contoso-2.azurewebsites.net` som offline.

> [!NOTE]
> Observera de förberedande stegen påverkar inte programmet i produktionsplatsen och den kan fungera i full åtkomst-läge.
>  

![Konfiguration av SQL Database Go-replikering. Katastrofåterställning i molnet.](media/sql-database-manage-application-rolling-upgrade/Option1-1.png)

Programmet är redo för den faktiska uppgraderingen när förberedelsesteg har slutförts. Följande diagram illustrerar stegen som ingår i uppgraderingsprocessen.

1. Ange den primära databasen i produktionsplatsen till skrivskyddat läge (3). Detta garanterar att instansen av programmet (V1) förblir skrivskyddat läge under uppgraderingen, vilket gör dataavvikelser mellan databasinstanser V1 och V2.  
2. Koppla från den sekundära databasen med hjälp av planerad avslutning-läget (4). Den skapar en helt synkroniserad oberoende kopia av den primära databasen. Den här databasen kommer att uppgraderas.
3. Aktivera den primära databasen till läs-/ skrivläge och kör uppgraderingsskriptet i steget fack (5).

![Konfiguration för SQL Database geo-replikering. Katastrofåterställning i molnet.](media/sql-database-manage-application-rolling-upgrade/Option1-2.png)

Om uppgraderingen slutförts är du nu redo att växla slutanvändarna till mellanlagrad kopiering programmet. Nu blir det produktionsplatsen av programmet.  Detta innebär att några fler steg som illustreras i följande diagram.

1. Växla online slutpunkten i ATM-profil till `contoso-2.azurewebsites.net`, som pekar på den V2-versionen av webbplatsen (6). Nu blir den produktionsplatsen med V2-program och slutanvändarens trafiken dirigeras till den.  
2. Om du inte längre behöver programkomponenter V1 så att du kan på ett säkert sätt kan du ta bort dem (7).

![Konfiguration för SQL Database geo-replikering. Katastrofåterställning i molnet.](media/sql-database-manage-application-rolling-upgrade/Option1-3.png)

Om uppgraderingen misslyckas, till exempel på grund av ett uppgraderingsskript, steg facket bör övervägas äventyras. Om du vill återställa programmet till förberedande tillstånd återställa du bara programmet på produktionsplatsen till fullständig åtkomst. Stegen som ingår visas på i nästa diagram.

1. Ange databaskopian till läs-/ skrivläge (8). Detta återställer fullständig V1 funktionellt i produktionsplatsen.
2. Utför Rotorsaksanalys och ta bort de komprometterade komponenterna i steget fack (9).

I det här läget programmet är helt funktionella och Uppgraderingsstegen kan upprepas.

> [!NOTE]
> Återställningen kräver inte ändringar i ATM-profilen eftersom den redan pekar på `contoso-1.azurewebsites.net` som aktiv slutpunkt.

![Konfiguration för SQL Database geo-replikering. Katastrofåterställning i molnet.](media/sql-database-manage-application-rolling-upgrade/Option1-4.png)

Nyckeln **nytta** för det här alternativet är att du kan uppgradera ett program i en enda region med en uppsättning enkla steg. Dollar kostnaden för uppgraderingen är relativt låg. Huvudsakliga **kompromiss** är att om ett oåterkalleligt fel inträffar under uppgraderingen kommer återställningen till tillståndet före uppgradering innebär ny distribution av programmet i en annan region och återställa databasen från säkerhetskopiering med GEO-återställning. Den här processen resulterar i betydande driftavbrott.

## <a name="upgrading-applications-that-rely-on-database-geo-replication-for-disaster-recovery"></a>Uppgradera program som förlitar sig på database geo-replikering för haveriberedskap

Om ditt program använder geo-replikering för affärskontinuitet, distribueras den till minst två olika regioner med en aktiv distribution i primär region och en standby-distribution i Backup-region. Förutom de faktorer som vi nämnde tidigare garanterar uppgraderingsprocessen att:

* Programmet förblir skyddat från kritiska fel vid alla tidpunkter under uppgraderingsprocessen
* Geo-redundanta komponenter i programmet uppgraderas parallellt med de aktiva komponenterna

För att uppnå dessa mål ska du använda Azure Traffic Manager (ATM) med redundans-profil med en aktiv och tre säkerhetskopiering slutpunkter.  Följande diagram illustrerar driftsmiljön före uppgraderingen. Webbplatserna `contoso-1.azurewebsites.net` och `contoso-dr.azurewebsites.net` representerar en produktionsplatsen av programmet med fullständig geografisk redundans. Om du vill göra möjligheten att återställa uppgraderingen måste du skapa en steg-plats med en helt synkroniserad kopia av programmet. Eftersom du behöver se till att programmet kan snabbt återställa om ett oåterkalleligt fel inträffar under uppgraderingsprocessen måste vara geo-redundant samt facket steg. Följande steg krävs för att förbereda programmet för uppgraderingen:

1. Skapa en steg-plats för uppgraderingen. Att göra det skapar en sekundär databas (1) och distribuerar en identisk kopia av webbplatsen i samma Azure-region. Övervaka sekundärt för att se om seeding processen är klar.
2. Skapa en geo-redundant sekundär databas i steg fack genom geo-replikering den sekundära databasen till den säkerhetskopiera regionen (kallas ”härledda geo-replikering”). Övervaka sekundära säkerhetskopian för att se om seeding processen är slutförd (3).
3. Skapa en standby kopia av webbplatsen i regionen säkerhetskopiering och länkar den till den geo-redundant sekundärt (4).  
4. Lägg till ytterligare slutpunkter `contoso-2.azurewebsites.net` och `contoso-3.azurewebsites.net` för redundans-profilen i ATM som offline slutpunkter (5).

> [!NOTE]
> Observera de förberedande stegen påverkar inte programmet i produktionsplatsen och den kan fungera i full åtkomst-läge.

![Konfiguration för SQL Database geo-replikering. Katastrofåterställning i molnet.](media/sql-database-manage-application-rolling-upgrade/Option2-1.png)

När de förberedande stegen har slutförts, är steget platsen uppgraderas. Följande diagram illustrerar Uppgraderingsstegen.

1. Ange den primära databasen i produktionsplatsen till skrivskyddat läge (6). Detta garanterar att instansen av programmet (V1) förblir skrivskyddat läge under uppgraderingen, vilket gör dataavvikelser mellan databasinstanser V1 och V2.  
2. Koppla från den sekundära databasen i samma region använder planerad avslutning-läge (7). En helt synkroniserad oberoende kopia av den primära databasen blir automatiskt en primär efter att skapas. Den här databasen kommer att uppgraderas.
3. Aktivera den primära databasen i steget fack till läs-/ skrivläge och kör uppgraderingsskriptet (8).

![Konfiguration för SQL Database geo-replikering. Katastrofåterställning i molnet.](media/sql-database-manage-application-rolling-upgrade/Option2-2.png)

Om uppgraderingen har slutförts du är nu redo att växla slutanvändarna till V2-versionen av programmet. Följande diagram illustrerar stegen som ingår.

1. Växla aktiv slutpunkt i ATM-profil till `contoso-2.azurewebsites.net`, som nu pekar på den V2-versionen av webbplatsen (9). Nu blir den en produktionsplatsen med V2-program och slutanvändarens trafiken börjar dirigeras till den.
2. Om du inte längre behöver V1-program så att du kan på ett säkert sätt kan du ta bort den (10 och 11).  

![Konfiguration för SQL Database geo-replikering. Katastrofåterställning i molnet.](media/sql-database-manage-application-rolling-upgrade/Option2-3.png)

Om uppgraderingen misslyckas, till exempel på grund av ett uppgraderingsskript, steg facket bör övervägas äventyras. Om du vill återställa programmet till förberedande tillstånd återgå du helt enkelt till med hjälp av programmet på produktionsplatsen med fullständig åtkomst. Stegen som ingår visas på i nästa diagram.

1. Ange den primära databaskopian i produktionsplatsen till läs-/ skrivläge (12). Detta återställer fullständig V1 funktionellt i produktionsplatsen.
2. Utför Rotorsaksanalys och ta bort de komprometterade komponenterna i steget fack (13 och 14).

I det här läget programmet är helt funktionella och Uppgraderingsstegen kan upprepas.

> [!NOTE]
> Återställningen kräver inte ändringar i ATM-profilen eftersom den redan pekar på `contoso-1.azurewebsites.net` som aktiv slutpunkt.

![Konfiguration för SQL Database geo-replikering. Katastrofåterställning i molnet.](media/sql-database-manage-application-rolling-upgrade/Option2-4.png)

Nyckeln **nytta** för det här alternativet är att du kan uppgradera både programmet och dess geo-redundant kopia parallellt utan att kompromissa med kontinuitet för företag under uppgraderingen. Huvudsakliga **kompromiss** är att den kräver dubbla redundans för varje programkomponent och därför medför högre dollar kostnad. Det innebär också en mer komplicerad arbetsflöde.

## <a name="summary"></a>Sammanfattning

De två uppgradera metoderna som beskrivs i artikel skiljer sig åt i komplexitet och dollar kostnad, men de båda fokusera på att minimera tiden när användaren är begränsat till skrivskyddade åtgärder. Då definieras direkt av varaktigheten för uppgraderingsskriptet. Den inte är beroende på databasens storlek, tjänstnivå som du har valt, webbplatskonfigurationen och andra faktorer som du enkelt inte kan styra. Detta är eftersom alla förberedelsesteg är fristående från Uppgraderingsstegen och kan göras utan att påverka programmet för produktion. Effektiviteten hos uppgraderingsskriptet är avgörande som anger slutanvändarens upplevelse under uppgraderingar. Så är det bästa sättet som du kan förbättra det genom att fokusera på att göra uppgraderingsskriptet så effektivt som möjligt.  

## <a name="next-steps"></a>Nästa steg

* En översikt över affärskontinuitet och scenarier finns i [översikt över affärskontinuitet](sql-database-business-continuity.md).
* Vill veta mer om Azure SQL Database automatiska säkerhetskopior, se [SQL Database automatiska säkerhetskopior](sql-database-automated-backups.md).
* Läs om hur du använder automatiska säkerhetskopieringar för återställning i [återställa en databas från automatiska säkerhetskopieringar](sql-database-recovery-using-backups.md).
