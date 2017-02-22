---
title: "SQL Database-prestanda: Tjänstnivåer | Microsoft Docs"
description: "Jämför tjänstenivåer för SQL Database."
keywords: databasalternativ, databasprestanda
services: sql-database
documentationcenter: 
author: janeng
manager: jhubbard
editor: 
ms.assetid: f5c5c596-cd1e-451f-92a7-b70d4916e974
ms.service: sql-database
ms.custom: overview
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: data-management
ms.date: 02/09/2017
ms.author: janeng
translationtype: Human Translation
ms.sourcegitcommit: 8ed4b3b30df6756c4e99e77476bc4c3a21bba90e
ms.openlocfilehash: e93d54910d8ed64879e66542c4c795101bc19a41


---
# <a name="sql-database-options-and-performance-understand-whats-available-in-each-service-tier"></a>SQL Database-alternativ och prestanda: Förstå vad varje tjänstnivå erbjuder
[Azure SQL Database](sql-database-technical-overview.md) erbjuder tre tjänstnivåer: **Basic**, **Standard** och **Premium**, med flera prestandanivåer för att hantera olika arbetsbelastningar. Högre prestandanivåer innebär fler resurser för högre dataflöde. Du kan byta [tjänstnivå och prestandanivå dynamiskt](sql-database-scale-up.md) utan driftavbrott. Tjänstnivåerna Basic, Standard och Premium har alla ett SLA med 99,99 % garanterad tillgänglighet, flexibla alternativ för affärskontinuitet, säkerhetsfunktioner och fakturering per timme. 

Du kan skapa enskilda databaser med dedikerade resurser på den [prestandanivå](sql-database-service-tiers.md#single-database-service-tiers-and-performance-levels) som du har valt. Du kan också hantera flera databaser i en [elastisk pool](sql-database-service-tiers.md#elastic-pool-service-tiers-and-performance-in-edtus) där resurserna delas mellan databaser. Resurserna som är tillgängliga för enskilda databaser uttrycks i databastransaktionsenheter (DTU, Database Transaction Unit), och för elastiska pooler i elastiska DTU:er (eDTU:er). Mer information om DTU:er och eDTU:er finns i [Vad är en DTU?](sql-database-what-is-a-dtu.md) 

I båda fallen kan du välja mellan tjänstnivåerna **Basic**, **Standard** och **Premium**. 

## <a name="choosing-a-service-tier"></a>Välja tjänstnivå
Följande tabell ger exempel på vilka nivåer som lämpar sig bäst för olika programbelasningar.

| Tjänstenivå | Målbelastningar |
| :--- | --- |
| **Basic** | Bäst lämpad för små databaser som vanligtvis stöder en aktiv åtgärd åt gången. Exempelvis databaser som används för utveckling och testning, eller småskaliga program som inte används ofta. |
| **Standard** |Det rekommenderade alternativet för molnprogram med låga till medelhöga IO-prestandakrav. Den här nivån har stöd för flera samtidiga frågor. Exempelvis arbetsgrupps- eller webbappar. |
| **Premium** | Utformad för höga transaktionsvolymer med höga IO-prestandakrav. Den här nivån har stöd för många samtidiga användare. Exempelvis databaser som stöder verksamhetskritiska program. |

Börja med att bestämma om du vill köra en enskild databas eller om du vill gruppera databaser som delar resurser. Läs avsnittet om [överväganden för elastiska pooler](sql-database-elastic-pool-guidance.md). När du ska välja tjänstnivå börjar du med att fastställa de minsta databasfunktioner som du behöver:

* Den högsta databasstorleken för enskilda databaser (högst 2 GB för Basic, högst 250 GB för Standard och högst 500 GB till 1 TB för Premium på nivån för högsta prestanda)
* Det största totala lagringsutrymmet för en elastisk pool (117 GB för Basic, 1200 GB för Standard och 750 GB för Premium)
* Det högsta antalet databaser per pool (400 för Basic, 400 för Standard och 50 för Premium)
* Kvarhållningsperiod för säkerhetskopior av databasen (7 dagar för Basic, 35 dagar för Standard och Premium)

När du har bestämt vilken minsta tjänstnivå du behöver är det dags att fastställa prestandanivån för databasen (antalet DTU:er). Prestandanivåerna Standard S2 och S3 är ofta bra startpunkter. För databaser med höga CPU- eller IO-krav är Premium-prestandanivåerna en lämplig startpunkt. Premium erbjuder mer CPU-kapacitet och börjar med tio gånger högre IO jämfört med den högsta Standard-prestandanivån.

## <a name="single-database-service-tiers-and-performance-levels"></a>Servicenivåer och prestandanivåer för enskilda databaser
För enskilda databaser finns det flera prestandanivåer inom varje tjänstnivå. Du kan flexibelt välja den tjänstnivå som bäst motsvarar dina arbetsbelastningsbehov. Om du behöver skala upp eller ned kan du enkelt byta nivå för din databas. Se [Ändra tjänstnivå och prestandanivå för databasen](sql-database-scale-up.md) för mer information.

Oavsett hur många databaser du har i värdtjänsten så får din databas en garanterad uppsättning resurser och databasens förväntade prestanda påverkas inte.

[!INCLUDE [SQL DB service tiers table](../../includes/sql-database-service-tiers-table.md)]

> [!NOTE]
> För en detaljerad förklaring av alla andra rader i den här tabellen över tjänstnivåer, se [Funktioner och begränsningar för tjänstnivåer](sql-database-performance-guidance.md#service-tier-capabilities-and-limits).
> 

## <a name="scaling-up-or-scaling-down-a-single-database"></a>Skala upp eller skala ned en enkel databas

När du har valt en tjänstnivå och en prestandanivå kan du skala upp eller ned en enkel databas dynamiskt utifrån det faktiska resultatet. 

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Azure-SQL-Database-dynamically-scale-up-or-scale-down/player]
>

När du ändrar servicenivån och/eller prestandanivån för en databas skapas en replik av den ursprungliga databasen på den nya prestandanivån och sedan växlas anslutningar över till repliken. Inga data förloras under den här processen men under kort tidsperiod när vi växlar över till repliken är anslutningar till databasen inaktiverade, så vissa transaktioner som sker då kan återställas. Det här tidsfönstret varierar, men är i genomsnitt är det under 4 sekunder, och i mer än 99% av fallen rör det sig om mindre än 30 sekunder. Om många transaktioner sker just då anslutningarna är inaktiverade kan det ta lite längre tid.  

Hur lång tid processen att skala upp tar beror på databasens storlek och tjänstnivå före och efter ändringen. Till exempel bör en 250 GB-databas som ändras till, från eller inom en standard-tjänstnivå slutföras inom 6 timmar. En databas som är lika stor som ändrar prestandanivå inom premium-tjänstnivån bör slutföras inom 3 timmar.

* För att nedgradera en databas bör den vara mindre än den maximala tillåtna storleken för måltjänstnivån. 
* När du uppgraderar en databas med [Geo-replikering](sql-database-geo-replication-portal.md) aktiverat måste du först uppgradera dess sekundära databaser till den önskade prestandanivån innan du uppgraderar den primära databasen.
* När du nedgraderar från en premium-tjänstnivå måste först avsluta alla geo-replikeringsrelationer. Du kan följa stegen som beskrivs i avsnittet om [återställning efter ett avbrott](sql-database-disaster-recovery.md) för att stoppa replikeringsprocessen mellan den primära och de aktiva sekundära databaserna.
* Erbjudandena för återställningstjänsterna är olika för de olika tjänstnivåerna. Om du nedgraderar kan du förlora möjligheten att återställa till en viss tidpunkt eller få en lägre kvarhållningsperiod för säkerhetskopiering. Mer information finns i [Azure SQL Database Backup and Restore](sql-database-business-continuity.md) (Säkerhetskopiera och återställa med Azure SQL Database).
* De nya egenskaperna för databasen tillämpas inte förrän ändringarna har slutförts.

> [!IMPORTANT]
> Detaljerade anvisningar finns i [Managing single databases with the Azure portal](sql-database-manage-single-databases-portal.md) (Hantera enkla databaser med Azure Portal), [Managing single databases with Powershell](sql-database-manage-single-databases-powershell.md) (Hantera enkla databaser med Powershell), eller [Managing single databases with Transact-SQL](sql-database-manage-single-databases-tsql.md) (Hantera enkla databaser med Transact-SQL).
>

## <a name="elastic-pool-service-tiers-and-performance-in-edtus"></a>Tjänstnivåer och prestanda för elastiska pooler i eDTU:er

Pooler gör att databaser kan dela och förbruka eDTU-resurser utan att varje databas i poolen behöver tilldelas en specifik prestandanivå. En enskild databas i en standardpool kan exempelvis gå från att använda 0 eDTU:er till de maximala databas-eDTU:erna som du ställt in när du konfigurerade poolen. Pooler gör att flera databaser med varierande arbetsbelastningar effektivt kan använda eDTU-resurser som är tillgängliga för hela poolen. Se [pris- och prestandaöverväganden för en elastisk databaspool](sql-database-elastic-pool-guidance.md) för mer information.

Följande tabell beskriver egenskaperna för pool-tjänstnivåer.

[!INCLUDE [SQL DB service tiers table for elastic pools](../../includes/sql-database-service-tiers-table-elastic-pools.md)]

Varje databas inom en pool följer också egenskaperna för enkla databaser på den nivån. Till exempel har en Basic-pool en gräns för högsta antal tillåtna sessioner per pool på 4 800–28 800, men en enskild databas i en Basic-pool har en databasgräns på högst 300 sessioner.

## <a name="scaling-up-or-scaling-down-an-elastic-pool"></a>Skala upp eller skala ned en elastisk pool

När du har valt en tjänstnivå och en prestandanivå kan du skala upp eller ned den elastiska poolen dynamiskt utifrån det faktiska resultatet. 

* Det tar vanligtvis fem minuter eller mindre för att ändra det minimala antalet eDTU:er per databas eller det maximala antal eDTU:er per databas.
* Hur lång tid det tar att ändra poolstorleken (eDTU:er) beror på den kombinerade storleken av alla databaser i poolen. Ändringarna tar i genomsnitt 90 minuter eller mindre per 100 GB. Om det totala utrymme för alla databaser i poolen är 200 GB, till exempel, så är den förväntade svarstiden för att ändra pool-eDTU per pool tre timmar eller mindre.

> [!IMPORTANT]
> Detaljerade anvisningar finns i [Managing elastic pools with the Azure portal](sql-database-elastic-pool-manage-portal.md) (Hantera elastiska pooler med Azure Portal), [Managing elastic pools with Powershell](sql-database-elastic-pool-manage-powershell.md) (Hantera elastiska pooler med Powershell), [Managing single databases with Transact-SQL](sql-database-elastic-pool-manage-tsql.md) (Hantera elastiska pooler med Transact-SQL) eller [Managing elastic pools with C#](sql-database-elastic-pool-manage-csharp.md) (Hantera elastiska pooler med C#).
>

## <a name="next-steps"></a>Nästa steg

* Lär dig mer om [elastiska pooler](sql-database-elastic-pool-guidance.md) och om [priser och prestanda för elastiska pooler](sql-database-elastic-pool-guidance.md).
* Läs mer om hur man [Övervakar, hanterar och ändrar storlek på elastiska pooler](sql-database-elastic-pool-manage-portal.md) och [Övervakar prestandan för enskilda databaser](sql-database-single-database-monitor.md).
* Nu när du lärt dig om SQL Database-nivåer, kan du testa dem med ett [kostnadsfritt konto](https://azure.microsoft.com/pricing/free-trial/) och lära dig [hur du skapar din första SQL-databas](sql-database-get-started.md).
* För migreringsscenarier kan du använda [DTU-kalkylatorn](http://dtucalculator.azurewebsites.net/) för att beräkna ungefär hur många DTU:er som behövs. 




<!--HONumber=Feb17_HO2-->


