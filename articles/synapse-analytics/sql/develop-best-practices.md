---
title: Bästa praxis för utveckling för Synapse SQL
description: Rekommendationer och metodtips som du bör känna till när du utvecklar för Synapse SQL.
services: synapse-analytics
author: XiaoyuMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 04/15/2020
ms.author: xiaoyul
ms.reviewer: igorstan
ms.openlocfilehash: 3356817fbaef955e05358f9c92619f3c04f747d7
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2020
ms.locfileid: "81429621"
---
# <a name="development-best-practices-for-synapse-sql"></a>Bästa praxis för utveckling för Synapse SQL
I den här artikeln beskrivs vägledning och metodtips när du utvecklar din informationslagerlösning. 

## <a name="development-best-practices-for-synapse-sql"></a>Bästa praxis för utveckling för Synapse SQL

### <a name="reduce-cost-with-pause-and-scale"></a>Minska kostnaderna genom att pausa och skala

Läs mer om att minska kostnaderna genom att pausa och skala i [Hantera beräkning](../sql-data-warehouse/sql-data-warehouse-manage-compute-overview.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json).

### <a name="maintain-statistics"></a>Underhålla statistik

Se till att du uppdaterar din statistik dagligen eller efter varje belastning.  Det finns alltid kompromisser mellan prestanda och kostnaden för att skapa och uppdatera statistik. Om det tar för lång tid att underhålla all statistik, var mer selektiv om vilka kolumner som har statistik eller vilka kolumner som behöver uppdateras ofta.  

Du kanske till exempel vill uppdatera datumkolumner, där nya värden kan läggas till dagligen. **Du kommer att få mest nytta av att ha statistik om kolumner inblandade i kopplingar, kolumner som används i WHERE-satsen och kolumner som finns i GROUP BY.**

Se även [Hantera tabellstatistik](develop-tables-statistics.md), [SKAPA STATISTIK](develop-tables-statistics.md), [UPPDATERA STATISTIK][UPDATE STATISTICS].

### <a name="hash-distribute-large-tables"></a>Hash-distribuera stora tabeller

Tabeller distribueras som standard med resursallokering (Round Robin).  Detta gör det enkelt för användare att börja skapa tabeller utan att behöva bestämma hur deras tabeller ska fördelas.  Round Robin-tabeller kan fungera tillräckligt för vissa arbetsbelastningar. Men i de flesta fall kommer valet av en distributionskolumn att fungera mycket bättre.  

Det vanligaste exemplet på när en tabell som distribueras med en kolumn presterar avsevärt mycket bättre än en resursallokeringstabell är när två stora faktatabeller kopplas.  Om du till exempel har en ordertabell, som distribueras av order_id, och en transaktionstabell, som också distribueras av order_id, när du ansluter ordertabellen till transaktionstabellen på order_id, blir den här frågan en genomströmningsfråga. Det innebär att vi eliminerar datarörelser.  Färre steg innebär en snabbare fråga.  Mindre dataflyttning gör också att frågor körs snabbare.

När du läser in en distribuerad tabell ser du till att inkommande data inte sorteras baserat på distributionsnyckeln eftersom det gör att inläsningarna tar längre tid.  Se länkarna nedan för ytterligare information om hur val av en distributionskolumn kan förbättra prestanda samt hur du definierar en distribuerad tabell i WITH-satsen i create tables-satsen.

Se även [Tabellöversikt,][Table overview] [Tabellfördelning,][Table distribution] [Välja tabellfördelning,][Selecting table distribution] [SKAPA TABELL][CREATE TABLE], SKAPA TABELL [SOM SELECT][CREATE TABLE AS SELECT].

### <a name="do-not-over-partition"></a>Överpartitionera inte
Medan partitionering data kan vara effektivt för att underhålla dina data genom partitionsväxling eller optimera genomsökningar med partition eliminering, med för många partitioner kan sakta ner dina frågor.  Ofta en hög granularitet partitionering strategi som kan fungera bra på SQL Server kanske inte fungerar bra på SQL Pool.  

För många partitioner kan också minska effektiviteten i grupperade columnstore-index om varje partition har färre än 1 miljoner rader. SQL pool partitionerar dina data för dig i 60 databaser. Så om du skapar en tabell med 100 partitioner blir resultatet 6000 partitioner.  Alla arbetsbelastningar är olika så det bästa rådet är att experimentera med partitioneringen för att se vad som fungerar bäst med din arbetsbelastning.  

Ett alternativ att tänka på är att använda en granularitet som är lägre än vad som kan ha fungerat för dig i SQL Server.  Överväg exempelvis att använda veckovisa eller månadsvisa partitioneringar i stället för dagliga.

Se även [Tabellpartitionering][Table partitioning].

### <a name="minimize-transaction-sizes"></a>Minimera transaktionsstorlekar

INSERT-, UPDATE- och DELETE-instruktioner körs i en transaktion och när de misslyckas måste de återställas.  Du kan minimera risken för en lång återställning genom att minska transaktionsstorlekarna om det går.  Du kan göra det genom att dela upp INSERT-, UPDATE- och DELETE-instruktioner i flera delar.  Om du till exempel har en INSERT som du förväntar dig att ta 1 timme kan du dela upp INSERT i fyra delar och därmed förkorta varje körning till 15 minuter.

Dra nytta av minimal loggning, t.ex. med CTAS, TRUNCATE, DROP TABLE eller INSERT, för att tömma tabeller och minska risken för återställning.  Ett annat sätt att eliminera återställningar är att använda åtgärder med endast metadata, t.ex. med partitionsväxling, för datahantering.  Till exempel kan du, i stället för att köra en DELETE-instruktion för att ta bort alla rader i en tabell där order_date var i oktober 2001, partitionera dina data månadsvis och sedan byta ut partitionen med data mot en tom partition från en annan tabell (se ALTER TABLE-exemplen).  

För opartitionerade tabeller bör du överväga att använda en CTAS för att skriva de data som du vill behålla i en tabell i stället för att använda DELETE.  Om en CTAS tar lika lång tid är det en mycket säkrare åtgärd att köra eftersom den har minimal transaktionsloggning och kan avbrytas snabbt om det behövs.

Se även [Förstå transaktioner][Understanding transactions], [Optimera transaktioner,][Optimizing transactions] [Tabellpartitionering,][Table partitioning] [TRUNKERA TABELL][TRUNCATE TABLE], ALTER [TABLE][ALTER TABLE], Skapa tabell som [select (CTAS)][Create table as select (CTAS)].

### <a name="use-the-smallest-possible-column-size"></a>Använda minsta möjliga kolumnstorlek

När du definierar din DDL kan du förbättra frågeprestanda genom att använda den minsta datatypen som stöder dina data.  Detta är särskilt viktigt för CHAR- och VARCHAR-kolumner.  Om det längsta värdet i en kolumn är 25 tecken definierar du kolumnen som VARCHAR(25).  Undvik att definiera alla teckenkolumner med en stor standardlängd.  Definiera också kolumner som VARCHAR när det är allt som krävs i stället för att använda NVARCHAR.

Se även [Table overview][Table overview] (Tabellöversikt), [Table data types](develop-tables-data-types.md) (Datatyper för tabeller), [CREATE TABLE][CREATE TABLE]

### <a name="optimize-clustered-columnstore-tables"></a>Optimera grupperade columnstore-tabeller

Klustrade columnstore-index är ett av de mest effektiva sätten att lagra data i SQL-poolen.  Som standard skapas tabeller i SQL-poolen som Clustered ColumnStore.  Det är viktigt att segmentkvaliteten är bra för att uppnå bästa prestanda med frågor mot columnstore-tabeller.  När rader skrivs till columnstore-tabeller när minnet är hårt belastat, kan columnstore-segmentens kvalitet påverkas.  

Segmentkvaliteten kan mätas utifrån antalet rader i en komprimerad radgrupp.  Stegvisa anvisningar för hur du identifierar och förbättrar segmentkvaliteten för grupperade columnstore-tabeller finns i avsnittet [Causes of poor columnstore index quality][Causes of poor columnstore index quality] (Orsaker till låg columnstore-indexkvalitet) i artikeln [Table indexes][Table indexes] (Tabellindex).  Eftersom högkvalitativa columnstore-segment är viktiga är det en bra idé att använda användar-ID:er som tillhör den medelstora eller stora resursklassen för inläsning av data. Om du använder lägre [informationslagerenheter](azure -synapse-resource-consumption-models.md) innebär du att du vill tilldela en större resursklass till din inläsningsanvändare.

Eftersom columnstore-tabeller i allmänhet inte kommer att skicka data till ett komprimerat columnstore-segment förrän det finns mer än 1 miljon rader per tabell, och varje SQL-biljardtabell är uppdelad i 60 tabeller, kommer columnstore-tabeller inte att gynna en fråga om inte tabellen har mer än 60 miljoner rader.  För tabeller med mindre än 60 miljoner rader kan det hända att ha ett columstore-index inte den optimala lösningen.  

Om du partitionerar data bör du dessutom tänka på att varje partition måste innehålla 1 miljon rader för att kunna dra nytta av ett grupperat columnstore-index.  Om en tabell har 100 partitioner måste den ha minst 6 miljarder rader för att dra nytta av ett klusterkolumnerarklagre (60 distributioner *100 partitioner* 1 miljon rader).  

Om tabellen inte har 6 miljarder rader kan du antingen minska antalet partitioner eller överväga att använda en heap-tabell i stället.  Det kan också vara värt att experimentera för att se om bättre prestanda kan vinnas genom att använda en heap tabell med sekundära index snarare än en columnstore tabell.

När du kör frågor mot en columnstore-tabell körs frågorna snabbare om du bara väljer de kolumner som du behöver.  

Se även [Tabellindex][Table indexes], [Indexguide för Columnstore,][Columnstore indexes guide] [Återskapa columnstore index][Rebuilding columnstore indexes].

### <a name="next-steps"></a>Nästa steg

Om du inte hittade det du letade efter i den här artikeln kan du prova att använda "Sök efter dokument" till vänster på den här sidan för att söka igenom alla Azure SQL-pooldokument.  [Azure SQL pool Forum][Azure SQL pool MSDN Forum] är en plats där du ställer frågor till andra användare och till SQL-poolproduktgruppen.  

Vi övervakar aktivt detta forum för att kontrollera att dina frågor besvaras antingen av en annan användare eller av någon av oss.  Om du föredrar att ställa dina frågor om Stack Overflow har vi också ett [Azure SQL-poolstackspillforum][Azure SQL pool Stack Overflow Forum].

Använd sidan [Feedback från Azure SQL-pool][Azure SQL pool Feedback] för att göra funktionsbegäranden.  Genom att skicka in dina egna önskemål eller rösta fram andras förfrågningar hjälper du oss att prioritera funktioner.

<!--Image references-->

<!--Article references-->
[Create a support ticket]: ../sql-data-warehouse/sql-data-warehouse-get-started-create-support-ticket.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json
[Create table as select (CTAS)]: ../sql-data-warehouse/sql-data-warehouse-develop-ctas.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json
[Table overview]:develop-tables-overview.md
[Table distribution]: ../sql-data-warehouse/sql-data-warehouse-tables-distribute.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json
[Table indexes]: ../sql-data-warehouse/sql-data-warehouse-tables-index.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json
[Causes of poor columnstore index quality]: ../sql-data-warehouse/sql-data-warehouse-tables-index.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json#causes-of-poor-columnstore-index-quality
[Rebuilding columnstore indexes]: ../sql-data-warehouse/sql-data-warehouse-tables-index.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json#rebuilding-indexes-to-improve-segment-quality
[Table partitioning]: ../sql-data-warehouse/sql-data-warehouse-tables-partition.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json
[Temporary tables]:develop-tables-temporary.md
[Guide for using PolyBase]:../sql-analytics/data-loading-best-practices.md
[Load data]: ../sql-data-warehouse/esign-elt-data-loading.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json
[Move data with Azure Data Factory]: ../../data-factory/transform-data-using-machine-learning.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json
[Load data with Azure Data Factory]: ../../data-factory/load-azure-sql-data-warehouse.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json
[Load data with bcp]: /sql/tools/bcp-utility
[Load data with PolyBase]: ../sql-data-warehouse/load-data-wideworldimportersdw.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json
[Monitor your workload using DMVs]: ../sql-data-warehouse/sql-data-warehouse-manage-monitor.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json
[Pause compute resources]: ../sql-data-warehouse/sql-data-warehouse-manage-compute-overview.md#pause-compute-bk?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json
[Resume compute resources]: ../sql-data-warehouse/sql-data-warehouse-manage-compute-overview.md#resume-compute-bk?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json
[Scale compute resources]: ../sql-data-warehouse/sql-data-warehouse-manage-compute-overview.md#scale-compute?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json
[Understanding transactions]:develop-transactions.md
[Optimizing transactions]: ../sql-data-warehouse/sql-data-warehouse-develop-best-practices-transactions.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json
[Troubleshooting]: ../sql-data-warehouse/sql-data-warehouse-troubleshoot.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json
[LABEL]:develop-label.md

<!--MSDN references-->
[ALTER TABLE]: /sql/t-sql/statements/alter-table-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest
[CREATE EXTERNAL FILE FORMAT]: /sql/t-sql/statements/create-external-file-format-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest
[CREATE STATISTICS]: /sql/t-sql/statements/create-statistics-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest
[CREATE TABLE]: /sql/t-sql/statements/create-table-azure-sql-data-warehouse?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest
[CREATE TABLE AS SELECT]: /sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest
[DBCC PDW_SHOWEXECUTIONPLAN]: /sql/t-sql/database-console-commands/dbcc-pdw-showexecutionplan-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest
[INSERT]: /sql/t-sql/statements/insert-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest
[OPTION]: /sql/t-sql/queries/option-clause-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest
[TRUNCATE TABLE]: /sql/t-sql/statements/truncate-table-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest
[UPDATE STATISTICS]: /sql/t-sql/statements/update-statistics-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest
[sys.dm_exec_sessions]: /sql/relational-databases/system-dynamic-management-views/sys-dm-exec-sessions-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest
[sys.dm_pdw_exec_requests]: /sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-exec-requests-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest
[sys.dm_pdw_request_steps]: /sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-request-steps-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest
[sys.dm_pdw_sql_requests]: /sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-sql-requests-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest
[sys.dm_pdw_dms_workers]: /sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-dms-workers-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest
[sys.dm_pdw_waits]: /sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-waits-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest
[Columnstore indexes guide]: /sql/relational-databases/indexes/columnstore-indexes-overview?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest

<!--Other Web references-->
[Selecting table distribution]: https://blogs.msdn.microsoft.com/sqlcat/20../../choosing-hash-distributed-table-vs-round-robin-distributed-table-in-azure-sql-dw-service/
[Azure SQL pool Feedback]: https://feedback.azure.com/forums/307516-sql-data-warehouse
[Azure SQL pool MSDN Forum]: https://social.msdn.microsoft.com/Forums/sqlserver/home?forum=AzureSQLDataWarehouse
[Azure SQL pool Stack Overflow Forum]:  https://stackoverflow.com/questions/tagged/azure-sqldw
[Azure SQL pool loading patterns and strategies]: https://blogs.msdn.microsoft.com/sqlcat/20../../azure-sql-data-warehouse-loading-patterns-and-strategies/

## <a name="development-best-practices-for-sql-on-demand-preview"></a>Metodtips för utveckling för SQL på begäran (förhandsversion)

### <a name="general-considerations"></a>Generella saker att tänka på

Med SQL on-demand kan du fråga filer i dina Azure-lagringskonton. Den har inte lokala lagrings- eller inmatningsfunktioner, vilket innebär att alla filer som frågemålen är externa för SQL på begäran. Därför kan allt som rör läsning av filer från lagring påverka frågeprestanda.

### <a name="colocate-azure-storage-account-and-sql-on-demand"></a>Colocate Azure Storage-konto och SQL på begäran

 
För att minimera svarstiden samlokalisera ditt Azure-lagringskonto och din SQL-slutpunkt på begäran. Lagringskonton och slutpunkter som etablerats när arbetsytan skapas finns i samma region. 
 
Om du använder andra lagringskonton med SQL på begäran för bästa prestanda måste du se till att de finns i samma region. Annars kommer det att finnas ökad svarstid för datas nätverksöverföring från fjärrregionen till slutpunktens region.

### <a name="azure-storage-throttling"></a>Azure Storage begränsning

Flera program och tjänster kan komma åt ditt lagringskonto. När det kombinerade IOPS- eller dataflödet som genereras av program, tjänster och SQL-arbetsbelastning på begäran överskrider gränserna för lagringskontot, sker lagringsbegränsning. När lagringsbegränsning inträffar finns det en betydande negativ effekt på frågeprestanda. 
 
När begränsning har identifierats har SQL on-demand inbyggd hantering av det här scenariot. SQL on-demand kommer att göra begäranden till lagring i långsammare takt tills begränsningen är löst. För optimal körning av frågor rekommenderas dock att du inte betonar lagringskontot med andra arbetsbelastningar under frågekörningen.

### <a name="prepare-files-for-querying"></a>Förbereda filer för frågor

Om möjligt kan du förbereda filer för bättre prestanda:

- Konvertera CSV till Parkett - Parkett är columnar format. Eftersom den komprimeras har den mindre filstorlekar än CSV-filer med samma data, och SQL on-demand behöver mindre tid och lagringsbegäranden för att läsa den.
- Om en fråga riktar sig till en enda stor fil kan du dra nytta av att dela upp den till flera mindre filer.
- Försök att hålla din CSV filstorlek under 10GB.
- Det är att föredra att ha lika stora filer för en enda OPENROWSET-sökväg eller en extern tabell PLATS.
- Partitionera dina data genom att lagra partitioner till olika mappar eller filnamn - kontrollera [använd filnamn och filsökvägsfunktioner för att rikta specifika partitioner](#use-fileinfo-and-filepath-functions-to-target-specific-partitions).

### <a name="use-fileinfo-and-filepath-functions-to-target-specific-partitions"></a>Använda filinfo- och filsökvägar för att rikta specifika partitioner

Data är ofta ordnade i partitioner. Du kan instruera SQL på begäran att fråga vissa mappar och filer. Detta minskar antalet filer och mängden data som frågan behöver läsa och bearbeta. Följaktligen kommer du att uppnå bättre prestanda. Mer information finns i [filnamns-](develop-storage-files-overview.md#filename-function) och [filsökvägsfunktioner](develop-storage-files-overview.md#filepath-function) och exempel på hur [du frågar specifika filer](query-specific-files.md).

Om dina data i lagring inte är partitionerade kan du överväga att partitionera dem så att du kan använda dessa funktioner för att optimera frågor som riktar sig till dessa filer.

När [du frågar partitionerade Spark-tabeller](develop-storage-files-spark-tables.md) från SQL on-demand, kommer frågan automatiskt att rikta endast filer som behövs.

### <a name="use-cetas-to-enhance-query-performance-and-joins"></a>Använd CETAS för att förbättra frågeprestanda och kopplingar

[CETAS](develop-tables-cetas.md) är en av de viktigaste funktionerna i SQL on-demand. CETAS är en parallell åtgärd som skapar externa tabellmetadata och exporterar resultatet av SELECT-frågan till en uppsättning filer i ditt lagringskonto.

Du kan använda CETAS för att lagra ofta använda delar av frågor, till exempel kopplade referenstabeller, till en ny uppsättning filer. Senare kan du gå med i den här enskilda externa tabellen i stället för att upprepa vanliga kopplingar i flera frågor. När CETAS genererar parquet-filer skapas statistik automatiskt när den första frågan är inriktad på den här externa tabellen och du får bättre prestanda.
