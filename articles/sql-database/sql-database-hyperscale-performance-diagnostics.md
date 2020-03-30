---
title: Prestandadiagnostik i hyperskala
description: I den här artikeln beskrivs felsÃ¶kning av prestandaproblem i Azure SQL Database.
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.custom: seo-lt-2019
ms.topic: troubleshooting
author: denzilribeiro
ms.author: denzilr
ms.reviewer: sstein
ms.date: 10/18/2019
ms.openlocfilehash: 26bd6ddb9d8255b8e2510133fc4b6aa645f89f68
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75615068"
---
# <a name="sql-hyperscale-performance-troubleshooting-diagnostics"></a>Felsökning av SQL-hyperskala-prestanda

För att felsöka prestandaproblem i en hyperskala databas är [allmänna prestandajusteringsmetoder](sql-database-monitor-tune-overview.md) på Azure SQL-databasberäkningsnoden utgångspunkten för en prestandaundersökning. Med tanke på den [distribuerade arkitekturen](sql-database-service-tier-hyperscale.md#distributed-functions-architecture) i Hyperscale har dock ytterligare diagnostik lagts till för att hjälpa till. I den här artikeln beskrivs hyperskalaspecifika diagnostikdata.

## <a name="log-rate-throttling-waits"></a>Logghastighetsbegränsning väntar

Varje Azure SQL Database-tjänstnivå har logggenereringshastighetsgränser som tillämpas via [loggfrekvensstyrning](sql-database-resource-limits-database-server.md#transaction-log-rate-governance). I Hyperskala är logggenereringsgränsen för närvarande inställd på 100 MB/sek, oavsett servicenivå. Det finns dock tillfällen då logggenereringshastigheten på den primära beräkningsrepliken måste begränsas för att upprätthålla återställningsbara SLA:er. Den här begränsningen inträffar när en [sidserver eller en annan beräkningsreplik](sql-database-service-tier-hyperscale.md#distributed-functions-architecture) ligger betydligt efter att nya loggposter från loggtjänsten tillämpas.

Följande väntetyper (i [sys.dm_os_wait_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-os-wait-stats-transact-sql/)) beskriver orsakerna till att logghastigheten kan begränsas på den primära beräkningsrepliken:

|Typ av väntetyp    |Beskrivning                         |
|-------------          |------------------------------------|
|RBIO_RG_STORAGE        | Inträffar när en primär beräkningsnodsgenereringshastighet för hyperskaladatabasen begränsas på grund av fördröjd loggförbrukning på sidservern(erna).         |
|RBIO_RG_DESTAGE        | Inträffar när en hyperskala databas beräkning nod logggenereringshastighet begränsas på grund av fördröjd loggförbrukning av den långsiktiga logglagring.         |
|RBIO_RG_REPLICA        | Inträffar när en hyperskala databas beräkning nod logggenereringshastighet begränsas på grund av fördröjd loggförbrukning av läsbara sekundära repliker (s).         |
|RBIO_RG_LOCALDESTAGE   | Inträffar när en hyperskala databas beräkning nod logggenereringshastighet begränsas på grund av fördröjd loggförbrukning av loggtjänsten.         |

## <a name="page-server-reads"></a>Läser sidservern

Beräkningsrepliker cachelagrar inte en fullständig kopia av databasen lokalt. De data som är lokala för beräkningsrepliken lagras i buffertpoolen (i minnet) och i den lokala RBPEX-cachen (Resilient Buffer Pool Extension) som är en partiell (icke-omfattande) cache av datasidor. Den här lokala RBPEX-cachen är dimensionerad proportionellt till beräkningsstorleken och är tre gånger så mycket minne som beräkningsnivån. RBPEX liknar buffertpoolen eftersom den har de data som används oftast. Varje sidserver, å andra sidan, har en täcker RBPEX cache för den del av databasen den upprätthåller.
 
När en läsning utfärdas på en beräkningsreplik, om data inte finns i buffertpoolen eller den lokala RBPEX-cachen, utfärdas ett getPage(pageId, LSN) funktionsanrop och sidan hämtas från motsvarande sidserver. Läsningar från sidservrar är fjärrläsningar och är därmed långsammare än läsningar från den lokala RBPEX. Vid felsökning av IO-relaterade prestandaproblem måste vi kunna se hur många IOs som gjordes via relativt långsammare fjärrsökserver.

Flera DMVs och utökade händelser har kolumner och fält som anger antalet fjärravläsningar från en sidserver, vilket kan jämföras med den totala läsningen. Frågearkivet fångar också fjärrläsningar som en del av frågekörningsstatistiken.

- Kolumner för att rapportera sidserverläsningar är tillgängliga i dmvs-program för körning och katalogvyer, till exempel:
    - [sys.dm_exec_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-requests-transact-sql/)
    - [sys.dm_exec_query_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-query-stats-transact-sql/)
    - [sys.dm_exec_procedure_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-procedure-stats-transact-sql/)
    - [sys.dm_exec_trigger_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-trigger-stats-transact-sql/)
    - [sys.query_store_runtime_stats](/sql/relational-databases/system-catalog-views/sys-query-store-runtime-stats-transact-sql/)
- Sidserverläsningar läggs till i följande utökade händelser:
    - sql_statement_completed
    - sp_statement_completed
    - sql_batch_completed
    - rpc_completed
    - scan_stopped
    - query_store_begin_persist_runtime_stat
    - fråga-store_execution_runtime_info
- ActualPageServerReads/ActualPageServerReadAheads läggs till i XML för frågeplan för faktiska planer. Ett exempel:

`<RunTimeCountersPerThread Thread="8" ActualRows="90466461" ActualRowsRead="90466461" Batches="0" ActualEndOfScans="1" ActualExecutions="1" ActualExecutionMode="Row" ActualElapsedms="133645" ActualCPUms="85105" ActualScans="1" ActualLogicalReads="6032256" ActualPhysicalReads="0" ActualPageServerReads="0" ActualReadAheads="6027814" ActualPageServerReadAheads="5687297" ActualLobLogicalReads="0" ActualLobPhysicalReads="0" ActualLobPageServerReads="0" ActualLobReadAheads="0" ActualLobPageServerReadAheads="0" />`

> [!NOTE]
> Om du vill visa dessa attribut i frågeplanseegenskaperna krävs SSMS 18.3 eller senare.

## <a name="virtual-file-stats-and-io-accounting"></a>Virtuell fil statistik och IO redovisning

I Azure SQL Database är [sys.dm_io_virtual_file_stats()](/sql/relational-databases/system-dynamic-management-views/sys-dm-io-virtual-file-stats-transact-sql/) DMF det primära sättet att övervaka SQL Server IO. IO-egenskaper i Hyperscale är olika på grund av dess [distribuerade arkitektur](sql-database-service-tier-hyperscale.md#distributed-functions-architecture). I det här avsnittet fokuserar vi på IO (läsningar och skrivningar) till datafiler som kan ses i denna DMF. I Hyperskala motsvarar varje datafil som är synlig i den här DMF en fjärrsidesserver. RBPEX-cachen som nämns här är en lokal SSD-baserad cache, som är en cache som inte täcker på beräkningsrepliken.

### <a name="local-rbpex-cache-usage"></a>Lokal RBPEX-cacheanvändning

Lokal RBPEX-cache finns på beräkningsrepliken, på lokal SSD-lagring. Därför är IO mot den här cachen snabbare än IO mot fjärrsidesservrar. [Sys.dm_io_virtual_file_stats()](/sql/relational-databases/system-dynamic-management-views/sys-dm-io-virtual-file-stats-transact-sql/) i en hyperskaladatabas har för närvarande en särskild rad som rapporterar IO mot den lokala RBPEX-cachen på beräkningsrepliken. Den här raden har värdet `database_id` 0 för båda och `file_id` kolumner. Frågan nedan returnerar till exempel RBPEX-användningsstatistik sedan databasen startades.

`select * from sys.dm_io_virtual_file_stats(0,NULL);`

Ett förhållande av läsningar som görs på RBPEX till aggregerade läsningar som görs på alla andra datafiler ger RBPEX-cachehitförhållande.

### <a name="data-reads"></a>Data läser

- När läsningar utfärdas av SQL Server-motorn på en beräkningsreplik kan de betjänas antingen av den lokala RBPEX-cachen eller av fjärrsidesservrar, eller av en kombination av de två om de läser flera sidor.
- När beräkningsrepliken läser vissa sidor från en viss fil, till exempel file_id 1, om dessa data finns enbart på den lokala RBPEX-cachen, redovisas all I/o för den här läsningen mot file_id 0 (RBPEX). Om någon del av dessa data finns i den lokala RBPEX-cachen, och en del finns på en fjärrsidesserver, redovisas IO mot file_id 0 för den del som betjänas från RBPEX, och den del som betjänas från fjärrsidesservern redovisas till file_id 1. 
- När en beräkningsreplik begär en sida på en viss [LSN](/sql/relational-databases/sql-server-transaction-log-architecture-and-management-guide/) från en sidserver, om sidservern inte har hunnit ikapp det LSN som begärts, väntar läsningen på beräkningsrepliken tills sidservern hinner ikapp innan sidan returneras till beräkningsrepliken. För alla läsningar från en sidserver på beräkningsrepliken visas PAGEIOLATCH_* vänta-typen om den väntar på den IO:an. I Hyperscale innehåller den här väntetiden både tid att komma ikapp den begärda sidan på sidservern till det LSN som krävs och den tid som krävs för att överföra sidan från sidservern till beräkningsrepliken.
- Stora läsningar som read-ahead görs ofta med ["Scatter-Gather" Läser](/sql/relational-databases/reading-pages/). Detta gör att läsningar på upp till 4 MB sidor åt gången, betraktas som en enda läsning i SQL Server-motorn. Men när data som läses finns i RBPEX, dessa läsningar redovisas som flera enskilda 8 KB läser, eftersom buffertpoolen och RBPEX alltid använder 8 KB sidor. Som ett resultat kan antalet lästa IOs som ses mot RBPEX vara större än det faktiska antalet IOs som utförs av motorn.

### <a name="data-writes"></a>Data skriver

- Den primära beräkningsrepliken skriver inte direkt till sidservrar. I stället spelas loggposter från loggtjänsten upp på motsvarande sidservrar. 
- Skrivningar som sker på beräkningsrepliken skrivs huvudsakligen till den lokala RBPEX (file_id 0). För skrivningar på logiska filer som är större än 8 KB, med andra ord de som görs med [Gather-write,](/sql/relational-databases/writing-pages/)varje skrivåtgärd översätts till flera 8 KB enskilda skrivningar till RBPEX eftersom buffertpoolen och RBPEX alltid använder 8 KB sidor. Som ett resultat kan antalet skriv-IOs som ses mot RBPEX vara större än det faktiska antalet IOs som utförs av motorn.
- Icke-RBPEX-filer, eller andra datafiler än file_id 0 som motsvarar sidservrar, visar också skrivningar. På tjänstnivån Hyperskala simuleras dessa skrivningar, eftersom beräkningsrepliker aldrig skriver direkt till sidservrar. Skriv IOPS och dataflöde redovisas när de inträffar på beräkningsrepliken, men svarstiden för andra datafiler än file_id 0 återspeglar inte den faktiska svarstiden för sidserverskrivningar.

### <a name="log-writes"></a>Logga skriver

- På den primära beräkningen redovisas en loggskrivning i file_id 2 i sys.dm_io_virtual_file_stats. En loggskrivning på primär beräkning är en skrivning till loggen Landing Zone.
- Loggposter naas inte på den sekundära repliken vid en commit. I Hyperskala används loggen av loggtjänsten på de sekundära replikerna asynkront. Eftersom loggskrivningar faktiskt inte förekommer på sekundära repliker, är all redovisning av logg-IOs på de sekundära replikerna endast för spårningsändamål.

## <a name="data-io-in-resource-utilization-statistics"></a>Data-I/O i resursanvändningsstatistik

I en icke-hyperskala databas rapporteras kombinerade läsa och skriva IOPS mot datafiler, i förhållande till iops-gränsen för [resursstyrningsdata,](/azure/sql-database/sql-database-resource-limits-database-server#resource-governance) i [sys.dm_db_resource_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-db-resource-stats-azure-sql-database) och [sys.resource_stats](/sql/relational-databases/system-catalog-views/sys-resource-stats-azure-sql-database) vyer i `avg_data_io_percent` kolumnen. Samma värde rapporteras i portalen som _data-IO-procent_. 

I en hyperskaladatabas rapporterar den här kolumnen dataanvändning av IOPS i förhållande till gränsen för `tempdb`lokal lagring endast på beräkningsreplik, särskilt IO mot RBPEX och . Värdet på 100 % i den här kolumnen anger att resursstyrning begränsar iops för lokal lagring. Om detta är korrelerat med ett prestandaproblem, justera arbetsbelastningen för att generera mindre IO, eller öka databastjänstmål för att öka gränsen för högsta _datadata IOPS_ [för](sql-database-vcore-resource-limits-single-databases.md)resursstyrning . För resursstyrning av RBPEX-läsningar och skrivningar räknar systemet enskilda 8 KB IOs, snarare än större IOs som kan utfärdas av SQL Server-motorn. 

Data-I/O mot fjärrsidesservrar rapporteras inte i resursanvändningsvyer eller i portalen, men rapporteras i [DMF för sys.dm_io_virtual_file_stats()](/sql/relational-databases/system-dynamic-management-views/sys-dm-io-virtual-file-stats-transact-sql/) som nämnts tidigare.


## <a name="additional-resources"></a>Ytterligare resurser

- För vCore-resursgränser för en hyperskala enkel databas se [Hyperscale service tier vCore Limits](sql-database-vcore-resource-limits-single-databases.md#hyperscale---provisioned-compute---gen5)
- Prestandajustering för Azure SQL Database finns [i Frågeprestanda i Azure SQL Database](sql-database-performance-guidance.md)
- Prestandajustering med Query Store finns i [Prestandaövervakning med Frågearkivet](/sql/relational-databases/performance/monitoring-performance-by-using-the-query-store/)
- För DMV-övervakningsskript finns i [Övervaka prestanda Azure SQL Database med hjälp av dynamiska hanteringsvyer](sql-database-monitoring-with-dmvs.md)
