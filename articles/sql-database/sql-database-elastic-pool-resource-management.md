---
title: Resurshantering i täta elastiska pooler | Microsoft-dokument
description: Hantera beräkningsresurser i Azure SQL-elastiska pooler med många databaser.
services: sql-database
ms.service: sql-database
ms.subservice: elastic-pools
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: dimitri-furman
ms.author: dfurman
ms.reviewer: carlrab
ms.date: 03/13/2019
ms.openlocfilehash: 014f9edca1706c39930c6e48bb64cd8873bcace9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79473735"
---
# <a name="resource-management-in-dense-elastic-pools"></a>Resurshantering i kompakta elastiska pooler

Elastiska azure SQL [Database-pooler](https://docs.microsoft.com/azure/sql-database/sql-database-elastic-pool) är en kostnadseffektiv lösning för hantering av många databaser med varierande resursanvändning. Alla databaser i en elastisk pool delar samma allokering av resurser, till exempel CPU, minne, arbetstrådar, lagringsutrymme, tempdb, under antagandet att **endast en delmängd av databaser i poolen kommer att använda beräkningsresurser vid en given tidpunkt**. Detta antagande gör att elastiska pooler är kostnadseffektiva. I stället för att betala för alla resurser som varje enskild databas kan behöva, betalar kunderna för en mycket mindre uppsättning resurser som delas mellan alla databaser i poolen.

## <a name="resource-governance"></a>Resursstyrning

Resursdelning kräver att systemet noggrant styr resursanvändningen för att minimera effekten "bullrig granne", där en databas med hög resursförbrukning påverkar andra databaser i samma elastiska pool. Samtidigt måste systemet tillhandahålla tillräckliga resurser för funktioner som hög tillgänglighet och haveriberedskap (HADR), säkerhetskopiering och återställning, övervakning, Query Store, Automatisk justering, etc. för att fungera tillförlitligt.

Azure SQL Database uppnår dessa mål med hjälp av flera resursstyrningsmekanismer, inklusive Windows [Job Objects](https://docs.microsoft.com/windows/win32/procthread/job-objects) for process level resource governance, Windows File Server Resource [Manager (FSRM)](https://docs.microsoft.com/windows-server/storage/fsrm/fsrm-overview) for storage quota management och en modifierad och utökad version av SQL Server [Resource Governor](https://docs.microsoft.com/sql/relational-databases/resource-governor/resource-governor) för att implementera resursstyrning inom varje SQL Server-instans som körs i Azure SQL Database.

Det primära designmålet för elastiska pooler är att vara kostnadseffektivt. Därför tillåter systemet avsiktligt kunder att skapa _täta_ pooler, det vill säga pooler med antalet databaser som närmar sig eller på det högsta tillåtna värdet, men med en måttlig allokering av beräkningsresurser. Av samma anledning reserverar systemet inte alla potentiellt nödvändiga resurser för sina interna processer, men tillåter resursdelning mellan interna processer och användararbetsbelastningar.

Den här metoden gör det möjligt för kunder att använda täta elastiska pooler för att uppnå tillräcklig prestanda och stora kostnadsbesparingar. Men om arbetsbelastningen mot många databaser i en tät pool är tillräckligt intensiv, blir resurskonkurrens betydande. Resurskonkurrens minskar användararbetsbelastningens prestanda och kan påverka interna processer negativt.

> [!IMPORTANT]
> I täta pooler med många aktiva databaser är det kanske inte möjligt att öka antalet databaser i poolen upp till maximum som dokumenteras för [elastiska DTU-](sql-database-dtu-resource-limits-elastic-pools.md) och [vCore-elastiska](sql-database-vcore-resource-limits-elastic-pools.md) pooler.
> 
> Antalet databaser som kan placeras i täta pooler utan att orsaka resurskonkurrens och prestandaproblem beror på antalet samtidiga aktiva databaser och på resursförbrukning av användararbetsbelastningar i varje databas. Det här antalet kan ändras med tiden när användararbetsbelastningar ändras.

När resurskonkurrens inträffar i en tätt packad pool kan kunderna välja en eller flera av följande åtgärder för att minska den:
- Justera frågearbetsbelastningen för att minska resursförbrukningen eller sprida resursförbrukning över flera databaser över tiden.
- Minska pooldensiteten genom att flytta vissa databaser till en annan pool eller genom att göra dem till fristående databaser.
- Skala upp poolen för att få mer resurser.

Förslag på hur du implementerar de två senaste åtgärderna finns i [Operativa rekommendationer](#operational-recommendations) senare i den här artikeln. Att minska resurskonkurrensen gynnar både användararbetsbelastningar och interna processer och gör att systemet på ett tillförlitligt sätt kan upprätthålla förväntad servicenivå.

## <a name="monitoring-resource-consumption"></a>Övervaka resursförbrukning

För att undvika prestandaförsämring på grund av resurskonkurrens bör kunder som använder täta elastiska pooler proaktivt övervaka resursförbrukningen och vidta åtgärder i rätt tid om ökande resurskonkurrens börjar påverka arbetsbelastningar. Kontinuerlig övervakning är viktigt eftersom resursanvändningen i en pool ändras över tiden, på grund av ändringar i användararbetsbelastningen, ändringar i datavolymer och distribution, ändringar i pooldensitet och ändringar i Azure SQL Database-tjänsten. 

Azure SQL Database innehåller flera mått som är relevanta för den här typen av övervakning. Om du överskrider det rekommenderade medelvärdet för varje mått anges resurskonkurrens i poolen och bör åtgärdas med hjälp av en av de åtgärder som nämndes tidigare.

|Måttnamn|Beskrivning|Rekommenderat medelvärde| 
|----------|--------------------------------|------------|
|`avg_instance_cpu_percent`|CPU-användning av SQL Server-processen som är associerad med en elastisk pool, mätt med det underliggande operativsystemet. Finns i [vyn sys.dm_db_resource_stats](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-db-resource-stats-azure-sql-database?view=azuresqldb-current) i alla databaser och i [vyn sys.elastic_pool_resource_stats](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-elastic-pool-resource-stats-azure-sql-database) i `master` databasen. Det här måttet skickas också till Azure Monitor, där det [heter](https://docs.microsoft.com/azure/azure-monitor/platform/metrics-supported#microsoftsqlserverselasticpools) `sqlserver_process_core_percent`, och kan visas i Azure-portalen. Det här värdet är detsamma för varje databas i samma elastiska pool.|Under 70%. Enstaka korta spikar upp till 90% kan vara acceptabelt.|
|`max_worker_percent`|[Användning av arbetstråd.]( https://docs.microsoft.com/sql/relational-databases/thread-and-task-architecture-guide) Tillhandahålls för varje databas i poolen, liksom för poolen själv. Det finns olika gränser för antalet arbetstrådar på databasnivå, och på poolnivå rekommenderas därför övervakning av det här måttet på båda nivåerna. Finns i [vyn sys.dm_db_resource_stats](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-db-resource-stats-azure-sql-database?view=azuresqldb-current) i alla databaser och i [vyn sys.elastic_pool_resource_stats](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-elastic-pool-resource-stats-azure-sql-database) i `master` databasen. Det här måttet skickas också till Azure Monitor, där det [heter](https://docs.microsoft.com/azure/azure-monitor/platform/metrics-supported#microsoftsqlserverselasticpools) `workers_percent`, och kan visas i Azure-portalen.|Under 80%. Toppar upp till 100% kommer att orsaka anslutningsförsök och frågor misslyckas.|
|`avg_data_io_percent`|IOPS-användning för att läsa och skriva fysisk IO. Tillhandahålls för varje databas i poolen, liksom för poolen själv. Det finns olika gränser för antalet IOPS på databasnivå, och på poolnivå rekommenderas därför övervakning av det här måttet på båda nivåerna. Finns i [vyn sys.dm_db_resource_stats](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-db-resource-stats-azure-sql-database?view=azuresqldb-current) i alla databaser och i [vyn sys.elastic_pool_resource_stats](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-elastic-pool-resource-stats-azure-sql-database) i `master` databasen. Det här måttet skickas också till Azure Monitor, där det [heter](https://docs.microsoft.com/azure/azure-monitor/platform/metrics-supported#microsoftsqlserverselasticpools) `physical_data_read_percent`, och kan visas i Azure-portalen.|Under 80%. Enstaka korta spikar upp till 100% kan vara acceptabelt.|
|`avg_log_write_percent`|Dataflödesutnyttjande för transaktionsloggskrivnings-I/O. Tillhandahålls för varje databas i poolen, liksom för poolen själv. Det finns olika gränser för loggdataflödet på databasnivå, och på poolnivå rekommenderas därför övervakning av det här måttet på båda nivåerna. Finns i [vyn sys.dm_db_resource_stats](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-db-resource-stats-azure-sql-database) i alla databaser och i [vyn sys.elastic_pool_resource_stats](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-elastic-pool-resource-stats-azure-sql-database) i `master` databasen. Det här måttet skickas också till Azure Monitor, där det [heter](https://docs.microsoft.com/azure/azure-monitor/platform/metrics-supported#microsoftsqlserverselasticpools) `log_write_percent`, och kan visas i Azure-portalen. När det här måttet är nära 100 %, alla databasändringar (INSERT, UPDATE, DELETE, MERGE-satser, SELECT ... IN I, BULKINSATS, etc.) kommer att bli långsammare.|Under 90%. Enstaka korta spikar upp till 100% kan vara acceptabelt.|
|`oom_per_second`|Hastigheten på OOM-fel (out-of-memory) i en elastisk pool, vilket är en indikator på minnestryck. Finns i [vyn sys.dm_resource_governor_resource_pools_history_ex.](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-resource-governor-resource-pools-history-ex-azure-sql-database?view=azuresqldb-current) Se [Exempel på](#examples) en exempelfråga för att beräkna det här måttet.|0|
|`avg_storage_percent`|Användning av lagringsutrymme på elastisk poolnivå. Finns i [vyn sys.elastic_pool_resource_stats](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-elastic-pool-resource-stats-azure-sql-database) `master` i databasen. Det här måttet skickas också till Azure Monitor, där det [heter](https://docs.microsoft.com/azure/azure-monitor/platform/metrics-supported#microsoftsqlserverselasticpools) `storage_percent`, och kan visas i Azure-portalen.|Under 80%. Kan närma sig 100% för pooler utan datatillväxt.|
|`tempdb_log_used_percent`|Användning av transaktionsloggutrymme i `tempdb` databasen. Även om temporära objekt som skapas i en databas `tempdb` inte visas i andra databaser i samma elastiska pool, är en delad resurs för alla databaser i samma pool. En tidskrävande eller inaktiv `tempdb` transaktion som startats från en databas i poolen kan förbruka en stor del av transaktionsloggen och orsaka fel för frågor i andra databaser i samma pool. Finns i [vyn sys.dm_db_log_space_usage.](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-db-log-space-usage-transact-sql) Det här måttet skickas också till Azure Monitor och kan visas i Azure-portalen. Se [Exempel på](#examples) en exempelfråga för att returnera det aktuella värdet för det här måttet.|Under 50%. Enstaka spikar upp till 80% är acceptabla.|
|||

Utöver dessa mått tillhandahåller Azure SQL Database en vy som returnerar faktiska resursstyrningsgränser samt ytterligare vyer som returnerar resursanvändningsstatistik på resurspoolnivå och på arbetsbelastningsgruppsnivå.

|Visa namn|Beskrivning|  
|-----------------|--------------------------------|  
|[sys.dm_user_db_resource_governance](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-user-db-resource-governor-azure-sql-database)|Returnerar faktiska konfigurations- och kapacitetsinställningar som används av resursstyrningsmekanismer i den aktuella databasen eller den elastiska poolen.|
|[sys.dm_resource_governor_resource_pools](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-resource-governor-resource-pools-transact-sql)|Returnerar information om det aktuella resurspooltillståndet, den aktuella konfigurationen av resurspooler och ackumulerad resurspoolstatistik.|
|[sys.dm_resource_governor_workload_groups](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-resource-governor-workload-groups-transact-sql)|Returnerar ackumulerad arbetsbelastningsgruppstatistik och den aktuella konfigurationen för arbetsbelastningsgruppen. Den här vyn kan sammanfogas med `pool_id` sys.dm_resource_governor_resource_pools i kolumnen för att hämta information om resurspoolen.|
|[sys.dm_resource_governor_resource_pools_history_ex](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-resource-governor-resource-pools-history-ex-azure-sql-database)|Returnerar resurspoolens användningsstatistik för de senaste 32 minuterna. Varje rad representerar ett intervall på 20 sekunder. Kolumnerna `delta_` returnerar ändringen i varje statistik under intervallet.|
|[sys.dm_resource_governor_workload_groups_history_ex](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-resource-governor-workload-groups-history-ex-azure-sql-database)|Returnerar användningsstatistik för arbetsbelastningsgrupper för de senaste 32 minuterna. Varje rad representerar ett intervall på 20 sekunder. Kolumnerna `delta_` returnerar ändringen i varje statistik under intervallet.|
|||

Dessa vyer kan användas för att övervaka resursutnyttjande och felsöka resurskonkurrens i nära realtid. Användararbetsbelastningen på de primära och läsbara sekundära replikerna, `SloSharedPool1` inklusive georepliker, klassificeras i resurspoolen och `UserPrimaryGroup.DBId[N]` arbetsbelastningsgruppen, där `N` står för databas-ID-värdet.

Förutom att övervaka aktuell resursanvändning kan kunder som använder täta pooler underhålla historiska resursutnyttjandedata i ett separat datalager. Dessa data kan användas i prediktiv analys för att proaktivt hantera resursutnyttjande baserat på historiska och säsongsbetonade trender.

## <a name="operational-recommendations"></a>Operativa rekommendationer

**Lämna tillräckligt med resursutrymme**. Om resurskonkurrens och prestandaförsämring inträffar kan begränsning innebära att vissa databaser flyttas från den berörda elastiska poolen eller att poolen skalas upp, vilket tidigare nämnts. Dessa åtgärder kräver dock ytterligare beräkningsresurser för att slutföra. I synnerhet för Premium- och affärskritiska pooler kräver dessa åtgärder att alla data överförs för de databaser som flyttas, eller för alla databaser i den elastiska poolen om poolen skalas upp. Dataöverföring är en tidskrävande och resurskrävande åtgärd. Om poolen redan är under högt resurstryck försämrar själva förmildrande åtgärden prestanda ytterligare. I extrema fall kanske det inte går att lösa resurskonkurrens via databasflytt eller pooluppskalning eftersom de nödvändiga resurserna inte är tillgängliga. I det här fallet kan tillfälligt minska frågearbetsbelastningen på den berörda elastiska poolen vara den enda lösningen.

Kunder som använder täta pooler bör noga övervaka resursutnyttjandetrender som beskrivits tidigare och vidta förmildrande åtgärder medan mått ligger inom de rekommenderade intervallen och det fortfarande finns tillräckliga resurser i den elastiska poolen.

Resursutnyttjandet beror på flera faktorer som ändras med tiden för varje databas och varje elastisk pool. För att uppnå optimalt pris/prestandaförhållande i täta pooler krävs kontinuerlig övervakning och ombalansering, det vill än att flytta databaser från mer utnyttjade pooler till mindre utnyttjade pooler och skapa nya pooler efter behov för att hantera ökad arbetsbelastning.

**Flytta inte "heta" databaser**. Om resurskonkurrens på poolnivå främst orsakas av ett litet antal högt utnyttjade databaser kan det vara frestande att flytta dessa databaser till en mindre utnyttjad pool eller göra dem till fristående databaser. Att göra detta medan en databas fortfarande är mycket utnyttjad rekommenderas dock inte, eftersom flytten ytterligare försämrar prestanda, både för databasen som flyttas och för hela poolen. Vänta i stället tills det höga utnyttjandet avtar eller flytta mindre utnyttjade databaser i stället för att minska resurstrycket på poolnivå. Men att flytta databaser med mycket lågt utnyttjande ger ingen fördel i det här fallet, eftersom det inte väsentligt minskar resursutnyttjandet på poolnivå.

**Skapa nya databaser i en "karantänpool".** I scenarier där nya databaser skapas ofta, till exempel program som använder modellen klient per databas, finns det risk för att en ny databas som placeras i en befintlig elastisk pool oväntat förbrukar betydande resurser och påverkar andra databaser och interna processer i poolen. För att minska den här risken skapar du en separat "karantänpool" med gott om resursfördelning. Använd den här poolen för nya databaser med ännu okända resursförbrukningsmönster. När en databas har stannat i den här poolen för en affärscykel, till exempel en vecka eller en månad, och dess resursförbrukning är känd, kan den flyttas till en pool med tillräcklig kapacitet för att hantera den här ytterligare resursanvändningen.

**Undvik alltför täta logiska servrar**. Azure SQL Database [stöder](https://docs.microsoft.com/azure/sql-database/sql-database-resource-limits-database-server) upp till 5000 databaser per logisk server. Kunder som använder elastiska pooler med tusentals databaser kan överväga att placera flera elastiska pooler på en enda server, med det totala antalet databaser upp till gränsen som stöds. Logiska servrar med tusentals databaser skapar dock operativa utmaningar. Åtgärder som kräver att alla databaser räknas upp på en server, till exempel visa databaser i portalen, blir långsammare. Driftfel, till exempel felaktig ändring av inloggningar på servernivå eller brandväggsregler, påverkar ett större antal databaser. Oavsiktlig borttagning av den logiska servern kräver hjälp från Microsoft Support för att återställa databaser på den borttagna servern och orsakar ett långvarigt avbrott för alla berörda databaser.

Vi rekommenderar att du begränsar antalet databaser per logisk server till ett lägre antal än det högsta antal som stöds. I många fall är det optimalt att använda upp till 1 000-2000 databaser per server. Om du vill minska risken för oavsiktlig borttagning av servern rekommenderar vi att du placerar ett [borttagningslås](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-lock-resources) på den logiska servern eller dess resursgrupp.

Tidigare var vissa scenarier med att flytta databaser in, ut eller mellan elastiska pooler på samma logiska server snabbare än när du flyttar databaser mellan logiska servrar. För närvarande körs alla databasflyttningar med samma hastighet oavsett käll- och mål logisk server.

## <a name="examples"></a>Exempel

### <a name="monitoring-memory-utilization"></a>Övervaka minnesanvändning

Den här frågan `oom_per_second` beräknar måttet för varje resurspool under de senaste 32 minuterna. Den här frågan kan köras i valfri databas i en elastisk pool.

```
SELECT pool_id,
       name AS resource_pool_name,
       IIF(name LIKE 'SloSharedPool%' OR name LIKE 'UserPool%', 'user', 'system') AS resource_pool_type,
       SUM(CAST(delta_out_of_memory_count AS decimal))/(SUM(duration_ms)/1000.) AS oom_per_second
FROM sys.dm_resource_governor_resource_pools_history_ex
GROUP BY pool_id, name
ORDER BY pool_id;
```
### <a name="monitoring-tempdb-log-space-utilization"></a>Övervakning `tempdb` av utnyttjande av loggutrymme

Den här frågan returnerar `tempdb_log_used_percent` det aktuella värdet för måttet. Den här frågan kan köras i valfri databas i en elastisk pool.

```
SELECT used_log_space_in_percent AS tempdb_log_used_percent
FROM tempdb.sys.dm_db_log_space_usage;
```

## <a name="next-steps"></a>Nästa steg

* En introduktion till elastiska pooler finns i [Elastiska pooler som hjälper dig att hantera och skala flera Azure SQL-databaser](https://docs.microsoft.com/azure/sql-database/sql-database-elastic-pool).
* Information om hur du justerar frågearbetsbelastningar för att minska resursanvändningen finns i [Övervaka och justera]( https://docs.microsoft.com/azure/sql-database/sql-database-monitoring-tuning-index)och övervaka och justera [prestanda.](https://docs.microsoft.com/azure/sql-database/sql-database-monitor-tune-overview)
