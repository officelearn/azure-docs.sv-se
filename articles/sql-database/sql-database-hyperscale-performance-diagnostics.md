---
title: Azure SQL Database storskalig prestanda fel sökning av diagnostik | Microsoft Docs
description: I den här artikeln beskrivs hur du felsöker storskaliga prestanda problem i SQL Database.
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.custom: ''
ms.devlang: ''
ms.topic: troubleshooting
author: denzilribeiro
ms.author: denzilr
ms.reviewer: sstein
ms.date: 10/09/2019
ms.openlocfilehash: 8c632866f942e27c4340dc83b7ef302dd4b21314
ms.sourcegitcommit: bb65043d5e49b8af94bba0e96c36796987f5a2be
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/16/2019
ms.locfileid: "72392831"
---
# <a name="sql-hyperscale-performance-troubleshooting-diagnostics"></a>SQL-storskalig prestanda vid fel sökning av diagnostik


För att felsöka prestanda problem i en storskalig databas, är [allmänna prestanda justerings metoder](sql-database-monitor-tune-overview.md) i Azure SQL Database Compute-noden Start punkten för en prestanda undersökning. Men med tanke på den [distribuerade arkitekturen](sql-database-service-tier-hyperscale.md) för storskalighet har ytterligare diagnostik lagts till för att hjälpa dig. I den här artikeln beskrivs grundliga diagnostikdata.


## <a name="log-rate-throttling-waits"></a>Begränsning av logg hastighet väntar


Varje Azure SQL Database Service nivå har gränser för logg skapande hastighet som tillämpas via [styrning av logg hastighet](sql-database-resource-limits-database-server.md#transaction-log-rate-governance). I stor skala är gränsen för logg skapande inställd på 100 MB/SEK, oavsett tjänst nivå. Det finns dock tillfällen då logg skapande frekvensen för den primära beräknings repliken måste begränsas för att upprätthålla återställnings service avtal. Den här begränsningen inträffar när en [sid Server eller en annan beräknings replik](sql-database-service-tier-hyperscale.md) är betydligt bakom att använda nya logg poster från logg tjänsten.

Följande vänte typer (i [sys. DM-_os_wait_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-os-wait-stats-transact-sql/)) beskriver orsakerna till varför logg frekvensen kan begränsas på den primära beräknings repliken:

|Wait-typ    |Beskrivning                         |
|-------------          |------------------------------------|
|RBIO_RG_STORAGE        | Inträffar när en storskalig databas för att skapa en primär Compute-datornod begränsas på grund av en fördröjd logg användning på sidans Server (er).         |
|RBIO_RG_DESTAGE        | Inträffar när en hastighet för databas skapande av en storskalig databas begränsas på grund av en fördröjd logg användning av den långsiktiga logg lagringen.         |
|RBIO_RG_REPLICA        | Inträffar när en storskalig databas för beräkning av databasens logg skapande begränsas på grund av fördröjd logg användning av de läsbara sekundära noderna.         |
|RBIO_RG_LOCALDESTAGE   | Inträffar när en storskalig databas för beräkning av databasens logg skapande begränsas på grund av en fördröjd logg användning av logg tjänsten.         |


## <a name="page-server-reads"></a>Sid Server läsningar

Beräknings replikerna cachelagrar inte en fullständig kopia av databasen lokalt. De data som är lokala för beräknings repliken lagras i bufferten (i minnet) och i den lokala RBPEX-cachen (elastisk buffertpooltillägget) som är partiell (icke-omfattande) cache för data sidor. Den här lokala RBPEX-cachen har storleks proportionellt till beräknings storlek och är 3 gånger minne för beräknings nivån. RBPEX liknar bufferten på så sätt att den innehåller data som används mest. Varje sida Server har å andra sidan en täcker RBPEX-cache för den del av databasen som hanteras.
 
När en läsning utfärdas för en beräknings replik, och om data inte finns i bufferten eller lokalt RBPEX cache, utfärdas ett getPage-funktions anrop (pageId, LSN) och sidan hämtas från motsvarande sid Server. Läsningar från sid servrar är fjärrläsningar och är därför långsammare än att läsa från den lokala RBPEX. Vid fel sökning av IO-relaterade prestanda problem måste vi kunna se hur många IOs som gjorts via relativt långsamma fjärrside Server-läsningar.

Vi har lagt till sid Server läsningar till en uppsättning DMV: er och utökade händelser som hjälper dig att identifiera vilka läsningar som är fjärrläsningar från en Page server v/s logiska läsningar

- Kolumner för att rapportera sid Server läsningar är tillgängliga i DMV: er för körning, till exempel:
    - [sys. DM-_exec_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-requests-transact-sql/)
    - [sys. DM-_exec_query_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-query-stats-transact-sql/)
    - [sys. DM-_exec_procedure_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-procedure-stats-transact-sql/)
    - [sys. DM-_exec_trigger_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-trigger-stats-transact-sql/)
- Sid Server läsningar läggs till i följande utökade händelser:
    - sql_statement_completed
    - sp_statement_completed
    - sql_batch_completed
    - rpc_completed
    - scan_stopped
    - query_store_begin_persist_runtime_stat
    - fråga – store_execution_runtime_info
- ActualPageServerReads/ActualPageServerReadAheads läggs till i Query plan-XML för faktiska planer.

`<RunTimeCountersPerThread Thread="8" ActualRows="90466461" ActualRowsRead="90466461" Batches="0" ActualEndOfScans="1" ActualExecutions="1" ActualExecutionMode="Row" ActualElapsedms="133645" ActualCPUms="85105" ActualScans="1" ActualLogicalReads="6032256" ActualPhysicalReads="0" ActualPageServerReads="0" ActualReadAheads="6027814" ActualPageServerReadAheads="5687297" ActualLobLogicalReads="0" ActualLobPhysicalReads="0" ActualLobPageServerReads="0" ActualLobReadAheads="0" ActualLobPageServerReadAheads="0" />`

> [!NOTE]
> Om du vill visa dessa attribut i fönstret Egenskaper för frågeplan i SSMS behöver du SSMS 18,3 eller senare.


## <a name="virtual-file-stats-and-io-accounting"></a>Virtuell fil statistik och IO-redovisning

I Azure SQL Database är [sys. DM _io_virtual_file_stats ()](/sql/relational-databases/system-dynamic-management-views/sys-dm-io-virtual-file-stats-transact-sql/) DMF det främsta sättet att övervaka SQL Server i/o. I/o-egenskaperna på storskaligheten är olika på grund av dess [distribuerade arkitektur](sql-database-service-tier-hyperscale.md#distributed-functions-architecture). I det här avsnittet fokuserar vi på IO (läsningar och skrivningar) till datafiler som visas i den här DMF-filen. I stor skala motsvarar varje datafil som visas i den här DMF en fjärrwebbserver. RBPEX-cachen som nämns här är en lokal SSD-baserad cache som inte omfattar cache på Compute-noden.


### <a name="local-rbpex-cache-usage"></a>Lokal RBPEX-cache-användning

Den lokala RBPEX-cachen finns på Compute-noden på den lokala SSD-lagringen. Därför är IO på denna RBPEX-cache snabbare än i/o på fjärrservrar. För närvarande har [sys. DM _io_virtual_file_stats ()](/sql/relational-databases/system-dynamic-management-views/sys-dm-io-virtual-file-stats-transact-sql/) i en storskalig databas en särskild rad som rapporterar IO-inläsningen för den lokala RBPEX-cachen på beräknings repliken. Den här raden har värdet 0 för både `database_id`-och `file_id`-kolumner. Frågan nedan returnerar t. ex. RBPEX användnings statistik sedan databasen startades.

`select * from sys.dm_io_virtual_file_stats(0,NULL);`

Förhållandet mellan läsningar som gjorts på RBPEX till sammanställda läsningar på alla andra datafiler ger RBPEX cache träff-förhållandet.


### <a name="data-reads"></a>Data läsningar

- När läsningar utfärdas av SQL Server-motorn på en beräknings replik, kan de hanteras antingen av den lokala RBPEX-cachen eller av fjärrservrar, eller genom en kombination av de två om du läser flera sidor.
- När beräknings repliken läser vissa sidor från en viss fil, till exempel file_id 1, om dessa data bara finns i den lokala RBPEX cache, är all i/o för denna läsning konto mot file_id 0 (RBPEX). Om en del av dessa data finns i den lokala RBPEX-cachen och en del finns på en fjärrserver, kommer i/o att redovisas mot file_id 0 för den del som hanteras från RBPEX, och den del som hanteras från fjärrservern redovisas mot file_id 1. 
- Om en beräknings replik begär en sida vid en viss [LSN](/sql/relational-databases/sql-server-transaction-log-architecture-and-management-guide/) från en sida Server, och om sidan Server inte har fångats upp till den begärda LSN, väntar läsningen på beräknings repliken tills sidan servern har hämtats innan sidan returneras till beräknings repliken. För all läsning från en sid server på Compute-repliken visas vänte läge för PAGEIOLATCH_XX om det väntar på denna i/o. Den här vänte tiden inkluderar både tiden det tar att fånga upp den begärda sidan på sidan till den LSN som krävs, och hur lång tid det tar att överföra sidan från sidan server till beräknings repliken.
- Stora läsningar som till exempel Read-Ahead görs ofta med ["punkt-samla in" läsningar](/sql/relational-databases/reading-pages/). Detta tillåter läsning av upp till 4 MB sidor i taget, och betraktas som en enda läsning i SQL Server motor. Men när data läses i RBPEX redovisas dessa läsningar som flera enskilda 8 KB-läsningar eftersom buffert-och RBPEX alltid använder 8 KB-sidor. Resultatet är att antalet Läs-IOs som ses mot RBPEX kan vara större än det faktiska antalet IOs som utförs av motorn.


### <a name="data-writes"></a>Data skrivningar

- Den primära beräknings repliken skriver inte direkt till sid servrar. I stället spelas logg poster från logg tjänsten upp på motsvarande sid servrar. 
- Skrivningar som sker på beräknings repliken skrivs huvudsakligen till den lokala RBPEX (file_id 0). För skrivningar på logiska filer som är större än 8 KB, d.v.s. de som görs med hjälp av [samlad skrivning](/sql/relational-databases/writing-pages/), översätts varje Skriv åtgärd till flera 8 KB-individuella SKRIVNINGAR till RBPEX eftersom bufferten och RBPEX alltid använder 8 KB sidor. Resultatet är att antalet skriv-IOs som ses mot RBPEX kan vara större än det faktiska antalet IOs som utförs av motorn.
- Icke-RBPEX filer eller datafiler förutom file_id 0 som motsvarar sid servrar, visar även skrivningar. I den storskaliga tjänst nivån simuleras dessa skrivningar, eftersom beräknings replikerna aldrig skriver direkt till sid servrar. Skrivning av IOPS och data flöde redovisas som de inträffar på beräknings repliken, men svars tiden för datafiler som inte är file_id 0 återspeglar inte den faktiska svars tiden för sid Server skrivningar.

### <a name="log-writes"></a>Logg skrivningar

- Vid den primära beräkningen redovisas en logg skrivning för i file_id 2 av sys. DM _io_virtual_file_stats. En logg skrivning vid primär beräkning är en skrivning till logg landnings zonen, som är en Azure Premium-lagring.
- På den sekundära repliken bevaras inte logg poster på den sekundära repliken vid en incheckning, loggen tillämpas av xlog-tjänsten på fjärranslutna repliker. De flesta logg skrivningar inträffar inte på sekundära repliker och är endast för spårning.

## <a name="additional-resources"></a>Ytterligare resurser

- För vCore resurs gränser för en storskalig enkel databas, se [storskaliga vCore-gränser för service nivå](sql-database-vcore-resource-limits-single-databases.md#hyperscale-service-tier-for-provisioned-compute)
- Azure SQL Database prestanda justering finns [i fråga om prestanda i Azure SQL Database](sql-database-performance-guidance.md)
- För prestanda justering med hjälp av Query Store, se [prestanda övervakning med Query Store](/sql/relational-databases/performance/monitoring-performance-by-using-the-query-store/)
- Information om hur du övervakar skript för DMV finns i [övervaknings prestanda Azure SQL Database använda vyer för dynamisk hantering](sql-database-monitoring-with-dmvs.md)