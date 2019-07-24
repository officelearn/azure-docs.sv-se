---
title: Övervaka & prestanda justering – Azure SQL Database | Microsoft Docs
description: Tips för prestanda justering i Azure SQL Database genom utvärdering och förbättringar.
services: sql-database
ms.service: sql-database
ms.subservice: performance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: jrasnik, carlrab
manager: craigg
ms.date: 01/25/2019
ms.openlocfilehash: fdc130a7ac13e1cc551c50a7ab284ff640ecbbe4
ms.sourcegitcommit: 770b060438122f090ab90d81e3ff2f023455213b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/17/2019
ms.locfileid: "68305762"
---
# <a name="monitoring-and-performance-tuning"></a>Övervakning och prestandajustering

Azure SQL Database innehåller verktyg och metoder som gör det enkelt att övervaka användningen, lägga till eller ta bort resurser (CPU, minne, I/O), felsöka potentiella problem och hitta rekommendationer som kan förbättra prestanda för en databas. Azure SQL Database har många funktioner som automatiskt kan åtgärda problemen i databaserna som gör det möjligt för en databas att anpassa sig till arbets belastningen och automatiskt optimera prestanda. Det finns dock vissa anpassade problem som kan behöva fel sökning. I den här artikeln beskrivs några metod tips och verktyg som kan användas för att felsöka prestanda problemen.

Det finns två huvudsakliga aktiviteter som ska utföras för att säkerställa att en databas körs utan problem:
- [Övervakning av databas prestanda](#monitoring-database-performance) för att se till att resurserna som är kopplade till databasen kan hantera arbets belastningen. Om du ser att en databas når resurs gränserna bör de främsta resurs krävande frågorna identifieras och optimeras, eller så bör fler resurser läggas till genom att uppgradera tjänst nivån.
- [Felsök prestanda problem](#troubleshoot-performance-issues) för att identifiera orsaken till vissa potentiella problem, identifiera rotor saken till problemet och utföra åtgärder som åtgärdar problemet.

## <a name="monitoring-database-performance"></a>Övervaka databas prestanda

Prestandaövervakning för en SQL-databas i Azure startar med att övervaka resursutnyttjandet i förhållande till nivån på databasprestanda som du valt. Följande resurser bör övervakas för samma:
 - **CPU-användning** – kontrol lera om databasen når 100 procent av CPU-användningen under en längre tids period. Detta kan tyda på att databasen eller instansen måste uppgraderas till en högre tjänst nivå eller att frågorna som använder de flesta beräknings kraften ska identifieras och justeras.
 - **Vänta med statistik** – kontrol lera varför är frågor som väntar på vissa resurser. Frågor väntar på att data ska hämtas eller sparas i databasfilerna, väntar på att en viss resurs gräns har nåtts osv.
 - **I/o-användning** – kontrol lera om databasen når IO-gränserna för det underliggande lagrings utrymmet.
 - **Minnes användning** – mängden minne som är tillgängligt för databasen eller instansen är proportionell till antalet virtuella kärnor och kontrol lera om detta räcker för arbets belastningen. Sidans livs längd förväntad är en av de parametrar som kan indikera hur snabbt sidorna tas bort från minnet.

Azure SQL Database tjänst **innehåller de verktyg och resurser som hjälper dig att felsöka och åtgärda potentiella prestanda problem**. Det går lätt att identifiera affärs möjligheter för att förbättra och optimera prestandan för frågor utan att ändra resurser genom att granska [rekommendationer för prestanda justering](sql-database-advisor.md). Index som saknas och dåligt optimerade frågor är vanliga orsaker till dåliga databasprestanda. Dessa justerings rekommendationer kan användas för att förbättra arbets Belastningens prestanda. Vi kan också låta Azure SQL Database [automatiskt optimera prestandan för frågorna](sql-database-automatic-tuning.md) genom att använda alla identifierade rekommendationer och kontrol lera att detta förbättrar databasens prestanda.

Följande alternativ är tillgängliga för övervakning och fel sökning av databas prestanda:

- I [Azure Portal](https://portal.azure.com)klickar du på **SQL-databaser**, väljer databasen och använder sedan övervaknings diagrammet för att söka efter resurser som närmar sig den maximala användningen. DTU-förbrukning visas som standard. Klicka på **Redigera** för att ändra tidsintervallet och värdena som visas.
- Verktyg som SQL Server Management Studio ger många användbara rapporter som en [instrument panel för prestanda](https://docs.microsoft.com/sql/relational-databases/performance/performance-dashboard?view=sql-server-2017) för att övervaka resursutnyttjande och identifiera de främsta resurs krävande frågorna eller [frågearkivet](https://docs.microsoft.com/sql/relational-databases/performance/monitoring-performance-by-using-the-query-store#Regressed) för att identifiera frågorna med försämrat historik.
- Använd [query Performance Insight](sql-database-query-performance.md) i [Azure Portal](https://portal.azure.com) för att identifiera de frågor som tillbringar de flesta av resurserna. Den här funktionen är endast tillgänglig i Enkel databas och elastiska pooler.
- Använd [SQL Database Advisor](sql-database-advisor-portal.md) för att Visa rekommendationer för att skapa och släppa index, parametriserade frågor och åtgärda schema problem. Den här funktionen är endast tillgänglig i Enkel databas och elastiska pooler.
- Använd [Azure SQL-intelligent Insights](sql-database-intelligent-insights.md) för automatisk övervakning av databasens prestanda. När ett prestanda problem har identifierats genereras en diagnostisk logg med information och RCA (rotor Saks analys) av problemet. Rekommendation om prestanda förbättring tillhandahålls när det är möjligt.
- [Aktivera automatisk justering](sql-database-automatic-tuning-enable.md) och låt Azure SQL Database automatiskt korrigera identifierade prestanda problem.
- Använd [vyer för dynamisk hantering (DMV: er)](sql-database-monitoring-with-dmvs.md), [utökade händelser](sql-database-xevent-db-diff-from-svr.md)och [frågearkivet](https://docs.microsoft.com/sql/relational-databases/performance/monitoring-performance-by-using-the-query-store) för mer detaljerad fel sökning av prestanda problem.

> [!TIP]
> Se [prestanda vägledning](sql-database-performance-guidance.md) för att hitta tekniker som du kan använda för att förbättra prestandan för Azure SQL Database när du har identifierat prestanda problem med en eller flera av metoderna ovan.

## <a name="troubleshoot-performance-issues"></a>Felsöka prestandaproblem

För att diagnostisera och lösa prestanda problem börjar du med att förstå tillståndet för varje aktiv fråga och de villkor som orsakar prestanda problem som är relevanta för varje arbets belastnings tillstånd. Du kan förbättra Azure SQL Database prestanda genom att förstå att varje aktiv förfrågan från programmet antingen är i ett tillstånd som körs eller väntar. När du felsöker ett prestanda problem i Azure SQL Database ska du hålla följande diagram i åtanke när vi läser igenom den här artikeln för att diagnostisera och lösa prestanda problem.

![Arbets belastnings status](./media/sql-database-monitor-tune-overview/workload-states.png)

För en arbets belastning med prestanda problem kan prestanda problemet bero på processor konkurrens (ett **aktivt** villkor) eller att enskilda frågor väntar på något (ett väntande villkor).

Orsakerna till att **köra** problem kan vara:
- **Kompileringsfel** – SQL Query Optimering kan producera ett underoptimerat schema på grund av inaktuell statistik, felaktig uppskattning av antalet rader som ska bearbetas eller beräkningen av nödvändigt minne. Om vi vet att frågan kördes snabbare tidigare eller på en annan instans (antingen hanterad instans eller SQL Server instans), tar du de faktiska körnings planerna och jämför dem för att se om de skiljer sig åt. Försök att använda frågetipset eller återskapa statistik eller återskapa index för att få bättre planer. Aktivera automatisk plan korrigering i Azure SQL Database för att automatiskt minimera problemen.
- **Körnings problem** – om Frågeuttrycket är optimalt kommer det förmodligen att visa vissa resurs gränser i databasen, t. ex. logg skrivnings data flöde eller använder defragmenterade index som ska återskapas. Ett stort antal samtidiga frågor som är utgifts för resurserna kan också vara orsaken till körnings problemen. **Väntande** problem i de flesta fall är relaterade till körnings problemen, eftersom frågor som inte körs effektivt väntar på vissa resurser.

Orsakerna till **väntande** problem kan vara:
- **Blockering** – en fråga kan innehålla låset på vissa objekt i databasen medan andra försöker komma åt samma objekt. Att blockera frågor kan enkelt identifieras med hjälp av DMV eller övervaknings verktyg.
- **I/o-problem** – frågor kan vänta på att sidorna skrivs till data-eller loggfilerna. I det här fallet `INSTANCE_LOG_RATE_GOVERNOR`kan `WRITE_LOG`du se `PAGEIOLATCH_*` , eller vänta i statistik i DMV.
- **Tempdb-problem** – om arbets belastningen använder mycket temporära tabeller eller om det finns många tempdb-spill i planerna kan frågorna ha problem med tempdb-dataflöde. 
- **Minnesrelaterade problem** – det kanske inte finns tillräckligt med minne för arbets belastningen så förväntad kan släppas, eller så får frågorna mindre minne än vad som behövs. I vissa fall kommer inbyggd intelligens i fråga optimering att åtgärda problemen.
 
 I följande avsnitt förklaras hur du kan identifiera och felsöka några av de här problemen.

## <a name="running-related-performance-issues"></a>Problem med att köra relaterade prestanda

Som en allmän rikt linje, om processor användningen är konsekvent på eller över 80%, finns det ett löpande prestanda problem. Om det finns ett problem som är aktivt kan det bero på otillräckliga processor resurser eller så kan det vara relaterat till något av följande villkor:

- För många körnings frågor
- För många kompilerings frågor
- En eller flera körnings frågor använder en under optimal frågeplan

Om det har fastställts att det finns ett pågående prestanda problem är målet att identifiera det exakta problemet med en eller flera metoder. De vanligaste metoderna för att identifiera körnings problem är:

- Använd [Azure Portal](sql-database-manage-after-migration.md#monitor-databases-using-the-azure-portal) för att övervaka användningen av CPU-procent.
- Använd följande [vyer för dynamisk hantering](sql-database-monitoring-with-dmvs.md):

  - [sys. DM _db_resource_stats](sql-database-monitoring-with-dmvs.md#monitor-resource-use) returnerar CPU, I/O och minnes användning för en Azure SQL Database. Det finns en rad för var 15: e sekund intervall, även om det inte finns någon aktivitet i databasen. Historiska data bevaras i en timme.
  - [sys. resource_stats](sql-database-monitoring-with-dmvs.md#monitor-resource-use) returnerar CPU-användning och lagrings data för en Azure SQL Database. Data samlas in och sammanställs inom fem minuters intervall.

> [!IMPORTANT]
> För en Set a-SQL-frågor med hjälp av dessa DMV: er för att felsöka problem med processor användning, se [identifiera problem med processor prestanda](sql-database-monitoring-with-dmvs.md#identify-cpu-performance-issues).

### <a name="ParamSniffing"></a>Felsöka frågor med problem med parameter känslig frågans körnings plan

Felet för parameter känslig plan (PSP) refererar till ett scenario där Query Optimering genererar en frågeplan för frågekörningen som är optimal endast för ett särskilt parameter värde (eller uppsättning värden) och den cachelagrade planen är sedan icke-optimal för parameter värden som används i löpande körningar. Icke-optimala planer kan sedan leda till problem med frågans prestanda och den övergripande försämringen av arbets flödet. Mer information om parameter identifiering och bearbetning av frågor finns i [arkitektur guiden för bearbetning av processer](/sql/relational-databases/query-processing-architecture-guide#ParamSniffing).

Det finns flera lösningar som används för att minimera problemen, var och en med tillhör ande kompromisser och nack delar:

- Använd frågetipset för att [kompilera](https://docs.microsoft.com/sql/t-sql/queries/hints-transact-sql-query) om frågan vid varje frågekörningen. Den här lösningen för att kompilera den här lösningen och öka CPU-tiden för bättre plan kvalitet. Att använda `RECOMPILE` alternativet är ofta inte möjligt för arbets belastningar som kräver ett högt data flöde.
- Använd frågetipset [alternativ (Optimize for...)](https://docs.microsoft.com/sql/t-sql/queries/hints-transact-sql-query) för att åsidosätta det faktiska parametervärdet med ett typiskt parameter värde som ger tillräckligt med schema för de flesta värde för parameter värde.   Det här alternativet kräver en god förståelse av optimala parameter värden och associerade plan egenskaper.
- Använd [alternativet (optimera för okända)](https://docs.microsoft.com/sql/t-sql/queries/hints-transact-sql-query) frågetipset för att åsidosätta det faktiska parametervärdet i Exchange för att använda medelvärdet för densitet Vector. Ett annat sätt att göra detta är genom att samla in inkommande parameter värden i lokala variabler och sedan använda de lokala variablerna i predikat i stället för att använda själva parametrarna. Den genomsnittliga densiteten måste vara *tillräckligt hög* med den här korrigeringen.
- Inaktivera parameter identifiering helt med [DISABLE_PARAMETER_SNIFFING](https://docs.microsoft.com/sql/t-sql/queries/hints-transact-sql-query) -frågetipset.
- Använd [KEEPFIXEDPLAN](https://docs.microsoft.com/sql/t-sql/queries/hints-transact-sql-query) -frågetipset för att förhindra omkompileringar i cacheminnet. Den här lösningen förutsätter att den *tillräckligt höga* planen är den som redan finns i cacheminnet. Du kan också inaktivera automatiska uppdateringar av statistik för att minska risken för att en bra plan avlägsnas och en ny dålig plan kompileras.
- Tvinga planen genom att uttryckligen använda [use plan](https://docs.microsoft.com/sql/t-sql/queries/hints-transact-sql-query) -frågetipset (genom att ange en specifik plan med hjälp av frågearkivet eller genom att aktivera [Automatisk justering](sql-database-automatic-tuning.md)).
- Ersätt den gemensamma proceduren med en kapslad uppsättning procedurer som varje kan användas baserat på villkorlig logik och associerade parameter värden.
- Skapa alternativ för dynamisk sträng körning till en statisk procedur definition.

Mer information om hur du löser dessa typer av problem finns i:

- Det här är [jag luktat ett blogg inlägg I en parameter](https://blogs.msdn.microsoft.com/queryoptteam/2006/03/31/i-smell-a-parameter/)
- Den här [dynamiska SQL-och plan-kvaliteten för blogg inlägg i parametriserade frågor](https://blogs.msdn.microsoft.com/conor_cunningham_msft/2009/06/03/conor-vs-dynamic-sql-vs-procedures-vs-plan-quality-for-parameterized-queries/)
- Den [här optimerings tekniken för SQL-frågor i SQL Server: Parameter identifiering](https://www.sqlshack.com/query-optimization-techniques-in-sql-server-parameter-sniffing/) av blogg inlägg

### <a name="troubleshooting-compile-activity-due-to-improper-parameterization"></a>Felsöka Compilation-aktivitet på grund av en felaktig Parameterisering

När en fråga har litteraler, väljer databas motorn automatiskt Parameterisera-instruktionen eller så kan en användare uttryckligen Parameterisera den för att minska antalet kompileringar. Ett stort antal kompileringar av en fråga med samma mönster men olika litterala värden kan resultera i hög CPU-användning. Om du bara delvis Parameterisera en fråga som fortsätter att ha litteraler, så Parameterisera inte databas motorn längre.  Nedan visas ett exempel på en delvis parametriserad fråga:

```sql
SELECT * FROM t1 JOIN t2 ON t1.c1 = t2.c1
WHERE t1.c1 = @p1 AND t2.c2 = '961C3970-0E54-4E8E-82B6-5545BE897F8F'
```

I föregående exempel `t1.c1` tar `@p1` men `t2.c2` fortsätter att ta GUID som literal. I detta fall behandlas frågan som en annan fråga `c2`och en ny kompilering görs om du ändrar värdet för. För att minska kompileringarna i föregående exempel, är lösningen också Parameterisera GUID.

Följande fråga visar antalet frågor efter frågans hash för att avgöra om en fråga är korrekt parametriserad eller inte:

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
### <a name="factors-influencing-query-plan-changes"></a>Faktorer som påverkar ändringar i frågeplan

En omkompilering av en frågeplan kan resultera i en genererad frågeplan som skiljer sig från vad som ursprungligen cachelagrades. Det finns olika orsaker till varför en befintlig ursprunglig plan kan omkompileras automatiskt:
- Ändringar i schemat som refereras av frågan
- Data ändringar i de tabeller som refereras av frågan 
- Ändringar av Sammanhangs alternativ för fråga 

En kompilerad plan kan matas ut från cachen av olika orsaker, inklusive inläsningar av databasen, konfigurations ändringar i databasen, minnes belastning och explicita begär Anden för att rensa cacheminnet. Dessutom innebär det att en plan inte cachelagras när du använder ett RECOMPILE-tips.

En RECOMPILE (eller ny kompilering efter cache-avtagningen) kan fortfarande resultera i att en identisk kör tids plan skapas från den som ursprungligen observerats.  Men om det finns ändringar i planen jämfört med föregående eller ursprungliga plan, är följande de vanligaste förklaringarna för varför en schema för frågekörningen har ändrats:

- **Ändrad fysisk design**. Till exempel skapas nya index som bättre beskriver kraven för en fråga. Dessa kan användas i en ny kompilering om Query Optimering bestämmer att det är mer optimalt att utnyttja det nya indexet än att använda data strukturen som ursprungligen valdes för den första versionen av frågekörningen.  Eventuella fysiska ändringar av refererade objekt kan resultera i en ny plan val vid kompilering.

- **Server resurs skillnader**. I ett scenario där en plan skiljer sig från "system A" eller "system B" – resursernas tillgänglighet, till exempel antalet tillgängliga processorer, kan påverka vilken plan som genereras.  Om ett system till exempel har ett större antal processorer kan en parallell plan väljas. 

- **Annan statistik**. Statistiken som är kopplad till de refererade objekten har ändrats eller skiljer sig väsentligt från det ursprungliga systemets statistik.  Om statistiken ändras och en omkompilering sker, kommer frågans optimering att använda statistik från den aktuella tidpunkten. Den reviderade statistiken kan ha betydligt olika data distributioner och frekvenser som inte var fallet i den ursprungliga kompileringen.  Dessa ändringar används för att uppskatta kardinalitet uppskattningar (antalet rader som förväntas flödas genom det logiska frågeuttrycket).  Ändringar i beräkningar av kardinalitet kan leda till att vi väljer olika fysiska operatörer och tillhör ande beställnings åtgärder.  Även smärre ändringar i statistiken kan resultera i en ändrad frågeplan för frågekörningen.

- **Ändrade kompatibilitetsnivå för databas eller kardinalitet**.  Ändringar av kompatibilitetsnivån för databaser kan möjliggöra nya strategier och funktioner som kan resultera i en annan frågeplan för körning.  Utöver databasens kompatibilitetsnivå kan inaktive ras eller aktive ras spårnings flagga 4199 eller om du ändrar tillstånd för den QUERY_OPTIMIZER_HOTFIXES konfigurationen av databasen kan också påverka val av frågekörning vid kompilering.  Spårnings flaggorna 9481 (framtvinga äldre CE) och 2312 (Force standard CE) planeras också. 

### <a name="resolve-problem-queries-or-provide-more-resources"></a>Lös problem frågor eller ange fler resurser

När du har identifierat problemet kan du antingen finjustera problemen eller uppgradera beräknings storleken eller tjänst nivån för att öka kapaciteten för Azure SQL-databasen så att du kan absorbera processor kraven. Information om hur du skalar resurser för enskilda databaser finns i [skala enkla databas resurser i Azure SQL Database](sql-database-single-database-scale.md) och för att skala resurser för elastiska pooler, se [skala elastiska pool resurser i Azure SQL Database](sql-database-elastic-pool-scale.md). Information om hur du skalar en hanterad instans finns i [resurs gränser på instans nivå](sql-database-managed-instance-resource-limits.md#instance-level-resource-limits).

### <a name="determine-if-running-issues-due-to-increase-workload-volume"></a>Ta reda på om det uppstår problem med att öka arbets belastnings volymen

En ökning av program trafiken och arbets belastningen kan ge ökad processor användning, men du måste vara noggrann för att kunna diagnostisera det här problemet. I ett scenario med hög processor svar kan du svara på de här frågorna för att avgöra om en CPU-ökning beror på belastningen på volym ändringar:

1. Är frågorna från programmet orsaken till det höga processor problemet?
2. För de vanligaste processor krävande frågorna (som kan identifieras):

   - Kontrol lera om det finns flera körnings planer kopplade till samma fråga. I så fall, ta reda på varför.
   - För frågor med samma körnings plan fastställer du om körnings tiderna var konsekventa och om körnings antalet ökade. Om ja, det uppstår sannolika prestanda problem på grund av ökad arbets belastning.

Om du vill sammanfatta om körnings planen inte kördes på olika sätt men CPU-belastningen ökade tillsammans med körnings antalet, är det sannolikt ett öknings fel för arbets belastningen.

Det är inte alltid lätt att sluta där det finns en ändring av arbets belastnings volym som kör ett CPU-problem.   Faktorer att tänka på: 

- **Resursanvändningen har ändrats**

  Anta till exempel ett scenario där CPU ökade till 80% under en längre tid.  Enbart processor användning innebär att arbets belastnings volymen har ändrats.  Frågor för körnings plan regressioner och data distributions ändringar kan också bidra till mer resursanvändning även om programmet kör samma exakta arbets belastning.

- **Ny fråga visades**

   Ett program kan köra en ny uppsättning frågor vid olika tidpunkter.

- **Antal begär Anden som ökats eller minskat**

   Det här scenariot är det mest uppenbara måttet på arbets belastningen. Antalet frågor motsvarar inte alltid mer resursutnyttjande. Detta mått är dock fortfarande en betydande signal som antar att andra faktorer är oförändrade.

## <a name="waiting-related-performance-issues"></a>Väntande-relaterade prestanda problem

När du är säker på att du inte är inriktad på ett högcpu-, prestanda problem som är aktivt, är det ett väntande-relaterat prestanda problem. Det innebär att dina CPU-resurser inte används effektivt eftersom processorn väntar på en annan resurs. I det här fallet är nästa steg att identifiera vad dina processor resurser väntar på. De vanligaste metoderna för att visa kategorierna topp vänte typ:

- [Frågearkivet](https://docs.microsoft.com/sql/relational-databases/performance/monitoring-performance-by-using-the-query-store) innehåller väntande statistik per fråga över tid. I Query Store kombineras vänte typer i väntande kategorier. Mappningen av väntande kategorier till vänte typer finns i [sys. query_store_wait_stats](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-query-store-wait-stats-transact-sql#wait-categories-mapping-table).
- [sys. DM _db_wait_stats](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-db-wait-stats-azure-sql-database) returnerar information om alla väntande processer som körs under åtgärden. Du kan använda den här aggregerade vyn för att diagnostisera prestanda problem med Azure SQL Database och även med specifika frågor och batchar.
- [sys. DM-_os_waiting_tasks](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-os-waiting-tasks-transact-sql) returnerar information om wait-kön med uppgifter som väntar på en resurs.

I scenarier med hög processor avspeglas inte alltid CPU-användningen på grund av dessa två orsaker:

- Frågor med hög CPU-användning kan fortfarande köras och frågorna har inte slutförts
- Frågor med hög processor användning kördes när ett fel inträffade

Query Store och vänta med statistik – spårning av dynamiska hanterings vyer visar bara resultat för slutförda och timeout-frågor och visar inte data för körnings instruktioner (tills de har slutförts). Med vyn för dynamisk hantering i [sys. DM _exec_requests](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-requests-transact-sql) kan du spåra frågor som körs för tillfället och den associerade arbets tiden.

Som du ser i föregående diagram är de vanligaste väntande:

- Lås (blockerar)
- I/O
- `tempdb`-relaterad konkurrens
- Minnes tilldelning väntar

> [!IMPORTANT]
> För en Set a-SQL-frågor som använder dessa DMV: er för att felsöka de här väntande problemen, se:
>
> - [Identifiera problem med I/O-prestanda](sql-database-monitoring-with-dmvs.md#identify-io-performance-issues)
> - [Identifiera `tempdb` prestanda problem](sql-database-monitoring-with-dmvs.md#identify-io-performance-issues)
> - [Identifiera minnes tilldelning väntar](sql-database-monitoring-with-dmvs.md#identify-memory-grant-wait-performance-issues)
> - [TigerToolbox – vänta och spärrar](https://github.com/Microsoft/tigertoolbox/tree/master/Waits-and-Latches)
> - [TigerToolbox - usp_whatsup](https://github.com/Microsoft/tigertoolbox/tree/master/usp_WhatsUp)

## <a name="improving-database-performance-with-more-resources"></a>Förbättra databas prestanda med fler resurser

Slutligen, om det inte finns några åtgärds bara objekt som kan förbättra databasens prestanda, kan du ändra mängden resurser som är tillgängliga i Azure SQL Database. Du kan tilldela fler resurser genom att ändra [DTU-tjänstens nivå](sql-database-service-tiers-dtu.md) för en enskild databas eller öka eDTU: er för en elastisk pool när som helst. Om du använder den [vCore-baserade inköps modellen](sql-database-service-tiers-vcore.md)kan du ändra antingen tjänst nivån eller öka resurserna som är allokerade till din databas.

1. För enskilda databaser kan du [ändra tjänst nivåer](sql-database-single-database-scale.md) eller beräknings [resurser](sql-database-single-database-scale.md) på begäran för att förbättra databasens prestanda.
2. För flera databaser bör du överväga att använda [elastiska pooler](sql-database-elastic-pool-guidance.md) för att skala resurser automatiskt.

## <a name="tune-and-refactor-application-or-database-code"></a>Finjustera program eller databas kod

Du kan ändra program koden till mer optimal användning av databasen, ändra index, framtvinga planer eller använda tips för att manuellt anpassa databasen till arbets belastningen. Hitta några rikt linjer och tips för manuell justering och skriv om koden i artikeln om [prestanda vägledning](sql-database-performance-guidance.md) .

## <a name="next-steps"></a>Nästa steg

- Om du vill aktivera automatisk justering i Azure SQL Database och låta funktionen för automatisk justering hantera din arbets belastning fullständigt, se [Aktivera automatisk justering](sql-database-automatic-tuning-enable.md).
- Om du vill använda manuell justering kan du granska [justerings rekommendationer i Azure Portal](sql-database-advisor-portal.md) och manuellt tillämpa de som förbättrar prestandan för dina frågor.
- Ändra resurser som är tillgängliga i databasen genom att ändra [Azure SQL Database tjänst nivåer](sql-database-performance-guidance.md)
