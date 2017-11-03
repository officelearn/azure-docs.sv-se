---
title: Analysera din arbetsbelastning - Azure SQL Data Warehouse | Microsoft Docs
description: "Metoder för att analysera fråga prioritering för din arbetsbelastning i Azure SQL Data Warehouse."
services: sql-data-warehouse
documentationcenter: NA
author: sqlmojo
manager: jhubbard
editor: 
ms.assetid: ef170f39-ae24-4b04-af76-53bb4c4d16d3
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: performance
ms.date: 10/23/2017
ms.author: joeyong;barbkess;kavithaj
ms.openlocfilehash: 98617f6b8366662e52d00420adc4c81abffc598d
ms.sourcegitcommit: b979d446ccbe0224109f71b3948d6235eb04a967
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/25/2017
---
# <a name="analyze-your-workload"></a>Analysera din arbetsbelastning
Metoder för att analysera fråga prioritering för din arbetsbelastning i Azure SQL Data Warehouse.

## <a name="workload-groups"></a>Arbetsbelastningsgrupper 
SQL Data Warehouse implementerar resursklasser med hjälp av arbetsbelastningsgrupper. Det finns totalt åtta arbetsbelastningsgrupper som styr beteendet för resursklasser över olika DWU-storleken. För alla DWU använder SQL Data Warehouse bara fyra grupperna åtta arbetsbelastning. Detta är meningsfullt eftersom varje arbetsbelastningsgruppen har tilldelats någon av fyra resursklasser: smallrc mediumrc, largerc, eller xlargerc. Vikten av att förstå grupperna arbetsbelastning är att vissa av dessa arbetsbelastningsgrupper som har angetts till högre *vikten*. Vikten används för CPU schemaläggning. Frågor som körs med hög prioritet får tre gånger så mycket CPU-cykler än de med medelhög prioritet. Därför avgör samtidighet fack mappningar också CPU-prioritet. När en fråga förbrukar 16 eller flera platser, körs det som hög prioritet.

Följande tabell visar vikten mappningar för varje arbetsbelastning.

### <a name="workload-group-mappings-to-concurrency-slots-and-importance"></a>Arbetsbelastningen gruppmappningar till samtidighet platser och betydelse

| Arbetsbelastningsgrupper | Concurrency fack mappning | MB / Distribution (elasticitet) | MB / Distribution (beräkning) | Vikten mappning |
|:---------------:|:------------------------:|:------------------------------:|:---------------------------:|:------------------:|
| SloDWGroupC00   | 1                        |    100                         | 250                         | Medel             |
| SloDWGroupC01   | 2                        |    200                         | 500                         | Medel             |
| SloDWGroupC02   | 4                        |    400                         | 1000                        | Medel             |
| SloDWGroupC03   | 8                        |    800                         | 2000                        | Medel             |
| SloDWGroupC04   | 16                       |  1,600                         | 4000                        | Hög               |
| SloDWGroupC05   | 32                       |  3,200                         | 8000                        | Hög               |
| SloDWGroupC06   | 64                       |  6,400                         | 16,000                      | Hög               |
| SloDWGroupC07   | 128                      | 12,800                         | 32,000                      | Hög               |
| SloDWGroupC08   | 256                      | 25,600                         | 64,000                      | Hög               |

<!-- where are the allocation and consumption of concurrency slots charts? -->
Från den **fördelningen och förbrukningen av samtidighet fack** diagram, ser du att en DW500 1, 4, 8 och 16 samtidighet fack för smallrc, mediumrc, largerc och xlargerc, respektive. Du kan slå dessa värden upp i det föregående att hitta vikten för varje resurs.

### <a name="dw500-mapping-of-resource-classes-to-importance"></a>DW500 mappning av resursklasser betydelse
| Resursklass | Arbetsbelastningsgruppen | Concurrency-platser som används | MB / Distribution | Betydelse |
|:-------------- |:-------------- |:----------------------:|:-----------------:|:---------- |
| smallrc        | SloDWGroupC00  | 1                      | 100               | Medel     |
| mediumrc       | SloDWGroupC02  | 4                      | 400               | Medel     |
| largerc        | SloDWGroupC03  | 8                      | 800               | Medel     |
| xlargerc       | SloDWGroupC04  | 16                     | 1,600             | Hög       |
| staticrc10     | SloDWGroupC00  | 1                      | 100               | Medel     |
| staticrc20     | SloDWGroupC01  | 2                      | 200               | Medel     |
| staticrc30     | SloDWGroupC02  | 4                      | 400               | Medel     |
| staticrc40     | SloDWGroupC03  | 8                      | 800               | Medel     |
| staticrc50     | SloDWGroupC03  | 16                     | 1,600             | Hög       |
| staticrc60     | SloDWGroupC03  | 16                     | 1,600             | Hög       |
| staticrc70     | SloDWGroupC03  | 16                     | 1,600             | Hög       |
| staticrc80     | SloDWGroupC03  | 16                     | 1,600             | Hög       |

## <a name="view-workload-groups"></a>Visa grupper av arbetsbelastning
Du kan använda följande DMV-frågan granskar skillnaderna i minnet resursallokeringen i detalj ur resursstyrningen eller för att analysera active och historisk användning av arbetsbelastningsgrupper när du felsöker.

```sql
WITH rg
AS
(   SELECT  
     pn.name                                AS node_name
    ,pn.[type]                              AS node_type
    ,pn.pdw_node_id                         AS node_id
    ,rp.name                                AS pool_name
    ,rp.max_memory_kb*1.0/1024              AS pool_max_mem_MB
    ,wg.name                                AS group_name
    ,wg.importance                          AS group_importance
    ,wg.request_max_memory_grant_percent    AS group_request_max_memory_grant_pcnt
    ,wg.max_dop                             AS group_max_dop
    ,wg.effective_max_dop                   AS group_effective_max_dop
    ,wg.total_request_count                 AS group_total_request_count
    ,wg.total_queued_request_count          AS group_total_queued_request_count
    ,wg.active_request_count                AS group_active_request_count
    ,wg.queued_request_count                AS group_queued_request_count
    FROM    sys.dm_pdw_nodes_resource_governor_workload_groups wg
    JOIN    sys.dm_pdw_nodes_resource_governor_resource_pools rp    
            ON  wg.pdw_node_id  = rp.pdw_node_id
            AND wg.pool_id      = rp.pool_id
    JOIN    sys.dm_pdw_nodes pn
            ON    wg.pdw_node_id    = pn.pdw_node_id
    WHERE   wg.name like 'SloDWGroup%'
    AND     rp.name    = 'SloDWPool'
)
SELECT  pool_name
,       pool_max_mem_MB
,       group_name
,       group_importance
,       (pool_max_mem_MB/100)*group_request_max_memory_grant_pcnt AS max_memory_grant_MB
,       node_name
,       node_type
,       group_total_request_count
,       group_total_queued_request_count
,       group_active_request_count
,       group_queued_request_count
FROM    rg
ORDER BY
        node_name
,       group_request_max_memory_grant_pcnt
,       group_importance
;
```

## <a name="queued-query-detection-and-other-dmvs"></a>Köade frågan identifiering och andra av DMV: er
Du kan använda den `sys.dm_pdw_exec_requests` DMV att identifiera frågor som väntar i en kö samtidighet. Frågar väntar på en plats för samtidighet har statusen **avbruten**.

```sql
SELECT  r.[request_id]                           AS Request_ID
,       r.[status]                               AS Request_Status
,       r.[submit_time]                          AS Request_SubmitTime
,       r.[start_time]                           AS Request_StartTime
,       DATEDIFF(ms,[submit_time],[start_time])  AS Request_InitiateDuration_ms
,       r.resource_class                         AS Request_resource_class
FROM    sys.dm_pdw_exec_requests r
;
```

Roller för hantering av arbetsbelastning kan visas med `sys.database_principals`.

```sql
SELECT  ro.[name]           AS [db_role_name]
FROM    sys.database_principals ro
WHERE   ro.[type_desc]      = 'DATABASE_ROLE'
AND     ro.[is_fixed_role]  = 0
;
```

Följande fråga visar vilken roll som varje användare har tilldelats.

```sql
SELECT  r.name AS role_principal_name
,       m.name AS member_principal_name
FROM    sys.database_role_members rm
JOIN    sys.database_principals AS r            ON rm.role_principal_id      = r.principal_id
JOIN    sys.database_principals AS m            ON rm.member_principal_id    = m.principal_id
WHERE   r.name IN ('mediumrc','largerc','xlargerc')
;
```

SQL Data Warehouse har följande vänta typer:

* **LocalQueriesConcurrencyResourceType**: frågor som är placerade utanför samtidighet fack ramen. DMV frågor och system fungerar som `SELECT @@VERSION` är exempel på lokala frågor.
* **UserConcurrencyResourceType**: frågor som visas inuti samtidighet fack ramen. Frågor mot tabeller slutanvändarens representerar exempel som använder den här resurstypen.
* **DmsConcurrencyResourceType**: Väntar till följd av dataflyttsåtgärderna.
* **BackupConcurrencyResourceType**: den här vänta anger att en databas säkerhetskopieras. Det maximala värdet för den här resurstypen är 1. Om flera säkerhetskopieringar har begärts samtidigt, kommer de andra kö.

Den `sys.dm_pdw_waits` DMV kan användas för att se vilka resurser som väntar på en begäran.

```sql
SELECT  w.[wait_id]
,       w.[session_id]
,       w.[type]                                           AS Wait_type
,       w.[object_type]
,       w.[object_name]
,       w.[request_id]
,       w.[request_time]
,       w.[acquire_time]
,       w.[state]
,       w.[priority]
,       SESSION_ID()                                       AS Current_session
,       s.[status]                                         AS Session_status
,       s.[login_name]
,       s.[query_count]
,       s.[client_id]
,       s.[sql_spid]
,       r.[command]                                        AS Request_command
,       r.[label]
,       r.[status]                                         AS Request_status
,       r.[submit_time]
,       r.[start_time]
,       r.[end_compile_time]
,       r.[end_time]
,       DATEDIFF(ms,r.[submit_time],r.[start_time])        AS Request_queue_time_ms
,       DATEDIFF(ms,r.[start_time],r.[end_compile_time])   AS Request_compile_time_ms
,       DATEDIFF(ms,r.[end_compile_time],r.[end_time])     AS Request_execution_time_ms
,       r.[total_elapsed_time]
FROM    sys.dm_pdw_waits w
JOIN    sys.dm_pdw_exec_sessions s  ON w.[session_id] = s.[session_id]
JOIN    sys.dm_pdw_exec_requests r  ON w.[request_id] = r.[request_id]
WHERE    w.[session_id] <> SESSION_ID()
;
```

Den `sys.dm_pdw_resource_waits` DMV visar endast de resursen väntar som används av en given fråga. Väntetiden för resursen endast mäter den tid som väntar på resurser som ska tillhandahållas, till skillnad från signal väntetiden, vilket är den tid det tar för de underliggande SQL-servrarna att schemalägga frågan till Processorn.

```sql
SELECT  [session_id]
,       [type]
,       [object_type]
,       [object_name]
,       [request_id]
,       [request_time]
,       [acquire_time]
,       DATEDIFF(ms,[request_time],[acquire_time])  AS acquire_duration_ms
,       [concurrency_slots_used]                    AS concurrency_slots_reserved
,       [resource_class]
,       [wait_id]                                   AS queue_position
FROM    sys.dm_pdw_resource_waits
WHERE    [session_id] <> SESSION_ID()
;
```
Du kan också använda den `sys.dm_pdw_resource_waits` DMV beräkna hur många samtidiga platser har beviljats.

```sql
SELECT  SUM([concurrency_slots_used]) as total_granted_slots 
FROM    sys.[dm_pdw_resource_waits] 
WHERE   [state]           = 'Granted' 
AND     [resource_class] is not null
AND     [session_id]     <> session_id()
;
```

Den `sys.dm_pdw_wait_stats` DMV kan användas för analys av historiska trender för väntar.

```sql
SELECT   w.[pdw_node_id]
,        w.[wait_name]
,        w.[max_wait_time]
,        w.[request_count]
,        w.[signal_time]
,        w.[completed_count]
,        w.[wait_time]
FROM    sys.dm_pdw_wait_stats w
;
```

## <a name="next-steps"></a>Nästa steg
Mer information om hur du hanterar databasanvändare och säkerhet finns [skydda en databas i SQL Data Warehouse][Secure a database in SQL Data Warehouse]. Mer information om hur större resursklasser kan förbättra kvaliteten för grupperade columnstore-index, finns [bygga om index för att förbättra kvaliteten segment].

<!--Image references-->

<!--Article references-->
[Secure a database in SQL Data Warehouse]: ./sql-data-warehouse-overview-manage-security.md
[bygga om index för att förbättra kvaliteten segment]: ./sql-data-warehouse-tables-index.md#rebuilding-indexes-to-improve-segment-quality
[Secure a database in SQL Data Warehouse]: ./sql-data-warehouse-overview-manage-security.md

<!--MSDN references-->
[Managing Databases and Logins in Azure SQL Database]:https://msdn.microsoft.com/library/azure/ee336235.aspx

<!--Other Web references-->
