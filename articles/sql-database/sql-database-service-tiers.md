---
title: 'SQL Database prestanda och alternativ: Tjänstnivåer | Microsoft Docs'
description: Jämför SQL Database-funktioner för prestanda och verksamhetskontinuitet för de olika tjänstnivåerna för att balansera kostnad och kapacitet när du skalar.
keywords: databasalternativ, databasprestanda
services: sql-database
documentationcenter: ''
author: CarlRabeler
manager: jhubbard
editor: CarlRabeler

ms.service: sql-database
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: data-management
ms.date: 08/10/2016
ms.author: carlrab

---
# SQL Database-alternativ och prestanda: Förstå vad varje tjänstnivå erbjuder
[Azure SQL Database](sql-database-technical-overview.md) erbjuder tre tjänstnivåer med flera prestandanivåer för hantering av olika arbetsbelastningar. Ju högre prestandanivå, desto större uppsättning resurser för ett högre genomflöde. Du kan hantera varje databas på dess egen [tjänstnivå](sql-database-service-tiers.md#standalone-database-service-tiers-and-performance-levels), på dess egen prestandanivå. Du kan också hantera flera databaser i en [elastisk pool](sql-database-service-tiers.md#elastic-pool-service-tiers-and-performance-in-edtus) med en delad uppsättning resurser. Resurserna som är tillgängliga för fristående databaser uttrycks som databastransaktionsenheter (DTU, Database Transaction Unit) och för elastiska pooler som elastiska DTU:er eller eDTU:er. Mer information om DTU:er och eDTU:er finns i [Vad är en DTU?](sql-database-what-is-a-dtu.md) 

I båda fallen kan du välja mellan tjänstnivåerna **Basic**, **Standard** och **Premium**. Databasalternativen på de här nivåerna är liknande för fristående databaser och elastiska pooler, men det finns fler saker att tänka på om du använder elastiska pooler. Den här artikeln innehåller information om tjänstnivåer för fristående databaser och elastiska pooler.

## Tjänstnivåer och databasalternativ
Tjänstenivåerna Basic, Standard och Premium har alla en upptids-SLA på 99,99 % och erbjuder förutsägbar prestanda, flexibla alternativ för verksamhetskontinuitet, säkerhetsfunktioner och timvis debitering. Följande tabell ger exempel på vilka nivåer som lämpar sig bäst för olika programbelasningar.

| Tjänstenivå | Målbelastningar |
| --- | --- |
| **Basic** |Bäst lämpad för små databaser som vanligtvis stöder en aktiv åtgärd åt gången. Exempelvis databaser som används för utveckling och testning, eller småskaliga program som inte används ofta. |
| **Standard** |Alternativet för de flesta molnprogram; stöder flera samtidiga frågor. Exempelvis arbetsgrupps- eller webbappar. |
| **Premium** |Utformad för höga transaktionsvolymer som stöder många samtidiga användare och kräver den högsta funktionsnivån vad gäller verksamhetskontinuitet. Exempelvis databaser som stöder verksamhetskritiska program. |

> [!NOTE]
> Webb- och Business-utgåvorna är föråldrade. Läs [Vanliga frågor och svar om Sunset](https://azure.microsoft.com/pricing/details/sql-database/web-business/) om du planerar att fortsätta använda Webb- och Business-utgåvorna.
> 
> 

## Tjänstnivåer och prestandanivåer för fristående databaser
För fristående databaser finns det flera prestandanivåer inom varje tjänstnivå. Du kan flexibelt välja den tjänstnivå som bäst motsvarar dina arbetsbelastningsbehov. Om du behöver skala upp eller ned kan du enkelt byta nivå för din databas. Se [Ändra tjänstnivå och prestandanivå för databasen](sql-database-scale-up.md) för mer information.

Prestandaegenskaperna som beskrivs här gäller för databaser som skapats med [SQL Database V12](sql-database-v12-whats-new.md). Oavsett hur många databaser du har i värdtjänsten så får din databas en garanterad uppsättning resurser och databasens förväntade prestanda påverkas inte.

[!INCLUDE [SQL DB service tiers table](../../includes/sql-database-service-tiers-table.md)]

> [!NOTE]
> För en detaljerad förklaring av alla andra rader i den här tabellen över tjänstnivåer, se [Funktioner och begränsningar för tjänstnivåer](sql-database-performance-guidance.md#service-tier-capabilities-and-limits).
> 
> 

## Tjänstnivåer och prestanda för elastiska pooler i eDTU:er
Förutom att skapa och skala en fristående databas kan du även hantera flera databaser i en [elastisk pool](sql-database-elastic-pool.md). Alla databaserna i en elastisk pool delar en gemensam uppsättning resurser. Prestandaegenskaperna mäts i *elastiska Database Transaction Units* (eDTU:er). Precis som med en fristående databas finns det tre tjänstnivåer för pooler: **Basic**, **Standard** och **Premium**. De här tre tjänstnivåerna definierar fortfarande de övergripande prestandabegränsningarna och funktionerna för pooler.

Pooler gör att databaser kan dela och förbruka DTU-resurser utan att varje databas i poolen behöver tilldelas en specifik prestandanivå. En fristående databas i en standardpool kan exempelvis gå från att använda 0 eDTU:er till de högsta antalet databas-eDTU:er som du angav när du konfigurerade poolen. Pooler gör att flera databaser med varierande arbetsbelastningar effektivt kan använda eDTU-resurser som är tillgängliga för hela poolen. Se [pris- och prestandaöverväganden för en elastisk databaspool](sql-database-elastic-pool-guidance.md) för mer information.

Följande tabell beskriver egenskaperna för pool-tjänstnivåer.

[!INCLUDE [SQL DB service tiers table for elastic pools](../../includes/sql-database-service-tiers-table-elastic-db-pools.md)]

Varje databas i en pool associeras dessutom med egenskaperna för fristående databaser på den aktuella nivån. Till exempel har en Basic-pool en gräns för högsta antal tillåtna sessioner per pool på 4 800–28 800, men en enskild databas i en Basic-pool har en databasgräns på högst 300 sessioner.

## Välja tjänstnivå
När du ska välja tjänstnivå börjar du med att bestämma om databasen ska vara en fristående databas eller en del av en elastisk pool. 

### Välja tjänstnivå för en fristående databas
När du ska välja tjänstnivå för en fristående databas börjar du med att bestämma vilka databasfunktioner du behöver för att välja rätt SQL Database-utgåva:

* Databasstorlek (högst 2 GB för Basic, högst 250 GB för Standard och högst 500 GB till 1 TB för Premium, beroende på prestandanivå).
* Kvarhållningsperiod för säkerhetskopior av databasen (7 dagar för Basic, 35 dagar för Standard och 35 dagar för Premium).

När du har bestämt vilken SQL Database-utgåva du behöver är det dags att fastställa prestandanivån för databasen (antalet DTU-enheter). Du kan gissa och sedan [skala upp eller ned dynamiskt](sql-database-scale-up.md) baserat på den faktiska upplevelsen. Du kan också använda [DTU-kalkylatorn](http://dtucalculator.azurewebsites.net/) för att beräkna ungefär hur många DTU:er som behövs. 

### Välja tjänstnivå för en elastisk databaspool
När du ska välja tjänstnivå för en elastisk databaspool börjar du med att bestämma vilka databasfunktioner som du behöver för att välja tjänstnivån för poolen.

* Databasstorlek (2 GB för Basic, 250 GB för Standard och 500 GB för Premium).
* Kvarhållningsperiod för säkerhetskopior av databasen (7 dagar för Basic, 35 dagar för Standard och 35 dagar för Premium).
* Antal databaser per pool (400 för Basic, 400 för Standard och 50 för Premium).
* Högsta lagring per pool (117 GB för Basic, 1 200 för Standard och 750 för Premium).

När du har valt tjänstnivå för poolen är det dags att fastställa prestandanivån för poolen (eDTU:er). Du kan gissa och sedan [skala upp eller ned dynamiskt](sql-database-elastic-pool-manage-portal.md#change-performance-settings-of-a-pool) baserat på den faktiska upplevelsen. Du kan använda [DTU-kalkylatorn](http://dtucalculator.azurewebsites.net/) för att beräkna ungefär hur många DTU:er som behövs för varje databas i en pool för att fastställa den övre gränsen för poolen.

## Nästa steg
* Läs mer om prissättning för de här nivåerna på [SQL Database-priser](https://azure.microsoft.com/pricing/details/sql-database/).
* Lär dig mer om [elastiska pooler](sql-database-elastic-pool-guidance.md) och om [priser och prestanda för elastiska pooler](sql-database-elastic-pool-guidance.md).
* Läs mer om hur du [övervakar, hanterar och ändrar storlek på elastiska pooler](sql-database-elastic-pool-manage-portal.md) och hur du [övervakar prestanda för fristående databaser](sql-database-single-database-monitor.md).
* Nu när du lärt dig om SQL Database-nivåer, kan du testa dem med ett [kostnadsfritt konto](https://azure.microsoft.com/pricing/free-trial/) och lära dig [hur du skapar din första SQL-databas](sql-database-get-started.md).

## Ytterligare resurser
* [Designmönster för SaaS-program med flera klientorganisationer med Azure SQL Database](sql-database-design-patterns-multi-tenancy-saas-applications.md)
* [Microsoft Virtual Academy-videokurs om möjligheterna med elastiska databaser i Azure SQL Database](https://mva.microsoft.com/en-US/training-courses/elastic-database-capabilities-with-azure-sql-db-16554)

<!--HONumber=Oct16_HO3-->


