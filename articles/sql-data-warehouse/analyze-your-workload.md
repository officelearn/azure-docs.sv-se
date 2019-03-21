---
title: Analysera din arbetsbelastning – Azure SQL Data Warehouse | Microsoft Docs
description: Tekniker för att analysera fråga prioritering för arbetsbelastningen i Azure SQL Data Warehouse.
services: sql-data-warehouse
author: ronortloff
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: workload management
ms.date: 03/13/2019
ms.author: rortloff
ms.reviewer: jrasnick
ms.openlocfilehash: 7b5ca738ef71e25dfe5e71a1983d701bb8868fe5
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/18/2019
ms.locfileid: "57896814"
---
# <a name="analyze-your-workload-in-azure-sql-data-warehouse"></a>Analysera din arbetsbelastning i Azure SQL Data Warehouse

Tekniker för att analysera din arbetsbelastning i Azure SQL Data Warehouse.

## <a name="resource-classes"></a>Resursklasser

SQL Data Warehouse ger resursklasser för att tilldela systemresurser till frågor.  Mer information om resursklasser finns [resursklasser arbetsbelastning resurshantering](resource-classes-for-workload-management.md).  Frågor väntar om resursklass som tilldelats en fråga behöver fler resurser än vad som är tillgängliga.

## <a name="queued-query-detection-and-other-dmvs"></a>Köade fråga identifiering och andra DMV: er

Du kan använda den `sys.dm_pdw_exec_requests` DMV att identifiera frågor som väntar i kö samtidighet. Frågor som väntar på ett fack för samtidighet har statusen **pausats**.

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

* **LocalQueriesConcurrencyResourceType**: Frågor som finns utanför ramen för samtidighet fack. DMV frågor och systemet fungerar som `SELECT @@VERSION` är exempel på lokala frågor.
* **UserConcurrencyResourceType**: Frågor som finns inom ramen för samtidighet fack. Frågor mot slutanvändare tabeller representerar exempel som använder den här resurstypen.
* **DmsConcurrencyResourceType**: Väntar som härrör från dataflyttningsåtgärder.
* **BackupConcurrencyResourceType**: Den här vänta indikerar att en databas säkerhetskopieras. Det maximala värdet för den här resurstypen är 1. Om flera säkerhetskopieringar har begärts på samma gång, de andra kön.

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

Den `sys.dm_pdw_resource_waits` DMV visar wait-information för en viss fråga. Resursen väntetid tidsmått den väntar på resurser tillhandahållas. Signal väntetid är den tid det tar för de underliggande SQL-servrarna att schemalägga frågan till Processorn.

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

Du kan också använda den `sys.dm_pdw_resource_waits` DMV beräkna hur många samtidighetsfack har beviljats.

```sql
SELECT  SUM([concurrency_slots_used]) as total_granted_slots
FROM    sys.[dm_pdw_resource_waits]
WHERE   [state]           = 'Granted'
AND     [resource_class] is not null
AND     [session_id]     <> session_id()
;
```

Den `sys.dm_pdw_wait_stats` DMV kan användas för historisk trendanalys av väntar.

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

Mer information om hur du hanterar databasanvändare och säkerhet finns i [skydda en databas i SQL Data Warehouse](sql-data-warehouse-overview-manage-security.md). Mer information om hur större resursklasser kan förbättra kvaliteten för grupperade columnstore-index, finns i [bygga om index för att förbättra segmentkvaliteten](sql-data-warehouse-tables-index.md#rebuilding-indexes-to-improve-segment-quality).
