---
title: Övervakning och prestanda justering – Azure SQL Database
description: Tips för prestanda justering i Azure SQL Database genom utvärdering och förbättringar.
services: sql-database
ms.service: sql-database
ms.subservice: performance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: jrasnick, carlrab
ms.date: 01/25/2019
ms.openlocfilehash: c11112963ec82a0e53df156048495e7b5141bcb7
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/06/2019
ms.locfileid: "73687764"
---
# <a name="monitoring-and-performance-tuning"></a>Övervakning och prestandajustering

Azure SQL Database innehåller verktyg och metoder som du kan använda för att övervaka användningen enkelt, lägga till eller ta bort resurser (till exempel processor, minne eller I/O), felsöka potentiella problem och göra rekommendationer för att förbättra databasens prestanda. Funktioner i Azure SQL Database kan automatiskt åtgärda problem i databaserna. 

Med automatisk justering kan en databas anpassas till arbets belastningen och automatiskt optimera prestanda. Vissa anpassade problem kan dock behöva fel sökning. I den här artikeln beskrivs några metod tips och några verktyg som du kan använda för att felsöka prestanda problem.

För att säkerställa att en databas körs utan problem bör du:
- [Övervaka databas prestanda](#monitor-database-performance) för att se till att resurserna som är kopplade till databasen kan hantera arbets belastningen. Om databasen når resurs gränser bör du tänka på följande:
   - Identifiera och optimera de mest resurs krävande frågorna.
   - Lägga till fler resurser genom att [Uppgradera tjänst nivån](https://docs.microsoft.com/azure/sql-database/sql-database-scale-resources).
- [Felsök prestanda problem](#troubleshoot-performance-problems) för att identifiera varför ett möjligt problem har uppstått och identifiera rotor saken till problemet. När du har identifierat rotor saken bör du vidta åtgärder för att åtgärda problemet.

## <a name="monitor-database-performance"></a>Övervaka databasprestanda

Om du vill övervaka prestanda för en SQL-databas i Azure börjar du med att övervaka de resurser som används i förhållande till den nivå av databas prestanda som du har valt. Övervaka följande resurser:
 - **CPU-användning**: kontrol lera om databasen når 100 procent av CPU-användningen under en längre tids period. Hög CPU-användning kan tyda på att du behöver identifiera och finjustera frågor som använder mest beräknings kraft. Hög CPU-användning kan även indikera att databasen eller instansen ska uppgraderas till en högre tjänst nivå. 
 - **Vänta med statistik**: Använd [sys. DM _os_wait_stats (Transact-SQL)](/sql/relational-databases/system-dynamic-management-views/sys-dm-os-wait-stats-transact-sql) för att avgöra hur lång tid frågorna väntar. Frågor kan vänta på resurser, kön väntar eller externa vänte tid. 
 - I **/o-användning**: kontrol lera om databasen når IO-gränserna för det underliggande lagrings utrymmet.
 - **Minnes användning**: mängden tillgängligt minne för databasen eller instansen är proportionell till antalet virtuella kärnor. Kontrol lera att minnet räcker för arbets belastningen. Sidans livs längd förväntad är en av de parametrar som kan indikera hur snabbt sidorna tas bort från minnet.

Tjänsten Azure SQL Database innehåller verktyg och resurser som hjälper dig att felsöka och åtgärda potentiella prestanda problem. Du kan identifiera möjligheter för att förbättra och optimera prestanda för frågor utan att ändra resurser genom att granska [rekommendationer för prestanda justering](sql-database-advisor.md). 

Index som saknas och dåligt optimerade frågor är vanliga orsaker till dåliga databasprestanda. Du kan använda justerings rekommendationer för att förbättra arbets Belastningens prestanda. Du kan också låta Azure SQL Database [automatiskt optimera prestanda för frågorna](sql-database-automatic-tuning.md) genom att tillämpa alla identifierade rekommendationer. Kontrol lera sedan att rekommendationerna har förbättrade databas prestanda.

> [!NOTE]
> Indexering är endast tillgängligt i enkla databaser och elastiska pooler. Indexering är inte tillgängligt i en hanterad instans.

Välj bland följande alternativ för att övervaka och felsöka databas prestanda:

- I [Azure Portal](https://portal.azure.com)väljer du **SQL-databaser** och väljer databasen. I **övervaknings** diagrammet söker du efter resurser som närmar sig den maximala användningen. DTU-förbrukning visas som standard. Välj **Redigera** för att ändra tidsintervallet och värdena som visas.
- Verktyg som SQL Server Management Studio ger många användbara rapporter, t. ex. [prestanda instrument panel](https://docs.microsoft.com/sql/relational-databases/performance/performance-dashboard). Använd de här rapporterna för att övervaka resursanvändningen och identifiera de främsta resurs krävande frågorna. Du kan använda [query Store](https://docs.microsoft.com/sql/relational-databases/performance/monitoring-performance-by-using-the-query-store#Regressed) för att identifiera frågor vars prestanda har försämrat.
- I [Azure Portal](https://portal.azure.com)använder du [query Performance Insight](sql-database-query-performance.md) för att identifiera de frågor som använder de flesta resurser. Den här funktionen är endast tillgänglig i enkla databaser och elastiska pooler.
- Använd [SQL Database Advisor](sql-database-advisor-portal.md) för att Visa rekommendationer som hjälper dig att skapa och släppa index, Parameterisera frågor och åtgärda schema problem. Den här funktionen är endast tillgänglig i enkla databaser och elastiska pooler.
- Använd [Azure SQL-intelligent Insights](sql-database-intelligent-insights.md) för att övervaka databas prestanda automatiskt. När ett prestanda problem upptäcks genereras en diagnostisk logg. Loggen innehåller information och en rotor Saks analys (RCA) av problemet. En rekommendation om prestanda förbättring tillhandahålls när det är möjligt.
- [Aktivera automatisk justering](sql-database-automatic-tuning-enable.md) för att låta Azure SQL Database automatiskt åtgärda prestanda problem.
- Använd [DMV: er (Dynamic Management views)](sql-database-monitoring-with-dmvs.md), [Extended Events](sql-database-xevent-db-diff-from-svr.md)och [query Store](https://docs.microsoft.com/sql/relational-databases/performance/monitoring-performance-by-using-the-query-store) om du vill ha mer detaljerad fel sökning av prestanda problem.

> [!TIP]
> När du har identifierat ett prestanda problem kan du läsa vår [prestanda vägledning](sql-database-performance-guidance.md) för att hitta metoder för att förbättra prestandan för Azure SQL Database.

## <a name="troubleshoot-performance-problems"></a>Felsöka prestanda problem

För att diagnostisera och lösa prestanda problem börjar du med att ta reda på status för varje aktiv fråga och de villkor som orsakar prestanda problem som är relevanta för varje arbets belastnings tillstånd. För att förbättra Azure SQL Database prestanda måste du förstå att varje aktiv fråga från programmet är i ett körnings tillstånd eller ett vänte läge. När du felsöker ett prestanda problem i Azure SQL Database bör du tänka på följande diagram.

![Arbets belastnings status](./media/sql-database-monitor-tune-overview/workload-states.png)

Ett prestanda problem i en arbets belastning kan orsakas av CPU-konkurrens (ett *Kör-relaterat* villkor) eller enskilda frågor som väntar på något (ett *väntande* villkor).

Körnings problem kan bero på följande:
- **Kompileringsfel**: SQL Query Optimering kan producera ett underoptimerat schema på grund av inaktuell statistik, en felaktig uppskattning av antalet rader som ska bearbetas eller en felaktig beräkning av nödvändigt minne. Om du vet att frågan kördes snabbare tidigare eller på en annan instans (antingen en hanterad instans eller en SQL Server instans) kan du jämföra de faktiska körnings planerna för att se om de är olika. Försök att använda frågetipset eller återskapa statistik eller index för att få bättre planer. Aktivera automatisk plan korrigering i Azure SQL Database för att automatiskt minimera de här problemen.
- **Körnings problem**: om frågesträngen är optimal, kommer den förmodligen att träffa databasens resurs gränser, till exempel logg skrivnings data flöde. Eller så kanske det använder fragmenterade index som ska återskapas. Körnings problem kan också inträffa när ett stort antal samtidiga frågor behöver samma resurser. *Väntande* problem är ofta relaterade till körnings problem, eftersom frågor som inte körs effektivt väntar på vissa resurser.

Väntande-relaterade problem kan orsakas av:
- **Blockering**: en fråga kan innehålla låset på objekt i databasen medan andra försöker komma åt samma objekt. Du kan identifiera blockerade frågor med hjälp av DMV: er eller övervaknings verktyg.
- **I/o-problem**: frågor kan vänta på att sidorna skrivs till data-eller loggfilerna. I det här fallet kontrollerar du `INSTANCE_LOG_RATE_GOVERNOR`, `WRITE_LOG`eller `PAGEIOLATCH_*` väntar på statistik i DMV.
- **Tempdb-problem**: om arbets belastningen använder temporära tabeller eller om det finns tempdb-spill i planerna kan frågorna ha problem med tempdb-dataflöde. 
- **Minnesrelaterade problem**: om arbets belastningen inte har tillräckligt med minne, kan det hända att förväntad släpps eller att frågorna kan få mindre minne än de behöver. I vissa fall kommer inbyggd intelligens i fråga optimering att åtgärda minnesrelaterade problem.
 
I följande avsnitt beskrivs hur du identifierar och felsöker vissa typer av problem.

## <a name="performance-problems-related-to-running"></a>Prestanda problem som rör körning

Som en allmän rikt linje, om CPU-användningen är konsekvent på eller över 80 procent, körs prestanda problemet. Ett pågående problem kan bero på otillräckliga processor resurser. Eller så kan det vara relaterat till något av följande villkor:

- För många körnings frågor
- För många kompilerings frågor
- En eller flera körnings frågor som använder en underoptimerad frågeplan

Om du hittar ett pågående prestanda problem är målet att identifiera det exakta problemet genom att använda en eller flera metoder. Dessa metoder är de vanligaste sätten att identifiera problem som rör körning:

- Använd [Azure Portal](sql-database-manage-after-migration.md#monitor-databases-using-the-azure-portal) för att övervaka användningen av CPU-procent.
- Använd följande [DMV: er](sql-database-monitoring-with-dmvs.md):

  - [Sys. DM _db_resource_stats](sql-database-monitoring-with-dmvs.md#monitor-resource-use) DMV returnerar CPU, I/O och minnes användning för en SQL-databas. Det finns en rad för varje 15-sekunders intervall, även om det inte finns någon aktivitet i databasen. Historiska data bevaras i en timme.
  - [Sys. resource_stats](sql-database-monitoring-with-dmvs.md#monitor-resource-use) DMV returnerar CPU-användning och lagrings data för Azure SQL Database. Data samlas in och sammanställs i fem minuters intervall.

> [!IMPORTANT]
> Information om hur du felsöker CPU-problem för T-SQL-frågor som använder sys. DM _db_resource_stats och sys. resource_stats DMV: er finns i [identifiera problem med processor prestanda](sql-database-monitoring-with-dmvs.md#identify-cpu-performance-issues).

### <a name="ParamSniffing"></a>Frågor som har PSP-problem

Det uppstår ett problem med en parameter känslig plan (PSP) när frågans optimering genererar en plan för frågekörningen som är optimal endast för ett särskilt parameter värde (eller uppsättning värden) och den cachelagrade planen är inte optimal för parameter värden som används i följd körningar. Planer som inte är optimala kan sedan orsaka problem med frågans prestanda och försämra det totala data flödet. 

Mer information om parameter-och bearbetning av frågor finns i [arkitektur guiden för frågekörning](/sql/relational-databases/query-processing-architecture-guide#ParamSniffing).

Flera lösningar kan minimera PSP-problem. Varje lösning har tillhör ande kompromisser och nack delar:

- Använd frågetipset för att [kompilera](https://docs.microsoft.com/sql/t-sql/queries/hints-transact-sql-query) om frågan vid varje frågekörningen. Den här lösningen för att kompilera den här lösningen och öka CPU-tiden för bättre plan kvalitet. Alternativet `RECOMPILE` är ofta inte möjligt för arbets belastningar som kräver ett högt data flöde.
- Använd frågetipset [alternativ (Optimize for...)](https://docs.microsoft.com/sql/t-sql/queries/hints-transact-sql-query) för att åsidosätta det faktiska parametervärdet med ett typiskt parameter värde som ger en plan som är tillräckligt stor för de flesta värde för parameter värden. Det här alternativet kräver en god förståelse av optimala parameter värden och associerade plan egenskaper.
- Använd frågetipset [OPTION (Optimize for UNknown)](https://docs.microsoft.com/sql/t-sql/queries/hints-transact-sql-query) för att åsidosätta det faktiska parametervärdet och Använd i stället densiteten densitet Vector. Du kan också göra detta genom att samla in inkommande parameter värden i lokala variabler och sedan använda de lokala variablerna i predikat i stället för att använda själva parametrarna. För den här korrigeringen måste den genomsnittliga densiteten vara *tillräckligt hög*.
- Inaktivera parameter identifiering helt och hållet med hjälp av [DISABLE_PARAMETER_SNIFFING](https://docs.microsoft.com/sql/t-sql/queries/hints-transact-sql-query) -frågetipset.
- Använd [KEEPFIXEDPLAN](https://docs.microsoft.com/sql/t-sql/queries/hints-transact-sql-query) -frågetipset för att förhindra omkompileringar i cacheminnet. Den här lösningen förutsätter att den tillräckligt höga planen är den som redan finns i cacheminnet. Du kan också inaktivera automatiska statistik uppdateringar för att minska risken för att den bra planen kommer att avlägsnas och en ny dålig plan kompileras.
- Tvinga planen genom att uttryckligen använda [use plan](https://docs.microsoft.com/sql/t-sql/queries/hints-transact-sql-query) -frågetipset genom att skriva om frågan och lägga till tipset i frågetexten. Eller ange en speciell plan genom att använda Query Store eller genom att aktivera [Automatisk justering](sql-database-automatic-tuning.md).
- Ersätt den gemensamma proceduren med en kapslad uppsättning procedurer som varje kan användas baserat på villkorlig logik och associerade parameter värden.
- Skapa alternativ för dynamisk sträng körning till en statisk procedur definition.

Mer information om hur du löser PSP-problem finns i följande blogg inlägg:

- [Jag har luktat en parameter](https://blogs.msdn.microsoft.com/queryoptteam/2006/03/31/i-smell-a-parameter/)
- [Conor jämfört med dynamiska SQL vs.-procedurer jämfört med plan kvalitet för parametriserade frågor](https://blogs.msdn.microsoft.com/conor_cunningham_msft/2009/06/03/conor-vs-dynamic-sql-vs-procedures-vs-plan-quality-for-parameterized-queries/)
- [Metoder för optimering av SQL-frågor i SQL Server: parameter avlyssning](https://www.sqlshack.com/query-optimization-techniques-in-sql-server-parameter-sniffing/)

### <a name="compile-activity-caused-by-improper-parameterization"></a>Kompilera aktivitet som orsakas av felaktig Parameterisering

När en fråga har litteraler, parameterizes-databas motorn automatiskt uttrycket eller en användare uttryckligen parameterizes satsen för att minska antalet kompileringar. Ett stort antal kompileringar för en fråga med samma mönster men olika litterala värden kan resultera i hög CPU-användning. Om du bara delvis Parameterisera en fråga som fortsätter att ha litteraler, så Parameterisera inte frågan längre i databas motorn.  

Här är ett exempel på en delvis parametriserad fråga:

```sql
SELECT * 
FROM t1 JOIN t2 ON t1.c1 = t2.c1
WHERE t1.c1 = @p1 AND t2.c2 = '961C3970-0E54-4E8E-82B6-5545BE897F8F'
```

I det här exemplet tar `t1.c1` `@p1`, men `t2.c2` fortsätter att ta GUID som literal. I detta fall behandlas frågan som en annan fråga och en ny kompilering sker om du ändrar värdet för `c2`. För att minska kompileringarna i det här exemplet skulle du också Parameterisera GUID.

Följande fråga visar antalet frågor efter frågans hash för att avgöra om en fråga är korrekt parametriserad:

```sql
SELECT  TOP 10  
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

### <a name="factors-that-affect-query-plan-changes"></a>Faktorer som påverkar ändringar i frågeplan

En omkompilering av en frågeplan kan resultera i en genererad frågeplan som skiljer sig från den ursprungliga cachelagrade planen. En befintlig ursprunglig plan kan omkompileras automatiskt av olika anledningar:
- Ändringar i schemat refereras av frågan.
- Data ändringar i tabellerna refereras till av frågan. 
- Frågans kontext alternativ har ändrats.

En kompilerad plan kan komma att matas ut från cachen av olika anledningar, t. ex.:

- Instansen startas om.
- Konfigurations ändringar i databasen.
- Minnes belastning.
- Explicita begär Anden om att rensa cacheminnet.

Om du använder ett omkompilerings tips cachelagras inte en plan.

En RECOMPILE (eller ny kompilering efter cache-avtagningen) kan fortfarande resultera i att en frågeplan som är identisk med originalet skapas. När planen ändras från föregående eller ursprungliga plan är dessa förklaringar förmodligen följande:

- **Ändrad fysisk design**: till exempel kan nyligen skapade index effektivt hantera kraven för en fråga. Nya index kan användas i en ny kompilering om Query Optimering bestämmer att det nya indexet är mer optimalt än att använda den data struktur som ursprungligen valdes för den första versionen av frågekörningen.  Eventuella fysiska ändringar av de refererade objekten kan leda till ett nytt schema val vid kompileringen.

- **Server resurs skillnader**: när en plan i ett system skiljer sig från planen i ett annat system kan resurs tillgängligheten, till exempel antalet tillgängliga processorer, påverka vilken plan som genereras.  Om ett system till exempel har fler processorer kan du välja en parallell plan. 

- **Annan statistik**: statistiken som är kopplad till de refererade objekten kan ha ändrats eller vara väsentlig annorlunda än det ursprungliga systemets statistik.  Om statistiken ändras och en omkompilering sker, använder Query Optimering statistiken som börjar från när de ändrades. Den reviderade statistikens data distributioner och frekvenser kan skilja sig från de ursprungliga kompileringarna.  Dessa ändringar används för att skapa beräkningar av kardinalitet. (*Beräkning av kardinalitet* är antalet rader som förväntas flöda genom det logiska frågeuttrycket.) Ändringar av beräkningar av kardinalitet kan leda till att du väljer olika fysiska operatörer och tillhör ande åtgärder.  Även smärre ändringar i statistiken kan resultera i en ändrad frågeplan för frågekörningen.

- **Ändrade kompatibilitetsnivån för databas eller kardinalitet**: ändringar av databasens kompatibilitetsnivå kan möjliggöra nya strategier och funktioner som kan resultera i en annan frågeplan för körning.  Utöver kompatibilitetsnivån för databas kan en inaktive rad eller aktive rad spårnings flagga 4199 eller ett ändrat tillstånd för QUERY_OPTIMIZER_HOTFIXES konfiguration av databasen även påverka val av frågeplan vid kompilering.  Spårnings flaggorna 9481 (framtvinga äldre CE) och 2312 (tvinga standard CE) påverkar också planen. 

### <a name="resolve-problem-queries-or-provide-more-resources"></a>Lös problem frågor eller ange fler resurser

När du har identifierat problemet kan du antingen finjustera problemen eller uppgradera beräknings storleken eller tjänst nivån för att öka kapaciteten hos SQL-databasen för att öka processor kraven. 

Mer information finns i [skala enkla databas resurser i Azure SQL Database](sql-database-single-database-scale.md) och [skala elastiska pool resurser i Azure SQL Database](sql-database-elastic-pool-scale.md). Information om hur du skalar en hanterad instans finns i [resurs gränser på tjänst nivå](sql-database-managed-instance-resource-limits.md#service-tier-characteristics).

### <a name="performance-problems-caused-by-increased-workload-volume"></a>Prestanda problem som orsakas av ökad arbets belastnings volym

En ökning av program trafik och arbets belastnings volym kan orsaka ökad CPU-användning. Men du måste vara noga med att diagnostisera det här problemet. När du ser ett högt processor problem kan du svara på dessa frågor för att avgöra om ökningen orsakas av ändringar av arbets belastnings volymen:

- Är frågorna från programmet orsaken till det höga processor problemet?
- För de vanligaste processor krävande frågorna som du kan identifiera:

   - Är flera körnings planer kopplade till samma fråga? I så fall, varför?
   - Kördes körnings tiderna för frågor med samma körnings plan? Ökade körnings antalet? I så fall kan arbets belastnings ökningen troligen orsaka prestanda problem.

I sammanfattning, om körnings planen inte kördes annorlunda men CPU-användningen ökade tillsammans med antalet körningar, är prestanda problemet sannolikt relaterat till en ökad arbets belastning.

Det är inte alltid lätt att identifiera en ändring av arbets belastnings volymer som kör ett CPU-problem. Tänk på följande faktorer: 

- **Ändrad resursanvändning**: anta till exempel ett scenario där CPU-användningen ökade till 80 procent under en längre tids period.  PROCESSOR användningen innebär däremot inte att arbets belastnings volymen har ändrats. Regressioner i fråge körnings planen och ändringar i data distributionen kan också bidra till mer resursanvändning även om programmet kör samma arbets belastning.

- **Utseendet på en ny fråga**: ett program kan köra en ny uppsättning frågor vid olika tidpunkter.

- **En ökning eller minskning av antalet begär Anden**: det här scenariot är det mest uppenbara måttet för en arbets belastning. Antalet frågor motsvarar inte alltid mer resursutnyttjande. Detta mått är dock fortfarande en betydande signal, förutsatt att andra faktorer är oförändrade.

## <a name="waiting-related-performance-problems"></a>Väntande-relaterade prestanda problem 

Om du är säker på att prestanda problemet inte är relaterat till hög CPU-användning eller om du kör, är ditt problem relaterat till väntar. Det innebär att dina CPU-resurser inte används effektivt eftersom processorn väntar på en annan resurs. I det här fallet identifierar du vad dina processor resurser väntar på. 

Dessa metoder används ofta för att visa de översta kategorierna av vänte typer:

- Använd [query Store](https://docs.microsoft.com/sql/relational-databases/performance/monitoring-performance-by-using-the-query-store) för att hitta väntande statistik för varje fråga över tid. I Query Store kombineras vänte typer i väntande kategorier. Du kan hitta mappningen av vänte kategorier till wait types i [sys. query_store_wait_stats](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-query-store-wait-stats-transact-sql#wait-categories-mapping-table).
- Använd [sys. DM _db_wait_stats](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-db-wait-stats-azure-sql-database) för att returnera information om alla väntande processer som körs under åtgärden. Du kan använda den här aggregerade vyn för att diagnostisera prestanda problem med Azure SQL Database och även med vissa frågor och batchar.
- Använd [sys. DM _os_waiting_tasks](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-os-waiting-tasks-transact-sql) för att returnera information om den kö med aktiviteter som väntar på en resurs.

I scenarier med hög processor kan Query Store och väntande statistik avspegla CPU-användning om:

- Frågor med hög processor användning körs fortfarande.
- Frågor med hög CPU-användning kördes när redundansväxlingen skedde.

DMV: er som spårar Frågearkivet och väntande statistik visar resultat för endast slutförda och uppnådde tids gräns frågor. De visar inga data för körnings instruktioner förrän instruktionerna är slutförda. Använd vyn för dynamisk hantering [sys. DM _exec_requests](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-requests-transact-sql) för att spåra aktuella körningar av frågor och tillhör ande arbets tid.

Diagrammet i början av den här artikeln visar att de vanligaste väntanarna är:

- Lås (blockerar)
- I/O
- Konkurrens som rör TempDB
- Minnes tilldelning väntar

> [!IMPORTANT]
> För en uppsättning T-SQL-frågor som använder DMV: er för att felsöka väntande problem, se:
>
> - [Identifiera problem med I/O-prestanda](sql-database-monitoring-with-dmvs.md#identify-io-performance-issues)
> - [Identifiera minnes tilldelning väntar](sql-database-monitoring-with-dmvs.md#identify-memory-grant-wait-performance-issues)
> - [TigerToolbox vänte tid och lås](https://github.com/Microsoft/tigertoolbox/tree/master/Waits-and-Latches)
> - [TigerToolbox usp_whatsup](https://github.com/Microsoft/tigertoolbox/tree/master/usp_WhatsUp)

## <a name="improve-database-performance-with-more-resources"></a>Förbättra databasens prestanda med fler resurser

Om inga åtgärds bara objekt kan förbättra databasens prestanda kan du ändra mängden resurser som är tillgängliga i Azure SQL Database. Tilldela fler resurser genom att ändra [DTU-tjänstens nivå](sql-database-service-tiers-dtu.md) för en enskild databas. Eller öka eDTU: er för en elastisk pool när som helst. Om du använder den [vCore-baserade inköps modellen](sql-database-service-tiers-vcore.md)kan du antingen ändra tjänst nivån eller öka resurserna som är allokerade till din databas.

För enskilda databaser kan du [ändra tjänst nivåer eller beräknings resurser](sql-database-single-database-scale.md) på begäran för att förbättra databasens prestanda. För flera databaser bör du överväga att använda [elastiska pooler](sql-database-elastic-pool-guidance.md) för att skala resurser automatiskt.

## <a name="tune-and-refactor-application-or-database-code"></a>Finjustera program eller databas kod

Du kan optimera program koden för databasen, ändra index, framtvinga planer eller använda tips för att anpassa databasen manuellt till arbets belastningen. Information om manuell justering och omskrivning av koden finns i [rikt linjer för prestanda justering](sql-database-performance-guidance.md).

## <a name="next-steps"></a>Nästa steg

- Om du vill aktivera automatisk justering i Azure SQL Database och låta funktionen för automatisk justering hantera hela arbets belastningen, se [Aktivera automatisk justering](sql-database-automatic-tuning-enable.md).
- Om du vill använda manuell justering granskar du [justerings rekommendationerna i Azure Portal](sql-database-advisor-portal.md). Tillämpa rekommendationerna manuellt för att förbättra prestandan för dina frågor.
- Ändra de resurser som är tillgängliga i databasen genom att ändra [Azure SQL Database tjänst nivåer](sql-database-performance-guidance.md).
