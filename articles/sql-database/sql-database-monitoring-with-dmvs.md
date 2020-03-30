---
title: Övervaka prestanda med hjälp av DMV:er
description: Lär dig hur du identifierar och diagnostiserar vanliga prestandaproblem med hjälp av dynamiska hanteringsvyer för att övervaka Microsoft Azure SQL Database.
services: sql-database
ms.service: sql-database
ms.subservice: performance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: juliemsft
ms.author: jrasnick
ms.reviewer: carlrab
ms.date: 03/10/2020
ms.openlocfilehash: 958dcd441d35b5c28746ff79a0b341e5aa7383a6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79214014"
---
# <a name="monitoring-performance-azure-sql-database-using-dynamic-management-views"></a>Övervaka prestanda Azure SQL Database med hjälp av dynamiska hanteringsvyer

Microsoft Azure SQL Database gör det möjligt för en delmängd dynamiska hanteringsvyer att diagnostisera prestandaproblem, som kan orsakas av blockerade eller långvariga frågor, resursflaskhalsar, dåliga frågeplaner och så vidare. Det här avsnittet innehåller information om hur du identifierar vanliga prestandaproblem med hjälp av dynamiska hanteringsvyer.

SQL Database stöder delvis tre kategorier av dynamiska hanteringsvyer:

- Databasrelaterade dynamiska hanteringsvyer.
- Körningsrelaterade dynamiska hanteringsvyer.
- Transaktionsrelaterade dynamiska hanteringsvyer.

Detaljerad information om dynamiska hanteringsvyer finns i [Dynamiska hanteringsvyer och funktioner (Transact-SQL)](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/system-dynamic-management-views) i SQL Server Books Online.

## <a name="permissions"></a>Behörigheter

I SQL Database kräver en dynamisk hanteringsvy **behörigheter för VYDATABASTILLSTÅND.** **Behörigheten VISA DATABASTILLSTÅND** returnerar information om alla objekt i den aktuella databasen.
Om du vill bevilja behörigheten **VISA DATABASTILLSTÅND** till en viss databasanvändare kör du följande fråga:

```sql
GRANT VIEW DATABASE STATE TO database_user;
```

I en instans av lokala SQL Server returnerar dynamiska hanteringsvyer information om servertillstånd. I SQL Database returnerar de endast information om den aktuella logiska databasen.

Den här artikeln innehåller en samling DMV-frågor som du kan köra med SQL Server Management Studio eller Azure Data Studio för att identifiera följande typer av frågeprestandaproblem:

- [Identifiera frågor relaterade till överdriven CPU-förbrukning](#identify-cpu-performance-issues)
- [PAGELATCH_* och WRITE_LOG väntar i samband med i/o-flaskhalsar](#identify-io-performance-issues)
- [PAGELATCH_* väntar på grund avtempDB påstående](#identify-tempdb-performance-issues)
- [RESOURCE_SEMAHPORE väntar på grund av väntande problem med minnesbidrag](#identify-memory-grant-wait-performance-issues)
- [Identifiera databas- och objektstorlekar](#calculating-database-and-objects-sizes)
- [Hämta information om aktiva sessioner](#monitoring-connections)
- [Hämta information om användning av systemomfattande resurser och databasresurser](#monitor-resource-use)
- [Hämta information om frågeprestanda](#monitoring-query-performance)

## <a name="identify-cpu-performance-issues"></a>Identifiera problem med cpu-prestanda

Om CPU-förbrukningen är över 80 % under längre tidsperioder bör du tänka på följande felsökningssteg:

### <a name="the-cpu-issue-is-occurring-now"></a>CPU-problemet uppstår nu

Om problemet uppstår just nu finns det två möjliga scenarier:

#### <a name="many-individual-queries-that-cumulatively-consume-high-cpu"></a>Många enskilda frågor som kumulativt förbrukar hög CPU

Använd följande fråga för att identifiera de översta frågehÃ¤1en:

```sql
PRINT '-- top 10 Active CPU Consuming Queries (aggregated)--';
SELECT TOP 10 GETDATE() runtime, *
FROM (SELECT query_stats.query_hash, SUM(query_stats.cpu_time) 'Total_Request_Cpu_Time_Ms', SUM(logical_reads) 'Total_Request_Logical_Reads', MIN(start_time) 'Earliest_Request_start_Time', COUNT(*) 'Number_Of_Requests', SUBSTRING(REPLACE(REPLACE(MIN(query_stats.statement_text), CHAR(10), ' '), CHAR(13), ' '), 1, 256) AS "Statement_Text"
    FROM (SELECT req.*, SUBSTRING(ST.text, (req.statement_start_offset / 2)+1, ((CASE statement_end_offset WHEN -1 THEN DATALENGTH(ST.text)ELSE req.statement_end_offset END-req.statement_start_offset)/ 2)+1) AS statement_text
          FROM sys.dm_exec_requests AS req
                CROSS APPLY sys.dm_exec_sql_text(req.sql_handle) AS ST ) AS query_stats
    GROUP BY query_hash) AS t
ORDER BY Total_Request_Cpu_Time_Ms DESC;
```

#### <a name="long-running-queries-that-consume-cpu-are-still-running"></a>Tidskrävande frågor som förbrukar CPU körs fortfarande

Använd följande fråga för att identifiera dessa frågor:

```sql
PRINT '--top 10 Active CPU Consuming Queries by sessions--';
SELECT TOP 10 req.session_id, req.start_time, cpu_time 'cpu_time_ms', OBJECT_NAME(ST.objectid, ST.dbid) 'ObjectName', SUBSTRING(REPLACE(REPLACE(SUBSTRING(ST.text, (req.statement_start_offset / 2)+1, ((CASE statement_end_offset WHEN -1 THEN DATALENGTH(ST.text)ELSE req.statement_end_offset END-req.statement_start_offset)/ 2)+1), CHAR(10), ' '), CHAR(13), ' '), 1, 512) AS statement_text
FROM sys.dm_exec_requests AS req
    CROSS APPLY sys.dm_exec_sql_text(req.sql_handle) AS ST
ORDER BY cpu_time DESC;
GO
```

### <a name="the-cpu-issue-occurred-in-the-past"></a>CPU-problemet uppstod tidigare

Om problemet uppstod tidigare och du vill göra grundorsaksanalys använder du [Query Store](https://docs.microsoft.com/sql/relational-databases/performance/monitoring-performance-by-using-the-query-store). Användare med databasåtkomst kan använda T-SQL för att fråga efter Frågelagringsdata. Standardkonfigurationer för Query Store använder en granularitet på 1 timme. Använd följande fråga för att titta på aktivitet för frågor som konsumerar hög CPU. Den här frågan returnerar de översta 15 CPU-tidskrävande frågorna. Kom ihåg `rsi.start_time >= DATEADD(hour, -2, GETUTCDATE()`att ändra:

```sql
-- Top 15 CPU consuming queries by query hash
-- note that a query  hash can have many query id if not parameterized or not parameterized properly
-- it grabs a sample query text by min
WITH AggregatedCPU AS (SELECT q.query_hash, SUM(count_executions * avg_cpu_time / 1000.0) AS total_cpu_millisec, SUM(count_executions * avg_cpu_time / 1000.0)/ SUM(count_executions) AS avg_cpu_millisec, MAX(rs.max_cpu_time / 1000.00) AS max_cpu_millisec, MAX(max_logical_io_reads) max_logical_reads, COUNT(DISTINCT p.plan_id) AS number_of_distinct_plans, COUNT(DISTINCT p.query_id) AS number_of_distinct_query_ids, SUM(CASE WHEN rs.execution_type_desc='Aborted' THEN count_executions ELSE 0 END) AS Aborted_Execution_Count, SUM(CASE WHEN rs.execution_type_desc='Regular' THEN count_executions ELSE 0 END) AS Regular_Execution_Count, SUM(CASE WHEN rs.execution_type_desc='Exception' THEN count_executions ELSE 0 END) AS Exception_Execution_Count, SUM(count_executions) AS total_executions, MIN(qt.query_sql_text) AS sampled_query_text
                       FROM sys.query_store_query_text AS qt
                            JOIN sys.query_store_query AS q ON qt.query_text_id=q.query_text_id
                            JOIN sys.query_store_plan AS p ON q.query_id=p.query_id
                            JOIN sys.query_store_runtime_stats AS rs ON rs.plan_id=p.plan_id
                            JOIN sys.query_store_runtime_stats_interval AS rsi ON rsi.runtime_stats_interval_id=rs.runtime_stats_interval_id
                       WHERE rs.execution_type_desc IN ('Regular', 'Aborted', 'Exception')AND rsi.start_time>=DATEADD(HOUR, -2, GETUTCDATE())
                       GROUP BY q.query_hash), OrderedCPU AS (SELECT query_hash, total_cpu_millisec, avg_cpu_millisec, max_cpu_millisec, max_logical_reads, number_of_distinct_plans, number_of_distinct_query_ids, total_executions, Aborted_Execution_Count, Regular_Execution_Count, Exception_Execution_Count, sampled_query_text, ROW_NUMBER() OVER (ORDER BY total_cpu_millisec DESC, query_hash ASC) AS RN
                                                              FROM AggregatedCPU)
SELECT OD.query_hash, OD.total_cpu_millisec, OD.avg_cpu_millisec, OD.max_cpu_millisec, OD.max_logical_reads, OD.number_of_distinct_plans, OD.number_of_distinct_query_ids, OD.total_executions, OD.Aborted_Execution_Count, OD.Regular_Execution_Count, OD.Exception_Execution_Count, OD.sampled_query_text, OD.RN
FROM OrderedCPU AS OD
WHERE OD.RN<=15
ORDER BY total_cpu_millisec DESC;
```

När du har identifierat de problematiska frågorna är det dags att ställa in dessa frågor för att minska CPU-användningen.  Om du inte har tid att ställa in frågorna kan du också välja att uppgradera SLO-för databasen för att komma runt problemet.

## <a name="identify-io-performance-issues"></a>Identifiera I/O-prestandaproblem

När du identifierar IO-prestandaproblem är de vanligaste väntetidstyperna som är associerade med I/O-problem:

- `PAGEIOLATCH_*`

  För problem med datafils-I/O (inklusive `PAGEIOLATCH_SH`, `PAGEIOLATCH_EX`, `PAGEIOLATCH_UP`).  Om namnet på väntatypen har **IO** i den pekar det på ett IO-problem. Om det inte finns någon **IO** i sidspärrens väntenamn pekar det på en annan typ av problem (till exempel tempdb-konkurrens).

- `WRITE_LOG`

  För IO-ärenden för transaktionslogg.

### <a name="if-the-io-issue-is-occurring-right-now"></a>Om IO-problemet uppstår just nu

Använd [sys.dm_exec_requests](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-requests-transact-sql) eller [sys.dm_os_waiting_tasks](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-os-waiting-tasks-transact-sql) för att se `wait_type` `wait_time`och .

#### <a name="identify-data-and-log-io-usage"></a>Identifiera data- och logganvändning av I/O

Använd följande fråga för att identifiera data och logga IO-användning. Om data eller logg-IO är över 80 % betyder det att användarna har använt den tillgängliga I/O för SQL DB-tjänstnivån.

```sql
SELECT end_time, avg_data_io_percent, avg_log_write_percent
FROM sys.dm_db_resource_stats
ORDER BY end_time DESC;
```

Om IO-gränsen har nåtts har du två alternativ:

- Alternativ 1: Uppgradera beräkningsstorleken eller tjänstnivån
- Alternativ 2: Identifiera och justera de frågor som förbrukar mest IO.

#### <a name="view-buffer-related-io-using-the-query-store"></a>Visa buffertrelaterad I/o med frågearkivet

För alternativ 2 kan du använda följande fråga mot Query Store för buffertrelaterad I/O för att visa de två senaste timmarnas spårad aktivitet:

```sql
-- top queries that waited on buffer
-- note these are finished queries
WITH Aggregated AS (SELECT q.query_hash, SUM(total_query_wait_time_ms) total_wait_time_ms, SUM(total_query_wait_time_ms / avg_query_wait_time_ms) AS total_executions, MIN(qt.query_sql_text) AS sampled_query_text, MIN(wait_category_desc) AS wait_category_desc
                    FROM sys.query_store_query_text AS qt
                         JOIN sys.query_store_query AS q ON qt.query_text_id=q.query_text_id
                         JOIN sys.query_store_plan AS p ON q.query_id=p.query_id
                         JOIN sys.query_store_wait_stats AS waits ON waits.plan_id=p.plan_id
                         JOIN sys.query_store_runtime_stats_interval AS rsi ON rsi.runtime_stats_interval_id=waits.runtime_stats_interval_id
                    WHERE wait_category_desc='Buffer IO' AND rsi.start_time>=DATEADD(HOUR, -2, GETUTCDATE())
                    GROUP BY q.query_hash), Ordered AS (SELECT query_hash, total_executions, total_wait_time_ms, sampled_query_text, wait_category_desc, ROW_NUMBER() OVER (ORDER BY total_wait_time_ms DESC, query_hash ASC) AS RN
                                                        FROM Aggregated)
SELECT OD.query_hash, OD.total_executions, OD.total_wait_time_ms, OD.sampled_query_text, OD.wait_category_desc, OD.RN
FROM Ordered AS OD
WHERE OD.RN<=15
ORDER BY total_wait_time_ms DESC;
GO
```

#### <a name="view-total-log-io-for-writelog-waits"></a>Visa total logg-I/o för WRITELOG-väntetider

Om väntetypen är `WRITELOG`använder du följande fråga för att visa total logg-I/O efter sats:

```sql
-- Top transaction log consumers
-- Adjust the time window by changing
-- rsi.start_time >= DATEADD(hour, -2, GETUTCDATE())
WITH AggregatedLogUsed
AS (SELECT q.query_hash,
           SUM(count_executions * avg_cpu_time / 1000.0) AS total_cpu_millisec,
           SUM(count_executions * avg_cpu_time / 1000.0) / SUM(count_executions) AS avg_cpu_millisec,
           SUM(count_executions * avg_log_bytes_used) AS total_log_bytes_used,
           MAX(rs.max_cpu_time / 1000.00) AS max_cpu_millisec,
           MAX(max_logical_io_reads) max_logical_reads,
           COUNT(DISTINCT p.plan_id) AS number_of_distinct_plans,
           COUNT(DISTINCT p.query_id) AS number_of_distinct_query_ids,
           SUM(   CASE
                      WHEN rs.execution_type_desc = 'Aborted' THEN
                          count_executions
                      ELSE
                          0
                  END
              ) AS Aborted_Execution_Count,
           SUM(   CASE
                      WHEN rs.execution_type_desc = 'Regular' THEN
                          count_executions
                      ELSE
                          0
                  END
              ) AS Regular_Execution_Count,
           SUM(   CASE
                      WHEN rs.execution_type_desc = 'Exception' THEN
                          count_executions
                      ELSE
                          0
                  END
              ) AS Exception_Execution_Count,
           SUM(count_executions) AS total_executions,
           MIN(qt.query_sql_text) AS sampled_query_text
    FROM sys.query_store_query_text AS qt
        JOIN sys.query_store_query AS q
            ON qt.query_text_id = q.query_text_id
        JOIN sys.query_store_plan AS p
            ON q.query_id = p.query_id
        JOIN sys.query_store_runtime_stats AS rs
            ON rs.plan_id = p.plan_id
        JOIN sys.query_store_runtime_stats_interval AS rsi
            ON rsi.runtime_stats_interval_id = rs.runtime_stats_interval_id
    WHERE rs.execution_type_desc IN ( 'Regular', 'Aborted', 'Exception' )
          AND rsi.start_time >= DATEADD(HOUR, -2, GETUTCDATE())
    GROUP BY q.query_hash),
     OrderedLogUsed
AS (SELECT query_hash,
           total_log_bytes_used,
           number_of_distinct_plans,
           number_of_distinct_query_ids,
           total_executions,
           Aborted_Execution_Count,
           Regular_Execution_Count,
           Exception_Execution_Count,
           sampled_query_text,
           ROW_NUMBER() OVER (ORDER BY total_log_bytes_used DESC, query_hash ASC) AS RN
    FROM AggregatedLogUsed)
SELECT OD.total_log_bytes_used,
       OD.number_of_distinct_plans,
       OD.number_of_distinct_query_ids,
       OD.total_executions,
       OD.Aborted_Execution_Count,
       OD.Regular_Execution_Count,
       OD.Exception_Execution_Count,
       OD.sampled_query_text,
       OD.RN
FROM OrderedLogUsed AS OD
WHERE OD.RN <= 15
ORDER BY total_log_bytes_used DESC;
GO
```

## <a name="identify-tempdb-performance-issues"></a>Identifiera `tempdb` prestandaproblem

När du identifierar IO-prestandaproblem är `tempdb` de `PAGELATCH_*` vanligaste `PAGEIOLATCH_*`väntetidstyperna som är associerade med problem (inte ). Men `PAGELATCH_*` väntar betyder inte alltid `tempdb` att du har stridigheter.  Den här väntetiden kan också innebära att du har konkurrens på datasidan för användarobjekt på grund av samtidiga begäranden som är inriktade på samma datasida. För att `tempdb` ytterligare bekräfta påstående, använd [sys.dm_exec_requests](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-requests-transact-sql) för att bekräfta att `2:x:y` wait_resource värdet `tempdb` börjar med där `x` 2 är databas-ID, är fil-ID och `y` är sid-ID.  

För tempdb påstående, en vanlig metod är att minska eller `tempdb`skriva om programkod som bygger på .  Vanliga `tempdb` användningsområden är:

- Temp-tabeller
- Tabellvariabler
- Tabellvärderade parametrar
- Användning av versionsarkiv (som är särskilt associerad med långvariga transaktioner)
- Frågor som har frågeplaner som använder sorterar, hash-kopplingar och buffrar

### <a name="top-queries-that-use-table-variables-and-temporary-tables"></a>De vanligaste frågorna som använder tabellvariabler och temporära tabeller

Använd följande fråga för att identifiera de vanligaste frågorna som använder tabellvariabler och temporära tabeller:

```sql
SELECT plan_handle, execution_count, query_plan
INTO #tmpPlan
FROM sys.dm_exec_query_stats
     CROSS APPLY sys.dm_exec_query_plan(plan_handle);
GO

WITH XMLNAMESPACES('http://schemas.microsoft.com/sqlserver/2004/07/showplan' AS sp)
SELECT plan_handle, stmt.stmt_details.value('@Database', 'varchar(max)') 'Database', stmt.stmt_details.value('@Schema', 'varchar(max)') 'Schema', stmt.stmt_details.value('@Table', 'varchar(max)') 'table'
INTO #tmp2
FROM(SELECT CAST(query_plan AS XML) sqlplan, plan_handle FROM #tmpPlan) AS p
    CROSS APPLY sqlplan.nodes('//sp:Object') AS stmt(stmt_details);
GO

SELECT t.plan_handle, [Database], [Schema], [table], execution_count
FROM(SELECT DISTINCT plan_handle, [Database], [Schema], [table]
     FROM #tmp2
     WHERE [table] LIKE '%@%' OR [table] LIKE '%#%') AS t
    JOIN #tmpPlan AS t2 ON t.plan_handle=t2.plan_handle;
```

### <a name="identify-long-running-transactions"></a>Identifiera tidskrävande transaktioner

Använd följande fråga för att identifiera tidskrävande transaktioner. Tidskrävande transaktioner förhindrar rensning av versionsarkiv.

```sql
SELECT DB_NAME(dtr.database_id) 'database_name',
       sess.session_id,
       atr.name AS 'tran_name',
       atr.transaction_id,
       transaction_type,
       transaction_begin_time,
       database_transaction_begin_time transaction_state,
       is_user_transaction,
       sess.open_transaction_count,
       LTRIM(RTRIM(REPLACE(
                              REPLACE(
                                         SUBSTRING(
                                                      SUBSTRING(
                                                                   txt.text,
                                                                   (req.statement_start_offset / 2) + 1,
                                                                   ((CASE req.statement_end_offset
                                                                         WHEN -1 THEN
                                                                             DATALENGTH(txt.text)
                                                                         ELSE
                                                                             req.statement_end_offset
                                                                     END - req.statement_start_offset
                                                                    ) / 2
                                                                   ) + 1
                                                               ),
                                                      1,
                                                      1000
                                                  ),
                                         CHAR(10),
                                         ' '
                                     ),
                              CHAR(13),
                              ' '
                          )
                  )
            ) Running_stmt_text,
       recenttxt.text 'MostRecentSQLText'
FROM sys.dm_tran_active_transactions AS atr
    INNER JOIN sys.dm_tran_database_transactions AS dtr
        ON dtr.transaction_id = atr.transaction_id
    LEFT JOIN sys.dm_tran_session_transactions AS sess
        ON sess.transaction_id = atr.transaction_id
    LEFT JOIN sys.dm_exec_requests AS req
        ON req.session_id = sess.session_id
           AND req.transaction_id = sess.transaction_id
    LEFT JOIN sys.dm_exec_connections AS conn
        ON sess.session_id = conn.session_id
    OUTER APPLY sys.dm_exec_sql_text(req.sql_handle) AS txt
    OUTER APPLY sys.dm_exec_sql_text(conn.most_recent_sql_handle) AS recenttxt
WHERE atr.transaction_type != 2
      AND sess.session_id != @@spid
ORDER BY start_time ASC;
```

## <a name="identify-memory-grant-wait-performance-issues"></a>Identifiera problem med väntande minnesbidrag

Om din översta `RESOURCE_SEMAHPORE` vänta typ är och du inte har en hög CPU-användning problem, kan du ha ett minne bevilja väntar problem.

### <a name="determine-if-a-resource_semahpore-wait-is-a-top-wait"></a>Ta reda `RESOURCE_SEMAHPORE` på om en vänta är en toppväntan

Använd följande fråga för `RESOURCE_SEMAHPORE` att avgöra om en vänta är en övre väntan

```sql
SELECT wait_type,
       SUM(wait_time) AS total_wait_time_ms
FROM sys.dm_exec_requests AS req
    JOIN sys.dm_exec_sessions AS sess
        ON req.session_id = sess.session_id
WHERE is_user_process = 1
GROUP BY wait_type
ORDER BY SUM(wait_time) DESC;
```

### <a name="identity-high-memory-consuming-statements"></a>Identitetshög minnesförbrukande satser

Använd följande fråga för att identifiera hög minnesförbrukande satser:

```sql
SELECT IDENTITY(INT, 1, 1) rowId,
    CAST(query_plan AS XML) query_plan,
    p.query_id
INTO #tmp
FROM sys.query_store_plan AS p
    JOIN sys.query_store_runtime_stats AS r
        ON p.plan_id = r.plan_id
    JOIN sys.query_store_runtime_stats_interval AS i
        ON r.runtime_stats_interval_id = i.runtime_stats_interval_id
WHERE start_time > '2018-10-11 14:00:00.0000000'
      AND end_time < '2018-10-17 20:00:00.0000000';
GO
;WITH cte
AS (SELECT query_id,
        query_plan,
        m.c.value('@SerialDesiredMemory', 'INT') AS SerialDesiredMemory
    FROM #tmp AS t
        CROSS APPLY t.query_plan.nodes('//*:MemoryGrantInfo[@SerialDesiredMemory[. > 0]]') AS m(c) )
SELECT TOP 50
    cte.query_id,
    t.query_sql_text,
    cte.query_plan,
    CAST(SerialDesiredMemory / 1024. AS DECIMAL(10, 2)) SerialDesiredMemory_MB
FROM cte
    JOIN sys.query_store_query AS q
        ON cte.query_id = q.query_id
    JOIN sys.query_store_query_text AS t
        ON q.query_text_id = t.query_text_id
ORDER BY SerialDesiredMemory DESC;
```

### <a name="identify-the-top-10-active-memory-grants"></a>Identifiera de 10 bästa aktiva minnesbidragen

Använd följande fråga för att identifiera de tio bästa aktiva minnesbidragen:

```sql
SELECT TOP 10
    CONVERT(VARCHAR(30), GETDATE(), 121) AS runtime,
       r.session_id,
       r.blocking_session_id,
       r.cpu_time,
       r.total_elapsed_time,
       r.reads,
       r.writes,
       r.logical_reads,
       r.row_count,
       wait_time,
       wait_type,
       r.command,
       OBJECT_NAME(txt.objectid, txt.dbid) 'Object_Name',
       LTRIM(RTRIM(REPLACE(
                              REPLACE(
                                         SUBSTRING(
                                                      SUBSTRING(
                                                                   text,
                                                                   (r.statement_start_offset / 2) + 1,
                                                                   ((CASE r.statement_end_offset
                                                                         WHEN -1 THEN
                                                                             DATALENGTH(text)
                                                                         ELSE
                                                                             r.statement_end_offset
                                                                     END - r.statement_start_offset
                                                                    ) / 2
                                                                   ) + 1
                                                               ),
                                                      1,
                                                      1000
                                                  ),
                                         CHAR(10),
                                         ' '
                                     ),
                              CHAR(13),
                              ' '
                          )
                  )
            ) stmt_text,
       mg.dop,                                               --Degree of parallelism
       mg.request_time,                                      --Date and time when this query requested the memory grant.
       mg.grant_time,                                        --NULL means memory has not been granted
       mg.requested_memory_kb / 1024.0 requested_memory_mb,  --Total requested amount of memory in megabytes
       mg.granted_memory_kb / 1024.0 AS granted_memory_mb,   --Total amount of memory actually granted in megabytes. NULL if not granted
       mg.required_memory_kb / 1024.0 AS required_memory_mb, --Minimum memory required to run this query in megabytes.
       max_used_memory_kb / 1024.0 AS max_used_memory_mb,
       mg.query_cost,                                        --Estimated query cost.
       mg.timeout_sec,                                       --Time-out in seconds before this query gives up the memory grant request.
       mg.resource_semaphore_id,                             --Non-unique ID of the resource semaphore on which this query is waiting.
       mg.wait_time_ms,                                      --Wait time in milliseconds. NULL if the memory is already granted.
       CASE mg.is_next_candidate --Is this process the next candidate for a memory grant
           WHEN 1 THEN
               'Yes'
           WHEN 0 THEN
               'No'
           ELSE
               'Memory has been granted'
       END AS 'Next Candidate for Memory Grant',
       qp.query_plan
FROM sys.dm_exec_requests AS r
    JOIN sys.dm_exec_query_memory_grants AS mg
        ON r.session_id = mg.session_id
           AND r.request_id = mg.request_id
    CROSS APPLY sys.dm_exec_sql_text(mg.sql_handle) AS txt
    CROSS APPLY sys.dm_exec_query_plan(r.plan_handle) AS qp
ORDER BY mg.granted_memory_kb DESC;
```

## <a name="calculating-database-and-objects-sizes"></a>Beräkna databas- och objektstorlekar

Följande fråga returnerar databasens storlek (i megabyte):

```sql
-- Calculates the size of the database.
SELECT SUM(CAST(FILEPROPERTY(name, 'SpaceUsed') AS bigint) * 8192.) / 1024 / 1024 AS DatabaseSizeInMB
FROM sys.database_files
WHERE type_desc = 'ROWS';
GO
```

Följande fråga returnerar storleken på enskilda objekt (i megabyte) i databasen:

```sql
-- Calculates the size of individual database objects.
SELECT sys.objects.name, SUM(reserved_page_count) * 8.0 / 1024
FROM sys.dm_db_partition_stats, sys.objects
WHERE sys.dm_db_partition_stats.object_id = sys.objects.object_id
GROUP BY sys.objects.name;
GO
```

## <a name="monitoring-connections"></a>Övervaka anslutningar

Du kan använda [vyn sys.dm_exec_connections](https://msdn.microsoft.com/library/ms181509.aspx) för att hämta information om de anslutningar som upprättats till en viss Azure SQL Database-server och information om varje anslutning. Dessutom är [vyn sys.dm_exec_sessions](https://msdn.microsoft.com/library/ms176013.aspx) användbar när du hämtar information om alla aktiva användaranslutningar och interna uppgifter.
Följande fråga hämtar information om den aktuella anslutningen:

```sql
SELECT
    c.session_id, c.net_transport, c.encrypt_option,
    c.auth_scheme, s.host_name, s.program_name,
    s.client_interface_name, s.login_name, s.nt_domain,
    s.nt_user_name, s.original_login_name, c.connect_time,
    s.login_time
FROM sys.dm_exec_connections AS c
JOIN sys.dm_exec_sessions AS s
    ON c.session_id = s.session_id
WHERE c.session_id = @@SPID;
```

> [!NOTE]
> När du kör **vyerna sys.dm_exec_requests** och **sys.dm_exec_sessions**visas alla körsessioner i databasen om du har **behörigheten VISA DATABASTILLSTÅND.** Annars ser du bara den aktuella sessionen.

## <a name="monitor-resource-use"></a>Övervaka resursanvändning

Du kan övervaka resursanvändning med hjälp av [SQL Database Query Performance Insight](sql-database-query-performance.md) och Query [Store](https://msdn.microsoft.com/library/dn817826.aspx).

Du kan också övervaka användningen med hjälp av dessa två vyer:

- [sys.dm_db_resource_stats](https://msdn.microsoft.com/library/dn800981.aspx)
- [sys.resource_stats](https://msdn.microsoft.com/library/dn269979.aspx)

### <a name="sysdm_db_resource_stats"></a>sys.dm_db_resource_stats

Du kan använda [vyn sys.dm_db_resource_stats](https://msdn.microsoft.com/library/dn800981.aspx) i alla SQL-databaser. Vyn **sys.dm_db_resource_stats** visar de senaste resursanvändningsdata som är relativa till tjänstnivån. Genomsnittliga procentsatser för CPU, data-IO, loggskrivningar och minne registreras var 15:e sekund och underhålls i 1 timme.

Eftersom den här vyn ger en mer detaljerad titt på resursanvändning, använder **du sys.dm_db_resource_stats** först för analys eller felsökning i aktuella tillstånd. Den här frågan visar till exempel den genomsnittliga och maximala resursanvändningen för den aktuella databasen under den senaste timmen:

```sql
SELECT  
    AVG(avg_cpu_percent) AS 'Average CPU use in percent',
    MAX(avg_cpu_percent) AS 'Maximum CPU use in percent',
    AVG(avg_data_io_percent) AS 'Average data IO in percent',
    MAX(avg_data_io_percent) AS 'Maximum data IO in percent',
    AVG(avg_log_write_percent) AS 'Average log write use in percent',
    MAX(avg_log_write_percent) AS 'Maximum log write use in percent',
    AVG(avg_memory_usage_percent) AS 'Average memory use in percent',
    MAX(avg_memory_usage_percent) AS 'Maximum memory use in percent'
FROM sys.dm_db_resource_stats;  
```

Andra frågor finns i exemplen i [sys.dm_db_resource_stats](https://msdn.microsoft.com/library/dn800981.aspx).

### <a name="sysresource_stats"></a>sys.resource_stats

Den [sys.resource_stats-vyn](https://msdn.microsoft.com/library/dn269979.aspx) i **huvuddatabasen** har ytterligare information som kan hjälpa dig att övervaka prestanda för DIN SQL-databas på dess specifika tjänstnivå och beräkningsstorlek. Uppgifterna samlas in var 5:e minut och bevaras i cirka 14 dagar. Den här vyn är användbar för en mer långsiktig historisk analys av hur SQL-databasen använder resurser.

Följande diagram visar cpu-resursanvändningen för en Premium-databas med P2-beräkningsstorleken för varje timme i en vecka. Det här diagrammet börjar på en måndag, visar 5 arbetsdagar och visar sedan en helg, när mycket mindre händer i programmet.

![Användning av SQL-databasresurser](./media/sql-database-performance-guidance/sql_db_resource_utilization.png)

Från data har den här databasen för närvarande en topp CPU-belastning på drygt 50 procent CPU-användning i förhållande till P2-beräkningsstorleken (middag på tisdag). Om CPU är den dominerande faktorn i programmets resursprofil kan du bestämma att P2 är rätt beräkningsstorlek för att garantera att arbetsbelastningen alltid passar. Om du förväntar dig att ett program ska växa med tiden är det en bra idé att ha en extra resursbuffert så att programmet aldrig når gränsen för prestandanivå. Om du ökar beräkningsstorleken kan du undvika kund synliga fel som kan uppstå när en databas inte har tillräckligt med ström för att bearbeta begäranden effektivt, särskilt i latenskänsliga miljöer. Ett exempel är en databas som stöder ett program som målar webbsidor baserat på resultatet av databasanrop.

Andra programtyper kan tolka samma diagram på olika sätt. Om ett program till exempel försöker bearbeta lönedata varje dag och har samma diagram, kan den här typen av "batchjobb"-modell göra bra i p1-beräkningsstorleken. P1-beräkningsstorleken har 100 DTUs jämfört med 200 DTUs vid P2-beräkningsstorleken. P1-beräkningsstorleken ger hälften av prestandan för P2-beräkningsstorleken. Så, 50 procent av CPU-användning i P2 motsvarar 100 procent CPU-användning i P1. Om programmet inte har timeout, kanske det inte spelar någon roll om ett jobb tar 2 timmar eller 2,5 timmar att slutföra, om det blir gjort idag. Ett program i den här kategorin kan förmodligen använda en P1-beräkningsstorlek. Du kan dra nytta av det faktum att det finns perioder under dagen när resursanvändningen är lägre, så att alla "stora topp" kan spilla över till en av tråg senare under dagen. P1-beräkningsstorleken kan vara bra för den typen av program (och spara pengar), så länge jobben kan avslutas i tid varje dag.

Azure SQL Database visar förbrukad resursinformation för varje aktiv databas i **sys.resource_stats** vy av **huvuddatabasen** på varje server. Data i tabellen aggregeras för 5-minutersintervall. Med tjänstnivåerna Basic, Standard och Premium kan det ta mer än 5 minuter att visa data i tabellen, så dessa data är mer användbara för historisk analys snarare än analys i nära realtid. Fråga **vyn sys.resource_stats** för att se den senaste historiken för en databas och för att verifiera om den reservation du valde levererade det resultat du vill ha när det behövs.

> [!NOTE]
> Du måste vara ansluten till **huvuddatabasen** för SQL Database-servern för att fråga **sys.resource_stats** i följande exempel.

I det här exemplet visas hur data i den här vyn är exponerade:

```sql
SELECT TOP 10 *
FROM sys.resource_stats
WHERE database_name = 'resource1'
ORDER BY start_time DESC
```

![Katalogvyn sys.resource_stats](./media/sql-database-performance-guidance/sys_resource_stats.png)

I nästa exempel visas olika sätt att använda **katalogvyn sys.resource_stats** för att få information om hur din SQL-databas använder resurser:

1. Om du vill titta på den senaste veckans resursanvändning för databasen userdb1 kan du köra den här frågan:

    ```sql
    SELECT *
    FROM sys.resource_stats
    WHERE database_name = 'userdb1' AND
        start_time > DATEADD(day, -7, GETDATE())
    ORDER BY start_time DESC;
    ```

2. För att utvärdera hur väl din arbetsbelastning passar beräkningsstorleken måste du öka detaljnivån i varje aspekt av resursmåtten: CPU, läsningar, skrivningar, antal arbetare och antal sessioner. Här är en reviderad fråga med **sys.resource_stats** för att rapportera medelvärden och högsta värden för dessa resursmått:

    ```sql
    SELECT
        avg(avg_cpu_percent) AS 'Average CPU use in percent',
        max(avg_cpu_percent) AS 'Maximum CPU use in percent',
        avg(avg_data_io_percent) AS 'Average physical data IO use in percent',
        max(avg_data_io_percent) AS 'Maximum physical data IO use in percent',
        avg(avg_log_write_percent) AS 'Average log write use in percent',
        max(avg_log_write_percent) AS 'Maximum log write use in percent',
        avg(max_session_percent) AS 'Average % of sessions',
        max(max_session_percent) AS 'Maximum % of sessions',
        avg(max_worker_percent) AS 'Average % of workers',
        max(max_worker_percent) AS 'Maximum % of workers'
    FROM sys.resource_stats
    WHERE database_name = 'userdb1' AND start_time > DATEADD(day, -7, GETDATE());
    ```

3. Med den här informationen om de genomsnittliga och högsta värdena för varje resursmått kan du bedöma hur väl din arbetsbelastning passar in i den beräkningsstorlek du har valt. Vanligtvis ger medelvärden från **sys.resource_stats** dig en bra baslinje att använda mot målstorleken. Det bör vara din primära måttsticka. Du kan till exempel använda standardtjänstnivån med S2-beräkningsstorlek. Den genomsnittliga användningen procentsatser för CPU och IO läser och skriver är under 40 procent, det genomsnittliga antalet arbetstagare är under 50, och det genomsnittliga antalet sessioner är under 200. Din arbetsbelastning kan passa in i S1-beräkningsstorleken. Det är lätt att se om databasen passar i arbets- och sessionsgränserna. Om du vill se om en databas passar in i en lägre beräkningsstorlek när det gäller CPU, läsningar och skrivningar delar du DTU-numret för den lägre beräkningsstorleken med DTU-numret för din aktuella beräkningsstorlek och multiplicerar sedan resultatet med 100:

    ```S1 DTU / S2 DTU * 100 = 20 / 50 * 100 = 40```

    Resultatet är den relativa prestandaskillnaden mellan de två beräkningsstorlekarna i procent. Om resursanvändningen inte överstiger det här beloppet kan arbetsbelastningen passa in i den lägre beräkningsstorleken. Du måste dock titta på alla intervall av resursanvändningsvärden och i procent bestämma hur ofta din databasarbetsbelastning skulle passa in i den lägre beräkningsstorleken. Följande fråga matar ut passningsprocenten per resursdimension, baserat på tröskelvärdet på 40 procent som vi beräknade i det här exemplet:

   ```sql
    SELECT
        (COUNT(database_name) - SUM(CASE WHEN avg_cpu_percent >= 40 THEN 1 ELSE 0 END) * 1.0) / COUNT(database_name) AS 'CPU Fit Percent',
        (COUNT(database_name) - SUM(CASE WHEN avg_log_write_percent >= 40 THEN 1 ELSE 0 END) * 1.0) / COUNT(database_name) AS 'Log Write Fit Percent',
        (COUNT(database_name) - SUM(CASE WHEN avg_data_io_percent >= 40 THEN 1 ELSE 0 END) * 1.0) / COUNT(database_name) AS 'Physical Data IO Fit Percent'
    FROM sys.resource_stats
    WHERE database_name = 'userdb1' AND start_time > DATEADD(day, -7, GETDATE());
    ```

    Baserat på din databastjänstnivå kan du bestämma om din arbetsbelastning passar in i den lägre beräkningsstorleken. Om ditt mål för databasarbetsbelastning är 99,9 procent och föregående fråga returnerar värden som är större än 99,9 procent för alla tre resursdimensionerna, passar arbetsbelastningen sannolikt in i den lägre beräkningsstorleken.

    Om du tittar på passningsprocenten får du också insikt i om du ska flytta till nästa högre beräkningsstorlek för att uppfylla ditt mål. Användardb1 visar till exempel följande CPU-användning för den senaste veckan:

   | Genomsnittlig CPU-procent | Maximal CPU-procent |
   | --- | --- |
   | 24.5 |100,00 |

    Den genomsnittliga processorn är ungefär en fjärdedel av gränsen för beräkningsstorleken, vilket skulle passa långt in i databasens beräkningsstorlek. Men det maximala värdet visar att databasen når gränsen för beräkningsstorleken. Behöver du flytta till nästa högre beräkningsstorlek? Titta på hur många gånger din arbetsbelastning når 100 procent och jämför den sedan med ditt mål för databasarbetsbelastningen.

    ```sql
    SELECT
        (COUNT(database_name) - SUM(CASE WHEN avg_cpu_percent >= 100 THEN 1 ELSE 0 END) * 1.0) / COUNT(database_name) AS 'CPU fit percent'
        ,(COUNT(database_name) - SUM(CASE WHEN avg_log_write_percent >= 100 THEN 1 ELSE 0 END) * 1.0) / COUNT(database_name) AS 'Log write fit percent'
        ,(COUNT(database_name) - SUM(CASE WHEN avg_data_io_percent >= 100 THEN 1 ELSE 0 END) * 1.0) / COUNT(database_name) AS 'Physical data IO fit percent'
        FROM sys.resource_stats
        WHERE database_name = 'userdb1' AND start_time > DATEADD(day, -7, GETDATE());
    ```

    Om den här frågan returnerar ett värde som är mindre än 99,9 procent för någon av de tre resursdimensionerna bör du överväga att antingen flytta till nästa högre beräkningsstorlek eller använda programjusteringstekniker för att minska belastningen på SQL-databasen.

4. Den här övningen tar också hänsyn till din beräknade arbetsbelastningsökning i framtiden.

För elastiska pooler kan du övervaka individuella databaser i poolen med de tekniker som beskrivs i det här avsnittet. Men du kan också övervaka poolen som helhet. Mer information finns i [Övervaka och hantera en elastisk pool](sql-database-elastic-pool-manage-portal.md).

### <a name="maximum-concurrent-requests"></a>Maximala samtidiga begäranden

Om du vill se antalet samtidiga begäranden kör du den här Transact-SQL-frågan i SQL-databasen:

```sql
SELECT COUNT(*) AS [Concurrent_Requests]
FROM sys.dm_exec_requests R;
```

Om du vill analysera arbetsbelastningen för en lokal SQL Server-databas ändrar du den här frågan för att filtrera på den specifika databas som du vill analysera. Om du till exempel har en lokal databas med namnet MyDatabase returnerar den här Transact-SQL-frågan antalet samtidiga begäranden i databasen:

```sql
SELECT COUNT(*) AS [Concurrent_Requests]
FROM sys.dm_exec_requests R
INNER JOIN sys.databases D ON D.database_id = R.database_id
AND D.name = 'MyDatabase';
```

Detta är bara en ögonblicksbild vid en enda tidpunkt. För att få en bättre förståelse för din arbetsbelastning och samtidiga krav begäran, måste du samla in många prover över tiden.

### <a name="maximum-concurrent-logins"></a>Maximalt antal samtidiga inloggningar

Du kan analysera dina användar- och applikationsmönster för att få en uppfattning om frekvensen av inloggningar. Du kan också köra verkliga laster i en testmiljö för att se till att du inte träffar den här eller andra gränser som vi diskuterar i den här artikeln. Det finns inte en enda fråga eller dynamisk hanteringsvy (DMV) som kan visa dig samtidiga inloggningsantal eller historik.

Om flera klienter använder samma anslutningssträng autentiserar tjänsten varje inloggning. Om 10 användare samtidigt ansluter till en databas med samma användarnamn och lösenord, skulle det finnas 10 samtidiga inloggningar. Den här gränsen gäller endast varaktigheten av inloggningen och autentiseringen. Om samma 10 användare ansluter till databasen sekventiellt, skulle antalet samtidiga inloggningar aldrig vara större än 1.

> [!NOTE]
> För närvarande gäller den här gränsen inte för databaser i elastiska pooler.

### <a name="maximum-sessions"></a>Maximala sessioner

Om du vill se antalet aktuella aktiva sessioner kör du den här Transact-SQL-frågan i SQL-databasen:

```sql
SELECT COUNT(*) AS [Sessions]
FROM sys.dm_exec_connections
```

Om du analyserar en lokal SQL Server-arbetsbelastning ändrar du frågan så att den fokuserar på en viss databas. Den här frågan hjälper dig att avgöra möjliga sessionsbehov för databasen om du funderar på att flytta den till Azure SQL Database.

```sql
SELECT COUNT(*) AS [Sessions]
FROM sys.dm_exec_connections C
INNER JOIN sys.dm_exec_sessions S ON (S.session_id = C.session_id)
INNER JOIN sys.databases D ON (D.database_id = S.database_id)
WHERE D.name = 'MyDatabase'
```

Återigen returnerar dessa frågor ett antal point-in-time. Om du samlar in flera exempel över tid har du den bästa förståelsen för din sessionsanvändning.

För SQL Database-analys kan du få historisk statistik om sessioner genom att fråga [sys.resource_stats-vyn](https://msdn.microsoft.com/library/dn269979.aspx) och granska **active_session_count** kolumn.

## <a name="monitoring-query-performance"></a>Övervaka frågeprestanda

Långsamma eller tidskrävande frågor kan förbruka betydande systemresurser. Det här avsnittet visar hur du använder dynamiska hanteringsvyer för att identifiera några vanliga frågeprestandaproblem. En äldre men ändå användbar referens för felsökning är [felsökningsprestandaproblemen i SQL Server 2008](https://download.microsoft.com/download/D/B/D/DBDE7972-1EB9-470A-BA18-58849DB3EB3B/TShootPerfProbs2008.docx) på Microsoft TechNet.

### <a name="finding-top-n-queries"></a>Hitta de vanligaste N-frågorna

I följande exempel returneras information om de fem vanligaste frågorna rangordnade efter genomsnittlig CPU-tid. I det här exemplet sammanställs frågorna enligt frågehhen, så att logiskt likvärdiga frågor grupperas efter deras kumulativa resursförbrukning.

```sql
SELECT TOP 5 query_stats.query_hash AS "Query Hash",
    SUM(query_stats.total_worker_time) / SUM(query_stats.execution_count) AS "Avg CPU Time",
     MIN(query_stats.statement_text) AS "Statement Text"
FROM
    (SELECT QS.*,
        SUBSTRING(ST.text, (QS.statement_start_offset/2) + 1,
            ((CASE statement_end_offset
                WHEN -1 THEN DATALENGTH(ST.text)
                ELSE QS.statement_end_offset END
            - QS.statement_start_offset)/2) + 1) AS statement_text
FROM sys.dm_exec_query_stats AS QS
CROSS APPLY sys.dm_exec_sql_text(QS.sql_handle) as ST) as query_stats
GROUP BY query_stats.query_hash
ORDER BY 2 DESC;
```

### <a name="monitoring-blocked-queries"></a>Övervaka blockerade frågor

Långsamma eller tidskrävande frågor kan bidra till överdriven resursförbrukning och vara en följd av blockerade frågor. Orsaken till blockeringen kan vara dålig programdesign, dåliga frågeplaner, bristen på användbara index och så vidare. Du kan använda vyn sys.dm_tran_locks för att få information om den aktuella låsaktiviteten i din Azure SQL-databas. Till exempel kod, se [sys.dm_tran_locks (Transact-SQL)](https://msdn.microsoft.com/library/ms190345.aspx) i SQL Server Books Online.

### <a name="monitoring-query-plans"></a>Övervaka frågeplaner

En ineffektiv frågeplan kan också öka CPU-förbrukningen. I följande exempel används [vyn sys.dm_exec_query_stats](https://msdn.microsoft.com/library/ms189741.aspx) för att avgöra vilken fråga som använder den mest kumulativa processorn.

```sql
SELECT
    highest_cpu_queries.plan_handle,
    highest_cpu_queries.total_worker_time,
    q.dbid,
    q.objectid,
    q.number,
    q.encrypted,
    q.[text]
FROM
    (SELECT TOP 50
        qs.plan_handle,
        qs.total_worker_time
    FROM
        sys.dm_exec_query_stats qs
ORDER BY qs.total_worker_time desc) AS highest_cpu_queries
CROSS APPLY sys.dm_exec_sql_text(plan_handle) AS q
ORDER BY highest_cpu_queries.total_worker_time DESC;
```

## <a name="see-also"></a>Se även

[Introduktion till SQL Database](sql-database-technical-overview.md)
