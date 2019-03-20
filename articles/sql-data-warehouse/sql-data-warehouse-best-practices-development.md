---
title: Utveckling Metodtips för Azure SQL Data Warehouse | Microsoft Docs
description: Rekommendationer och metodtips som du bör känna till när du utvecklar lösningar för Azure SQL Data Warehouse.
services: sql-data-warehouse
author: ronortloff
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: implement
ms.date: 09/04/2018
ms.author: rortloff
ms.reviewer: igorstan
ms.openlocfilehash: 1682a26ba713db564484e8984010e9c12ce9d79e
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2019
ms.locfileid: "57838518"
---
# <a name="development-best-practices-for-azure-sql-data-warehouse"></a>Utveckling Metodtips för Azure SQL Data Warehouse
Den här artikeln beskriver riktlinjer och bästa praxis när du utvecklar din lösning för informationslager. 

## <a name="reduce-cost-with-pause-and-scale"></a>Minska kostnaderna genom att pausa och skala
Läs mer om att minska kostnaderna genom att pausa och skala i [Hantera beräkning](sql-data-warehouse-manage-compute-overview.md). 


## <a name="maintain-statistics"></a>Underhålla statistik
Kontrollera att du uppdaterar statistiken varje dag eller efter varje belastning.  Det finns alltid kompromisser mellan prestanda och kostnaden för att skapa och uppdatera statistik. Om du tycker att det tar för lång tid att underhålla all statistik kanske du vill prova att vara mer selektiv när du väljer vilka kolumner som ska innehålla statistik eller vilka kolumner som ska uppdateras regelbundet.  Du kanske till exempel vill uppdatera datumkolumner, där nya värden kan läggas till, varje dag. **Du får ut mest genom att använda statistik för kolumner som ingår i kopplingar, kolumner som används i WHERE-satsen och kolumner som finns i GROUP BY.**

Se även [Manage table statistics][Manage table statistics] (Hantera tabellstatistik), [CREATE STATISTICS][CREATE STATISTICS] och [UPDATE STATISTICS][UPDATE STATISTICS]

## <a name="hash-distribute-large-tables"></a>Hash-distribuera stora tabeller
Tabeller distribueras som standard med resursallokering (Round Robin).  Detta gör det enkelt för användarna att börja skapa tabeller utan att de behöver bestämma hur tabellerna ska distribueras.  Resursallokeringstabeller kan prestera bra för vissa arbetsbelastningar, men i de flesta fall blir prestanda bättre om du väljer en distributionskolumn.  Det vanligaste exemplet på när en tabell som distribueras med en kolumn presterar avsevärt mycket bättre än en resursallokeringstabell är när två stora faktatabeller kopplas.  Om du till exempel har en ordertabell, som distribueras efter order_id, och en transaktionstabell, som också distribueras efter order_id, och du kopplar ordertabellen till transaktionstabellen baserat på order_id, så blir den här frågan en direktfråga, vilket innebär att vi eliminerar dataflyttningsåtgärder.  Färre steg innebär en snabbare fråga.  Mindre dataflyttning gör också att frågor körs snabbare.  Den här förklaringen scratches bara på ytan. När du läser in en distribuerad tabell ser du till att inkommande data inte sorteras baserat på distributionsnyckeln eftersom det gör att inläsningarna tar längre tid.  På länkarna nedan hittar du mycket mer information om hur du kan förbättra prestanda genom att välja en distributionskolumn, samt information om hur du definierar en distribuerad tabell i WITH-satsen för CREATE TABLES-instruktionen.

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

## <a name="optimize-clustered-columnstore-tables"></a>Optimera grupperade columnstore-tabeller
Grupperade columnstore-index är ett av de mest effektiva sätten att lagra data i SQL Data Warehouse.  Som standard skapas tabeller i SQL Data Warehouse som grupperade ColumnStore-tabeller.  Det är viktigt att segmentkvaliteten är bra för att uppnå bästa prestanda med frågor mot columnstore-tabeller.  När rader skrivs till columnstore-tabeller när minnet är hårt belastat, kan columnstore-segmentens kvalitet påverkas.  Segmentkvaliteten kan mätas utifrån antalet rader i en komprimerad radgrupp.  Stegvisa anvisningar för hur du identifierar och förbättrar segmentkvaliteten för grupperade columnstore-tabeller finns i avsnittet [Causes of poor columnstore index quality][Causes of poor columnstore index quality] (Orsaker till låg columnstore-indexkvalitet) i artikeln [Table indexes][Table indexes] (Tabellindex).  Eftersom högkvalitativa columnstore-segmenten är viktiga, är det en bra idé att använda användare ID: N som finns i den medelstora eller stora resursklassen för inläsning av data. Använder lägre [data informationslagerenheter](what-is-a-data-warehouse-unit-dwu-cdwu.md) innebär att du vill tilldela en större resursklass till användaren som läser in.

Eftersom columnstore-tabeller normalt inte skickar data till ett komprimerat columnstore-segment förrän det finns fler än 1 miljon rader per tabell och varje SQL Data Warehouse-tabell har partitionerats i 60 tabeller, så är tumregeln att columnstore-tabeller inte har nytta av en fråga om tabellen innehåller färre än 60 miljoner rader.  För tabeller med färre än 60 miljoner rader kanske det inte tjänar något till att ha ett columnstore-index.  Men det skadar inte heller.  Om du partitionerar data bör du dessutom tänka på att varje partition måste innehålla 1 miljon rader för att kunna dra nytta av ett grupperat columnstore-index.  Om en tabell har 100 partitioner måste den innehålla minst 6 miljarder rader för att ha nytta av ett arkiv med grupperade kolumner (60 distributioner * 100 partitioner * 1 miljoner rader).  Om din tabell inte innehåller 6 miljarder rader i det här exemplet minskar du antalet partitioner eller överväger att använda en heap-tabell i stället.  Det kan också vara värt att experimentera och se om du kan förbättra prestanda med en heap-tabell med sekundära index i stället för en columnstore-tabell.

När du kör frågor mot en columnstore-tabell körs frågorna snabbare om du bara väljer de kolumner som du behöver.  

Se även [Table indexes][Table indexes] (Tabellindex), [Columnstore indexes guide][Columnstore indexes guide] (Guide för columnstore-index), [Rebuilding columnstore indexes][Rebuilding columnstore indexes] (Återskapa columnstore-index)

## <a name="next-steps"></a>Nästa steg
Om du inte hittar det du letar efter i den här artikeln provar du att använda ”Sök efter dokument” till vänster på den här sidan för att söka igenom alla Azure SQL Data Warehouse-dokument.  Den [Forum för Azure SQL Data Warehouse] [ Azure SQL Data Warehouse MSDN Forum] är en plats där du kan ställa frågor till andra användare och till SQL Data Warehouse-produktgruppen.  Vi övervakar aktivt detta forum för att kontrollera att dina frågor besvaras antingen av en annan användare eller av någon av oss.  Om du föredrar att ställa dina frågor i Stack Overflow finns det även ett [Stack Overflow-forum för Azure SQL Data Warehouse][Azure SQL Data Warehouse Stack Overflow Forum].

Avslutningsvis ber vi dig att använda sidan för [Azure SQL Data Warehouse-feedback][Azure SQL Data Warehouse Feedback] om du har önskemål om nya funktioner.  Genom att skicka in dina egna önskemål eller rösta fram andras förfrågningar hjälper du oss att prioritera funktioner.

<!--Image references-->

<!--Article references-->
[Create a support ticket]: ./sql-data-warehouse-get-started-create-support-ticket.md
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
[Guide for using PolyBase]: ./guidance-for-loading-data.md
[Load data]: ./design-elt-data-loading.md
[Move data with Azure Data Factory]: ../data-factory/transform-data-using-machine-learning.md
[Load data with Azure Data Factory]: ../data-factory/load-azure-sql-data-warehouse.md
[Load data with bcp]: /sql/tools/bcp-utility
[Load data with PolyBase]: ./load-data-wideworldimportersdw.md
[Monitor your workload using DMVs]: ./sql-data-warehouse-manage-monitor.md
[Pause compute resources]: ./sql-data-warehouse-manage-compute-overview.md#pause-compute-bk
[Resume compute resources]: ./sql-data-warehouse-manage-compute-overview.md#resume-compute-bk
[Scale compute resources]: ./sql-data-warehouse-manage-compute-overview.md#scale-compute
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
[Selecting table distribution]: https://blogs.msdn.microsoft.com/sqlcat/20../../choosing-hash-distributed-table-vs-round-robin-distributed-table-in-azure-sql-dw-service/
[Azure SQL Data Warehouse Feedback]: https://feedback.azure.com/forums/307516-sql-data-warehouse
[Azure SQL Data Warehouse MSDN Forum]: https://social.msdn.microsoft.com/Forums/sqlserver/home?forum=AzureSQLDataWarehouse
[Azure SQL Data Warehouse Stack Overflow Forum]:  https://stackoverflow.com/questions/tagged/azure-sqldw
[Azure SQL Data Warehouse loading patterns and strategies]: https://blogs.msdn.microsoft.com/sqlcat/20../../azure-sql-data-warehouse-loading-patterns-and-strategies/
