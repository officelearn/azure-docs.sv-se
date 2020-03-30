---
title: Typer av frågeprestandaproblem i Azure SQL Database
description: I den här artikeln kan du läsa mer om typer av frågeprestandaproblem i Azure SQL Database och även lära dig hur du identifierar och löser frågor med dessa problem
services: sql-database
ms.service: sql-database
ms.subservice: performance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: jrasnick, carlrab
ms.date: 03/10/2020
ms.openlocfilehash: e155321c2727bf4ee871ef7be7b61b6a523ec1fc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79256138"
---
# <a name="detectable-types-of-query-performance-bottlenecks-in-azure-sql-database"></a>Detekterbara typer av flaskhalsar i frågeprestanda i Azure SQL Database

När du försöker lösa en flaskhals i prestanda börjar du med att avgöra om flaskhalsen uppstår medan frågan är i körläge eller ett vänteläge. Olika upplösningar applicerar beroende på denna beslutsamhet. Använd följande diagram för att förstå de faktorer som kan orsaka antingen ett problem som körs eller ett vänterelaterade problem. Problem och lösningar som rör varje typ av problem diskuteras i den här artikeln.

Du kan använda Azure SQL Database [Intelligent Insights](sql-database-intelligent-insights-troubleshoot-performance.md#detectable-database-performance-patterns) eller SQL Server [DMV:er](sql-database-monitoring-with-dmvs.md) för att identifiera dessa typer av flaskhalsar i prestanda.

![Arbetsbelastningstillstånd](./media/sql-database-monitor-tune-overview/workload-states.png)

**Körrelaterade problem**: Körrelaterade problem är i allmänhet relaterade till kompileringsproblem som resulterar i en suboptimal frågeplan eller körningsproblem relaterade till otillräckliga eller överanvända resurser.
**Vänterelaterade problem**: Vänterelaterade problem är i allmänhet relaterade till:

- Lås (blockering)
- I/o
- Påstående relaterad till TempDB-användning
- Minnesbidrag väntar

## <a name="compilation-problems-resulting-in-a-suboptimal-query-plan"></a>Kompileringsproblem som resulterar i en suboptimal frågeplan

En underoptimal plan som genereras av SQL Query Optimizer kan vara orsaken till långsam frågeprestanda. SQL Query Optimizer kan skapa en deloptimal plan på grund av ett index som saknas, inaktuell statistik, en felaktig uppskattning av antalet rader som ska bearbetas eller en felaktig uppskattning av det nödvändiga minnet. Om du vet att frågan har körts snabbare tidigare eller på en annan instans (antingen en hanterad instans eller en SQL Server-instans) kan du jämföra de faktiska körningsplanerna för att se om de är annorlunda.

- Identifiera eventuella saknade index med någon av dessa metoder:

  - Använd [intelligenta insikter](sql-database-intelligent-insights-troubleshoot-performance.md#missing-index).
  - [Databasrådgivare](sql-database-advisor.md) för enskilda och poolade databaser.
  - DVS. I det här exemplet visas effekten av ett index som saknas, hur du identifierar saknade [index](sql-database-performance-guidance.md#identifying-and-adding-missing-indexes) med hjälp av DVS och effekten av att implementera den saknade indexrekommendationen.
- Försök att använda [frågetips,](https://docs.microsoft.com/sql/t-sql/queries/hints-transact-sql-query) [uppdatera statistik](https://docs.microsoft.com/sql/t-sql/statements/update-statistics-transact-sql)eller [återskapa index](https://docs.microsoft.com/sql/relational-databases/indexes/reorganize-and-rebuild-indexes) för att få en bättre plan. Aktivera [automatisk plankorrigering](sql-database-automatic-tuning.md) i Azure SQL Database för att automatiskt minska dessa problem.

  I det här [exemplet](sql-database-performance-guidance.md#query-tuning-and-hinting) visas effekten av en suboptimal frågeplan på grund av en parameteriserad fråga, hur du identifierar det här villkoret och hur du använder en frågeledtråd för att matcha.

- Prova att ändra kompatibilitetsnivån för databasen och implementera intelligent frågebearbetning. SQL Query Optimizer kan generera en annan frågeplan beroende på databasens kompatibilitetsnivå. Högre kompatibilitetsnivåer ger [intelligentare funktioner för frågebearbetning](https://docs.microsoft.com/sql/relational-databases/performance/intelligent-query-processing).

  - Mer information om frågebearbetning finns i [Frågebearbetningsarkitekturguiden](https://docs.microsoft.com/sql/relational-databases/query-processing-architecture-guide).
  - Information om hur du ändrar kompatibilitetsnivåer för databaser och läser mer om skillnaderna mellan kompatibilitetsnivåerna [i ALTER DATABASE](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql-compatibility-level).
  - Mer information om kardinalitetsuppskattning finns i [Kardinalitetsuppskattning](https://docs.microsoft.com/sql/relational-databases/performance/cardinality-estimation-sql-server)

## <a name="resolving-queries-with-suboptimal-query-execution-plans"></a>Lösa frågor med underoptimala frågekörningsplaner

I följande avsnitt beskrivs hur du löser frågor med underoptimal frågekörningsplan.

### <a name="queries-that-have-parameter-sensitive-plan-psp-problems"></a><a name="ParamSniffing"></a>Frågor som har problem med parameterkänslig plan (PSP)

Ett parameterkänsligt planproblem (PSP) uppstår när frågeoptimeraren genererar en frågekörningsplan som är optimal endast för ett visst parametervärde (eller uppsättning värden) och den cachelagrade planen är då inte optimal för parametervärden som används i följd Avrättningar. Planer som inte är optimala kan sedan orsaka problem med frågeprestanda och försämra det totala dataflödet för arbetsbelastningen.

Mer information om parametersniffning och frågebearbetning finns i [arkitekturguiden för frågebearbetning](/sql/relational-databases/query-processing-architecture-guide#ParamSniffing).

Flera lösningar kan minska PSP-problem. Varje lösning har associerade kompromisser och nackdelar:

- Använd [frågetipset RECOMPILE](https://docs.microsoft.com/sql/t-sql/queries/hints-transact-sql-query) vid varje frågekörning. Den här lösningen handlar om kompileringstid och ökad cpu för bättre plankvalitet. Alternativet `RECOMPILE` är ofta inte möjligt för arbetsbelastningar som kräver ett högt dataflöde.
- Använd [frågetipset OPTION (OPTIMERA FÖR...) för](https://docs.microsoft.com/sql/t-sql/queries/hints-transact-sql-query) att åsidosätta det faktiska parametervärdet med ett typiskt parametervärde som ger en plan som är tillräckligt bra för de flesta parametervärdemöjligheter. Det här alternativet kräver en god förståelse av optimala parametervärden och tillhörande planegenskaper.
- Använd [frågetipset OPTION (OPTIMERA FÖR OKÄND) för](https://docs.microsoft.com/sql/t-sql/queries/hints-transact-sql-query) att åsidosätta det faktiska parametervärdet och i stället använda densitetsvektorgenomsnittet. Du kan också göra detta genom att samla in inkommande parametervärden i lokala variabler och sedan använda de lokala variablerna i predikaten i stället för att använda parametrarna själva. För denna fix måste den genomsnittliga densiteten vara *tillräckligt bra*.
- Inaktivera parametersniffning helt med hjälp av [DISABLE_PARAMETER_SNIFFING](https://docs.microsoft.com/sql/t-sql/queries/hints-transact-sql-query) frågetipset.
- Använd [frågetipset KEEPFIXEDPLAN](https://docs.microsoft.com/sql/t-sql/queries/hints-transact-sql-query) för att förhindra omkompilering i cacheminnet. Den här lösningen förutsätter att den tillräckligt bra gemensamma planen redan är den i cacheminnet. Du kan också inaktivera automatiska statistikuppdateringar för att minska risken för att den bra planen kommer att vräkas och en ny dålig plan kommer att sammanställas.
- Tvinga planen genom att uttryckligen använda [frågetipset USE PLAN](https://docs.microsoft.com/sql/t-sql/queries/hints-transact-sql-query) genom att skriva om frågan och lägga till tipset i frågetexten. Eller ange en specifik plan med hjälp av Query Store eller genom att aktivera [automatisk justering](sql-database-automatic-tuning.md).
- Ersätt den enstaka proceduren med en kapslad uppsättning procedurer som var och en kan användas baserat på villkorslogik och associerade parametervärden.
- Skapa alternativ för dynamisk strängkörning till en statisk procedurdefinition.

Mer information om hur du löser PSP-problem finns i följande blogginlägg:

- [Jag luktar en parameter](https://docs.microsoft.com/archive/blogs/queryoptteam/i-smell-a-parameter)
- [Conor vs dynamiska SQL vs. procedurer kontra plankvalitet för parameteriserade frågor](https://blogs.msdn.microsoft.com/conor_cunningham_msft/2009/06/03/conor-vs-dynamic-sql-vs-procedures-vs-plan-quality-for-parameterized-queries/)
- [SQL-frågeoptimeringstekniker i SQL Server: Parametersniffning](https://www.sqlshack.com/query-optimization-techniques-in-sql-server-parameter-sniffing/)

### <a name="compile-activity-caused-by-improper-parameterization"></a>Kompilera aktivitet som orsakas av felaktig parameterisering

När en fråga har litteraler, antingen databasmotorn automatiskt parameteriserar satsen eller en användare uttryckligen parameteriserar satsen för att minska antalet kompileringar. Ett stort antal kompileringar för en fråga med samma mönster men olika litterala värden kan resultera i hög CPU-användning. På samma sätt, om du bara delvis parameterisera en fråga som fortsätter att ha litteraler, skapar databasmotorn inte frågan ytterligare.

Här är ett exempel på en delvis parameteriserad fråga:

```sql
SELECT *
FROM t1 JOIN t2 ON t1.c1 = t2.c1
WHERE t1.c1 = @p1 AND t2.c2 = '961C3970-0E54-4E8E-82B6-5545BE897F8F'
```

I det `t1.c1` här `@p1`exemplet `t2.c2` tar , men fortsätter att ta GUID som litteral. I det här fallet, om `c2`du ändrar värdet för , behandlas frågan som en annan fråga och en ny kompilering kommer att ske. Om du vill minska kompileringen i det här exemplet skulle du också parametriera GUID.

Följande fråga visar antalet frågor per frågehh för att avgöra om en fråga är korrekt parameteriserad:

```sql
SELECT TOP 10
  q.query_hash
  , count (distinct p.query_id ) AS number_of_distinct_query_ids
  , min(qt.query_sql_text) AS sampled_query_text
FROM sys.query_store_query_text AS qt
  JOIN sys.query_store_query AS q
     ON qt.query_text_id = q.query_text_id
  JOIN sys.query_store_plan AS p
     ON q.query_id = p.query_id
  JOIN sys.query_store_runtime_stats AS rs
     ON rs.plan_id = p.plan_id
  JOIN sys.query_store_runtime_stats_interval AS rsi
     ON rsi.runtime_stats_interval_id = rs.runtime_stats_interval_id
WHERE
  rsi.start_time >= DATEADD(hour, -2, GETUTCDATE())
  AND query_parameterization_type_desc IN ('User', 'None')
GROUP BY q.query_hash
ORDER BY count (distinct p.query_id) DESC
```

### <a name="factors-that-affect-query-plan-changes"></a>Faktorer som påverkar frågeplansändringar

En omkompileringsplan för frågekörningsplan kan resultera i en genererad frågeplan som skiljer sig från den ursprungliga cachelagrade planen. En befintlig ursprungsplan kan automatiskt kompileras om av olika skäl:

- Ändringar i schemat refereras av frågan
- Dataändringar i tabellerna refereras av frågan
- Alternativ för frågekontext har ändrats

En kompilerad plan kan matas ut från cacheminnet av olika skäl, till exempel:

- Instansen startas om
- Konfigurationsändringar för databasomfattade
- Minnesbelastning
- Explicita begäranden om att rensa cacheminnet

Om du använder en OMKOMPILERINGSTIPS cachelagras inte en plan.

En omkompilering (eller ny kompilering efter cachevräkning) kan fortfarande resultera i generering av en frågekörningsplan som är identisk med originalet. När planen ändras från den tidigare eller ursprungliga planen är dessa förklaringar sannolikt:

- **Ändrad fysisk design**: Till exempel täcker nyskapade index mer effektivt kraven för en fråga. De nya indexen kan användas på en ny kompilering om frågeoptimeraren bestämmer sig för att det nya indexet är mer optimalt än att använda den datastruktur som ursprungligen valdes för den första versionen av frågekörningen. Fysiska ändringar av de refererade objekten kan resultera i ett nytt planval vid kompilering.

- **Skillnader i serverresurs:** När en plan i ett system skiljer sig från planen i ett annat system kan resurstillgänglighet, till exempel antalet tillgängliga processorer, påverka vilken plan som genereras. Om ett system till exempel har fler processorer kan en parallell plan väljas.

- **Annan statistik**: Statistiken som är kopplad till de refererade objekten kan ha ändrats eller kan skilja sig väsentligt från det ursprungliga systemets statistik. Om statistiken ändras och en omkompilering sker använder frågeoptimeraren statistiken från och med när den ändrades. Den reviderade statistikens datafördelningar och datafrekvenser kan skilja sig från den ursprungliga sammanställningen. Dessa ändringar används för att skapa kardinalitetsuppskattningar. (*Kardinalitetsuppskattningar* är antalet rader som förväntas flöda genom det logiska frågeträdet.) Ändringar av kardinalitetsuppskattningar kan leda till att du väljer olika fysiska operatorer och associerade order av operationer. Även mindre ändringar av statistik kan resultera i en ändrad frågekörningsplan.

- **Ändrad databaskompatibilitetsnivå eller kardinalitetsuppskattningsversion**: Ändringar i databaskompatibilitetsnivån kan aktivera nya strategier och funktioner som kan resultera i en annan frågekörningsplan. Utöver databaskompatibilitetsnivån kan en inaktiverad eller aktiverad spårningsflagga 4199 eller ett ändrat tillstånd för den databasomfattade konfigurationen QUERY_OPTIMIZER_HOTFIXES också påverka valen för frågekörningsplan vid kompileringstillfället. Spåra flaggor 9481 (tvinga äldre CE) och 2312 (kraft standard CE) påverkar också planen.

## <a name="resource-limits-issues"></a>Problem med resursbegränsningar

Långsamma frågeprestanda som inte är relaterade till suboptimala frågeplaner och saknade index är i allmänhet relaterade till otillräckliga eller överanvända resurser. Om frågeplanen är optimal kan frågan (och databasen) slå mot resursgränserna för databasen, den elastiska poolen eller hanterade instansen. Ett exempel kan vara överskott logg skriva dataflöde för servicenivå.

- Identifiera resursproblem med Hjälp av Azure-portalen: Information om resursgränser är problemet finns i [SQL Database-resursövervakning](sql-database-monitor-tune-overview.md#sql-database-resource-monitoring). För enskilda databaser och elastiska pooler finns i [prestandarekommendationer för Databasrådgivare](sql-database-advisor.md) och [Frågeprestandainsikter](sql-database-query-performance.md).
- Identifiera resursgränser med hjälp av [Intelligenta insikter](sql-database-intelligent-insights-troubleshoot-performance.md#reaching-resource-limits)
- Identifiera resursproblem med hjälp av [DMVs:](sql-database-monitoring-with-dmvs.md)

  - [Sys.dm_db_resource_stats](sql-database-monitoring-with-dmvs.md#monitor-resource-use) DMV returnerar CPU, I/O och minnesförbrukning för en SQL-databas. Det finns en rad för varje intervall på 15 sekunder, även om det inte finns någon aktivitet i databasen. Historiska data bevaras i en timme.
  - [Sys.resource_stats](sql-database-monitoring-with-dmvs.md#monitor-resource-use) DMV returnerar CPU-användning och lagringsdata för Azure SQL Database. Uppgifterna samlas in och aggregeras i fem minuters intervall.
  - [Många enskilda frågor som kumulativt förbrukar hög CPU](sql-database-monitoring-with-dmvs.md#many-individual-queries-that-cumulatively-consume-high-cpu)
  - 

Om du identifierar problemet som otillräcklig resurs kan du uppgradera resurser för att öka kapaciteten för SQL-databasen för att absorbera CPU-kraven. Mer information finns [i Skala enskilda databasresurser i Azure SQL Database](sql-database-single-database-scale.md) och Skala [elastiska poolresurser i Azure SQL Database](sql-database-elastic-pool-scale.md). Information om hur du skalar en hanterad instans finns i [Resursbegränsningar på tjänstnivå](sql-database-managed-instance-resource-limits.md#service-tier-characteristics).

## <a name="performance-problems-caused-by-increased-workload-volume"></a>Prestandaproblem som orsakas av ökad arbetsbelastningsvolym

En ökning av programtrafik och arbetsbelastningsvolym kan orsaka ökad CPU-användning. Men du måste vara noga med att korrekt diagnostisera detta problem. När du ser ett problem med hög cpu kan du svara på de här frågorna för att avgöra om ökningen orsakas av ändringar i arbetsbelastningsvolymen:

- Är frågorna från programmet orsaken till problemet med hög CPU?
- För de [översta CPU-tidskrävande frågor som du kan identifiera:](sql-database-monitoring-with-dmvs.md#the-cpu-issue-occurred-in-the-past)

  - Var flera körningsplaner associerade med samma fråga? Om ja, varför?
  - Var körningstiderna konsekventa för frågor med samma körningsplan? Ökade antalet utföranden? Om så är fallet, arbetsbelastningen ökar sannolikt orsakar prestandaproblem.

Sammanfattningsvis, om frågekörningsplanen inte har körningen annorlunda men CPU-användningen ökade tillsammans med körningsantal, är prestandaproblemet sannolikt relaterat till en arbetsbelastningsökning.

Det är inte alltid lätt att identifiera en arbetsbelastning volymförändring som driver en CPU-problem. Tänk på dessa faktorer:

- **Ändrad resursanvändning**: Tänk till exempel på ett scenario där CPU-användningen ökade till 80 procent under en längre tidsperiod. CPU-användning ensam betyder inte att arbetsbelastningsvolymen har ändrats. Regressioner i frågekörningsplanen och ändringar i datadistribution kan också bidra till mer resursanvändning även om programmet kör samma arbetsbelastning.

- **Utseendet**på en ny fråga : Ett program kan driva en ny uppsättning frågor vid olika tidpunkter.

- **En ökning eller minskning av antalet begäranden**: Det här scenariot är det mest uppenbara måttet på en arbetsbelastning. Antalet frågor motsvarar inte alltid mer resursutnyttjande. Detta mått är dock fortfarande en betydande signal, förutsatt att andra faktorer är oförändrade.

Använd Intelligenta insikter för att identifiera [arbetsbelastningsökningar](sql-database-intelligent-insights-troubleshoot-performance.md#workload-increase) och [planera regressioner](sql-database-intelligent-insights-troubleshoot-performance.md#plan-regression).

## <a name="waiting-related-problems"></a>Vänterelaterade problem

När du har eliminerat en deloptimal plan och *vänterelaterade* problem som är relaterade till körningsproblem, är prestandaproblemet i allmänhet frågorna väntar förmodligen på vissa resurser. Vänterelaterade problem kan orsakas av:

- **Blockering:**

  En fråga kan innehålla låset på objekt i databasen medan andra försöker komma åt samma objekt. Du kan identifiera blockeringsfrågor med hjälp av [DMV:er](sql-database-monitoring-with-dmvs.md#monitoring-blocked-queries) eller [Intelligenta insikter](sql-database-intelligent-insights-troubleshoot-performance.md#locking).
- **IO-problem**

  Frågor kan vänta på att sidorna ska skrivas till data eller loggfiler. I det här `INSTANCE_LOG_RATE_GOVERNOR`fallet `WRITE_LOG`kontrollerar du statistik för , eller `PAGEIOLATCH_*` vänta i DMV. Se använda DMV:er för att [identifiera I/O-prestandaproblem](sql-database-monitoring-with-dmvs.md#identify-io-performance-issues).
- **TempDB problem**

  Om arbetsbelastningen använder temporära tabeller eller om det finns TempDB-spill i planerna kan frågorna ha problem med TempDB-dataflöde. Se använda DMV:er för [att identifiera TempDB-problem](sql-database-monitoring-with-dmvs.md#identify-tempdb-performance-issues).
- **Minnesrelaterade problem**

  Om arbetsbelastningen inte har tillräckligt med minne kan sidlivslängden minska eller så kan frågorna få mindre minne än de behöver. I vissa fall åtgärdar inbyggd intelligens i Frågeoptimer minnesrelaterade problem. Se använda DMV:er för att [identifiera problem med minnesbidrag](sql-database-monitoring-with-dmvs.md#identify-memory-grant-wait-performance-issues).

### <a name="methods-to-show-top-wait-categories"></a>Metoder för att visa de bästa väntekategorierna

Dessa metoder används ofta för att visa de bästa kategorierna av väntetyper:

- Använd Intelligenta insikter för att identifiera frågor med prestandaförsämring på grund av [ökade väntetider](sql-database-intelligent-insights-troubleshoot-performance.md#increased-wait-statistic)
- Använd [Query Store](https://docs.microsoft.com/sql/relational-databases/performance/monitoring-performance-by-using-the-query-store) för att hitta väntestatistik för varje fråga över tid. I Query Store kombineras väntetyper till väntekategorier. Du hittar mappningen av väntekategorier för att vänta typer i [sys.query_store_wait_stats](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-query-store-wait-stats-transact-sql#wait-categories-mapping-table).
- Använd [sys.dm_db_wait_stats](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-db-wait-stats-azure-sql-database) för att returnera information om alla vänta som påträffas av trådar som kördes under en frågeåtgärd. Du kan använda den här aggregerade vyn för att diagnostisera prestandaproblem med Azure SQL Database och även med specifika frågor och batchar. Frågor kan vänta på resurser, kö väntar eller externa väntar.
- Använd [sys.dm_os_waiting_tasks](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-os-waiting-tasks-transact-sql) för att returnera information om kön med aktiviteter som väntar på vissa resurser.

I hög-CPU-scenarier kanske Query Store- och wait-statistik inte återspeglar CPU-användning om:

- Frågor som konsumerar hög cpu körs fortfarande.
- De frågor som konsumerar med hög CPU kördes när en redundans inträffade.

DMVs som spårar Query Store och vänta statistik visar resultat för endast framgångsrikt avslutade och tidsinställda frågor. De visar inte data för körning av utdrag förrän uttalandena är klara. Använd den dynamiska [hanteringsvyn sys.dm_exec_requests](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-requests-transact-sql) för att spåra frågor som körs och tillhörande arbetartid.

> [!TIP]
> Ytterligare verktyg:
>
> - [TigerToolbox väntar och lås](https://github.com/Microsoft/tigertoolbox/tree/master/Waits-and-Latches)
> - [TigerToolbox usp_whatsup](https://github.com/Microsoft/tigertoolbox/tree/master/usp_WhatsUp)

## <a name="next-steps"></a>Nästa steg

[Översikt över övervakning och justering av SQL-databas](sql-database-monitor-tune-overview.md)