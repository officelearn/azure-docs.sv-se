---
title: "Metodtips för Azure SQL Data Warehouse | Microsoft Docs"
description: "Rekommendationer och metodtips som du bör känna till när du utvecklar lösningar för Azure SQL Data Warehouse. De hjälper dig att lyckas!"
services: sql-data-warehouse
documentationcenter: NA
author: shivaniguptamsft
manager: jhubbard
editor: 
ms.assetid: 7b698cad-b152-4d33-97f5-5155dfa60f79
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: get-started-article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: performance
ms.date: 10/31/2016
ms.author: shigu;barbkess
ms.openlocfilehash: b3a7755281ceb2818f80e0e6b31cf51a46c8f650
ms.sourcegitcommit: d03907a25fb7f22bec6a33c9c91b877897e96197
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/12/2017
---
# <a name="best-practices-for-azure-sql-data-warehouse"></a>Metodtips för Azure SQL Data Warehouse
Den här artikeln innehåller en samling metodtips som hjälper dig att uppnå optimala prestanda med Azure SQL Data Warehouse.  Vissa begrepp i artikeln är grundläggande och enkla att förklara. Andra begrepp är mer avancerade och vi kommer bara att skrapa på ytan i den här artikeln.  Avsikten med den här artikeln är att ge dig grundläggande vägledning och att göra dig uppmärksam på viktiga områden som du bör fokusera på när du skapar ditt informationslager.  Varje avsnitt innehåller en introduktion till ett begrepp och hänvisar till mer detaljerade artiklar som beskriver begreppet i mer detalj.

Om du precis har satt igång med Azure SQL Data Warehouse kan artikeln kännas överväldigande, men oroa dig inte.  Avsnitten är i hög grad ordnade efter hur viktiga de är.  Om du börjar med att fokusera på några av de första begreppen så är det en bra start.  När du är mer van vid att använda SQL Data Warehouse kan du komma tillbaka och studera fler begrepp.  Det tar inte lång tid innan allt faller på plats.

## <a name="reduce-cost-with-pause-and-scale"></a>Minska kostnaderna genom att pausa och skala
En viktig funktion i SQL Data Warehouse är möjligheten att kunna pausa tjänsten när du inte använder den, vilket gör att faktureringen för beräkningsresurser stoppas.  En annan viktig funktion är möjligheten att skala resurser.  Du kan pausa och skala via Azure Portal eller med hjälp av PowerShell-kommandon.  Bekanta dig med de här funktionerna eftersom de avsevärt kan minska kostnaden för ditt informationslager när det inte används.  Om du alltid vill att informationslagret ska vara tillgängligt kan du överväga att skala ned det till den minsta storleken, ett DW100 i stället för att pausa.

Se även [Pause compute resources][Pause compute resources] (Pausa beräkningsresurser), [Resume compute resources][Resume compute resources] (Återuppta beräkningsresurser) och [Scale compute resources] (Skala beräkningsresurser).

## <a name="drain-transactions-before-pausing-or-scaling"></a>Tömma transaktioner före pausning eller skalning
När du pausar eller skalar SQL Data Warehouse avbryts dina frågor i bakgrunden när du initierar paus- eller skalningsbegäran.  Att avbryta en enkel SELECT-fråga är en snabb åtgärd och påverkar nästan inte alls den tid det tar att pausa eller skala instansen.  Transaktionsfrågor, som ändrar data eller datastrukturen, kan däremot ta längre tid att stoppa.  **Transaktionsfrågor måste per definition slutföras i sin helhet eller så måste ändringarna återställas.**  Det kan ta lång tid att återställa arbetet som en transaktionsfråga har utfört, till och med längre tid än den ursprungliga ändringen som frågan tillämpade.  Om du till exempel avbryter en fråga som tog bort rader och som redan har körts i en timme, kan det ta en timme för systemet att lägga till de borttagna raderna igen.  Om du pausar eller skalar under pågående transaktioner kan det verka som åtgärden tar lång tid eftersom pausningen och skalningen måste vänta tills återställningen har slutförts innan de kan fortsätta.

Se även [Understanding transactions][Understanding transactions] (Förstå transaktioner) och [Optimizing transactions][Optimizing transactions] (Optimera transaktioner)

## <a name="maintain-statistics"></a>Underhålla statistik
Till skillnad från SQL Server, som automatiskt identifierar och skapar eller uppdaterar statistik i kolumner, kräver SQL Data Warehouse manuellt underhåll av statistik.  Vi planerar att ändra detta i framtiden, men tills vidare är det bra om du underhåller din statistik för att säkerställa att SQL Data Warehouse-planerna är optimerade.  Kvaliteten på planerna som skapas av optimeraren beror på den tillgängliga statistiken.  **Ett enkelt sätt att komma igång med statistik är att skapa exempelstatistik för varje kolumn.**  Det är lika viktigt att uppdatera statistik när viktiga dataändringar görs.  En konservativ metod kan vara att uppdatera statistiken varje dag eller efter varje belastning.  Det finns alltid kompromisser mellan prestanda och kostnaden för att skapa och uppdatera statistik. Om du tycker att det tar för lång tid att underhålla all statistik kanske du vill prova att vara mer selektiv när du väljer vilka kolumner som ska innehålla statistik eller vilka kolumner som ska uppdateras regelbundet.  Du kanske till exempel vill uppdatera datumkolumner, där nya värden kan läggas till, varje dag. **Du får ut mest genom att använda statistik för kolumner som ingår i kopplingar, kolumner som används i WHERE-satsen och kolumner som finns i GROUP BY.**

Se även [Manage table statistics][Manage table statistics] (Hantera tabellstatistik), [CREATE STATISTICS][CREATE STATISTICS] och [UPDATE STATISTICS][UPDATE STATISTICS]

## <a name="group-insert-statements-into-batches"></a>Gruppera INSERT-satser i batchar
En engångsinläsning till en liten tabell med en INSERT-instruktion eller en regelbunden inläsning av en sökning kan prestera utmärkt för dina behov med en instruktion som `INSERT INTO MyLookup VALUES (1, 'Type 1')`.  Men om du behöver läsa in flera tusen eller flera miljoner rader under en dag kanske du märker att singleton-infogningar med INSERT inte hänger med.  I så fall utvecklar du i stället dina processer så att de skriver till en fil och så att en annan process regelbundet körs och läser in filen.

Se även [INSERT][INSERT]

## <a name="use-polybase-to-load-and-export-data-quickly"></a>Använda PolyBase för att snabbt läsa in och exportera data
SQL Data Warehouse stöder inläsning och export av data via flera verktyg, bland annat Azure Data Factory, PolyBase och BCP.  För små datamängder där prestanda inte är viktigt räcker alla verktygen för dina behov.  Om du däremot läser in eller exporterar stora mängder data eller om snabba prestanda krävs är PolyBase det bästa valet.  PolyBase är utformat att utnyttja MPP-arkitekturen (Massively Parallel Processing) i SQL Data Warehouse och kommer därför att läsa in och exportera datamängder snabbare än andra verktyg.  PolyBase-inläsningar kan utföras med hjälp av CTAS eller INSERT INTO.  **CTAS minimerar transaktionsloggningen och är det snabbaste sättet att läsa in data.**  Azure Data Factory stöder också PolyBase-inläsningar.  PolyBase stöder olika filformat, inklusive Gzip-filer.  **Du kan maximera genomflödet när du använder Gzip-textfiler genom att dela upp filerna i grupper om 60 eller fler filer för att maximera parallellbearbetningen vid inläsningen.**  För snabbare totalt genomflöde bör du överväga att använda samtidig inläsning av data.

Se även [Load data][Load data] (Läsa in data), [Guide for using PolyBase][Guide for using PolyBase] (Guide för att använda PolyBase), [Azure SQL Data Warehouse loading patterns and strategies][Azure SQL Data Warehouse loading patterns and strategies] (Azure SQL Data Warehouse: inläsningsmönster och strategier), [Load Data with Azure Data Factory][Load Data with Azure Data Factory] (Läsa in data med Azure Data Factory), [Move data with Azure Data Factory][Move data with Azure Data Factory] (Flytta data med Azure Data Factory), [CREATE EXTERNAL FILE FORMAT][CREATE EXTERNAL FILE FORMAT], [Create Table As Select (CTAS)][Create table as select (CTAS)]

## <a name="load-then-query-external-tables"></a>Läsa in och sedan fråga externa tabeller
Även om Polybase, även kallat externa tabeller, kan vara det snabbaste sättet att läsa in data, är det inte optimalt för frågor. Polybase-tabeller i SQL Data Warehouse har för närvarande endast stöd för Azure-blobbfiler. Dessa filer har inte några beräkningsresurser som backar upp dem.  Det betyder att SQL Data Warehouse inte kan avlasta detta arbete och därför måste läsa hela filen genom att läsa in den till tempdb för att kunna läsa data.  Om du har flera frågor som ska köras mot dessa data är det därför bättre att läsa in dem en gång och låta frågorna använda den lokala tabellen.

Se även [Guide for using PolyBase][Guide for using PolyBase] (Guide för att använda PolyBase)

## <a name="hash-distribute-large-tables"></a>Hash-distribuera stora tabeller
Tabeller distribueras som standard med resursallokering (Round Robin).  Detta gör det enkelt för användarna att börja skapa tabeller utan att de behöver bestämma hur tabellerna ska distribueras.  Resursallokeringstabeller kan prestera bra för vissa arbetsbelastningar, men i de flesta fall blir prestanda bättre om du väljer en distributionskolumn.  Det vanligaste exemplet på när en tabell som distribueras med en kolumn presterar avsevärt mycket bättre än en resursallokeringstabell är när två stora faktatabeller kopplas.  Om du till exempel har en ordertabell, som distribueras efter order_id, och en transaktionstabell, som också distribueras efter order_id, och du kopplar ordertabellen till transaktionstabellen baserat på order_id, så blir den här frågan en direktfråga, vilket innebär att vi eliminerar dataflyttningsåtgärder.  Färre steg innebär en snabbare fråga.  Mindre dataflyttning gör också att frågor körs snabbare.  Den här förklaringen skrapar bara på ytan. När du läser in en distribuerad tabell ser du till att inkommande data inte sorteras baserat på distributionsnyckeln eftersom det gör att inläsningarna tar längre tid.  På länkarna nedan hittar du mycket mer information om hur du kan förbättra prestanda genom att välja en distributionskolumn, samt information om hur du definierar en distribuerad tabell i WITH-satsen för CREATE TABLES-instruktionen.

Se även [Table overview][Table overview] (Tabellöversikt), [Table distribution][Table distribution] (Tabelldistribution), [Selecting table distribution][Selecting table distribution] (Välja tabelldistribution), [CREATE TABLE][CREATE TABLE], [CREATE TABLE AS SELECT][CREATE TABLE AS SELECT]

## <a name="do-not-over-partition"></a>Överpartitionera inte
Partitionering av data kan vara mycket effektivt för att hantera data genom partitionsväxlingar eller optimeringsgenomsökningar med partitionseliminering, men för många partitioner kan göra att dina frågor körs långsammare.  En partitioneringsstrategi med hög granularitet som fungerar bra med SQL Server fungerar ofta inte lika bra med SQL Data Warehouse.  För många partitioner kan också minska effektiviteten i grupperade columnstore-index om varje partition har färre än 1 miljoner rader.  Tänk på att SQL Data Warehouse partitionerar dina data i 60 databaser i bakgrunden. Det betyder att om du skapar en tabell med 100 partitioner, så resulterar detta i 6 000 partitioner.  Alla arbetsbelastningar är olika så det bästa rådet är att experimentera med partitioneringen för att se vad som fungerar bäst med din arbetsbelastning.  Överväg att använda lägre granularitet än vad som har fungerat i SQL Server.  Överväg exempelvis att använda veckovisa eller månadsvisa partitioneringar i stället för dagliga.

Se även [Table partitioning][Table partitioning] (Tabellpartitionering)

## <a name="minimize-transaction-sizes"></a>Minimera transaktionsstorlekar
INSERT-, UPDATE- och DELETE-instruktioner körs i en transaktion och när de misslyckas måste de återställas.  Du kan minimera risken för en lång återställning genom att minska transaktionsstorlekarna om det går.  Du kan göra det genom att dela upp INSERT-, UPDATE- och DELETE-instruktioner i flera delar.  Om du till exempel har en INSERT som du förväntar dig ska ta 1 timme kan du, om möjligt, dela upp den i 4 delar, som var och en körs i 15 minuter.  Dra nytta av minimal loggning, t.ex. med CTAS, TRUNCATE, DROP TABLE eller INSERT, för att tömma tabeller och minska risken för återställning.  Ett annat sätt att eliminera återställningar är att använda åtgärder med endast metadata, t.ex. med partitionsväxling, för datahantering.  Till exempel kan du, i stället för att köra en DELETE-instruktion för att ta bort alla rader i en tabell där order_date var i oktober 2001, partitionera dina data månadsvis och sedan byta ut partitionen med data mot en tom partition från en annan tabell (se ALTER TABLE-exemplen).  För opartitionerade tabeller bör du överväga att använda en CTAS för att skriva de data som du vill behålla i en tabell i stället för att använda DELETE.  Om en CTAS tar lika lång tid är det en mycket säkrare åtgärd att köra eftersom den har minimal transaktionsloggning och kan avbrytas snabbt om det behövs.

Se även [Understanding transactions][Understanding transactions] (Förstå transaktioner), [Optimizing transactions][Optimizing transactions] (Optimera transaktioner), [Table partitioning][Table partitioning] (Tabellpartitionering), [TRUNCATE TABLE][TRUNCATE TABLE], [ALTER TABLE][ALTER TABLE], [Create Table As Select (CTAS)][Create table as select (CTAS)]

## <a name="use-the-smallest-possible-column-size"></a>Använda minsta möjliga kolumnstorlek
När du definierar din DDL kan du förbättra frågeprestanda genom att använda den minsta datatypen som stöder dina data.  Detta är särskilt viktigt för CHAR- och VARCHAR-kolumner.  Om det längsta värdet i en kolumn är 25 tecken definierar du kolumnen som VARCHAR(25).  Undvik att definiera alla teckenkolumner med en stor standardlängd.  Definiera också kolumner som VARCHAR när det är allt som krävs i stället för att använda NVARCHAR.

Se även [Table overview][Table overview] (Tabellöversikt), [Table data types][Table data types] (Datatyper för tabeller) och [CREATE TABLE][CREATE TABLE]

## <a name="use-temporary-heap-tables-for-transient-data"></a>Använda tillfälliga heap-tabeller för tillfälliga data
När du tillfälligt hanterar data i SQL Data Warehouse kan processen gå snabbare om du använder en heap-tabell.  Om du bara läser in data för att mellanlagra dem innan du kör fler transformationer går det mycket snabbare om du läser in tabellen till en heap-tabell än om du läser in data till en grupperad columnstore-tabell.  Om du läser in data till en temporär tabell går inläsningen dessutom mycket fortare än om du läser in en tabell till permanent lagring.  Temporära tabeller börjar med ”#” och är endast tillgängliga för den session som skapade dem, vilket innebär att de endast fungerar i vissa scenarier.   Heap-tabeller definieras i WITH-satsen i en CREATE TABLE-instruktion.  Om du använder en temporär tabell måste du också komma ihåg att skapa statistik för den temporära tabellen.

Se även [Temporary tables][Temporary tables] (Temporära tabeller), [CREATE TABLE][CREATE TABLE] och [CREATE TABLE AS SELECT][CREATE TABLE AS SELECT]

## <a name="optimize-clustered-columnstore-tables"></a>Optimera grupperade columnstore-tabeller
Grupperade columnstore-index är ett av de mest effektiva sätten att lagra data i Azure SQL Data Warehouse.  Som standard skapas tabeller i SQL Data Warehouse som grupperade ColumnStore-tabeller.  Det är viktigt att segmentkvaliteten är bra för att uppnå bästa prestanda med frågor mot columnstore-tabeller.  När rader skrivs till columnstore-tabeller när minnet är hårt belastat, kan columnstore-segmentens kvalitet påverkas.  Segmentkvaliteten kan mätas utifrån antalet rader i en komprimerad radgrupp.  Stegvisa anvisningar för hur du identifierar och förbättrar segmentkvaliteten för grupperade columnstore-tabeller finns i avsnittet [Causes of poor columnstore index quality][Causes of poor columnstore index quality] (Orsaker till låg columnstore-indexkvalitet) i artikeln [Table indexes][Table indexes] (Tabellindex).  Eftersom det är viktigt att columnstore-segmenten har hög kvalitet är det en bra idé att använda användar-ID:n som finns i den medelstora eller stora resursklassen för inläsning av data.  Ju färre DWU:er du använder, desto större resursklass bör du tilldela till inläsningsanvändaren.

Eftersom columnstore-tabeller normalt inte skickar data till ett komprimerat columnstore-segment förrän det finns fler än 1 miljon rader per tabell och varje SQL Data Warehouse-tabell har partitionerats i 60 tabeller, så är tumregeln att columnstore-tabeller inte har nytta av en fråga om tabellen innehåller färre än 60 miljoner rader.  För tabeller med färre än 60 miljoner rader kanske det inte tjänar något till att ha ett columnstore-index.  Men det skadar inte heller.  Om du partitionerar data bör du dessutom tänka på att varje partition måste innehålla 1 miljon rader för att kunna dra nytta av ett grupperat columnstore-index.  Om en tabell har 100 partitioner måste den innehålla minst 6 miljarder rader för att ha nytta av ett arkiv med grupperade kolumner (60 distributioner * 100 partitioner * 1 miljoner rader).  Om din tabell inte innehåller 6 miljarder rader i det här exemplet minskar du antalet partitioner eller överväger att använda en heap-tabell i stället.  Det kan också vara värt att experimentera och se om du kan förbättra prestanda med en heap-tabell med sekundära index i stället för en columnstore-tabell.  Columnstore-tabeller stöder inte sekundära index än.

När du kör frågor mot en columnstore-tabell körs frågorna snabbare om du bara väljer de kolumner som du behöver.  

Se även [Table indexes][Table indexes] (Tabellindex), [Columnstore indexes guide][Columnstore indexes guide] (Guide för columnstore-index), [Rebuilding columnstore indexes][Rebuilding columnstore indexes] (Återskapa columnstore-index)

## <a name="use-larger-resource-class-to-improve-query-performance"></a>Använda en större resursklass för att förbättra frågeprestanda
SQL Data Warehouse använder resursgrupper som ett sätt att allokera minne till frågor.  Alla användare tilldelas som standard den mindre resursklassen som ger 100 MB minne per distribution.  Eftersom det alltid är 60 distributioner och varje distribution tilldelas minst 100 MB, är den totala minnesallokeringen i systemet 6 000 MB, eller strax under 6 GB.  Vissa frågor, t.ex. stora kopplingar eller inläsningar till grupperade columnstore-tabeller, kan dra nytta av större minnesallokeringar.  Vissa frågor, t.ex. rena genomsökningar, har ingen nytta av detta.  Å andra sidan påverkar användningen av större resursklasser samtidiga körningar, något som du bör ha i åtanke innan du flyttar alla dina användare till en stor resursklass.

Se även [Concurrency and workload management][Concurrency and workload management] (Hantering av samtidiga körningar och arbetsbelastningar)

## <a name="use-smaller-resource-class-to-increase-concurrency"></a>Använda den mindre resursklassen för bättre prestanda vid samtidig körning
Om du märker att användarfrågor har långa fördröjningar kan det bero på att användarna kör i större resursklasser och förbrukar många samtidighetsfack, vilket gör att andra frågor måste placeras i kö.  Du kan se om användarnas frågor köas genom att köra `SELECT * FROM sys.dm_pdw_waits` för att se om några rader returneras.

Se även [Concurrency and workload management][Concurrency and workload management] (Hantering av samtidiga körningar och arbetsbelastningar) och [sys.dm_pdw_waits][sys.dm_pdw_waits]

## <a name="use-dmvs-to-monitor-and-optimize-your-queries"></a>Använda DMV:er för att övervaka och optimera frågor
SQL Data Warehouse har flera datahanteringsvyer (DMV) som du kan använda för att övervaka frågekörningen.  Artikeln om övervakning nedan innehåller stegvisa anvisningar för hur du analyserar informationen från en fråga som körs.  För att snabbt hitta frågor i dessa DMV:er kan det vara bra att använda alternativet LABEL med dina frågor.

Se även [Monitor your workload using DMVs][Monitor your workload using DMVs] (Övervaka arbetsbelastningen med datahanteringsvyer), [LABEL][LABEL], [OPTION][OPTION], [sys.dm_exec_sessions][sys.dm_exec_sessions], [sys.dm_pdw_exec_requests][sys.dm_pdw_exec_requests], [sys.dm_pdw_request_steps][sys.dm_pdw_request_steps], [sys.dm_pdw_sql_requests][sys.dm_pdw_sql_requests], [sys.dm_pdw_dms_workers], [DBCC PDW_SHOWEXECUTIONPLAN][DBCC PDW_SHOWEXECUTIONPLAN], [sys.dm_pdw_waits][sys.dm_pdw_waits]

## <a name="other-resources"></a>Andra resurser
Information om vanliga problem och lösningar finns i vår [felsökningsartikel][Troubleshooting].

Om du inte hittar det du letar efter i den här artikeln provar du att använda ”Sök efter dokument” till vänster på den här sidan för att söka igenom alla Azure SQL Data Warehouse-dokument.  [Azure SQL Data Warehouse-forumet på MSDN][Azure SQL Data Warehouse MSDN Forum] är en plats där du kan ställa frågor till andra användare och till SQL Data Warehouse-produktgruppen.  Vi övervakar aktivt detta forum för att kontrollera att dina frågor besvaras antingen av en annan användare eller av någon av oss.  Om du föredrar att ställa dina frågor i Stack Overflow finns det även ett [Stack Overflow-forum för Azure SQL Data Warehouse][Azure SQL Data Warehouse Stack Overflow Forum].

Avslutningsvis ber vi dig att använda sidan för [Azure SQL Data Warehouse-feedback][Azure SQL Data Warehouse Feedback] om du har önskemål om nya funktioner.  Genom att skicka in dina egna önskemål eller rösta fram andras förfrågningar hjälper du oss att prioritera funktioner.

<!--Image references-->

<!--Article references-->
[Create a support ticket]: ./sql-data-warehouse-get-started-create-support-ticket.md
[Concurrency and workload management]: ./sql-data-warehouse-develop-concurrency.md
[Create table as select (CTAS)]: ./sql-data-warehouse-develop-ctas.md
[Table overview]: ./sql-data-warehouse-tables-overview.md
[Table data types]: ./sql-data-warehouse-tables-data-types.md
[Table distribution]: ./sql-data-warehouse-tables-distribute.md
[Table indexes]: ./sql-data-warehouse-tables-index.md
[Causes of poor columnstore index quality]: ./sql-data-warehouse-tables-index.md#causes-of-poor-columnstore-index-quality
[Rebuilding columnstore indexes]: ./sql-data-warehouse-tables-index.md#rebuilding-indexes-to-improve-segment-quality
[Table partitioning]: ./sql-data-warehouse-tables-partition.md
[Manage table statistics]: ./sql-data-warehouse-tables-statistics.md
[Temporary tables]: ./sql-data-warehouse-tables-temporary.md
[Guide for using PolyBase]: ./sql-data-warehouse-load-polybase-guide.md
[Load data]: ./sql-data-warehouse-overview-load.md
[Move data with Azure Data Factory]: ../data-factory/transform-data-using-machine-learning.md
[Load data with Azure Data Factory]: ./sql-data-warehouse-get-started-load-with-azure-data-factory.md
[Load data with bcp]: ./sql-data-warehouse-load-with-bcp.md
[Load data with PolyBase]: ./sql-data-warehouse-get-started-load-with-polybase.md
[Monitor your workload using DMVs]: ./sql-data-warehouse-manage-monitor.md
[Pause compute resources]: ./sql-data-warehouse-manage-compute-overview.md#pause-compute-bk
[Resume compute resources]: ./sql-data-warehouse-manage-compute-overview.md#resume-compute-bk
[Scale compute resources]: ./sql-data-warehouse-manage-compute-overview.md#scale-compute (Skala beräkningsresurser)
[Understanding transactions]: ./sql-data-warehouse-develop-transactions.md
[Optimizing transactions]: ./sql-data-warehouse-develop-best-practices-transactions.md
[Troubleshooting]: ./sql-data-warehouse-troubleshoot.md
[LABEL]: ./sql-data-warehouse-develop-label.md

<!--MSDN references-->
[ALTER TABLE]: https://msdn.microsoft.com/library/ms190273.aspx
[CREATE EXTERNAL FILE FORMAT]: https://msdn.microsoft.com/library/dn935026.aspx
[CREATE STATISTICS]: https://msdn.microsoft.com/library/ms188038.aspx
[CREATE TABLE]: https://msdn.microsoft.com/library/mt203953.aspx
[CREATE TABLE AS SELECT]: https://msdn.microsoft.com/library/mt204041.aspx
[DBCC PDW_SHOWEXECUTIONPLAN]: https://msdn.microsoft.com/library/mt204017.aspx
[INSERT]: https://msdn.microsoft.com/library/ms174335.aspx
[OPTION]: https://msdn.microsoft.com/library/ms190322.aspx
[TRUNCATE TABLE]: https://msdn.microsoft.com/library/ms177570.aspx
[UPDATE STATISTICS]: https://msdn.microsoft.com/library/ms187348.aspx
[sys.dm_exec_sessions]: https://msdn.microsoft.com/library/ms176013.aspx
[sys.dm_pdw_exec_requests]: https://msdn.microsoft.com/library/mt203887.aspx
[sys.dm_pdw_request_steps]: https://msdn.microsoft.com/library/mt203913.aspx
[sys.dm_pdw_sql_requests]: https://msdn.microsoft.com/library/mt203889.aspx
[sys.dm_pdw_dms_workers]: https://msdn.microsoft.com/library/mt203878.aspx
[sys.dm_pdw_waits]: https://msdn.microsoft.com/library/mt203893.aspx
[Columnstore indexes guide]: https://msdn.microsoft.com/library/gg492088.aspx

<!--Other Web references-->
[Selecting table distribution]: https://blogs.msdn.microsoft.com/sqlcat/2015/08/11/choosing-hash-distributed-table-vs-round-robin-distributed-table-in-azure-sql-dw-service/
[Azure SQL Data Warehouse Feedback]: https://feedback.azure.com/forums/307516-sql-data-warehouse
[Azure SQL Data Warehouse MSDN Forum]: https://social.msdn.microsoft.com/Forums/sqlserver/home?forum=AzureSQLDataWarehouse
[Azure SQL Data Warehouse Stack Overflow Forum]:  http://stackoverflow.com/questions/tagged/azure-sqldw
[Azure SQL Data Warehouse loading patterns and strategies]: http://blogs.msdn.microsoft.com/sqlcat/2017/05/17/azure-sql-data-warehouse-loading-patterns-and-strategies/
