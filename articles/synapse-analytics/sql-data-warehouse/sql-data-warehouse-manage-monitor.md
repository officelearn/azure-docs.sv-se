---
title: Övervaka din SQL-poolarbetsbelastning med hjälp av DMV:er
description: Lär dig hur du övervakar din Azure Synapse Analytics SQL-poolarbetsbelastning och frågekörning med hjälp av DMVs.
services: synapse-analytics
author: ronortloff
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 03/24/2020
ms.author: rortloff
ms.reviewer: igorstan
ms.custom: synapse-analytics
ms.openlocfilehash: b2eee4cdf822b6904b7a407aa2796770a2502135
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "80351457"
---
# <a name="monitor-your-azure-synapse-analytics-sql-pool-workload-using-dmvs"></a>Övervaka din AZURE Synapse Analytics SQL-poolarbetsbelastning med hjälp av DMV:er

I den här artikeln beskrivs hur du använder DVS (Dynamic Management Views) för att övervaka din arbetsbelastning, inklusive att undersöka frågekörning i SQL-pool.

## <a name="permissions"></a>Behörigheter

Om du vill fråga dmverna i den här artikeln måste du antingen **visa databastillstånd** eller **behörighet för kontroll.** Att bevilja **VIEW DATABASE STATE** är vanligtvis den önskade behörigheten eftersom den är mycket mer restriktiv.

```sql
GRANT VIEW DATABASE STATE TO myuser;
```

## <a name="monitor-connections"></a>Övervaka anslutningar

Alla inloggningar till ditt informationslager loggas till [sys.dm_pdw_exec_sessions](https://msdn.microsoft.com/library/mt203883.aspx).  Denna DMV innehåller de senaste 10.000 inloggningar.  Den session_id är primärnyckeln och tilldelas sekventiellt för varje ny inloggning.

```sql
-- Other Active Connections
SELECT * FROM sys.dm_pdw_exec_sessions where status <> 'Closed' and session_id <> session_id();
```

## <a name="monitor-query-execution"></a>Övervaka körning av frågor

Alla frågor som körs i SQL-poolen loggas till [sys.dm_pdw_exec_requests](https://msdn.microsoft.com/library/mt203887.aspx).  Den här DMV innehåller de senaste 10 000 frågorna som har körts.  Request_id identifierar varje fråga unikt och är den primära nyckeln för den här DMV:n.  Den request_id tilldelas sekventiellt för varje ny fråga och föregås av QID, som står för fråge-ID.  När du frågar om den här DMV:n för en viss session_id visas alla frågor för en viss inloggning.

> [!NOTE]
> Lagrade procedurer använder flera begärande-ID:er.  Begärande-ID:er tilldelas i sekventiell ordning.

Här är några steg att följa för att undersöka frågekörningsplaner och tider för en viss fråga.

### <a name="step-1-identify-the-query-you-wish-to-investigate"></a>STEG 1: Identifiera den fråga du vill undersöka

```sql
-- Monitor active queries
SELECT * 
FROM sys.dm_pdw_exec_requests 
WHERE status not in ('Completed','Failed','Cancelled')
  AND session_id <> session_id()
ORDER BY submit_time DESC;

-- Find top 10 queries longest running queries
SELECT TOP 10 * 
FROM sys.dm_pdw_exec_requests 
ORDER BY total_elapsed_time DESC;

```

Från föregående frågeresultat **noterar du begärande-ID:t** för den fråga som du vill undersöka.

Frågor i det **pausade** tillståndet kan köas på grund av ett stort antal aktiva frågor som körs. Dessa frågor visas också i [frågan sys.dm_pdw_waits](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-waits-transact-sql) väntar med en typ av UserConcurrencyResourceType. Information om samtidighetsgränser finns i Begränsningar för [minne och samtidighet](../sql-data-warehouse/memory-concurrency-limits.md) eller [Resursklasser för arbetsbelastningshantering](resource-classes-for-workload-management.md). Frågor kan också vänta av andra orsaker, till exempel för objektlås.  Om frågan väntar på en resurs läser [du Undersöka frågor som väntar på resurser](#monitor-waiting-queries) längre ned i den här artikeln.

Om du vill förenkla uppslagningen av en fråga i tabellen [sys.dm_pdw_exec_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-exec-requests-transact-sql) använder du [ETIKETT](https://msdn.microsoft.com/library/ms190322.aspx) för att tilldela en kommentar till frågan, som kan slås upp i vyn sys.dm_pdw_exec_requests.

```sql
-- Query with Label
SELECT *
FROM sys.tables
OPTION (LABEL = 'My Query')
;

-- Find a query with the Label 'My Query'
-- Use brackets when querying the label column, as it it a key word
SELECT  *
FROM    sys.dm_pdw_exec_requests
WHERE   [label] = 'My Query';
```

### <a name="step-2-investigate-the-query-plan"></a>STEG 2: Undersök frågeplanen

Använd begärande-ID:t för att hämta frågans distribuerade SQL-plan (DSQL) från [sys.dm_pdw_request_steps](https://msdn.microsoft.com/library/mt203913.aspx).

```sql
-- Find the distributed query plan steps for a specific query.
-- Replace request_id with value from Step 1.

SELECT * FROM sys.dm_pdw_request_steps
WHERE request_id = 'QID####'
ORDER BY step_index;
```

När en DSQL-plan tar längre tid än väntat kan orsaken vara en komplex plan med många DSQL-steg eller bara ett steg som tar lång tid.  Om planen är många steg med flera flyttåtgärder kan du överväga att optimera tabelldistributionerna för att minska dataförflyttningen. Artikeln [Tabelldistribution](sql-data-warehouse-tables-distribute.md) förklarar varför data måste flyttas för att lösa en fråga. Artikeln förklarar också vissa distributionsstrategier för att minimera datarörelser.

Om du vill undersöka mer information om ett enskilt steg *operation_type* kolumnen i det tidskrävande frågesteget och notera **stegindex:**

* Fortsätt med steg 3a för **SQL-åtgärder:** OnOperation, RemoteOperation, ReturnOperation.
* Fortsätt med steg 3b för **datarörelser:** ShuffleMoveOperation, BroadcastMoveOperation, TrimMoveOperation, PartitionMoveOperation, MoveOperation, CopyOperation.

### <a name="step-3-investigate-sql-on-the-distributed-databases"></a>STEG 3: Undersök SQL i distribuerade databaser

Använd request ID och Step Index för att hämta information från [sys.dm_pdw_sql_requests](https://msdn.microsoft.com/library/mt203889.aspx), som innehåller körningsinformation för frågesteget för alla distribuerade databaser.

```sql
-- Find the distribution run times for a SQL step.
-- Replace request_id and step_index with values from Step 1 and 3.

SELECT * FROM sys.dm_pdw_sql_requests
WHERE request_id = 'QID####' AND step_index = 2;
```

När frågesteget körs kan [DBCC PDW_SHOWEXECUTIONPLAN](https://msdn.microsoft.com/library/mt204017.aspx) användas för att hämta SQL Server-uppskattade planen från SQL Server-plancachen för det steg som körs på en viss distribution.

```sql
-- Find the SQL Server execution plan for a query running on a specific SQL pool or control node.
-- Replace distribution_id and spid with values from previous query.

DBCC PDW_SHOWEXECUTIONPLAN(1, 78);
```

### <a name="step-4-investigate-data-movement-on-the-distributed-databases"></a>STEG 4: Undersöka datarörelser i de distribuerade databaserna
Använd request ID och Step Index för att hämta information om ett dataförflyttningssteg som körs på varje distribution från [sys.dm_pdw_dms_workers](https://msdn.microsoft.com/library/mt203878.aspx).

```sql
-- Find information about all the workers completing a Data Movement Step.
-- Replace request_id and step_index with values from Step 1 and 3.

SELECT * FROM sys.dm_pdw_dms_workers
WHERE request_id = 'QID####' AND step_index = 2;
```

* Kontrollera *total_elapsed_time* kolumn för att se om en viss distribution tar betydligt längre tid än andra för data förflyttning.
* För den tidskrävande distributionen kontrollerar du *kolumnen rows_processed* för att se om antalet rader som flyttas från distributionen är betydligt större än andra. Om så är fallet kan den här identifieringen indikera skevhet av underliggande data.

Om frågan körs kan du använda [DBCC-PDW_SHOWEXECUTIONPLAN](https://msdn.microsoft.com/library/mt204017.aspx) för att hämta SQL Server-uppskattningsplanen från SQL Server-plancachen för det SQL-steg som körs i en viss distribution.

```sql
-- Find the SQL Server estimated plan for a query running on a specific SQL pool Compute or control node.
-- Replace distribution_id and spid with values from previous query.

DBCC PDW_SHOWEXECUTIONPLAN(55, 238);
```

<a name="waiting"></a>

## <a name="monitor-waiting-queries"></a>Övervaka väntande frågor
Om du upptäcker att frågan inte gör framsteg eftersom den väntar på en resurs, här är en fråga som visar alla resurser som en fråga väntar på.

```sql
-- Find queries 
-- Replace request_id with value from Step 1.

SELECT waits.session_id,
      waits.request_id,  
      requests.command,
      requests.status,
      requests.start_time,  
      waits.type,
      waits.state,
      waits.object_type,
      waits.object_name
FROM   sys.dm_pdw_waits waits
   JOIN  sys.dm_pdw_exec_requests requests
   ON waits.request_id=requests.request_id
WHERE waits.request_id = 'QID####'
ORDER BY waits.object_name, waits.object_type, waits.state;
```

Om frågan aktivt väntar på resurser från en annan fråga, kommer tillståndet att **förvärvaResurser**.  Om frågan har alla nödvändiga resurser, kommer tillståndet att **beviljas**.

## <a name="monitor-tempdb"></a>Övervaka tempdb

Tempdb används för att hålla mellanliggande resultat under frågekörning. Hög användning av tempdb-databasen kan leda till långsam frågeprestanda. För varje DW100c konfigurerad tilldelas 399 GB tempdb-utrymme (DW1000c skulle ha 3,99 TB totalt tempdbutrymme).  Nedan finns tips för övervakning tempdb användning och för att minska tempdb användning i dina frågor. 

### <a name="monitoring-tempdb-with-views"></a>Övervakning av tempdb med vyer

Om du vill övervaka tempdb-användning installerar du först [vyn microsoft.vw_sql_requests](https://github.com/Microsoft/sql-data-warehouse-samples/blob/master/solutions/monitoring/scripts/views/microsoft.vw_sql_requests.sql) från [Microsoft Toolkit for SQL-poolen](https://github.com/Microsoft/sql-data-warehouse-samples/tree/master/solutions/monitoring). Du kan sedan köra följande fråga för att se tempdb-användningen per nod för alla utförda frågor:

```sql
-- Monitor tempdb
SELECT
    sr.request_id,
    ssu.session_id,
    ssu.pdw_node_id,
    sr.command,
    sr.total_elapsed_time,
    es.login_name AS 'LoginName',
    DB_NAME(ssu.database_id) AS 'DatabaseName',
    (es.memory_usage * 8) AS 'MemoryUsage (in KB)',
    (ssu.user_objects_alloc_page_count * 8) AS 'Space Allocated For User Objects (in KB)',
    (ssu.user_objects_dealloc_page_count * 8) AS 'Space Deallocated For User Objects (in KB)',
    (ssu.internal_objects_alloc_page_count * 8) AS 'Space Allocated For Internal Objects (in KB)',
    (ssu.internal_objects_dealloc_page_count * 8) AS 'Space Deallocated For Internal Objects (in KB)',
    CASE es.is_user_process
    WHEN 1 THEN 'User Session'
    WHEN 0 THEN 'System Session'
    END AS 'SessionType',
    es.row_count AS 'RowCount'
FROM sys.dm_pdw_nodes_db_session_space_usage AS ssu
    INNER JOIN sys.dm_pdw_nodes_exec_sessions AS es ON ssu.session_id = es.session_id AND ssu.pdw_node_id = es.pdw_node_id
    INNER JOIN sys.dm_pdw_nodes_exec_connections AS er ON ssu.session_id = er.session_id AND ssu.pdw_node_id = er.pdw_node_id
    INNER JOIN microsoft.vw_sql_requests AS sr ON ssu.session_id = sr.spid AND ssu.pdw_node_id = sr.pdw_node_id
WHERE DB_NAME(ssu.database_id) = 'tempdb'
    AND es.session_id <> @@SPID
    AND es.login_name <> 'sa' 
ORDER BY sr.request_id;
```

Om du har en fråga som förbrukar en stor mängd minne eller har fått ett felmeddelande om allokering av tempdb, kan det bero på en mycket stor [CREATE TABLE AS SELECT (CTAS)](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse) eller INSERT [SELECT-sats](/sql/t-sql/statements/insert-transact-sql) som körs som misslyckas i den slutliga dataförflyttningsåtgärden. Detta kan vanligtvis identifieras som en ShuffleMove-åtgärd i den distribuerade frågeplanen precis före den slutliga INSERT SELECT.  Använd [sys.dm_pdw_request_steps](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-request-steps-transact-sql) för att övervaka ShuffleMove-åtgärder. 

Den vanligaste begränsningen är att bryta ctas- eller INSERT SELECT-satsen i flera belastningssatser så att datavolymen inte överskrider gränsen på 1 TB per nodtempdb. Du kan också skala klustret till en större storlek som sprider tempdb-storleken över fler noder som minskar tempdb på varje enskild nod.

Förutom CTAS- och INSERT SELECT-satser kan stora, komplexa frågor som körs med otillräckligt minne spillas ut i tempdb vilket gör att frågor misslyckas.  Överväg att köra med en större [resursklass](resource-classes-for-workload-management.md) för att undvika att spilla in i tempdb.

## <a name="monitor-memory"></a>Övervaka minne

Minne kan vara orsaken till långsam prestanda och på minnesproblem. Överväg att skala ditt informationslager om du hittar SQL Server-minnesanvändning som når dess gränser under frågekörningen.

Följande fråga returnerar SQL Server-minnesanvändning och minnestryck per nod:    
```sql
-- Memory consumption
SELECT
  pc1.cntr_value as Curr_Mem_KB, 
  pc1.cntr_value/1024.0 as Curr_Mem_MB,
  (pc1.cntr_value/1048576.0) as Curr_Mem_GB,
  pc2.cntr_value as Max_Mem_KB,
  pc2.cntr_value/1024.0 as Max_Mem_MB,
  (pc2.cntr_value/1048576.0) as Max_Mem_GB,
  pc1.cntr_value * 100.0/pc2.cntr_value AS Memory_Utilization_Percentage,
  pc1.pdw_node_id
FROM
-- pc1: current memory
sys.dm_pdw_nodes_os_performance_counters AS pc1
-- pc2: total memory allowed for this SQL instance
JOIN sys.dm_pdw_nodes_os_performance_counters AS pc2 
ON pc1.object_name = pc2.object_name AND pc1.pdw_node_id = pc2.pdw_node_id
WHERE
pc1.counter_name = 'Total Server Memory (KB)'
AND pc2.counter_name = 'Target Server Memory (KB)'
```
## <a name="monitor-transaction-log-size"></a>Övervaka transaktionsloggstorlek
Följande fråga returnerar transaktionsloggstorleken för varje distribution. Om en av loggfilerna når 160 GB bör du överväga att skala upp din instans eller begränsa transaktionsstorleken.

```sql
-- Transaction log size
SELECT
  instance_name as distribution_db,
  cntr_value*1.0/1048576 as log_file_size_used_GB,
  pdw_node_id 
FROM sys.dm_pdw_nodes_os_performance_counters 
WHERE 
instance_name like 'Distribution_%' 
AND counter_name = 'Log File(s) Used Size (KB)'
```

## <a name="monitor-transaction-log-rollback"></a>Återställa återställning av transaktionslogg

Om dina frågor misslyckas eller tar lång tid att fortsätta kan du kontrollera och övervaka om du har några transaktioner som rullas tillbaka.
```sql
-- Monitor rollback
SELECT 
    SUM(CASE WHEN t.database_transaction_next_undo_lsn IS NOT NULL THEN 1 ELSE 0 END),
    t.pdw_node_id,
    nod.[type]
FROM sys.dm_pdw_nodes_tran_database_transactions t
JOIN sys.dm_pdw_nodes nod ON t.pdw_node_id = nod.pdw_node_id
GROUP BY t.pdw_node_id, nod.[type]
```

## <a name="monitor-polybase-load"></a>Övervaka PolyBase-inläsning

Följande fråga ger en ungefärlig uppskattning av förloppet för din belastning. Frågan visar bara filer som för närvarande bearbetas. 

```sql

-- To track bytes and files
SELECT
    r.command,
    s.request_id,
    r.status,
    count(distinct input_name) as nbr_files, 
    sum(s.bytes_processed)/1024/1024/1024 as gb_processed
FROM
    sys.dm_pdw_exec_requests r
    inner join sys.dm_pdw_dms_external_work s
        on r.request_id = s.request_id
GROUP BY
    r.command,
    s.request_id,
    r.status
ORDER BY
    nbr_files desc,
    gb_processed desc;
```

## <a name="next-steps"></a>Nästa steg

Mer information om DMV:er finns i [Systemvyer](sql-data-warehouse-reference-tsql-system-views.md).
