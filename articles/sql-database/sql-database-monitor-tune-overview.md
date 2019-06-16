---
title: Övervakning och prestandajustering – Azure SQL Database | Microsoft Docs
description: Tips för justering i Azure SQL Database via utvärdera och förbättra prestanda.
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
ms.openlocfilehash: 2fa43fcd48736a3d044deb07ed690af580c3b987
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/13/2019
ms.locfileid: "66416274"
---
# <a name="monitoring-and-performance-tuning"></a>Övervakning och prestandajustering

I Azure SQL Database kan du enkelt övervaka användning, lägga till eller ta bort resurser (processor, minne och I/O), felsöka potentiella problem och hitta rekommendationer som kan förbättra databasens prestanda. Azure SQL-databas har många funktioner som automatiskt kan lösa problem i dina databaser om du vill att databasen anpassas efter din arbetsbelastning och automatiskt optimera prestanda. Det finns dock några anpassade problem som du kan behöva för att felsöka. Den här artikeln beskriver några metodtips och verktyg som du kan använda för att felsöka problem med prestanda.

Det finns två huvudsakliga aktiviteter som du behöver göra för att säkerställa att du databasen körs utan problem:
- [Övervaka databasprestanda](#monitoring-database-performance) för att se till att resurserna som tilldelats din databas kan hantera din arbetsbelastning. Om du ser att du stöter på resursbegränsningar måste identifiera övre resurskrävande frågor och optimera dem eller lägga till fler resurser genom att uppgradera tjänstnivå.
- [Felsöka prestandaproblem](#troubleshoot-performance-issues) för att identifiera varför vissa potentiella problem inträffade, identifiera grundorsaken till problemet och vilken åtgärd som ska åtgärda problemet.

## <a name="monitoring-database-performance"></a>Övervaka databasprestanda

Prestandaövervakning för en SQL-databas i Azure startar med att övervaka resursutnyttjandet i förhållande till nivån på databasprestanda som du valt. Du behöver övervaka följande resurser:
 - **CPU-användning** -måste du kontrollera att du ansluter till 100% av CPU-användning under en längre tidsperiod. Detta kan tyda på att du kan behöva uppgradera din databas eller instans eller identifiera och justera frågor som använder de flesta av beräkningskraft.
 - **Vänta statistik** -måste du kontrollera vad anledningen till att dina frågor väntar på några resurser. Queriesmig vänta tills data hämtas eller sparas till databasfilerna, väntar på eftersom vissa resursgränsen har nåtts osv.
 - **I/o-användning** -måste du kontrollera att du ansluter till i/o-gränserna för det underliggande lagringsutrymmet.
 - **Minnesanvändning** – mängden minne som är tillgängliga för din databas eller en instans är proportion till antalet virtuella kärnor och du måste kontrollera är det tillräckligt för din arbetsbelastning. Varaktighet för sida är en av de parametrar som kan tyda på sidorna snabbt tas bort från minnet.

Azure SQL Database **innehåller advices som kan hjälpa dig att felsöka och åtgärda potentiella prestandaproblem**. Du kan enkelt identifiera möjligheter att förbättra och optimera frågeprestanda utan att ändra resurser genom att granska [rekommendationer för prestandajustering](sql-database-advisor.md). Index som saknas och dåligt optimerade frågor är vanliga orsaker till dåliga databasprestanda. Du kan använda de här justeringsrekommendationerna att förbättra prestanda för din arbetsbelastning. Du kan också låta Azure SQL database till [automatiskt optimera prestandan för dina frågor](sql-database-automatic-tuning.md) genom att tillämpa alla identifierade rekommendationer och verifiera att de förbättras databasens prestanda.

Du har följande alternativ för övervakning och felsökning databasprestanda:

- I den [Azure-portalen](https://portal.azure.com), klickar du på **SQL-databaser**, väljer du databasen och sedan använda övervakning diagrammet för att söka efter resurser som närmar sig sin maximala. DTU-användning visas som standard. Klicka på **redigera** att ändra tidsintervall och värden som visas.
- Verktygen som SQL Server Management Studio innehåller många användbara rapporter som en [prestanda instrumentpanelen](https://docs.microsoft.com/sql/relational-databases/performance/performance-dashboard?view=sql-server-2017) där du kan övervaka Resursanvändning och identifiera övre resurskrävande frågor, eller [Query Store](https://docs.microsoft.com/sql/relational-databases/performance/monitoring-performance-by-using-the-query-store#Regressed)där du kan identifiera frågor med försämrad prestanda.
- Använd [Query Performance Insight](sql-database-query-performance.md) den [Azure-portalen](https://portal.azure.com) i för att identifiera de frågor som lägger ut det mesta av resurser. Den här funktionen finns bara på enkel databas och elastiska pooler.
- Använd [SQL Database Advisor](sql-database-advisor-portal.md) att visa rekommendationer för att skapa och släppa index, Parameterisera frågorna och åtgärda problem med databasscheman. Den här funktionen finns bara på enkel databas och elastiska pooler.
- Använd [smarta insikter för Azure SQL](sql-database-intelligent-insights.md) för automatisk övervakning av databasens prestanda. När ett prestandaproblem har identifierats genereras en diagnostiklogg med information om och rot orsak Analysis (RCA) av problemet. Rekommendation för förbättring av prestanda tillhandahålls när det är möjligt.
- [Aktivera automatisk justering](sql-database-automatic-tuning-enable.md) och låta Azure SQL-databasen automatiskt korrigering identifieras prestandaproblem.
- Använd [dynamiska hanteringsvyer (DMV)](sql-database-monitoring-with-dmvs.md), [utökade händelser](sql-database-xevent-db-diff-from-svr.md), och [Query Store](https://docs.microsoft.com/sql/relational-databases/performance/monitoring-performance-by-using-the-query-store) mer detaljerad felsökning av problem med prestanda.

> [!TIP]
> Se [prestandavägledning](sql-database-performance-guidance.md) att hitta tekniker som du kan använda för att förbättra prestanda i Azure SQL Database när du har identifierat prestandaproblemet med hjälp av en eller flera av metoderna ovan.

## <a name="troubleshoot-performance-issues"></a>Felsöka prestandaproblem

Börja med att förstå statusen för varje aktiv fråga och de villkor som kan orsakar prestandaproblem som är relevanta för varje arbetsbelastning tillstånd för att diagnostisera och lösa prestandaproblem. För att förbättra prestandan för Azure SQL Database, förstår du att varje aktiva frågebegäran från ditt program är antingen i en löpande eller väntande läge. När du felsöker problem med prestandan i Azure SQL Database, Tänk på följande diagram som du läser igenom den här artikeln att diagnostisera och lösa prestandaproblem.

![Tillstånd för arbetsbelastning](./media/sql-database-monitor-tune-overview/workload-states.png)

För en arbetsbelastning med prestandaproblem prestandaproblemet kan bero på CPU-konkurrens (en **körs-relaterade** villkor) eller enskilda frågor väntar på något (en **väntar-relaterade** villkor ).

Orsakerna eller **körs-relaterade** problem kan vara:
- **Kompileringsproblem** -SQL-Frågeoptimerare kan ge bästa möjliga plan på grund av inaktuella statistik, felaktig uppskattning av antalet rader som ska bearbetas eller uppskattning av minne. Om du vet att frågan har körts snabbare tidigare eller på andra instans (Managed Instance eller SQL Server-instans), gör den faktiska körningen planer och jämföra dem är de olika. Försök att installera frågetips eller behöver statistik eller index kan få bättre planen. Aktivera plan för automatisk korrigering i Azure SQL Database för att automatiskt åtgärda dessa problem.
- **Körningsproblem** – om frågeplanen är optimalt och sedan den förmodligen belastas vissa resursbegränsningar i databasen, till exempel genomströmning för skrivning av loggen eller kan den använda defragmenteras index som ska byggas. Ett stort antal samtidiga frågor som tillbringar resurserna kan också vara orsaken till körningsproblem. **Väntar-relaterade** problem finns i de flesta fall som rör körningsproblem eftersom de frågor som inte körs effektivt förmodligen väntar på några resurser.

Orsakerna eller **väntar-relaterade** problem kan vara:
- **Blockera** – en fråga kan vänta låset vissa objekt i databasen medan andra försöker få åtkomst till samma objekt. Du kan enkelt identifiera de blockera frågor med hjälp av DMV- eller övervakningsverktyg.
- **I/o-problem** -frågor kan vänta på sidorna som ska skrivas till de data eller log-filerna. I det här fallet visas `INSTANCE_LOG_RATE_GOVERNOR`, `WRITE_LOG`, eller `PAGEIOLATCH_*` vänta statistik i DMV.
- **TempDB problem** – om du använder en massa temporära tabeller eller om du ser en massa TempDB spill i dina planer dina frågor som du kanske har ett problem med TempDB-dataflöde. 
- **Minnesrelaterade problem** -kanske du inte har tillräckligt med minne för din arbetsbelastning så att din varaktighet för sida kan släppa eller dina frågor får mindre minnestilldelningen än vad som behövs. I vissa fall kommer inbyggd intelligens i Frågeoptimeringen åtgärda dessa problem.
 
I följande avsnitt förklaras hur du identifierar och felsöka vissa av dessa problem.

## <a name="running-related-performance-issues"></a>Körs minnesrelaterade prestandaproblem

Som en generell riktlinje CPU-användning är konsekvent vid eller över 80%, måste köras minnesrelaterade prestandaproblem. Om du har en körs problem det kan bero på otillräckliga processorresurser eller kan vara relaterade till något av följande villkor:

- För många frågor som körs
- För många kompilerar frågor
- En eller flera av de frågor som körs använder en icke-optimala frågeplan

Om du har fastställt att du har ett körs minnesrelaterade prestandaproblem, är målet att identifiera exakta problemet med hjälp av en eller flera metoder. De vanligaste metoderna för att identifiera körs-relaterade problem är:

- Använd den [Azure-portalen](sql-database-manage-after-migration.md#monitor-databases-using-the-azure-portal) övervakar processoranvändning i procent.
- Använd följande [dynamiska hanteringsvyer](sql-database-monitoring-with-dmvs.md):

  - [sys.dm_db_resource_stats](sql-database-monitoring-with-dmvs.md#monitor-resource-use) returnerar CPU, I/O och minne i samband med en Azure SQL Database-databas. Det finns en rad för var 15: e sekund, även om det finns ingen aktivitet i databasen. Historiska data bevaras under en timme.
  - [sys.resource_stats](sql-database-monitoring-with-dmvs.md#monitor-resource-use) returnerar CPU-användning och lagring data för en Azure SQL Database. Data som samlas in och sammanställs inom fem minuter långa intervall.

> [!IMPORTANT]
> En uppsättning en T-SQL-frågor med dessa DMV: er för att felsöka problem med CPU, finns i [identifiera CPU prestandaproblem](sql-database-monitoring-with-dmvs.md#identify-cpu-performance-issues).

### <a name="ParamSniffing"></a> Felsöka frågor med parametern-känsliga fråga körningsproblem för plan

Känsliga plan (PSP) parameterproblemet refererar till ett scenario där Frågeoptimeringen genererar en frågeplan för körning som är optimalt endast för en specifik parametervärdet (eller uppsättning värden) och cachelagrad plan sedan är icke-optimala för parametervärden som används i efterföljande körningar. Icke-optimala planer kan sedan resultera i prestandaproblem för frågan och övergripande arbetsbelastningen dataflöde försämring. Mer information om parametern kontroll och frågebearbetning finns i den [fråga bearbetning Architecture-guiden](/sql/relational-databases/query-processing-architecture-guide#ParamSniffing).

Det finns flera sätt för att åtgärda problem med associerade kompromisser och nackdelar:

- Använd den [kompilera om](https://docs.microsoft.com/sql/t-sql/queries/hints-transact-sql-query) frågetipset vid varje Frågekörningen. Den här lösningen handlar nödvändig kompileringstid och ökade CPU få bättre kvalitet på planen. Med hjälp av den `RECOMPILE` alternativet ofta inte är möjligt för arbetsbelastningar som kräver ett högt dataflöde.
- Använd den [ALTERNATIVET (OPTIMERA för...) ](https://docs.microsoft.com/sql/t-sql/queries/hints-transact-sql-query) frågetipset åsidosätta faktiska parametervärdet med en typisk parametervärde som producerar en tillräckligt bra plan för de flesta parametern värdet möjligheter.   Det här alternativet kräver en god förståelse för optimal parametervärden och associerade plan egenskaper.
- Använd [ALTERNATIVET (OPTIMERA för okänd)](https://docs.microsoft.com/sql/t-sql/queries/hints-transact-sql-query) frågetipset åsidosätta faktiska parametervärdet i utbyte mot med densitet vektor medelvärdet. Ett annat sätt att göra detta är genom att samla in de inkommande parametervärdena till lokala variabler och sedan använda de lokala variablerna i predikat istället för att använda parametrarna själva. Den genomsnittliga tätheten måste vara *tillräckligt bra* med denna viss snabbkorrigering.
- Inaktivera parametern kontroll helt och hållet med hjälp av den [DISABLE_PARAMETER_SNIFFING](https://docs.microsoft.com/sql/t-sql/queries/hints-transact-sql-query) frågetipset.
- Använd den [KEEPFIXEDPLAN](https://docs.microsoft.com/sql/t-sql/queries/hints-transact-sql-query) frågetipset att förhindra kompilerar i cachen. Denna lösning förutsätter att den *tillräckligt bra* vanliga planen är det i cacheminnet redan. Du kan också inaktivera automatiska uppdateringar ska statistik för att minska risken för bra planen tas bort och en ny felaktiga plan kompilerats.
- Framtvinga planen genom att uttryckligen använda [USE PLAN](https://docs.microsoft.com/sql/t-sql/queries/hints-transact-sql-query) frågetipset (genom att ange, genom att ange en specifik plan med hjälp av Query Store eller genom att aktivera [automatisk justering](sql-database-automatic-tuning.md).
- Ersätt den enda proceduren med en kapslad uppsättning procedurer som du kan använda varje utifrån villkorslogik och associerade parametrarnas värden.
- Skapa dynamiska sträng körning alternativ till en Procedurdefinition av statiska.

Det finns ytterligare information om hur du löser dessa typer av problem i:

- Detta [jag anar en parameter](https://blogs.msdn.microsoft.com/queryoptteam/2006/03/31/i-smell-a-parameter/) blogginlägget
- Detta [dynamisk sql jämfört med planen kvalitet för frågor med parametrar](https://blogs.msdn.microsoft.com/conor_cunningham_msft/2009/06/03/conor-vs-dynamic-sql-vs-procedures-vs-plan-quality-for-parameterized-queries/) blogginlägget
- Detta [Optimeringstekniker för SQL-fråga i SQLServer: Parametern Sniffing](https://www.sqlshack.com/query-optimization-techniques-in-sql-server-parameter-sniffing/) blogginlägget

### <a name="troubleshooting-compile-activity-due-to-improper-parameterization"></a>Felsöka kompilera aktiviteten på grund av felaktig parameterisering

När en fråga har litteraler, databasmotorn väljer att automatiskt Parameterisera instruktionen eller en användare kan uttryckligen Parameterisera den för att minska antalet kompileras. Ett stort antal kompileras av en fråga med samma mönster men olika literalvärden kan leda till hög processoranvändning. På samma sätt, om du endast delvis Parameterisera en fråga som fortsätter att ha litteraler, database engine inte Parameterisera den ytterligare.  Nedan visas ett exempel på en fråga med delvis parametrar:

```sql
SELECT * FROM t1 JOIN t2 ON t1.c1 = t2.c1
WHERE t1.c1 = @p1 AND t2.c2 = '961C3970-0E54-4E8E-82B6-5545BE897F8F'
```

I föregående exempel `t1.c1` tar `@p1` men `t2.c2` fortsätter ta GUID som literal. I det här fallet, om du ändrar värdet för `c2`, frågan kommer att behandlas som en annan fråga och en ny kompilering sker. För att minska kompileringar i föregående exempel är lösningen att också Parameterisera GUID.

Följande fråga visar antal frågor med fråge-hash för att avgöra om en fråga är korrekt som innehåller parametrar eller inte:

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
### <a name="factors-influencing-query-plan-changes"></a>Faktorer som påverkar ändringar av frågeplan

Plan kompileras om en fråga körning kan resultera i en genererad frågeplan som skiljer sig från vad cachelagrades ursprungligen. Det finns olika orsaker till varför en befintlig ursprungliga plan kan kompileras om automatiskt:
- Ändringar i schemat som refereras av frågan
- Dataändringar till de tabeller som refereras av frågan 
- Ändringar i kontexten frågealternativ 

En kompilerad plan kan matas ut från cachen för en mängd orsaker, bland annat instansen startas om, databasbegränsade konfigurationsändringar, minnesbelastning och explicit begäranden att rensa cacheminnet. Med hjälp av en RECOMPILE-tipset innebär dessutom en plan inte cachelagras.

En kompileras om (eller en ny kompilering efter cache borttagningen) kan fortfarande resultera i generering av en identisk frågeplan körning från den som ursprungligen observerats.  Om det finns dock ändringar i planen jämfört med tidigare eller ursprungliga planen kan är följande de vanligaste förklaringarna till varför en plan för körning av frågan ändras:

- **Ändra fysiska design**. Till exempel skapa nya index detta mer effektivt med kraven för en fråga som kan användas för en ny sammanställning om Frågeoptimeringen beslutar den är mer optimala för att använda det nya indexet än datastruktur som ursprungligen har valts för den första versionen av körningen av frågan.  Alla fysiska ändringar i de refererade objekt kan resultera i en ny valet av frågeplan under kompileringen.

- **Server resurs skillnader**. I ett scenario där en plan skiljer sig på ”system A” och ”system B” – tillgängligheten för resurser, till exempel antalet tillgängliga processorer och kan påverka vilken plan hämtar genereras.  Till exempel om ett system har ett högre antal processorer, kan ett parallellt plan väljas. 

- **Olika statistik**. Statistik som är associerade med de refererade objekt ändras eller avviker väsentligt från den ursprungliga system statistik.  Om statistiken ändrar och kompileras inträffar, använder Frågeoptimeringen statistik från och med den specifika tidpunkten i tid. Reviderade statistik kan ha mycket olika distributioner och frekvenser som inte är skiftläget för den ursprungliga kompileringen.  De här ändringarna används för att beräkna kardinalitet beräknar (antal rader som förväntat kan passera trädet logiska fråga).  Ändringar av kardinalitet uppskattningar kan leda oss att välja olika fysiska operatörer och associerade ordning för åtgärder.  Även mindre ändringar till statistik kan resultera i en plan för körning av ändrade frågan.

- **Ändrade databasens kompatibilitet nivå eller kardinalitet kostnadsuppskattning version**.  Ändringar av kompatibilitetsnivån för databas kan aktivera nya strategier och funktioner som kan resultera i en annan frågeplan för körning.  Fråga körning plan val under kompileringen utöver kompatibilitetsnivån för databas, inaktivera eller aktivera spårningsflagga 4199 eller ändra tillståndet för begränsade databaskonfigurationen QUERY_OPTIMIZER_HOTFIXES kan också påverka.  Spårningsflaggor 9481 (force äldre CE) och 2312 (tvingar standard CE) är också planera att påverkas. 

### <a name="resolve-problem-queries-or-provide-more-resources"></a>Lösa problem med frågor eller ange fler resurser

När du har identifierat problemet kan du justera problemet frågor eller uppgradera beräkningsstorleken eller tjänstnivån ökar du kapaciteten för din Azure SQL database att absorbera CPU-kraven. Information om att skala resurser för enskilda databaser finns i [skala resurser för enkel databas i Azure SQL Database](sql-database-single-database-scale.md) och skala resurser för elastiska pooler finns i [skala resurser för elastisk pool i Azure SQL Databasen](sql-database-elastic-pool-scale.md). Information om att skala en hanterad instans finns i [på instansnivå resursbegränsningar](sql-database-managed-instance-resource-limits.md#instance-level-resource-limits).

### <a name="determine-if-running-issues-due-to-increase-workload-volume"></a>Avgöra om kör problem som orsakas av öka arbetsbelastningen volym

En ökning av programtrafik och arbetsbelastning kan redovisa ökade CPU-användning, men du måste vara noga med att korrekt diagnostisera problemet. Besvara de här frågorna för att avgöra om verkligen är en CPU-ökning på grund av arbetsbelastning volymförändringar i ett scenario med hög CPU:

1. Är frågor från programmet orsaken till hög CPU-problemet?
2. För de främsta CPU förbrukar frågor (som kan identifieras):

   - Avgör om det fanns flera körningsplaner som är associerade med samma fråga. I så, fall ta reda på varför.
   - Anger om så går tidsgränsen har konsekventa och om ökat antal körningar för frågor med samma Körningsplan. Om Ja, finns det troligen prestandaproblem på grund av arbetsbelastningen ökar.

Sammanfattningsvis om körningen frågeplan kunde inte utföras annorlunda men processoranvändningen ökar tillsammans med antal körningar, är det förmodligen ett arbetsbelastningen ökar minnesrelaterade prestandaproblem.

Det är inte alltid lätt att ingå arbetsbelastning volymen har ändrats som driver en CPU-problem.   Faktorer att tänka på: 

- **Resursanvändning som har ändrats**

  Tänk dig ett scenario där CPU ökat till 80% under en längre tid.  CPU-belastning som enbart innebär inte att arbetsbelastningen volymen har ändrats.  Fråga Körningsplan upprepningar och distribution av dataändringar kan också bidra till mer Resursanvändning även om programmet körs exakt samma arbetsbelastning.

- **Ny fråga visas**

   Ett program kan driva en ny uppsättning frågor vid olika tidpunkter.

- **Antal begäranden ökar eller minskar**

   Det här scenariot är det mest uppenbara måttet på arbetsbelastningen. Antalet frågor som motsvarar inte alltid mer resursutnyttjande. Det här måttet är dock fortfarande en betydande signal förutsatt att andra faktorer är oförändrade.

## <a name="waiting-related-performance-issues"></a>Väntetiden minnesrelaterade prestandaproblem

När du är säker på att du inte får en hög CPU, körs minnesrelaterade prestandaproblem, får du vänta minnesrelaterade prestandaproblem. Nämligen som CPU-resurser inte används effektivt eftersom Processorn väntar på några andra resurser. I det här fallet är nästa steg att identifiera vad din processorresurser väntar på. De vanligaste metoderna för att visa upp vänta kategorier av typen:

- Den [Query Store](https://docs.microsoft.com/sql/relational-databases/performance/monitoring-performance-by-using-the-query-store) innehåller vänta statistik per fråga över tid. I Query Store kombineras vänta typer i vänta kategorier. Mappningen av vänta kategorier för att vänta typer finns i [sys.query_store_wait_stats](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-query-store-wait-stats-transact-sql#wait-categories-mapping-table).
- [sys.dm_db_wait_stats](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-db-wait-stats-azure-sql-database) returnerar information om den väntar påträffades av trådar som körs under åtgärden. Du kan använda den här aggregerade vyn för att diagnostisera prestandaproblem med Azure SQL Database och med specifika frågor och -batchar.
- [sys.dm_os_waiting_tasks](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-os-waiting-tasks-transact-sql) returnerar information om kön vänta på uppgifter som väntar på några resurser.

I scenarier med hög CPU återspeglar Query Store och vänta statistik alltid inte CPU-användning av dessa två skäl:

- Hög CPU-användning frågor kan fortfarande köras och frågorna är inte klar
- Hög CPU konsumerande frågorna kördes när ett fel uppstod

Query Store och vänta statistik spårning dynamiska hanteringsvyer bara visa resultat för har slutfört och en timeout uppstod i frågor och visar inte data för att köra uttryck för närvarande (tills den är klar). Den dynamiska hanteringsvyn [sys.dm_exec_requests](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-requests-transact-sql) kan du spåra för närvarande kör frågor och associerade worker-tid.

I det föregående diagrammet visas är den vanligaste väntar:

- Lås (blockera)
- I/O
- `tempdb`-relaterade konkurrens
- Minne bevilja väntar

> [!IMPORTANT]
> En uppsättning finns en T-SQL-frågor med dessa DMV: er för att felsöka problemen väntar-relaterade:
>
> - [Identifiera prestandaproblem för i/o](sql-database-monitoring-with-dmvs.md#identify-io-performance-issues)
> - [Identifiera `tempdb` prestandaproblem](sql-database-monitoring-with-dmvs.md#identify-io-performance-issues)
> - [Identifiera minne bevilja väntar](sql-database-monitoring-with-dmvs.md#identify-memory-grant-wait-performance-issues)
> - [TigerToolbox - väntar och lås](https://github.com/Microsoft/tigertoolbox/tree/master/Waits-and-Latches)
> - [TigerToolbox - usp_whatsup](https://github.com/Microsoft/tigertoolbox/tree/master/usp_WhatsUp)

## <a name="improving-database-performance-with-more-resources"></a>Förbättra databasens prestanda med fler resurser

Slutligen, om det finns inga användbara objekt som kan förbättra databasens prestanda kan du ändra mängden resurser som är tillgängliga i Azure SQL Database. Du kan tilldela mer resurser genom att ändra den [DTU tjänstnivå](sql-database-service-tiers-dtu.md) för en enkel databas eller öka edtu: er för en elastisk pool när som helst. Du kan också om du använder den [vCore-baserade inköpsmodellen](sql-database-service-tiers-vcore.md), du kan ändra antingen tjänstnivå eller öka resurserna för din databas.

1. För enskilda databaser kan du [ändra tjänstnivåer](sql-database-single-database-scale.md) eller [beräkningsresurser](sql-database-single-database-scale.md) på begäran för att förbättra databasprestanda.
2. Överväg att använda för flera databaser [elastiska pooler](sql-database-elastic-pool-guidance.md) att skala resurser automatiskt.

## <a name="tune-and-refactor-application-or-database-code"></a>Justera och omstrukturera program eller databasen

Du kan ändra koden för att använda databasen, ändra index, tvinga planer på eller använda tips att manuellt anpassa databasen till din arbetsbelastning mer optimalt. Hitta vissa vägledning och tips för manuell justering och skriva om koden i den [prestanda vägledning avsnittet](sql-database-performance-guidance.md) artikeln.

## <a name="next-steps"></a>Nästa steg

- Om du vill aktivera automatisk justering i Azure SQL Database och att funktionen för automatisk justering helt hantera din arbetsbelastning kan se [aktivera automatisk justering](sql-database-automatic-tuning-enable.md).
- För att använda manuell inställning, kan du granska [Justeringsrekommendationer i Azure-portalen](sql-database-advisor-portal.md) och tillämpa manuellt de som förbättrar prestandan för dina frågor.
- Ändra resurser som är tillgängliga i din databas genom att ändra [Azure SQL Database-tjänstnivåer](sql-database-performance-guidance.md)
