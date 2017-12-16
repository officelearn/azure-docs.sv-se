---
title: "Övervaka din arbetsbelastning med av DMV: er | Microsoft Docs"
description: "Lär dig att övervaka din arbetsbelastning med av DMV: er."
services: sql-data-warehouse
documentationcenter: NA
author: sqlmojo
manager: jhubbard
editor: 
ms.assetid: 69ecd479-0941-48df-b3d0-cf54c79e6549
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: performance
ms.date: 12/14/2017
ms.author: joeyong;barbkess;kevin
ms.openlocfilehash: 56bae284bb83b1ff18bf2caf644e6dd071b8eb69
ms.sourcegitcommit: 821b6306aab244d2feacbd722f60d99881e9d2a4
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/16/2017
---
# <a name="monitor-your-workload-using-dmvs"></a>Övervaka din arbetsbelastning med DMV:er
Den här artikeln beskriver hur du använder dynamiska hanteringsvyer (av DMV: er) för att övervaka din arbetsbelastning och undersöka Frågekörningen i Azure SQL Data Warehouse.

## <a name="permissions"></a>Behörigheter
Om du vill fråga de av DMV: er i den här artikeln behöver behörighet att visa status för databasen eller kontroll. Beviljande VISNINGSSTATUS för databasen är vanligtvis prioriterade behörigheten eftersom den är mycket mer restriktiva.

```sql
GRANT VIEW DATABASE STATE TO myuser;
```

## <a name="monitor-connections"></a>Övervaka anslutningar
Alla inloggningar till SQL Data Warehouse loggas [sys.dm_pdw_exec_sessions][sys.dm_pdw_exec_sessions].  Den här DMV innehåller de senaste 10 000-inloggningar.  Session_id är den primära nyckeln och har tilldelats sekventiellt för varje ny inloggning.

```sql
-- Other Active Connections
SELECT * FROM sys.dm_pdw_exec_sessions where status <> 'Closed' and session_id <> session_id();
```

## <a name="monitor-query-execution"></a>Körning av övervakaren fråga
Alla frågor som körs på SQL Data Warehouse loggas [sys.dm_pdw_exec_requests][sys.dm_pdw_exec_requests].  Den här DMV innehåller de senaste 10 000 frågor som körs.  Request_id unikt identifierar varje fråga och är den primära nyckeln för den här DMV.  Request_id tilldelas sekventiellt för varje ny fråga och med prefixet QID, vilket står för frågan-ID.  Frågor till den här DMV för en given session_id visas alla frågor för en viss inloggning.

> [!NOTE]
> Lagrade procedurer använda flera begäran-ID: N.  Begäran-ID: N tilldelas i följd. 
> 
> 

Här följer stegen för att undersöka frågeplaner för körning och tid för en viss fråga.

### <a name="step-1-identify-the-query-you-wish-to-investigate"></a>STEG 1: Identifiera den fråga som du vill undersöka
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

-- Find a query with the Label 'My Query'
-- Use brackets when querying the label column, as it it a key word
SELECT  *
FROM    sys.dm_pdw_exec_requests
WHERE   [label] = 'My Query';
```

Från föregående frågeresultaten **Observera begäran-ID** till den fråga som du vill undersöka.

Frågor i den **pausad** tillstånd köas på grund av samtidiga gränser. De här frågorna visas också i sys.dm_pdw_waits väntar frågan med en typ av UserConcurrencyResourceType. Se [samtidighet och arbetsbelastningen] [ Concurrency and workload management] för mer information om samtidighet gränser. Frågor kan också vänta tills andra orsaker som för lås för objektet.  Om din fråga väntar på en resurs, se [undersöker frågor som väntar på resurser] [ Investigating queries waiting for resources] längre ned i den här artikeln.

För att förenkla sökning efter en fråga i tabellen sys.dm_pdw_exec_requests, Använd [etikett] [ LABEL] att tilldela en kommentar till din fråga kan sökas i vyn sys.dm_pdw_exec_requests.

```sql
-- Query with Label
SELECT *
FROM sys.tables
OPTION (LABEL = 'My Query')
;
```

### <a name="step-2-investigate-the-query-plan"></a>STEG 2: Undersöka frågeplanen
Använda begäran-ID för att hämta frågans distribuerade SQL (DSQL) plan från [sys.dm_pdw_request_steps][sys.dm_pdw_request_steps].

```sql
-- Find the distributed query plan steps for a specific query.
-- Replace request_id with value from Step 1.

SELECT * FROM sys.dm_pdw_request_steps
WHERE request_id = 'QID####'
ORDER BY step_index;
```

När en plan för DSQL tar längre tid än förväntat, kan orsaken vara en komplicerad plan med många DSQL steg eller ett enda steg som tar lång tid.  Om planen många steg med flera move-åtgärder kan du optimera din tabell distributioner för att minska dataflyttning. Den [tabell distribution] [ Table distribution] artikel som förklarar varför data måste flyttas till att lösa en fråga och förklarar vissa distributionsstrategier för att minimera dataflyttning.

Att undersöka ytterligare information om ett enskilt steg i *operation_type* kolumnen tidskrävande frågesteg och den **steg Index**:

* Fortsätt med steg 3a för **SQL-åtgärder**: OnOperation, RemoteOperation, ReturnOperation.
* Fortsätt med steg 3b för **dataflyttning operations**: ShuffleMoveOperation, BroadcastMoveOperation, TrimMoveOperation, PartitionMoveOperation, MoveOperation, CopyOperation.

### <a name="step-3a-investigate-sql-on-the-distributed-databases"></a>STEG 3a: undersöka SQL på de distribuerade databaserna
Använda begäran-ID och indexet steg för att hämta information från [sys.dm_pdw_sql_requests][sys.dm_pdw_sql_requests], som innehåller information om körningen av steget fråga på alla distribuerade databaser.

```sql
-- Find the distribution run times for a SQL step.
-- Replace request_id and step_index with values from Step 1 and 3.

SELECT * FROM sys.dm_pdw_sql_requests
WHERE request_id = 'QID####' AND step_index = 2;
```

När du kör steget frågan [DBCC PDW_SHOWEXECUTIONPLAN] [ DBCC PDW_SHOWEXECUTIONPLAN] kan användas för att hämta den uppskattade planen för SQL Server från SQL Server-plancache för steget körs på en viss distributionsplats.

```sql
-- Find the SQL Server execution plan for a query running on a specific SQL Data Warehouse Compute or Control node.
-- Replace distribution_id and spid with values from previous query.

DBCC PDW_SHOWEXECUTIONPLAN(1, 78);
```

### <a name="step-3b-investigate-data-movement-on-the-distributed-databases"></a>STEG 3b: undersöka flytt av data på de distribuerade databaserna
Använda begäran-ID och indexet steg för att hämta information om ett steg för flytt av data som körs på varje distributionsplats från [sys.dm_pdw_dms_workers][sys.dm_pdw_dms_workers].

```sql
-- Find the information about all the workers completing a Data Movement Step.
-- Replace request_id and step_index with values from Step 1 and 3.

SELECT * FROM sys.dm_pdw_dms_workers
WHERE request_id = 'QID####' AND step_index = 2;
```

* Kontrollera den *total_elapsed_time* kolumnen att se om en viss distributionsplats tar betydligt längre än andra för dataförflyttning.
* Långvariga-distribution Kontrollera den *rows_processed* kolumn om antalet rader som flyttas från den distributionsplatsen är betydligt större än andra. I så fall kan detta tyda skeva underliggande data.

Om frågan körs [DBCC PDW_SHOWEXECUTIONPLAN] [ DBCC PDW_SHOWEXECUTIONPLAN] kan användas för att hämta den uppskattade planen för SQL Server från SQL Server-plancache för pågående SQL steg inom en viss distribution.

```sql
-- Find the SQL Server estimated plan for a query running on a specific SQL Data Warehouse Compute or Control node.
-- Replace distribution_id and spid with values from previous query.

DBCC PDW_SHOWEXECUTIONPLAN(55, 238);
```

<a name="waiting"></a>

## <a name="monitor-waiting-queries"></a>Övervaka väntar frågor
Om du upptäcker att frågan inte är framsteg eftersom den väntar på en resurs, är här en fråga som visar alla resurser som väntar på en fråga.

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

Om frågan är aktivt väntar på resurser från en annan fråga, så kommer att vara i tillståndet **AcquireResources**.  Om frågan har alla nödvändiga resurser och tillståndet kommer att **beviljas**.

## <a name="monitor-tempdb"></a>Övervakaren tempdb
Hög tempdb-användning kan vara orsaken för långsam prestanda och ut ur minnesproblem. Överväg att skala ditt informationslager om du hittar tempdb når gränsen vid körning av fråga. Nedan beskrivs hur du identifierar tempdb-användning per fråga på varje nod. 

Skapa följande vy om du vill associera lämpliga nod-id för sys.dm_pdw_sql_requests. Detta kan du dra nytta av andra direkt av DMV: er och koppla de tabellerna med sys.dm_pdw_sql_requests.

```sql
-- sys.dm_pdw_sql_requests with the correct node id
CREATE VIEW sql_requests AS
(SELECT
       sr.request_id,
       sr.step_index,
       (CASE 
              WHEN (sr.distribution_id = -1 ) THEN 
              (SELECT pdw_node_id FROM sys.dm_pdw_nodes WHERE type = 'CONTROL') 
              ELSE d.pdw_node_id END) AS pdw_node_id,
       sr.distribution_id,
       sr.status,
       sr.error_id,
       sr.start_time,
       sr.end_time,
       sr.total_elapsed_time,
       sr.row_count,
       sr.spid,
       sr.command
FROM sys.pdw_distributions AS d
RIGHT JOIN sys.dm_pdw_sql_requests AS sr ON d.distribution_id = sr.distribution_id)
```
Kör följande fråga för att övervaka tempdb:

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
    INNER JOIN sql_requests AS sr ON ssu.session_id = sr.spid AND ssu.pdw_node_id = sr.pdw_node_id
WHERE DB_NAME(ssu.database_id) = 'tempdb'
    AND es.session_id <> @@SPID
    AND es.login_name <> 'sa' 
ORDER BY sr.request_id;
```
## <a name="monitor-memory"></a>Övervaka minne

Minne kan vara orsaken för långsam prestanda och ut ur minnesproblem. Överväg att skala ditt informationslager om du hittar minnesanvändning för SQL Server når gränsen vid körning av fråga.

Följande fråga returnerar SQL Server och minnesbelastning per nod:   
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
## <a name="monitor-transaction-log-size"></a>Övervaka transaktion loggfilens storlek
Följande fråga returnerar storleken på transaktionsloggen på varje distributionsplats. Om en av filerna når 160GB, bör du skala upp din instans eller begränsa storleken på din transaktion. 
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
AND counter_name = 'Target Server Memory (KB)'
```
## <a name="monitor-transaction-log-rollback"></a>Övervaka logg transaktionsåterställning
Om dina frågor misslyckas eller tar lång tid att fortsätta, kan du kontrollera och övervaka om du har några transaktioner återställs.
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

## <a name="next-steps"></a>Nästa steg
Se [systemvyer] [ System views] mer information om av DMV: er.
Se [SQL Data Warehouse metodtips] [ SQL Data Warehouse best practices] för mer information om bästa praxis

<!--Image references-->

<!--Article references-->
[Manage overview]: ./sql-data-warehouse-overview-manage.md
[SQL Data Warehouse best practices]: ./sql-data-warehouse-best-practices.md
[System views]: ./sql-data-warehouse-reference-tsql-system-views.md
[Table distribution]: ./sql-data-warehouse-tables-distribute.md
[Concurrency and workload management]: ./sql-data-warehouse-develop-concurrency.md
[Investigating queries waiting for resources]: ./sql-data-warehouse-manage-monitor.md#waiting

<!--MSDN references-->
[sys.dm_pdw_dms_workers]: http://msdn.microsoft.com/library/mt203878.aspx
[sys.dm_pdw_exec_requests]: http://msdn.microsoft.com/library/mt203887.aspx
[sys.dm_pdw_exec_sessions]: http://msdn.microsoft.com/library/mt203883.aspx
[sys.dm_pdw_request_steps]: http://msdn.microsoft.com/library/mt203913.aspx
[sys.dm_pdw_sql_requests]: http://msdn.microsoft.com/library/mt203889.aspx
[DBCC PDW_SHOWEXECUTIONPLAN]: http://msdn.microsoft.com/library/mt204017.aspx
[DBCC PDW_SHOWSPACEUSED]: http://msdn.microsoft.com/library/mt204028.aspx
[LABEL]: https://msdn.microsoft.com/library/ms190322.aspx
