---
title: Metodtips för SQL-pooler
description: Rekommendationer och metodtips som du bör känna till när du arbetar med SQL-pooler.
services: synapse-analytics
author: mlee3gsd
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 04/15/2020
ms.author: martinle
ms.reviewer: igorstan
ms.openlocfilehash: b3dc111fe62cbae857f3369165ba29cf40e90342
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2020
ms.locfileid: "81427801"
---
# <a name="best-practices-for-sql-pools-in-azure-synapse-analytics"></a>Metodtips för SQL-pooler i Azure Synapse Analytics

Den här artikeln innehåller en samling metodtips som hjälper dig att uppnå optimal prestanda för SQL-pooler i Azure Synapse Analytics. Nedan hittar du grundläggande vägledning och viktiga områden att fokusera på när du bygger din lösning. Varje avsnitt introducerar dig till ett koncept och sedan pekar dig till mer detaljerade artiklar som täcker konceptet på djupet.

## <a name="sql-pools-loading"></a>Sql-pooler laddas

Vägledning för SQL-pooler finns i [Vägledning för inläsning av data](data-loading-best-practices.md).

## <a name="reduce-cost-with-pause-and-scale"></a>Minska kostnaderna genom att pausa och skala

Mer information om hur du minskar kostnaderna genom pausning och skalning finns i [Hantera beräkning](../sql-data-warehouse/sql-data-warehouse-manage-compute-overview.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json).

## <a name="maintain-statistics"></a>Underhålla statistik

Sql Server identifierar och skapar eller uppdaterar statistik om kolumner automatiskt, men SQL-pooler kräver manuellt underhåll av statistik. Du bör underhålla din statistik för att säkerställa att SQL-poolplanerna är optimerade.  Kvaliteten på planerna som skapas av optimeraren beror på den tillgängliga statistiken.

> [!TIP]
> Ett enkelt sätt att komma igång med statistik är att skapa exempelstatistik för varje kolumn.  

Det är lika viktigt att uppdatera statistik när viktiga dataändringar görs.  En konservativ metod kan vara att uppdatera statistiken varje dag eller efter varje belastning.  Det finns alltid kompromisser mellan prestanda och kostnaden för att skapa och uppdatera statistik.

Om du vill förkorta underhållstiden för statistik bör du vara selektiv om vilka kolumner som har statistik eller behöver den vanligaste uppdateringen. Du kanske till exempel vill uppdatera datumkolumner där nya värden kan läggas till dagligen. Fokusera på att ha statistik för kolumner som ingår i kopplingar, kolumner som används i WHERE-satsen och kolumner som finns i GROUP BY.

Ytterligare information om statistik finns i artiklarna [Hantera tabellstatistik,](develop-tables-statistics.md) [SKAPA STATISTIK](/sql/t-sql/statements/create-statistics-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)och [UPPDATERA STATISTIK.](/sql/t-sql/statements/update-statistics-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)

## <a name="group-insert-statements-into-batches"></a>Gruppera INSERT-satser i batchar

En engångsbelastning till en liten tabell med `INSERT INTO MyLookup VALUES (1, 'Type 1')`en INSERT-sats som kan vara den bästa metoden beroende på dina behov. Men om du behöver ladda tusentals eller miljontals rader under hela dagen, är det troligt att singleton skär inte är optimala.

Ett sätt att lösa problemet är att utveckla en process som skriver till en fil och sedan en annan process för att regelbundet läsa in filen. Mer information finns i [INSERT-artikeln.](/sql/t-sql/statements/insert-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)

## <a name="use-polybase-to-load-and-export-data-quickly"></a>Använda PolyBase för att snabbt läsa in och exportera data

SQL-pool stöder inläsning och export av data via flera verktyg, inklusive Azure Data Factory, PolyBase och BCP.  För små datamängder där prestanda inte är viktigt räcker alla verktygen för dina behov.  

> [!NOTE]
> Polybase är det bästa valet när du läser in eller exporterar stora mängder data, eller om du behöver snabbare prestanda.

PolyBase-inläsningar kan utföras med hjälp av CTAS eller INSERT INTO. CTAS minimerar transaktionsloggningen och är det snabbaste sättet att läsa in dina data. Azure Data Factory stöder också PolyBase-belastningar och kan uppnå prestanda som liknar CTAS. PolyBase stöder olika filformat, inklusive Gzip-filer.

För att maximera dataflödet när du använder Gzip-textfiler, dela upp filer i 60 eller fler filer för att maximera parallellismen i din belastning. För snabbare totalt genomflöde bör du överväga att använda samtidig inläsning av data. Ytterligare information om de ämnen som är relevanta för det här avsnittet ingår i följande artiklar:

- [Läsa in data](data-loading-overview.md)
- [Guide for using PolyBase](data-loading-best-practices.md) (Guide för att använda PolyBase)
- [Azure SQL pool inläsning mönster och strategier](https://blogs.msdn.microsoft.com/sqlcat/20../../azure-sql-data-warehouse-loading-patterns-and-strategies/)
- [Läs in data med Azure Data Factory](../../data-factory/load-azure-sql-data-warehouse.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)
- [Flytta data med Azure Data Factory](../../data-factory/transform-data-using-machine-learning.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)
- [CREATE EXTERNAL FILE FORMAT](/sql/t-sql/statements/create-external-file-format-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)
- [Create table as select (CTAS)](../sql-data-warehouse/sql-data-warehouse-develop-ctas.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)

## <a name="load-then-query-external-tables"></a>Läsa in och sedan fråga externa tabeller

Polybase är inte optimalt för frågor. Polybase-tabeller för SQL-pooler stöder för närvarande endast Azure blob-filer och Azure Data Lake-lagring. Dessa filer har inga beräkningsresurser som stöder dem. Därför kan SQL-pooler inte avlasta det här arbetet och måste läsa hela filen genom att läsa in den till tempdb så att den kan läsa data.

Om du har flera frågor för att fråga dessa data är det bättre att läsa in dessa data en gång och låta frågor använda den lokala tabellen. Ytterligare Polybase vägledning ingår i [guiden för att använda PolyBase](data-loading-best-practices.md) artikeln.

## <a name="hash-distribute-large-tables"></a>Hash-distribuera stora tabeller

Tabeller distribueras som standard med resursallokering (Round Robin).   Den här standardinställningen gör det enkelt för användare att börja skapa tabeller utan att behöva bestämma hur deras tabeller ska fördelas. Round Robin-tabeller kan fungera tillräckligt för vissa arbetsbelastningar. Men i de flesta fall ger en distributionskolumn bättre prestanda.  

Det vanligaste exemplet på en tabell som distribueras av en kolumn som är bättre än en round robin-tabell är när två stora faktatabeller är kopplade.  

Om du till exempel har en ordertabell distribuerad av order_id och en transaktionstabell som också distribueras av order_id, när du ansluter ordertabellen till transaktionstabellen på order_id, blir den här frågan en genomströmningsfråga. Dataflyttningsoperationer elimineras sedan. Färre steg innebär en snabbare fråga. Mindre dataflyttning gör också att frågor körs snabbare.

> [!NOTE]
> När du läser in en distribuerad tabell ska inkommande data inte sorteras på distributionsnyckeln. Om du gör det kommer att sakta ner dina laster.

Artikeln länkar nedan kommer att ge dig ytterligare information om hur du förbättrar prestanda via att välja en distribution kolumn. Du hittar också information om hur du definierar en distribuerad tabell i WITH-satsen i CREATE TABLE-satsen:

- [Översikt över tabell](develop-tables-overview.md)
- [Table distribution](../sql-data-warehouse/sql-data-warehouse-tables-distribute.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) (Tabelldistribution)
- [Selecting table distribution](https://blogs.msdn.microsoft.com/sqlcat/20../../choosing-hash-distributed-table-vs-round-robin-distributed-table-in-azure-sql-dw-service/) (Välja tabelldistribution)
- [CREATE TABLE](/sql/t-sql/statements/create-table-azure-sql-data-warehouse?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)
- [CREATE TABLE AS SELECT](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)

## <a name="do-not-over-partition"></a>Överpartitionera inte

Medan partitionering data kan vara effektivt för att underhålla dina data genom partitionsväxling eller optimera genomsökningar med partition eliminering, med för många partitioner kan sakta ner dina frågor.  Ofta en hög granularitet partitionering strategi som kan fungera bra på SQL Server kanske inte fungerar bra på SQL Pool.  

Om du har för många partitioner kan du minska effektiviteten hos klustrade columnstore-index om varje partition har färre än 1 miljon rader. SQL-pooler partitionerar automatiskt dina data i 60 databaser. Så om du skapar en tabell med 100 partitioner blir resultatet 6000 partitioner. Varje arbetsbelastning är olika, så det bästa rådet är att experimentera med partitionering för att se vad som fungerar bäst för din arbetsbelastning.  

Ett alternativ att tänka på är att använda en granularitet som är lägre än vad du har implementerat med SQL Server. Överväg till exempel att använda veckovisa eller månatliga partitioner i stället för dagliga partitioner.

Mer information om partitionering finns i artikeln [Tabellpartitionering.](../sql-data-warehouse/sql-data-warehouse-tables-partition.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)

## <a name="minimize-transaction-sizes"></a>Minimera transaktionsstorlekar

INFOGA,UPPDATERA och TA BORT-satser körs i en transaktion. När de misslyckas måste de rullas tillbaka. För att minska risken för en lång återställning, minimera transaktionsstorlekar när det är möjligt.  Minimera transaktionsstorlekar kan göras genom att dela infoga, UPPDATERA och TA bort satser i delar. Om du till exempel har en INFOGA som du förväntar dig att ta 1 timme kan du dela upp INFOGA i fyra delar. Varje körning förkortas sedan till 15 minuter.  

> [!TIP]
> Utnyttja särskilda minimala loggningsärenden, till exempel CTAS, TRUNKATE, DROP TABLE eller INSERT till tomma tabeller för att minska återställningsrisken.  

Ett annat sätt att eliminera återställningar är att använda åtgärder med endast metadata, t.ex. med partitionsväxling, för datahantering.  I stället för att till exempel köra en DELETE-sats för att ta bort alla rader i en tabell där order_date var i oktober 2001 kan du partitionera dina data varje månad. Sedan kan du växla ut partitionen med data för en tom partition från en annan tabell (se ALTER TABLE exempel).  

För opartitionerade tabeller bör du överväga att använda en CTAS för att skriva de data som du vill behålla i en tabell i stället för att använda DELETE.  Om ett CTAS tar lika lång tid är det mycket säkrare att köra eftersom den har minimal transaktionsloggning och kan avbrytas snabbt om det behövs.

Ytterligare information om innehåll relaterat till detta avsnitt finns i artiklarna nedan:

- [Create table as select (CTAS)](../sql-data-warehouse/sql-data-warehouse-develop-ctas.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)
- [Förstå transaktioner](develop-transactions.md)
- [Optimizing transactions](../sql-data-warehouse/sql-data-warehouse-develop-best-practices-transactions.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) (Optimera transaktioner)
- [Tabellpartitionering](../sql-data-warehouse/sql-data-warehouse-tables-partition.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)
- [TRUNKERA TABELL](/sql/t-sql/statements/truncate-table-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)
- [ALTER TABLE](/sql/t-sql/statements/alter-table-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)

## <a name="reduce-query-result-sizes"></a>Minska frågeresultatstorlekar

Genom att minska frågeresultatstorlekarna kan du undvika problem på klientsidan som orsakas av stora frågeresultat.  Du kan redigera frågan för att minska antalet returnerade rader. Med vissa frågegenereringsverktyg kan du lägga till syntaxen "topp N" i varje fråga.  Du kan också CETAS frågeresultatet till en temporär tabell och sedan använda PolyBase-export för bearbetning på nednivå.

## <a name="use-the-smallest-possible-column-size"></a>Använda minsta möjliga kolumnstorlek

När du definierar din DDL, använd den minsta datatyp som stöder dina data som gör det kommer att förbättra frågeprestanda.  Denna rekommendation är särskilt viktig för KOLUMNERNA CHAR och VARCHAR.  Om det längsta värdet i en kolumn är 25 tecken definierar du kolumnen som VARCHAR(25).  Undvik att definiera alla teckenkolumner med en stor standardlängd.  Dessutom definiera kolumner som VARCHAR när det är allt som behövs i stället för att använda NVARCHAR.

Se artiklarna [Tabellöversikt,](develop-tables-overview.md) [Tabelldatatyper](develop-tables-data-types.md)och [SKAPA TABELL](/sql/t-sql/statements/create-table-azure-sql-data-warehouse?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) för en mer detaljerad genomgång av viktiga begrepp som är relevanta för ovanstående information.

## <a name="use-temporary-heap-tables-for-transient-data"></a>Använda tillfälliga heap-tabeller för tillfälliga data

När du tillfälligt landar data på SQL-pooler gör heap-tabeller i allmänhet den övergripande processen snabbare.  Om du läser in data bara för att iscensätta dem innan du kör fler omvandlingar går det snabbare att läsa in tabellen i en heap-tabell än att läsa in data i en grupperad columnstore-tabell.  

Om du läser in data i en temporär tabell läses också in mycket snabbare än att läsa in en tabell till permanent lagring.  Temporära tabeller börjar med ett "#" och är endast tillgängliga för den session som skapade den. Följaktligen får de endast arbeta i begränsade scenarier. Heap-tabeller definieras i WITH-satsen i en CREATE TABLE-instruktion.  Om du använder en temporär tabell måste du också komma ihåg att skapa statistik för den temporära tabellen.

Mer information finns i artiklarna [Temporära tabeller,](/sql/t-sql/statements/alter-table-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) [SKAPA TABELL](/sql/t-sql/statements/create-table-azure-sql-data-warehouse?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)och SKAPA TABELL [SOM SELECT.](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)

## <a name="optimize-clustered-columnstore-tables"></a>Optimera grupperade columnstore-tabeller

Klustrade columnstore-index är ett av de mest effektiva sätten att lagra data i SQL-poolen.  Som standard skapas tabeller i SQL-poolen som Clustered ColumnStore.  Det är viktigt att segmentkvaliteten är bra för att uppnå bästa prestanda med frågor mot columnstore-tabeller.  När rader skrivs till columnstore-tabeller när minnet är hårt belastat, kan columnstore-segmentens kvalitet påverkas.  

Segmentkvaliteten kan mätas med antalet rader i en komprimerad radgrupp. Se [orsakerna till dålig columnstore-indexkvalitet](../sql-data-warehouse/sql-data-warehouse-tables-index.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json#causes-of-poor-columnstore-index-quality) i artikeln [Tabellindex](../sql-data-warehouse/sql-data-warehouse-tables-index.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) för steg-för-steg-instruktioner om hur du identifierar och förbättrar segmentkvaliteten för klustrade columnstore-tabeller.  

Eftersom högkvalitativa columnstore-segment är viktiga är det en bra idé att använda användar-ID:er som tillhör den medelstora eller stora resursklassen för inläsning av data. Om du använder lägre [informationslagerenheter](resource-consumption-models.md) innebär du att du vill tilldela en större resursklass till din inläsningsanvändare.

Columnstore-tabeller skickar i allmänhet inte data till ett komprimerat columnstore-segment förrän det finns mer än 1 miljon rader per tabell. Varje SQL-biljardtabell är uppdelad i 60 tabeller. Därför kommer columnstore-tabeller inte att gynna en fråga om inte tabellen har mer än 60 miljoner rader.  

> [!TIP]
> För tabeller med mindre än 60 miljoner rader kan det hända att ett columnstore-index inte är den optimala lösningen.  

Om du partitionerar dina data måste varje partition ha 1 miljon rader för att dra nytta av ett grupperat columnstore-index.  För en tabell med 100 partitioner måste den ha minst 6 miljarder rader för att dra nytta av ett klusterkolumnerarklagre (60 distributioner *100 partitioner* 1 miljon rader).  

Om tabellen inte har 6 miljarder rader har du två huvudalternativ. Antingen minska antalet partitioner eller överväga att använda en heap-tabell i stället.  Det kan också vara värt att experimentera för att se om bättre prestanda kan vinnas genom att använda en heap tabell med sekundära index snarare än en columnstore tabell.

När du kör frågor mot en columnstore-tabell körs frågorna snabbare om du bara väljer de kolumner som du behöver.  Mer information om tabell- och columnstore-index finns i artiklarna [Tabellindex](../sql-data-warehouse/sql-data-warehouse-tables-index.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json), [Columnstore indexes](/sql/relational-databases/indexes/columnstore-indexes-overview?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)och [Rebuilding columnstore indexes.](../sql-data-warehouse/sql-data-warehouse-tables-index.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest#rebuilding-indexes-to-improve-segment-quality)

## <a name="use-larger-resource-class-to-improve-query-performance"></a>Använda en större resursklass för att förbättra frågeprestanda

SQL-pooler använder resursgrupper som ett sätt att allokera minne till frågor. Inledningsvis tilldelas alla användare till den lilla resursklassen, som ger 100 MB minne per distribution.  Det finns alltid 60 fördelningar. Varje distribution ges minst 100 MB. Den totala systemomfattande minnesallokeringen är 6 000 MB eller strax under 6 GB.  

Vissa frågor, t.ex. stora kopplingar eller inläsningar till grupperade columnstore-tabeller, kan dra nytta av större minnesallokeringar.  Vissa frågor, till exempel rena genomsökningar, ser ingen fördel. Om du använder större resursklasser påverkar samtidighet. Så du vill ha dessa fakta i åtanke innan du flyttar alla dina användare till en stor resursklass.

Mer information om resursklasser finns i artikeln [Resursklasser för arbetsbelastningshantering.](../sql-data-warehouse/resource-classes-for-workload-management.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)

## <a name="use-smaller-resource-class-to-increase-concurrency"></a>Använd mindre resursklass för att öka samtidigheten

Om du märker en lång fördröjning i användarfrågor kan användarna köras i större resursklasser. Det här scenariot främjar förbrukningen av samtidighetsplatser, vilket kan orsaka att andra frågor köar.  Om du vill ta reda på `SELECT * FROM sys.dm_pdw_waits` om användarnas frågor är i kö kör du för att se om några rader returneras.

[Resursklasserna för arbetsbelastningshantering](../sql-data-warehouse/resource-classes-for-workload-management.md) och [sys.dm_pdw_waits](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-waits-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) artiklar ger dig mer information.

## <a name="use-dmvs-to-monitor-and-optimize-your-queries"></a>Använda DMV:er för att övervaka och optimera frågor

SQL-pooler har flera DMVs som kan användas för att övervaka körning av frågor.  Övervakningsartikeln nedan går igenom steg-för-steg-instruktioner om hur du visar information om en körningsfråga.  För att snabbt hitta frågor i dessa DMV:er kan det vara bra att använda alternativet LABEL med dina frågor. För ytterligare detaljerad information, se de artiklar som ingår i listan nedan:

- [Övervaka din arbetsbelastning med DMV:er](../sql-data-warehouse/sql-data-warehouse-manage-monitor.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)

- [Etikett](develop-label.md)
- [OPTION](/sql/t-sql/queries/option-clause-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)
- [sys.dm_exec_sessions](/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-sessions-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)
- [sys.dm_pdw_exec_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-exec-requests-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)
- [sys.dm_pdw_request_steps](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-request-steps-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)
- [sys.dm_pdw_sql_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-sql-requests-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)
- [sys.dm_pdw_dms_workers](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-dms-workers-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)
- [DBCC PDW_SHOWEXECUTIONPLAN](/sql/t-sql/database-console-commands/dbcc-pdw-showexecutionplan-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)
- [sys.dm_pdw_waits](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-waits-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)

## <a name="next-steps"></a>Nästa steg

Se även [felsökningsartikeln](../sql-data-warehouse/sql-data-warehouse-troubleshoot.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) för vanliga problem och lösningar.

Om du behöver information som inte finns i den här artikeln använder du "Sök efter dokument" till vänster på den här sidan för att söka i alla SQL-pooldokument.  [SQL pool Forum](https://social.msdn.microsoft.com/Forums/sqlserver/home?forum=AzureSQLDataWarehouse) är en plats där du kan ställa frågor till andra användare och till SQL pool Product Group.  

Vi övervakar aktivt detta forum för att kontrollera att dina frågor besvaras antingen av en annan användare eller av någon av oss.  Om du föredrar att ställa dina frågor om Stack Overflow har vi också ett [Azure SQL-poolstackspillforum](https://stackoverflow.com/questions/tagged/azure-sqldw).

Använd sidan feedback för [Azure SQL-pool](https://feedback.azure.com/forums/307516-sql-data-warehouse) för funktionsbegäranden.  Genom att lägga till dina förfrågningar eller rösta upp andra förfrågningar kan vi fokusera på de mest efterfrågade funktionerna.
