---
title: Övervaka din arbetsbelastning med DMV:er
description: 'Lär dig hur du övervakar din arbets belastning med DMV: er.'
services: sql-data-warehouse
author: ronortloff
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: manage
ms.date: 08/23/2019
ms.author: rortloff
ms.reviewer: igorstan
ms.openlocfilehash: e1a754747ae5c0fb7c50653f4881b67a81e011ef
ms.sourcegitcommit: 359930a9387dd3d15d39abd97ad2b8cb69b8c18b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/06/2019
ms.locfileid: "73645658"
---
# <a name="monitor-your-workload-using-dmvs"></a>Övervaka din arbetsbelastning med DMV:er
Den här artikeln beskriver hur du använder DMV: er (Dynamic Management views) för att övervaka din arbets belastning. Detta inkluderar undersökning av frågekörningen i Azure SQL Data Warehouse.

## <a name="permissions"></a>Behörigheter
Om du vill fråga DMV: er i den här artikeln måste du antingen visa databas tillstånd eller kontroll behörighet. Det är vanligt vis att Granting VIEW DATABASE STATE är den bästa behörigheten eftersom det är mycket mer restriktivt.

```sql
GRANT VIEW DATABASE STATE TO myuser;
```

## <a name="monitor-connections"></a>Övervaka anslutningar
Alla inloggningar till SQL Data Warehouse loggas till [sys. DM _pdw_exec_sessions][sys.dm_pdw_exec_sessions].  Denna DMV innehåller de senaste 10 000 inloggningarna.  Session_id är den primära nyckeln och tilldelas sekventiellt för varje ny inloggning.

```sql
-- Other Active Connections
SELECT * FROM sys.dm_pdw_exec_sessions where status <> 'Closed' and session_id <> session_id();
```

## <a name="monitor-query-execution"></a>Övervaka frågekörningen
Alla frågor som körs på SQL Data Warehouse loggas till [sys. DM _pdw_exec_requests][sys.dm_pdw_exec_requests].  Denna DMV innehåller de senaste 10 000 frågorna som kördes.  Request_id identifierar varje fråga unikt och är den primära nyckeln för denna DMV.  Request_id tilldelas sekventiellt för varje ny fråga och föregås av QID, som står för fråge-ID.  Om du frågar denna DMV för en specifik session_id visas alla frågor för en specifik inloggning.

> [!NOTE]
> Lagrade procedurer använder flera ID: n för begäran.  ID för begäran tilldelas i sekventiell ordning. 
> 
> 

Här följer några steg som du följer för att undersöka körnings planer och tids perioder för en viss fråga.

### <a name="step-1-identify-the-query-you-wish-to-investigate"></a>STEG 1: identifiera den fråga som du vill undersöka
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

I föregående frågeresultat **noterar du fråge-ID** för frågan som du vill undersöka.

Frågor i tillståndet **Suspended** kan placeras i kö på grund av ett stort antal aktiva körnings frågor. Dessa frågor visas också i [sys. DM-_pdw_waits](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-waits-transact-sql) väntar på fråga med en typ av UserConcurrencyResourceType. Information om samtidiga gränser finns i [prestanda nivåer](performance-tiers.md) eller [resurs klasser för hantering av arbets belastning](resource-classes-for-workload-management.md). Frågor kan också vänta på andra orsaker till exempel för objekt lås.  Om din fråga väntar på en resurs, se [undersöka frågor som väntar på resurser][Investigating queries waiting for resources] ytterligare i den här artikeln.

För att förenkla sökningen av en fråga i tabellen [sys. DM _pdw_exec_requests](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-exec-requests-transact-sql) , använder du [etikett][LABEL] för att tilldela en kommentar till din fråga som kan visas i vyn sys. DM _pdw_exec_requests.

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

### <a name="step-2-investigate-the-query-plan"></a>STEG 2: Undersök frågeplan
Använd förfrågnings-ID: t för att hämta frågans distribuerade SQL-plan (DSQL) från [sys. DM _pdw_request_steps][sys.dm_pdw_request_steps].

```sql
-- Find the distributed query plan steps for a specific query.
-- Replace request_id with value from Step 1.

SELECT * FROM sys.dm_pdw_request_steps
WHERE request_id = 'QID####'
ORDER BY step_index;
```

När en DSQL-plan tar längre tid än förväntat, kan orsaken vara en komplex plan med många DSQL-steg eller bara ett steg tar lång tid.  Om planen är många steg med flera flyttnings åtgärder bör du överväga att optimera tabell distributionerna för att minska data flytten. [Tabellen distributions][Table distribution] artikel förklarar varför data måste flyttas för att lösa en fråga och förklarar vissa distributions strategier för att minimera data flytten.

Om du vill undersöka ytterligare information om ett enda steg, kolumnen *operation_type* i det långvariga fråge steget och notera **steg indexet**:

* Fortsätt med Steg 3a för **SQL-åtgärder**: OnOperation, RemoteOperation, ReturnOperation.
* Fortsätt med steg 3b för **data förflyttnings åtgärder**: ShuffleMoveOperation, BroadcastMoveOperation, TrimMoveOperation, PartitionMoveOperation, MoveOperation, CopyOperation.

### <a name="step-3a-investigate-sql-on-the-distributed-databases"></a>Steg 3a: Undersök SQL på de distribuerade databaserna
Använd förfrågnings-ID och steg index för att hämta information från [sys. DM _pdw_sql_requests][sys.dm_pdw_sql_requests], som innehåller körnings information för steget fråga på alla distribuerade databaser.

```sql
-- Find the distribution run times for a SQL step.
-- Replace request_id and step_index with values from Step 1 and 3.

SELECT * FROM sys.dm_pdw_sql_requests
WHERE request_id = 'QID####' AND step_index = 2;
```

När steget körs kan [DBCC PDW_SHOWEXECUTIONPLAN][DBCC PDW_SHOWEXECUTIONPLAN] användas för att hämta SQL Server uppskattad plan från SQL Server planera cache för steget som körs på en viss distribution.

```sql
-- Find the SQL Server execution plan for a query running on a specific SQL Data Warehouse Compute or Control node.
-- Replace distribution_id and spid with values from previous query.

DBCC PDW_SHOWEXECUTIONPLAN(1, 78);
```

### <a name="step-3b-investigate-data-movement-on-the-distributed-databases"></a>STEG 3B: Undersök data förflyttning på distribuerade databaser
Använd förfrågnings-ID och steg index för att hämta information om ett steg för data förflyttning som körs på varje distribution från [sys. DM _pdw_dms_workers][sys.dm_pdw_dms_workers].

```sql
-- Find the information about all the workers completing a Data Movement Step.
-- Replace request_id and step_index with values from Step 1 and 3.

SELECT * FROM sys.dm_pdw_dms_workers
WHERE request_id = 'QID####' AND step_index = 2;
```

* Kontrol lera kolumnen *total_elapsed_time* för att se om en viss fördelning tar betydligt längre tid än andra för data förflyttning.
* För den tids krävande distributionen kontrollerar du kolumnen *rows_processed* för att se om antalet rader som flyttas från den distributionen är betydligt större än andra. I så fall kan det hända att den här sökningen visar skevning av underliggande data.

Om frågan körs kan [DBCC PDW_SHOWEXECUTIONPLAN][DBCC PDW_SHOWEXECUTIONPLAN] användas för att hämta SQL Server uppskattad plan från SQL Server plan-cachen för det SQL-steg som körs för närvarande i en viss distribution.

```sql
-- Find the SQL Server estimated plan for a query running on a specific SQL Data Warehouse Compute or Control node.
-- Replace distribution_id and spid with values from previous query.

DBCC PDW_SHOWEXECUTIONPLAN(55, 238);
```

<a name="waiting"></a>

## <a name="monitor-waiting-queries"></a>Övervaka väntande frågor
Om du upptäcker att din fråga inte gör något, eftersom den väntar på en resurs, är här en fråga som visar alla resurser som en fråga väntar på.

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

Om frågan väntar på resurser från en annan fråga blir statusen **AcquireResources**.  Om frågan har alla nödvändiga resurser kommer statusen att **beviljas**.

## <a name="monitor-tempdb"></a>Övervaka tempdb
TempDB används för att lagra mellanliggande resultat under frågekörningen. Hög användning av tempdb-databasen kan leda till långsam frågans prestanda. Varje nod i Azure SQL Data Warehouse har ungefär 1 TB RAW-utrymme för tempdb. Nedan visas tips för att övervaka tempdb-användning och för att minska tempdb-användning i dina frågor. 

### <a name="monitoring-tempdb-with-views"></a>Övervaka tempdb med vyer
Om du vill övervaka tempdb-användningen installerar du först [Microsoft. vw_sql_requests](https://github.com/Microsoft/sql-data-warehouse-samples/blob/master/solutions/monitoring/scripts/views/microsoft.vw_sql_requests.sql) -vyn från [microsoft Toolkit för SQL Data Warehouse](https://github.com/Microsoft/sql-data-warehouse-samples/tree/master/solutions/monitoring). Du kan sedan köra följande fråga för att se tempdb-användning per nod för alla utförda frågor:

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

Om du har en fråga som förbrukar en stor mängd minne eller har fått ett fel meddelande som rör allokering av tempdb, kan det bero på en mycket stor [CREATE TABLE som Select (CTAS)](https://docs.microsoft.com/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse) eller [insert Select](https://docs.microsoft.com/sql/t-sql/statements/insert-transact-sql) -instruktionen som körs och som inte fungerar i åtgärd för slutgiltig data förflyttning. Detta kan vanligt vis identifieras som en ShuffleMove-åtgärd i den distribuerade fråge planen direkt före den slutliga INFOGNINGen.  Använd [sys. DM _pdw_request_steps](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-request-steps-transact-sql) för att övervaka ShuffleMove-åtgärder. 

Den vanligaste lösningen är att bryta din CTAS eller infoga SELECT-instruktion i flera load-instruktioner så att data volymen inte överskrider tempdb-gränsen på 1 TB per nod. Du kan också skala klustret till en större storlek som kommer att sprida tempdb-storleken mellan fler noder som minskar tempdb på varje enskild nod.

Förutom CTAS och INSERT SELECT-instruktioner, kan stora, komplexa frågor som körs med otillräckligt minne spill i tempdb som orsakar att frågor inte kan köras.  Överväg att köra med en större [resurs klass](https://docs.microsoft.com/azure/sql-data-warehouse/resource-classes-for-workload-management) för att undvika spill i tempdb.

## <a name="monitor-memory"></a>Övervaka minne

Minnet kan vara rotor saken för långsam prestanda och minnes problem. Överväg att skala ditt informations lager om du upptäcker SQL Server minnes användningen når gränserna under frågekörningen.

Följande fråga returnerar SQL Server minnes användning och minnes belastning per nod:   
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
## <a name="monitor-transaction-log-size"></a>Övervaka transaktions logg storlek
Följande fråga returnerar transaktions logg storleken för varje distribution. Om en av loggfilerna når 160 GB bör du överväga att skala upp instansen eller begränsa transaktions storleken. 
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
## <a name="monitor-transaction-log-rollback"></a>Övervaka återställning av transaktions logg
Om dina frågor inte fungerar eller tar lång tid att gå vidare kan du kontrol lera och övervaka om du har några transaktioner att återställa.
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

## <a name="monitor-polybase-load"></a>Övervaka PolyBase-belastning
Följande fråga ger en ungefärligt uppskattning av förloppet för din belastning. Frågan visar bara filer som bearbetas just nu. 

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
Mer information om DMV: er finns i [systemvyer][System views].


<!--Image references-->

<!--Article references-->
[SQL Data Warehouse best practices]: ./sql-data-warehouse-best-practices.md
[System views]: ./sql-data-warehouse-reference-tsql-system-views.md
[Table distribution]: ./sql-data-warehouse-tables-distribute.md
[Investigating queries waiting for resources]: ./sql-data-warehouse-manage-monitor.md#waiting

<!--MSDN references-->
[sys.dm_pdw_dms_workers]: https://msdn.microsoft.com/library/mt203878.aspx
[sys.dm_pdw_exec_requests]: https://msdn.microsoft.com/library/mt203887.aspx
[sys.dm_pdw_exec_sessions]: https://msdn.microsoft.com/library/mt203883.aspx
[sys.dm_pdw_request_steps]: https://msdn.microsoft.com/library/mt203913.aspx
[sys.dm_pdw_sql_requests]: https://msdn.microsoft.com/library/mt203889.aspx
[DBCC PDW_SHOWEXECUTIONPLAN]: https://msdn.microsoft.com/library/mt204017.aspx
[DBCC PDW_SHOWSPACEUSED]: https://msdn.microsoft.com/library/mt204028.aspx
[LABEL]: https://msdn.microsoft.com/library/ms190322.aspx
