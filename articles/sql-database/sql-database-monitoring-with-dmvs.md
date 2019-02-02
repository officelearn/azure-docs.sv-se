---
title: 'Övervakning av prestanda Azure SQL Database med DMV: er | Microsoft Docs'
description: Lär dig att identifiera och diagnostisera vanliga prestandaproblem med dynamiska hanteringsvyer för att övervaka Microsoft Azure SQL Database.
services: sql-database
ms.service: sql-database
ms.subservice: performance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: juliemsft
ms.author: jrasnick
ms.reviewer: carlrab
manager: craigg
ms.date: 12/19/2018
ms.openlocfilehash: 371632a28d22583f8b206e4d8b9d2b6b4e510ab0
ms.sourcegitcommit: ba035bfe9fab85dd1e6134a98af1ad7cf6891033
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/01/2019
ms.locfileid: "55563957"
---
# <a name="monitoring-performance-azure-sql-database-using-dynamic-management-views"></a>Övervakning av prestanda Azure SQL Database med dynamiska hanteringsvyer

Microsoft Azure SQL Database gör det möjligt för en delmängd av dynamiska hanteringsvyer att diagnostisera problem med prestanda, vilket kan orsakas av blockerade eller långvariga frågor, flaskhalsar för resurser, dålig frågeplaner och så vidare. Det här avsnittet innehåller information om hur du identifierar vanliga prestandaproblem med dynamiska hanteringsvyer.

SQL Database stöder delvis tre kategorier av dynamiska hanteringsvyer:

- Databasrelaterade dynamiska hanteringsvyer.
- Körning-relaterade dynamiska hanteringsvyer.
- Transaktionen att göra dynamiska hanteringsvyer.

Detaljerad information om dynamiska hanteringsvyer finns [Dynamic Management Views och Functions (Transact-SQL)](https://msdn.microsoft.com/library/ms188754.aspx) i SQL Server Books Online.

## <a name="permissions"></a>Behörigheter

I SQL-databas, frågar en dynamisk hanteringsvy kräver **visa DATABASTILLSTÅND** behörigheter. Den **visa DATABASTILLSTÅND** behörighet returnerar information om alla objekt i den aktuella databasen.
Att bevilja de **visa DATABASTILLSTÅND** behörighet till en viss databasanvändare, kör följande fråga:

```sql
GRANT VIEW DATABASE STATE TO database_user;
```

I en instans av en lokal SQL Server returnera dynamiska hanteringsvyer server statusinformation. Returnerar information om din aktuella logiska databasen endast i SQL-databas.

## <a name="identify-cpu-performance-issues"></a>Identifiera problem med CPU-prestanda

Om processoranvändningen är över 80 procent under längre tidsperioder, bör följande felsökningssteg:

### <a name="the-cpu-issue-is-occurring-now"></a>CPU-problemet inträffar nu

Om problemet inträffar just nu, finns det två möjliga scenarier:

#### <a name="many-individual-queries-that-cumulatively-consume-high-cpu"></a>Många enskilda frågor som förbrukar kumulativt hög CPU

Använd följande fråga för att identifiera övre fråga hashvärden:

```sql
PRINT '-- top 10 Active CPU Consuming Queries (aggregated)--';
SELECT TOP 10 GETDATE() runtime, *
FROM(SELECT query_stats.query_hash, SUM(query_stats.cpu_time) 'Total_Request_Cpu_Time_Ms', SUM(logical_reads) 'Total_Request_Logical_Reads', MIN(start_time) 'Earliest_Request_start_Time', COUNT(*) 'Number_Of_Requests', SUBSTRING(REPLACE(REPLACE(MIN(query_stats.statement_text), CHAR(10), ' '), CHAR(13), ' '), 1, 256) AS "Statement_Text"
     FROM(SELECT req.*, SUBSTRING(ST.text, (req.statement_start_offset / 2)+1, ((CASE statement_end_offset WHEN -1 THEN DATALENGTH(ST.text)ELSE req.statement_end_offset END-req.statement_start_offset)/ 2)+1) AS statement_text
          FROM sys.dm_exec_requests AS req
               CROSS APPLY sys.dm_exec_sql_text(req.sql_handle) AS ST ) AS query_stats
     GROUP BY query_hash) AS t
ORDER BY Total_Request_Cpu_Time_Ms DESC;
```

#### <a name="long-running-queries-that-consume-cpu-are-still-running"></a>Långvariga frågor som förbrukar CPU körs fortfarande

Använd följande fråga för att identifiera dessa frågor:

```sql
PRINT '--top 10 Active CPU Consuming Queries by sessions--';
SELECT TOP 10 req.session_id, req.start_time, cpu_time 'cpu_time_ms', OBJECT_NAME(ST.objectid, ST.dbid) 'ObjectName', SUBSTRING(REPLACE(REPLACE(SUBSTRING(ST.text, (req.statement_start_offset / 2)+1, ((CASE statement_end_offset WHEN -1 THEN DATALENGTH(ST.text)ELSE req.statement_end_offset END-req.statement_start_offset)/ 2)+1), CHAR(10), ' '), CHAR(13), ' '), 1, 512) AS statement_text
FROM sys.dm_exec_requests AS req
     CROSS APPLY sys.dm_exec_sql_text(req.sql_handle) AS ST
ORDER BY cpu_time DESC;
GO
```

### <a name="the-cpu-issue-occurred-in-the-past"></a>CPU-problemet uppstod i förflutna

Om problemet uppstod tidigare och du vill rot analys av grundorsaken, använda [Query Store](https://docs.microsoft.com/sql/relational-databases/performance/monitoring-performance-by-using-the-query-store). Användare med åtkomst till databasen kan använda T-SQL för att köra frågor mot Query Store-data.  Query Store standardkonfigurationer använder en Granulariteten för 1 timme.  Använd följande fråga för att kontrollera aktiviteter för hög processor frågor som förbrukar. Den här frågan returnerar de översta 15 CPU konsumerande frågorna.  Kom ihåg att ändra `rsi.start_time >= DATEADD(hour, -2, GETUTCDATE()`:

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

När du har identifierat problematiska frågor är det dags att justera dessa frågor för att minska CPU-användning.  Om du inte har tid att justera frågorna, kan du också välja att uppgradera på SLO av databasen för att undvika problemet.

## <a name="identify-io-performance-issues"></a>Identifiera prestandaproblem för i/o

När du identifierar problem med i/o-prestanda, är de främsta vänta typer som är associerad med i/o-problem:

- `PAGEIOLATCH_*`

  För problem med i/o-fil (inklusive `PAGEIOLATCH_SH`, `PAGEIOLATCH_EX`, `PAGEIOLATCH_UP`).  Om namnet på vänta har **i/o** , den pekar på ett i/o-problem. Om det finns inga **i/o** i sidans spärr vänta namn som den pekar på en annan typ av problem (till exempel tempdb konkurrens).

- `WRITE_LOG`

  För transaktionen logg-i/o-problem.

### <a name="if-the-io-issue-is-occurring-right-now"></a>Om i/o-problemet inträffar just nu

Använd den [sys.dm_exec_requests](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-requests-transact-sql) eller [sys.dm_os_waiting_tasks](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-os-waiting-tasks-transact-sql) att se den `wait_type` och `wait_time`.

#### <a name="identify-data-and-log-io-usage"></a>Identifiera data och logga i/o-användning

Använd följande fråga för att identifiera data och logga i/o-användning. Om de data eller logg-i/o är högre än 80%, innebär det att användare har använt den tillgängliga i/o för tjänstnivån SQL DB.

```sql
SELECT end_time, avg_data_io_percent, avg_log_write_percent
FROM sys.dm_db_resource_stats
ORDER BY end_time DESC;
```

Om i/o-gränsen har uppnåtts, har du två alternativ:

- Alternativ 1: Uppgradera beräkningsstorleken eller tjänstenivån
- Alternativ 2: Identifiera och justera frågor som förbrukar de flesta I/O.

#### <a name="view-buffer-related-io-using-the-query-store"></a>Visa buffert-relaterade-i/o med hjälp av Query Store

För alternativ 2, kan du använda följande fråga mot Query Store för buffert-relaterade-i/o för att visa de senaste två timmarna av spårade aktivitet:

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

#### <a name="view-total-log-io-for-writelog-waits"></a>Visa totala logg-i/o för WRITELOG väntar

Om wait-typen är `WRITELOG`, Använd följande fråga Visa totala logg-IO med instruktionen:

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

När du identifierar problem med i/o-prestanda upp vänta som är associerade med `tempdb` problem är `PAGELATCH_*` (inte `PAGEIOLATCH_*`). Dock `PAGELATCH_*` väntar inte alltid betyder du har `tempdb` konkurrens.  Den här vänta kan också innebär att du måste användarobjekt sidan för datakonkurrens på grund av samtidiga begäranden som riktar in sig på sidan för samma data. Ytterligare Bekräfta `tempdb` konkurrens, Använd [sys.dm_exec_requests](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-requests-transact-sql) att bekräfta att wait_resource värdet börjar med `2:x:y` där 2 är `tempdb` är databas-id `x` är fil-id och `y` är sid-id.  

För tempdb konkurrens, en vanlig metod är att minska eller skriva programkod som förlitar sig på `tempdb`.  Vanliga `tempdb` användningsområdena omfattar:

- Temporära tabeller
- tabellvariabler
- Tabellvärderade parametrar
- Version store användning (särskilt kopplade tidskrävande transaktioner)
- Frågor som har frågeplaner som använder typer, hash-kopplingar och spolar

### <a name="top-queries-that-use-table-variables-and-temporary-tables"></a>De viktigaste frågorna som använder tabellvariabler och temporära tabeller

Använd följande fråga för att identifiera de viktigaste frågorna som använder tabellvariabler och temporära tabeller:

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

### <a name="identify-long-running-transactions"></a>Identifiera långvariga transaktioner

Använd följande fråga för att identifiera lång köra transaktioner. Långvariga transaktioner förhindra version store rensning.

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

## <a name="identify-memory-grant-wait-performance-issues"></a>Identifiera minne bevilja vänta prestandaproblem

Om högsta vänta typ är `RESOURCE_SEMAHPORE` och du inte har en hög CPU-användning problem, du kan ha en minne bevilja att vänta på problemet.

### <a name="determine-if-a-resourcesemahpore-wait-is-a-top-wait"></a>Ta reda på om en `RESOURCE_SEMAHPORE` Vänteperioden är översta väntan

Använd följande fråga för att avgöra om en `RESOURCE_SEMAHPORE` Vänteperioden är översta väntan

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

### <a name="identity-high-memory-consuming-statements"></a>Identitet hög minne förbrukar instruktioner

Använd följande fråga för att identifiera hög minne förbrukar instruktioner:

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

### <a name="identify-the-top-10-active-memory-grants"></a>Identifiera de översta 10 active minne ger

Använd följande fråga för att identifiera de översta 10 active minne ger:

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

## <a name="calculating-database-and-objects-sizes"></a>Beräkning av storlekar för databasen och objekt

Följande fråga returnerar storleken på din databas (i megabyte):

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

## <a name="monitoring-connections"></a>Övervakar anslutningar

Du kan använda den [sys.dm_exec_connections](https://msdn.microsoft.com/library/ms181509.aspx) vy för att hämta information om anslutningar till en specifik Azure SQL Database-server och information om varje anslutning. Dessutom kan den [sys.dm_exec_sessions](https://msdn.microsoft.com/library/ms176013.aspx) vyn är användbar vid hämtning av information om alla aktiva användaranslutningar och interna aktiviteter.
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
> När du kör den **sys.dm_exec_requests** och **sys.dm_exec_sessions vyer**, om du har **visa DATABASTILLSTÅND** behörighet på databasen, visas alla körning sessioner på databasen. Annars ser du bara den aktuella sessionen.

## <a name="monitor-resource-use"></a>Övervaka Resursanvändning

Du kan övervaka resource användning med [SQL Database Query Performance Insight](sql-database-query-performance.md) och [Query Store](https://msdn.microsoft.com/library/dn817826.aspx).

Du kan också övervaka användning med hjälp av dessa två vyer:

- [sys.dm_db_resource_stats](https://msdn.microsoft.com/library/dn800981.aspx)
- [sys.resource_stats](https://msdn.microsoft.com/library/dn269979.aspx)

### <a name="sysdmdbresourcestats"></a>sys.dm_db_resource_stats

Du kan använda den [sys.dm_db_resource_stats](https://msdn.microsoft.com/library/dn800981.aspx) vyn i varje SQL-databas. Den **sys.dm_db_resource_stats** vyn visar de senaste användning resursdata i förhållande till tjänstnivån. Genomsnittlig procent för processor, data-i/o, skrivs loggen och minne registreras var 15: e sekund och bevaras i timmen.

Eftersom den här vyn ger en mer detaljerad titt på Resursanvändning, använda **sys.dm_db_resource_stats** första för alla aktuella tillstånd analys eller felsökning. Den här frågan visar till exempel den genomsnittliga och högsta resursanvändningen för den aktuella databasen under den senaste timman:

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

### <a name="sysresourcestats"></a>sys.resource_stats

Den [sys.resource_stats](https://msdn.microsoft.com/library/dn269979.aspx) visa i den **master** databasen finns ytterligare information som hjälper dig att övervaka prestanda för din SQL-databas på dess specifika tjänstnivå och beräkna storleken. Data samlas in var femte minut och bibehålls för ungefär 14 dagar. Den här vyn är användbar för en mer långsiktiga historisk analys av hur din SQL-databas använder resurser.

I följande diagram visar CPU Resursanvändning för en Premium-databas med P2 beräkningsstorleken för varje timme i en vecka. Det här diagrammet startas på en måndag visar 5 arbetsdagar och sedan visas en helg när mycket mindre sker på programmet.

![SQL database-Resursanvändning](./media/sql-database-performance-guidance/sql_db_resource_utilization.png)

Från data, den här databasen har en hög CPU-belastning på strax över 50 procent CPU-användning i förhållande till P2 compute storlek (kl tisdagen). Om Processorn är den dominerande faktorn i programprofilen resurs, kan du välja att P2 är rätt beräkningsstorleken för att garantera att arbetsbelastningen alltid får plats. Om du förväntar dig ett program att växa med tiden är det en bra idé att ha en buffert extraresursen så att programmet inte någonsin uppnår sin gräns för prestandanivå. Om du ökar beräkningsstorleken kan du undvika kunden visas fel som kan uppstå när en databas inte har tillräckligt med kraften att bearbeta begäranden effektivt, särskilt i miljöer för känslig. Ett exempel är en databas som har stöd för ett program som ritar webbsidor baserat på resultatet av databasanrop.

Andra programtyper kan tolka i samma diagram på olika sätt. Till exempel om ett program försöker bearbeta löneuppgifter data varje dag och har samma diagram, kan den här typen av ”batchjobb”-modellen göra bra vid en P1 beräkningsstorleken. P1-beräkningsstorleken har 100 dtu: er jämfört med 200 dtu: er på P2 compute storlek. P1-beräkningsstorleken innehåller halva prestanda för P2 olika storlek. Därför är 50 procent av CPU-användning i P2 lika med 100 procent CPU-användning i P1. Om programmet inte har tidsgränser kan det ingen roll om ett jobb tar två timmar eller 2,5 timmar att slutföra, om det går idag. Ett program i den här kategorin kan förmodligen använda en P1 beräkningsstorleken. Du kan dra nytta av det faktum att det är tidsperioder under dagen när Resursanvändning är lägre, så att alla ”big högsta” kan hamnar i någon av de fördjupningar försedda senare under dagen. P1-beräkningsstorleken kan vara bra för den typen av program och spara pengar, förutsatt att jobben kan slutföras på tid på dagen.

Azure SQL Database visar förbrukas resursinformation för varje aktiv databas i den **sys.resource_stats** vy av den **master** databasen i varje server. Data i tabellen sammanställs för 5 minuters intervall. Data kan ta mer än 5 minuter innan den visas i tabellen, så att dessa data är mer användbar för historisk analys i stället för analys i nära realtid med tjänstnivåer Basic, Standard och Premium. Fråga den **sys.resource_stats** visas för att se den senaste historiken för en databas och validera om reservationen du valde levereras önskad prestanda när det behövs.

> [!NOTE]
> Du måste vara ansluten till den **master** databas med SQL Database-server för att fråga **sys.resource_stats** i följande exempel.

Det här exemplet visar hur data i den här vyn visas:

```sql
SELECT TOP 10 *
FROM sys.resource_stats
WHERE database_name = 'resource1'
ORDER BY start_time DESC
```

![Katalogvy sys.resource_stats](./media/sql-database-performance-guidance/sys_resource_stats.png)

I nästa exempel visar olika sätt som du kan använda den **sys.resource_stats** katalogvy att hämta information om hur SQL-databasen använder resurser:

1. Att titta på den senaste veckan resource använder för databas-userdb1 kan du köra den här frågan:

    ```sql
    SELECT *
    FROM sys.resource_stats
    WHERE database_name = 'userdb1' AND
        start_time > DATEADD(day, -7, GETDATE())
    ORDER BY start_time DESC;
    ```

2. Om du vill utvärdera hur väl som passar din arbetsbelastning beräkningsstorleken, måste du granska nedåt i varje aspekt av mätvärden för resurs: CPU, läsningar, skrivningar, antal arbetare och antalet sessioner. Här är en reviderad ställa frågor med **sys.resource_stats** att rapportera genomsnittliga och högsta värden för de här måtten för resursen:

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

3. Med den här informationen om de genomsnittliga och högsta värdena för varje Resursmått, kan du utvärdera hur väl din arbetsbelastning passar in i beräkningsstorleken som du har valt. Vanligtvis medelvärden från **sys.resource_stats** ger dig en god baslinje att använda mot målstorleken. Det bör vara primära mätning-minne. Du kanske använder Standard-tjänstnivå med S2 beräkningsstorleken för ett exempel. Medelvärdet använda procentandelar för processor- och i/o-läsningar och skrivningar är lägre än 40 procent, det genomsnittliga antalet arbetare är under 50 och det genomsnittliga antalet sessioner är under 200. Din arbetsbelastning kan passar beräkningsstorleken S1. Det är enkelt att se om din databas passar in i worker och sessionen gränserna. För att se om en databas passar in i en lägre beräkningsstorleken för CPU, läsningar och skrivningar division DTU-numret på nedre compute storlek efter DTU antalet beräkningsstorleken på din aktuella och sedan multiplicera resultatet med 100:

    ```S1 DTU / S2 DTU * 100 = 20 / 50 * 100 = 40```

    Resultatet är den relativa prestandaskillnaden mellan två compute storlekar i procent. Om din Resursanvändning inte överstiger den mängden, kan arbetsbelastningen passar i lägre beräkningsstorleken. Men behöver du titta på alla intervall för resursen Använd värden och procent avgör hur ofta din databas-arbetsbelastning skulle passa lägre beräkningsstorleken. Följande fråga visar anpassa procentandelen per resursdimension, baserat på tröskelvärdet på 40 procent som vi har beräknats i det här exemplet:

   ```sql
    SELECT
        (COUNT(database_name) - SUM(CASE WHEN avg_cpu_percent >= 40 THEN 1 ELSE 0 END) * 1.0) / COUNT(database_name) AS 'CPU Fit Percent',
        (COUNT(database_name) - SUM(CASE WHEN avg_log_write_percent >= 40 THEN 1 ELSE 0 END) * 1.0) / COUNT(database_name) AS 'Log Write Fit Percent',
        (COUNT(database_name) - SUM(CASE WHEN avg_data_io_percent >= 40 THEN 1 ELSE 0 END) * 1.0) / COUNT(database_name) AS 'Physical Data IO Fit Percent'
    FROM sys.resource_stats
    WHERE database_name = 'userdb1' AND start_time > DATEADD(day, -7, GETDATE());
    ```

    Baserat på din tjänstenivå för databasen kan avgöra du om din arbetsbelastning passar in i lägre beräkningsstorleken. Om ditt mål för databas-arbetsbelastning är 99,9 procent av föregående fråga returnerar värden som är större än 99,9 procent för alla tre resource dimensioner, passar din arbetsbelastning som sannolikt in lägre beräkningsstorleken.

    Titta på Anpassa procentandelen får också inblick i om du ska flytta till nästa högre beräkningsstorleken att uppfylla dina mål. Userdb1 visar till exempel följande CPU-användning för den senaste veckan:

   | Genomsnittlig CPU-procent | Högsta CPU-procent |
   | --- | --- |
   | 24.5 |100.00 |

    Den genomsnittliga CPU handlar om en fjärdedel av gränsen på beräkningsstorleken, vilket skulle passar in i beräkningsstorleken för databasen. Men det högsta värdet visar att databasen når gränsen på beräkningsstorleken. Behöver du flytta till nästa högre beräkningsstorleken? Titta på hur många gånger din arbetsbelastning når 100 procent och jämför den med ditt mål för databas-arbetsbelastning.

    ```sql
    SELECT
        (COUNT(database_name) - SUM(CASE WHEN avg_cpu_percent >= 100 THEN 1 ELSE 0 END) * 1.0) / COUNT(database_name) AS 'CPU fit percent'
        ,(COUNT(database_name) - SUM(CASE WHEN avg_log_write_percent >= 100 THEN 1 ELSE 0 END) * 1.0) / COUNT(database_name) AS 'Log write fit percent'
        ,(COUNT(database_name) - SUM(CASE WHEN avg_data_io_percent >= 100 THEN 1 ELSE 0 END) * 1.0) / COUNT(database_name) AS 'Physical data IO fit percent'
        FROM sys.resource_stats
        WHERE database_name = 'userdb1' AND start_time > DATEADD(day, -7, GETDATE());
    ```

    Om den här frågan returnerar ett värde mindre än 99,9 procent av något av tre resursdimensioner bör du antingen flytta till nästa högre beräkningsstorleken eller använda programmet justering teknik för att minska belastningen på SQL-databasen.

4. Den här övningen tar också hänsyn planerade arbetsbelastning-ökning i framtiden.

För elastiska pooler kan du övervaka individuella databaser i poolen med de tekniker som beskrivs i det här avsnittet. Men du kan också övervaka poolen som helhet. Mer information finns i [Övervaka och hantera en elastisk pool](sql-database-elastic-pool-manage-portal.md).

### <a name="maximum-concurrent-requests"></a>Högsta antal samtidiga begäranden

Om du vill se hur många samtidiga begäranden, kör du den här Transact-SQL-frågan på din SQL-databas:

    ```sql
    SELECT COUNT(*) AS [Concurrent_Requests]
    FROM sys.dm_exec_requests R;
    ```

Ändra den här frågan att filtrera på en viss databas som du vill analysera för att analysera arbetsbelastningen på en lokal SQL Server-databas. Till exempel om du har en lokal databas med namnet MyDatabase kan returnerar den här Transact-SQL-frågan antalet samtidiga begäranden i databasen:

    ```sql
    SELECT COUNT(*) AS [Concurrent_Requests]
    FROM sys.dm_exec_requests R
    INNER JOIN sys.databases D ON D.database_id = R.database_id
    AND D.name = 'MyDatabase';
    ```

Detta är bara en ögonblicksbild på ett ställe i tid. För att få en bättre förståelse för din arbetsbelastning och samtidig begäran krav, måste du samla in många exempel över tid.

### <a name="maximum-concurrent-logins"></a>Maximal samtidiga inloggningar

Du kan analysera dina användar- och mönster för att få en uppfattning om frekvensen för inloggningar. Du kan också köra verkliga belastningar i en testmiljö för att se till att du inte får det här eller andra begränsningar som beskrivs i den här artikeln. Det finns inte en enskild fråga eller dynamisk hanteringsvy (DMV) som kan visa samtidiga inloggningen räknar eller historik.

Om flera klienter använder samma anslutningssträng, autentiserar tjänsten varje inloggning. Om 10 användare samtidigt ansluta till en databas med samma användarnamn och lösenord, skulle det vara 10 samtidiga inloggningar. Den här gränsen gäller enbart för varaktigheten för inloggning och autentisering. Om samma 10 användare ansluta till databasen sekventiellt, blir antalet samtidiga inloggningar aldrig större än 1.

> [!NOTE]
> Den här begränsningen gäller för närvarande inte för databaser i elastiska pooler.

### <a name="maximum-sessions"></a>Maximalt antal sessioner

Om du vill se antalet aktuella aktiva sessioner, kör du den här Transact-SQL-frågan på din SQL-databas:

    SELECT COUNT(*) AS [Sessions]
    FROM sys.dm_exec_connections

Om du analyserar en lokal SQL Server-arbetsbelastning kan du ändra frågan för att fokusera på en viss databas. Den här frågan hjälper dig att avgöra en session behov för databasen om du ska flytta den till Azure SQL Database.

    SELECT COUNT(*)  AS [Sessions]
    FROM sys.dm_exec_connections C
    INNER JOIN sys.dm_exec_sessions S ON (S.session_id = C.session_id)
    INNER JOIN sys.databases D ON (D.database_id = S.database_id)
    WHERE D.name = 'MyDatabase'

De här frågorna returnerar igen, point-in-time-värdet. Om du samlar in flera insamlingar över tid, får du bäst förståelse för din session använder.

För SQL Database-analys, kan du få historiska statistik på sessioner genom att fråga den [sys.resource_stats](https://msdn.microsoft.com/library/dn269979.aspx) vy och granska de **active_session_count** kolumn.

## <a name="monitoring-query-performance"></a>Övervaka prestanda för frågor

Långsamt eller länge körning av frågor kan du använda betydande systemresurser. Det här avsnittet visar hur du använder dynamiska hanteringsvyer för att identifiera några prestandaproblem för vanliga frågor. En äldre, men fortfarande användbart referens för felsökning, är den [Felsöka prestandaproblem i SQL Server 2008](https://download.microsoft.com/download/D/B/D/DBDE7972-1EB9-470A-BA18-58849DB3EB3B/TShootPerfProbs2008.docx) artikel på Microsoft TechNet.

### <a name="finding-top-n-queries"></a>Hitta främsta frågor

I följande exempel returnerar information om de översta fem frågorna rangordnade med Genomsnittlig CPU-tid. Det här exemplet aggregerar frågor enligt deras fråge-hash, så att logiskt motsvarande frågor grupperas efter deras kumulativa resursförbrukningen.

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

Långsam eller långvariga frågor kan bidra till abnorm resursförbrukning och var en följd av blockerade frågor. Orsaken till att den blockerar kan vara dålig programdesign, felaktig frågeplaner, bristen på användbara index och så vidare. Du kan använda sys.dm_tran_locks vyn för att hämta information om den aktuella låsning aktiviteten i din Azure SQL Database. Exempelkod, se [sys.dm_tran_locks (Transact-SQL)](https://msdn.microsoft.com/library/ms190345.aspx) i SQL Server Books Online.

### <a name="monitoring-query-plans"></a>Övervakningsprogram för fråga

En ineffektiv frågeplanen kan också minska CPU-förbrukning. I följande exempel används den [sys.dm_exec_query_stats](https://msdn.microsoft.com/library/ms189741.aspx) vyn för att avgöra vilken fråga använder mest kumulativa Processorn.

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

## <a name="see-also"></a>Se också

[Introduktion till SQL Database](sql-database-technical-overview.md)
