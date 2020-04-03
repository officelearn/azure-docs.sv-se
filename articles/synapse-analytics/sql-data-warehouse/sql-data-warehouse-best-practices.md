---
title: Metodtips för SQL Analytics i Azure Synapse Analytics (tidigare SQL DW)
description: Rekommendationer och metodtips för att utveckla lösningar för SQL Analytics i Azure Synapse Analytics (tidigare SQL DW).
services: synapse-analytics
author: mlee3gsd
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 11/04/2019
ms.author: martinle
ms.reviewer: igorstan
ms.openlocfilehash: 44dbc03a41cfde94c344ae331b21d7536778050c
ms.sourcegitcommit: bc738d2986f9d9601921baf9dded778853489b16
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/02/2020
ms.locfileid: "80619103"
---
# <a name="best-practices-for-sql-analytics-in-azure-synapse-analytics-formerly-sql-dw"></a>Metodtips för SQL Analytics i Azure Synapse Analytics (tidigare SQL DW)

Den här artikeln är en samling metodtips som hjälper dig att uppnå optimala prestanda från din [SQL Analytics-distribution.](sql-data-warehouse-overview-what-is.md#synapse-sql-pool-in-azure-synapse)  Syftet med den här artikeln är att ge dig grundläggande vägledning och lyfta fram viktiga fokusområden.  Varje avsnitt introducerar dig till ett koncept och sedan pekar dig till mer detaljerade artiklar som täcker konceptet på djupet. Sekvensen av ämnen är i prioritetsordning. 

## <a name="reduce-cost-with-pause-and-scale"></a>Minska kostnaderna genom att pausa och skala

Läs mer om att minska kostnaderna genom att pausa och skala i [Hantera beräkning](sql-data-warehouse-manage-compute-overview.md). 

## <a name="maintain-statistics"></a>Underhålla statistik
Azure SQL Data Warehouse kan konfigureras för att automatiskt identifiera och skapa statistik för kolumner.  De frågeplaner som skapats av optimeraren är bara lika bra som den tillgängliga statistiken.  Vi rekommenderar att du aktiverar AUTO_CREATE_STATISTICS för dina databaser och håller statistiken uppdaterad dagligen eller efter varje inläsning för att säkerställa att statistik över kolumner som används i dina frågor alltid är uppdaterad. 

Om det tar för lång tid att uppdatera all statistik kan du försöka vara mer selektiv om vilka kolumner som kräver frekventa statistikuppdateringar. Du kanske till exempel vill uppdatera datumkolumner, där nya värden kan läggas till, varje dag. **Du kommer att få mest nytta av att ha uppdaterad statistik om kolumner inblandade i kopplingar, kolumner som används i WHERE-satsen och kolumner som finns i GROUP BY.**

Se även [Manage table statistics][Manage table statistics] (Hantera tabellstatistik), [CREATE STATISTICS][CREATE STATISTICS], [UPDATE STATISTICS][UPDATE STATISTICS]

## <a name="use-dmvs-to-monitor-and-optimize-your-queries"></a>Använda DMV:er för att övervaka och optimera frågor
SQL Analytics har flera DMVs som kan användas för att övervaka körning av frågor.  Artikeln om övervakning nedan innehåller stegvisa anvisningar för hur du analyserar informationen från en fråga som körs.  För att snabbt hitta frågor i dessa DMV:er kan det vara bra att använda alternativet LABEL med dina frågor.

Se även [Monitor your workload using DMVs][Monitor your workload using DMVs] (Övervaka arbetsbelastningen med datahanteringsvyer), [LABEL][LABEL], [OPTION][OPTION], [sys.dm_exec_sessions][sys.dm_exec_sessions], [sys.dm_pdw_exec_requests][sys.dm_pdw_exec_requests], [sys.dm_pdw_request_steps][sys.dm_pdw_request_steps], [sys.dm_pdw_sql_requests][sys.dm_pdw_sql_requests], [sys.dm_pdw_dms_workers], [DBCC PDW_SHOWEXECUTIONPLAN][DBCC PDW_SHOWEXECUTIONPLAN], [sys.dm_pdw_waits][sys.dm_pdw_waits]

## <a name="tune-query-performance-with-new-product-enhancements"></a>Justera frågeprestanda med nya produktförbättringar
- [Prestandajustering med materialiserade vyer](https://docs.microsoft.com/azure/sql-data-warehouse/performance-tuning-materialized-views)
- [Prestandajustering med grupperade kolumnlagringsindex](https://docs.microsoft.com/azure/sql-data-warehouse/performance-tuning-ordered-cci)
- [Prestandajustering med cachelagring av resultatuppsättningar](https://docs.microsoft.com/azure/sql-data-warehouse/performance-tuning-result-set-caching)

## <a name="group-insert-statements-into-batches"></a>Gruppera INSERT-satser i batchar
En engångsinläsning till en liten tabell med en INSERT-instruktion eller en regelbunden inläsning av en sökning kan prestera utmärkt för dina behov med en instruktion som `INSERT INTO MyLookup VALUES (1, 'Type 1')`.  Men om du behöver läsa in flera tusen eller flera miljoner rader under en dag kanske du märker att singleton-infogningar med INSERT inte hänger med.  I så fall utvecklar du i stället dina processer så att de skriver till en fil och så att en annan process regelbundet körs och läser in filen.

Se även [INSERT][INSERT]

## <a name="use-polybase-to-load-and-export-data-quickly"></a>Använda PolyBase för att snabbt läsa in och exportera data

 SQL Analytics stöder inläsning och export av data via flera verktyg, inklusive Azure Data Factory, PolyBase och BCP.  För små datamängder där prestanda inte är viktigt räcker alla verktygen för dina behov.  Om du däremot läser in eller exporterar stora mängder data eller om snabba prestanda krävs är PolyBase det bästa valet.  
 
 PolyBase är utformad för att utnyttja MPP-arkitekturen (Massively Parallel Processing) och kommer att läsa in och exportera data magnituder snabbare än något annat verktyg.  PolyBase-inläsningar kan utföras med hjälp av CTAS eller INSERT INTO.  **CTAS minimerar transaktionsloggningen och är det snabbaste sättet att läsa in data.** 
 
  Azure Data Factory stöder också PolyBase-belastningar och kan uppnå liknande prestanda som CTAS.  PolyBase stöder olika filformat, inklusive Gzip-filer.  **För att maximera dataflödet när du använder gzip-textfiler, dela upp filer i 60 eller fler filer för att maximera parallellismen i din belastning.**  För snabbare totalt genomflöde bör du överväga att använda samtidig inläsning av data.

Se även [Läs in data][Load data], Guide för användning av [PolyBase,][Guide for using PolyBase] [SQL-poolinläsningsmönster och strategier][Azure SQL Data Warehouse loading patterns and strategies], Läs in data med Azure Data [Factory][Load Data with Azure Data Factory], Flytta data med Azure [Data Factory][Move data with Azure Data Factory], SKAPA [EXTERNT FILFORMAT][CREATE EXTERNAL FILE FORMAT], [Skapa tabell som välj (CTAS)][Create table as select (CTAS)]

## <a name="load-then-query-external-tables"></a>Läsa in och sedan fråga externa tabeller
Även om Polybase, även kallat externa tabeller, kan vara det snabbaste sättet att läsa in data, är det inte optimalt för frågor. Polybase-tabeller stöder för närvarande endast Azure blob-filer och Azure Data Lake-lagring. Dessa filer har inte några beräkningsresurser som backar upp dem.  

Därför kan SQL Analytics inte avlasta det här arbetet och måste därför läsa hela filen genom att läsa in den till tempdb för att läsa data.  Om du har flera frågor som ska köras mot dessa data är det därför bättre att läsa in dem en gång och låta frågorna använda den lokala tabellen.

Se även [Guide for using PolyBase][Guide for using PolyBase] (Guide för att använda PolyBase)

## <a name="hash-distribute-large-tables"></a>Hash-distribuera stora tabeller
Tabeller distribueras som standard med resursallokering (Round Robin).  Detta gör det enkelt för användarna att börja skapa tabeller utan att de behöver bestämma hur tabellerna ska distribueras.  Resursallokeringstabeller kan prestera bra för vissa arbetsbelastningar, men i de flesta fall blir prestanda bättre om du väljer en distributionskolumn.  Det vanligaste exemplet på när en tabell som distribueras med en kolumn presterar avsevärt mycket bättre än en resursallokeringstabell är när två stora faktatabeller kopplas.  

Om du till exempel har en ordertabell, som distribueras efter order_id, och en transaktionstabell, som också distribueras efter order_id, och du kopplar ordertabellen till transaktionstabellen baserat på order_id, så blir den här frågan en direktfråga, vilket innebär att vi eliminerar dataflyttningsåtgärder.  Färre steg innebär en snabbare fråga.  Mindre dataflyttning gör också att frågor körs snabbare.  

När du läser in en distribuerad tabell ser du till att inkommande data inte sorteras baserat på distributionsnyckeln eftersom det gör att inläsningarna tar längre tid.  Se nedanstående länkar för mer information om hur val av en distributionskolumn kan förbättra prestanda samt hur du definierar en distribuerad tabell i WITH-satsen i create table-satsen.

Se även [Table overview][Table overview] (Tabellöversikt), [Table distribution][Table distribution] (Tabelldistribution), [Selecting table distribution][Selecting table distribution] (Välja tabelldistribution), [CREATE TABLE][CREATE TABLE], [CREATE TABLE AS SELECT][CREATE TABLE AS SELECT]

## <a name="do-not-over-partition"></a>Överpartitionera inte
Medan partitionering data kan vara effektivt för att underhålla dina data genom partitionsväxling eller optimera genomsökningar med partition eliminering, med för många partitioner kan sakta ner dina frågor.  Ofta en hög granularitet partitionering strategi, som kan fungera bra på SQL Server kanske inte fungerar bra i SQL Analytics.  

För många partitioner kan också minska effektiviteten i grupperade columnstore-index om varje partition har färre än 1 miljoner rader.  Tänk på att bakom kulisserna partitionerar SQL Analytics dina data åt dig i 60 databaser, så om du skapar en tabell med 100 partitioner resulterar detta faktiskt i 6000 partitioner.  

Alla arbetsbelastningar är olika så det bästa rådet är att experimentera med partitioneringen för att se vad som fungerar bäst med din arbetsbelastning.  Överväg att använda lägre granularitet än vad som har fungerat i SQL Server.  Överväg exempelvis att använda veckovisa eller månadsvisa partitioneringar i stället för dagliga.

Se även [Table partitioning][Table partitioning] (Tabellpartitionering)

## <a name="minimize-transaction-sizes"></a>Minimera transaktionsstorlekar
INSERT-, UPDATE- och DELETE-instruktioner körs i en transaktion och när de misslyckas måste de återställas.  Du kan minimera risken för en lång återställning genom att minska transaktionsstorlekarna om det går.  Du kan göra det genom att dela upp INSERT-, UPDATE- och DELETE-instruktioner i flera delar.  

Om du till exempel har en INSERT som du förväntar dig att ta 1 timme, om möjligt, dela upp INSERT i fyra delar, som var och en körs i 15 minuter.  Dra nytta av minimal loggning, t.ex. med CTAS, TRUNCATE, DROP TABLE eller INSERT, för att tömma tabeller och minska risken för återställning.  

Ett annat sätt att eliminera återställningar är att använda åtgärder med endast metadata, t.ex. med partitionsväxling, för datahantering.  Till exempel kan du, i stället för att köra en DELETE-instruktion för att ta bort alla rader i en tabell där order_date var i oktober 2001, partitionera dina data månadsvis och sedan byta ut partitionen med data mot en tom partition från en annan tabell (se ALTER TABLE-exemplen).  

För opartitionerade tabeller bör du överväga att använda en CTAS för att skriva de data som du vill behålla i en tabell i stället för att använda DELETE.  Om en CTAS tar lika lång tid är det en mycket säkrare åtgärd att köra eftersom den har minimal transaktionsloggning och kan avbrytas snabbt om det behövs.

Se även [Understanding transactions][Understanding transactions] (Förstå transaktioner), [Optimizing transactions][Optimizing transactions] (Optimera transaktioner), [Table partitioning][Table partitioning] (Tabellpartitionering), [TRUNCATE TABLE][TRUNCATE TABLE], [ALTER TABLE][ALTER TABLE], [Create Table As Select (CTAS)][Create table as select (CTAS)]

## <a name="reduce-query-result-sizes"></a>Minska frågeresultatstorlekar  
Det här steget hjälper dig att undvika problem på klientsidan som orsakas av stort frågeresultat.  Du kan redigera frågan för att minska antalet returnerade rader. Med vissa frågegenereringsverktyg kan du lägga till syntaxen "topp N" i varje fråga.  Du kan också CETAS frågeresultatet till en temporär tabell och sedan använda PolyBase-export för bearbetning på nednivå.

## <a name="use-the-smallest-possible-column-size"></a>Använda minsta möjliga kolumnstorlek
När du definierar din DDL, med hjälp av den minsta datatyp som stöder dina data kommer att förbättra frågeprestanda.  Detta är särskilt viktigt för CHAR- och VARCHAR-kolumner.  Om det längsta värdet i en kolumn är 25 tecken definierar du kolumnen som VARCHAR(25).  Undvik att definiera alla teckenkolumner med en stor standardlängd.  Definiera också kolumner som VARCHAR när det är allt som krävs i stället för att använda NVARCHAR.

Se även [Table overview][Table overview] (Tabellöversikt), [Table data types][Table data types] (Datatyper för tabeller), [CREATE TABLE][CREATE TABLE]

## <a name="use-temporary-heap-tables-for-transient-data"></a>Använda tillfälliga heap-tabeller för tillfälliga data
När du tillfälligt landar data kan det hända att det går snabbare att använda en heap-tabell.  Om du bara läser in data för att mellanlagra dem innan du kör fler transformationer går det mycket snabbare om du läser in tabellen till en heap-tabell än om du läser in data till en grupperad columnstore-tabell.  

Om du läser in data till en temporär tabell går inläsningen dessutom mycket fortare än om du läser in en tabell till permanent lagring.  Temporära tabeller börjar med ett "#" och är endast tillgängliga för den session som skapade den, så de kan bara fungera i begränsade scenarier.   Heap-tabeller definieras i WITH-satsen i en CREATE TABLE-instruktion.  Om du använder en temporär tabell måste du också komma ihåg att skapa statistik för den temporära tabellen.

Se även [Temporary tables][Temporary tables] (Temporära tabeller), [CREATE TABLE][CREATE TABLE], [CREATE TABLE AS SELECT][CREATE TABLE AS SELECT]

## <a name="optimize-clustered-columnstore-tables"></a>Optimera grupperade columnstore-tabeller
Klustrade columnstore-index är ett av de mest effektiva sätten att lagra dina data i SQL Analytics.  Som standard skapas tabeller i SQL Analytics som Clustered ColumnStore.  Det är viktigt att segmentkvaliteten är bra för att uppnå bästa prestanda med frågor mot columnstore-tabeller.  

När rader skrivs till columnstore-tabeller när minnet är hårt belastat, kan columnstore-segmentens kvalitet påverkas.  Segmentkvaliteten kan mätas utifrån antalet rader i en komprimerad radgrupp.  Stegvisa anvisningar för hur du identifierar och förbättrar segmentkvaliteten för grupperade columnstore-tabeller finns i avsnittet [Causes of poor columnstore index quality][Causes of poor columnstore index quality] (Orsaker till låg columnstore-indexkvalitet) i artikeln [Table indexes][Table indexes] (Tabellindex).  

Eftersom högkvalitativa columnstore-segment är viktiga är det en bra idé att använda användar-ID:er som tillhör den medelstora eller stora resursklassen för inläsning av data. Om du använder lägre [informationslagerenheter](what-is-a-data-warehouse-unit-dwu-cdwu.md) innebär du att du vill tilldela en större resursklass till din inläsningsanvändare.

Eftersom columnstore-tabeller i allmänhet inte kommer att skicka data till ett komprimerat columnstore-segment förrän det finns mer än 1 miljon rader per tabell och varje SQL Analytics-tabell är uppdelad i 60 tabeller, som en tumregel, kommer columnstore-tabeller inte att gynna en fråga om inte tabellen har mer än 60 miljoner rader.  För tabeller med färre än 60 miljoner rader kanske det inte tjänar något till att ha ett columnstore-index.  Men det skadar inte heller.  

Om du partitionerar data bör du dessutom tänka på att varje partition måste innehålla 1 miljon rader för att kunna dra nytta av ett grupperat columnstore-index.  Om en tabell har 100 partitioner måste den innehålla minst 6 miljarder rader för att ha nytta av ett arkiv med grupperade kolumner (60 distributioner * 100 partitioner * 1 miljoner rader).  

Om din tabell inte innehåller 6 miljarder rader i det här exemplet minskar du antalet partitioner eller överväger att använda en heap-tabell i stället.  Det kan också vara värt att experimentera och se om du kan förbättra prestanda med en heap-tabell med sekundära index i stället för en columnstore-tabell.

När du kör frågor mot en columnstore-tabell körs frågorna snabbare om du bara väljer de kolumner som du behöver.  

Se även [Table indexes][Table indexes] (Tabellindex), [Columnstore indexes guide][Columnstore indexes guide] (Guide för columnstore-index), [Rebuilding columnstore indexes][Rebuilding columnstore indexes] (Återskapa columnstore-index)

## <a name="use-larger-resource-class-to-improve-query-performance"></a>Använda en större resursklass för att förbättra frågeprestanda
SQL Analytics använder resursgrupper som ett sätt att allokera minne till frågor.  Utanför i rutan tilldelas alla användare till den lilla resursklassen, som ger 100 MB minne per distribution.  Eftersom det alltid är 60 distributioner och varje distribution tilldelas minst 100 MB, är den totala minnesallokeringen i systemet 6 000 MB, eller strax under 6 GB.  

Vissa frågor, t.ex. stora kopplingar eller inläsningar till grupperade columnstore-tabeller, kan dra nytta av större minnesallokeringar.  Vissa frågor, t.ex. rena genomsökningar, har ingen nytta av detta.  På baksidan minskar användningen av större resursklasser samtidighet, så du kommer att vilja ta hänsyn till den här effekten innan du flyttar alla dina användare till en stor resursklass.

Se även [Resursklasser för hantering av arbetsbelastning](resource-classes-for-workload-management.md)

## <a name="use-smaller-resource-class-to-increase-concurrency"></a>Använda den mindre resursklassen för bättre prestanda vid samtidig körning
Om du märker att användarfrågor verkar ha en lång fördröjning, kan det vara så att användarna körs i större resursklasser och förbrukar många samtidighetsplatser som orsakar andra frågor att köa.  Du kan se om användarnas frågor köas genom att köra `SELECT * FROM sys.dm_pdw_waits` för att se om några rader returneras.

Se även [Resursklasser för hantering av arbetsbelastning](resource-classes-for-workload-management.md), [sys.dm_pdw_waits][sys.dm_pdw_waits]

## <a name="other-resources"></a>Andra resurser
Information om vanliga problem och lösningar finns i vår [felsökningsartikel][Troubleshooting].

Om du inte hittar det du letar efter i den här artikeln kan du prova att använda "Sök efter dokument" till vänster på den här sidan för att söka igenom alla Azure Synapse-dokument.  [Azure Synapse Forum][Azure SQL Data Warehouse MSDN Forum] är en plats där du kan ställa frågor till andra användare och till Azure Synapse-produktgruppen. 

Vi övervakar aktivt detta forum för att kontrollera att dina frågor besvaras antingen av en annan användare eller av någon av oss.  Om du föredrar att ställa dina frågor om Stack Overflow har vi också ett [Azure Synapse Stack Overflow Forum][Azure SQL Data Warehouse Stack Overflow Forum].

Slutligen använder du sidan [Azure Synapse Feedback][Azure SQL Data Warehouse Feedback] för att göra funktionsförfrågningar.  Genom att skicka in dina egna önskemål eller rösta fram andras förfrågningar hjälper du oss att prioritera funktioner.

<!--Image references-->

<!--Article references-->
[Create a support ticket]:sql-data-warehouse-get-started-create-support-ticket.md
[Create table as select (CTAS)]:sql-data-warehouse-develop-ctas.md
[Table overview]:sql-data-warehouse-tables-overview.md
[Table data types]:sql-data-warehouse-tables-data-types.md
[Table distribution]:sql-data-warehouse-tables-distribute.md
[Table indexes]:sql-data-warehouse-tables-index.md
[Causes of poor columnstore index quality]:sql-data-warehouse-tables-index.md#causes-of-poor-columnstore-index-quality
[Rebuilding columnstore indexes]:sql-data-warehouse-tables-index.md#rebuilding-indexes-to-improve-segment-quality
[Table partitioning]:sql-data-warehouse-tables-partition.md
[Manage table statistics]:sql-data-warehouse-tables-statistics.md
[Temporary tables]:sql-data-warehouse-tables-temporary.md
[Guide for using PolyBase]:guidance-for-loading-data.md
[Load data]:design-elt-data-loading.md
[Move data with Azure Data Factory]: ../../data-factory/transform-data-using-machine-learning.md
[Load data with Azure Data Factory]: ../../data-factory/load-azure-sql-data-warehouse.md
[Load data with bcp]: /sql/tools/bcp-utility
[Load data with PolyBase]:load-data-wideworldimportersdw.md
[Monitor your workload using DMVs]:sql-data-warehouse-manage-monitor.md
[Pause compute resources]: ../../sql-data-warehouse/sql-data-warehouse-manage-compute-overview.md#pause-compute-bk
[Resume compute resources]: ../../sql-data-warehouse/sql-data-warehouse-manage-compute-overview.md#resume-compute-bk
[Scale compute resources]: ../../sql-data-warehouse/sql-data-warehouse-manage-compute-overview.md#scale-compute
[Understanding transactions]:sql-data-warehouse-develop-transactions.md
[Optimizing transactions]:sql-data-warehouse-develop-best-practices-transactions.md
[Troubleshooting]:sql-data-warehouse-troubleshoot.md
[LABEL]:sql-data-warehouse-develop-label.md

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
[Selecting table distribution]: https://blogs.msdn.microsoft.com/sqlcat/20../../choosing-hash-distributed-table-vs-round-robin-distributed-table-in-azure-sql-dw-service/
[Azure SQL Data Warehouse Feedback]: https://feedback.azure.com/forums/307516-sql-data-warehouse
[Azure SQL Data Warehouse MSDN Forum]: https://social.msdn.microsoft.com/Forums/sqlserver/home?forum=AzureSQLDataWarehouse
[Azure SQL Data Warehouse Stack Overflow Forum]:  https://stackoverflow.com/questions/tagged/azure-sqldw
[Azure SQL Data Warehouse loading patterns and strategies]: https://blogs.msdn.microsoft.com/sqlcat/20../../azure-sql-data-warehouse-loading-patterns-and-strategies/
