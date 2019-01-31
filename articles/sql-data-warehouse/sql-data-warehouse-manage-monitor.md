---
title: Övervaka arbetsbelastningen med Datahanteringsvyer | Microsoft Docs
description: 'Lär dig mer om att övervaka din arbetsbelastning med DMV: er.'
services: sql-data-warehouse
author: kevinvngo
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: manage
ms.date: 04/17/2018
ms.author: kevin
ms.reviewer: igorstan
ms.openlocfilehash: fdb51bf249990a10b8476a55be1103cb05c5821b
ms.sourcegitcommit: 698a3d3c7e0cc48f784a7e8f081928888712f34b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/31/2019
ms.locfileid: "55466991"
---
# <a name="monitor-your-workload-using-dmvs"></a>Övervaka din arbetsbelastning med DMV:er
Den här artikeln beskriver hur du använder dynamiska hanteringsvyer (DMV) för att övervaka din arbetsbelastning. Detta inkluderar att undersöka Frågekörningen i Azure SQL Data Warehouse.

## <a name="permissions"></a>Behörigheter
Om du vill fråga DMV: er i den här artikeln, måste du antingen visa DATABASTILLSTÅND eller behörighet. Beviljad visa DATABASTILLSTÅND är vanligtvis den önskade behörigheten eftersom den är mycket mer restriktiva.

```sql
GRANT VIEW DATABASE STATE TO myuser;
```

## <a name="monitor-connections"></a>Övervaka anslutningar
Alla inloggningar till SQL Data Warehouse är inloggade [sys.dm_pdw_exec_sessions][sys.dm_pdw_exec_sessions].  Den här DMV innehåller de senaste 10 000 inloggningarna.  Session_id är den primära nyckeln och har tilldelats sekventiellt för varje ny inloggning.

```sql
-- Other Active Connections
SELECT * FROM sys.dm_pdw_exec_sessions where status <> 'Closed' and session_id <> session_id();
```

## <a name="monitor-query-execution"></a>Övervaka Frågekörningen
Alla frågor som körs på SQL Data Warehouse är inloggade [sys.dm_pdw_exec_requests][sys.dm_pdw_exec_requests].  Den här DMV innehåller de senaste 10 000 frågor som körs.  Request_id identifierar varje fråga och är den primära nyckeln för den här DMV.  Request_id tilldelas sekventiellt för varje ny fråga och har prefixet QID som står för frågan-ID.  Frågor till den här DMV för en viss session_id visas alla frågor för en viss inloggning.

> [!NOTE]
> Lagrade procedurer använder flera ID: N som begär.  Begäran-ID: N har tilldelats i sekventiell ordning. 
> 
> 

Här är steg att följa om du vill undersöka frågeplaner för körning och tid för en viss fråga.

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

Från resultatet av föregående frågan **Observera ID för begäran** till den fråga som du vill undersöka.

Frågor i den **pausad** tillstånd köas på grund av samtidighetsgränser. De här frågorna visas även i sys.dm_pdw_waits väntar frågan med en typ av UserConcurrencyResourceType. Information om samtidighetsgränser finns [prestandanivåer](performance-tiers.md) eller [resursklasser för hantering av arbetsbelastning](resource-classes-for-workload-management.md). Frågor kan också vänta av andra orsaker som för lås för objektet.  Om din fråga väntar på en resurs, se [undersöka frågor som väntar på resurser] [ Investigating queries waiting for resources] längre ned i den här artikeln.

För att förenkla sökning av en fråga i tabellen sys.dm_pdw_exec_requests kan använda [etikett] [ LABEL] att tilldela en kommentar för frågan som kan sökas i vyn sys.dm_pdw_exec_requests.

```sql
-- Query with Label
SELECT *
FROM sys.tables
OPTION (LABEL = 'My Query')
;
```

### <a name="step-2-investigate-the-query-plan"></a>STEG 2: Undersöka frågeplanen
Använda ID för begäran för att hämta frågans distribuerade SQL (DSQL) planen från [sys.dm_pdw_request_steps][sys.dm_pdw_request_steps].

```sql
-- Find the distributed query plan steps for a specific query.
-- Replace request_id with value from Step 1.

SELECT * FROM sys.dm_pdw_request_steps
WHERE request_id = 'QID####'
ORDER BY step_index;
```

När en plan för DSQL tar längre tid än förväntat kan vara orsaken en komplex plan med många DSQL steg eller bara ett steg som tar lång tid.  Om planen är många steg med flera flyttåtgärder kan du överväga att optimera din tabell distributioner för att minska dataförflyttning. Den [tabelldistribution] [ Table distribution] artikeln förklarar varför data måste flyttas till att lösa en fråga och förklarar vissa distributionsstrategier för att minimera dataförflyttning.

Att undersöka vidare information om ett enskilt steg i *operation_type* kolumnen tidskrävande frågesteg och den **Stegindex**:

* Fortsätt med steg 3a för **SQL operations**: OnOperation, RemoteOperation, ReturnOperation.
* Fortsätt med steg 3b för **dataförflyttning operations**: ShuffleMoveOperation, BroadcastMoveOperation, TrimMoveOperation, PartitionMoveOperation, MoveOperation, CopyOperation.

### <a name="step-3a-investigate-sql-on-the-distributed-databases"></a>STEG 3a: Undersöka SQL på distribuerade databaser
Använd ID för begäran och steg indexet för att hämta information från [sys.dm_pdw_sql_requests][sys.dm_pdw_sql_requests], som innehåller information om körning av fråga steg på alla distribuerade databaser.

```sql
-- Find the distribution run times for a SQL step.
-- Replace request_id and step_index with values from Step 1 and 3.

SELECT * FROM sys.dm_pdw_sql_requests
WHERE request_id = 'QID####' AND step_index = 2;
```

När frågesteg körs [DBCC PDW_SHOWEXECUTIONPLAN] [ DBCC PDW_SHOWEXECUTIONPLAN] kan användas för att hämta den uppskattade planen för SQL Server från SQL Server-plancachen för steg som körs på en viss distribution.

```sql
-- Find the SQL Server execution plan for a query running on a specific SQL Data Warehouse Compute or Control node.
-- Replace distribution_id and spid with values from previous query.

DBCC PDW_SHOWEXECUTIONPLAN(1, 78);
```

### <a name="step-3b-investigate-data-movement-on-the-distributed-databases"></a>STEG 3b: Undersöka dataförflyttning på distribuerade databaser
Använda ID för begäran och indexet steg för att hämta information om ett steg för flytt av data som körs på varje distributionsplats från [sys.dm_pdw_dms_workers][sys.dm_pdw_dms_workers].

```sql
-- Find the information about all the workers completing a Data Movement Step.
-- Replace request_id and step_index with values from Step 1 and 3.

SELECT * FROM sys.dm_pdw_dms_workers
WHERE request_id = 'QID####' AND step_index = 2;
```

* Kontrollera den *total_elapsed_time* kolumnen för att se om en viss distribution tar avsevärt längre tid än andra för dataförflyttning.
* Tidskrävande distribution, kontrollera den *rows_processed* kolumnen för att se om antalet rader som flyttas från den distributionsplatsen är betydligt större än andra. I så, fall kan detta tyda på Skeva dina underliggande data.

Om frågan körs [DBCC PDW_SHOWEXECUTIONPLAN] [ DBCC PDW_SHOWEXECUTIONPLAN] kan användas för att hämta den uppskattade planen för SQL Server från SQL Server-plancachen för pågående SQL-steg inom en viss distribution.

```sql
-- Find the SQL Server estimated plan for a query running on a specific SQL Data Warehouse Compute or Control node.
-- Replace distribution_id and spid with values from previous query.

DBCC PDW_SHOWEXECUTIONPLAN(55, 238);
```

<a name="waiting"></a>

## <a name="monitor-waiting-queries"></a>Övervakaren frågar för att vänta
Om du upptäcker att frågan inte är framsteg eftersom den väntar för en resurs, är här en fråga som visar alla resurser som väntar på en fråga.

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

Om frågan är aktivt väntar på resurser från en annan fråga så kommer att vara i tillståndet **AcquireResources**.  Om frågan har alla nödvändiga resurser så kommer att vara i tillståndet **beviljas**.

## <a name="monitor-tempdb"></a>Övervaka tempdb
Hög tempdb-användning kan vara orsaken för långsam prestanda och ut ur minnesproblem. Överväg att skala ditt informationslager om du hittar tempdb når gränsen under Frågekörningen. Följande information beskriver hur du identifierar tempdb-användningen per fråga på varje nod. 

Skapa följande vy för att associera lämplig nod-ID för sys.dm_pdw_sql_requests. Med nod-ID kan du använda andra direkt DMV: er och delta i dessa tabeller med sys.dm_pdw_sql_requests.

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
För att övervaka tempdb, kör du följande fråga:

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

Minne kan vara orsaken för långsam prestanda och ut ur minnesproblem. Överväg att skala ditt informationslager om du hittar minnesanvändning på SQL Server når gränsen under Frågekörningen.

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
## <a name="monitor-transaction-log-size"></a>Övervaka transaktionsloggarnas storlek
Följande fråga returnerar storleken på transaktionsloggen på varje distribution. Om en av filerna når 160 GB, bör du överväga att skala upp din instans eller begränsa storleken på din transaktion. 
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
## <a name="monitor-transaction-log-rollback"></a>Övervaka log transaktionsåterställning
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
Mer information om DMV: er finns i [systemvyer][System views].


<!--Image references-->

<!--Article references-->
[SQL Data Warehouse best practices]: ./sql-data-warehouse-best-practices.md
[System views]: ./sql-data-warehouse-reference-tsql-system-views.md
[Table distribution]: ./sql-data-warehouse-tables-distribute.md
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
