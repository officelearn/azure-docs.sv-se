---
title: Analysera din arbetsbelastning
description: Tekniker för att analysera frågeprioritering för din arbetsbelastning i Azure Synapse Analytics.
services: synapse-analytics
author: ronortloff
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 02/04/2020
ms.author: rortloff
ms.reviewer: jrasnick
ms.custom: azure-synapse
ms.openlocfilehash: a382ef2d93f10e69569ecbbed1399f256a7afbb3
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "80351211"
---
# <a name="analyze-your-workload-in-azure-synapse-analytics"></a>Analysera din arbetsbelastning i Azure Synapse Analytics

Tekniker för att analysera din SQL Analytics-arbetsbelastning i Azure Synapse Analytics.

## <a name="resource-classes"></a>Resursklasser

SQL Analytics tillhandahåller resursklasser för att tilldela systemresurser till frågor.  Mer information om resursklasser finns i [Resursklasser & arbetsbelastningshantering](resource-classes-for-workload-management.md).  Frågor väntar om resursklassen som tilldelats en fråga behöver mer resurser än vad som för närvarande är tillgängligt.

## <a name="queued-query-detection-and-other-dmvs"></a>Identifiering av köfrågor och andra DMVs

Du kan `sys.dm_pdw_exec_requests` använda DMV för att identifiera frågor som väntar i en samtidig kö. Frågor som väntar på en samtidighetsplats har statusen **pausad**.

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

Roller för arbetsbelastningshantering `sys.database_principals`kan visas med .

```sql
SELECT  ro.[name]           AS [db_role_name]
FROM    sys.database_principals ro
WHERE   ro.[type_desc]      = 'DATABASE_ROLE'
AND     ro.[is_fixed_role]  = 0
;
```

Följande fråga visar vilken roll varje användare har tilldelats.

```sql
SELECT  r.name AS role_principal_name
,       m.name AS member_principal_name
FROM    sys.database_role_members rm
JOIN    sys.database_principals AS r            ON rm.role_principal_id      = r.principal_id
JOIN    sys.database_principals AS m            ON rm.member_principal_id    = m.principal_id
WHERE   r.name IN ('mediumrc','largerc','xlargerc')
;
```

SQL Analytics har följande vänta typer:

* **LocalQueriesConcurrencyResourceType**: Frågor som sitter utanför ramverket för samtidighetsfack. DMV-frågor och systemfunktioner `SELECT @@VERSION` som är exempel på lokala frågor.
* **UserConcurrencyResourceType**: Frågor som sitter i ramverket för samtidighetsfack. Frågor mot slutanvändartabeller representerar exempel som använder den här resurstypen.
* **DmsConcurrencyResourceType**: Väntar på resultat från dataförflyttningsåtgärder.
* **BackupConcurrencyResourceType**: Den här väntan anger att en databas säkerhetskopieras. Det maximala värdet för den här resurstypen är 1. Om flera säkerhetskopior har begärts samtidigt, köar de andra. I allmänhet rekommenderar vi en minsta tid mellan på varandra följande ögonblicksbilder på 10 minuter. 

`sys.dm_pdw_waits` DMV kan användas för att se vilka resurser en begäran väntar på.

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

`sys.dm_pdw_resource_waits` DMV visar vänta information för en viss fråga. Resursvändtiden mäter den tid som väntar på att resurser ska tillhandahållas. Signal väntetid är den tid det tar för underliggande SQL-servrar att schemalägga frågan på processorn.

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

Du kan också `sys.dm_pdw_resource_waits` använda DMV beräkna hur många samtidighetsplatser som har beviljats.

```sql
SELECT  SUM([concurrency_slots_used]) as total_granted_slots
FROM    sys.[dm_pdw_resource_waits]
WHERE   [state]           = 'Granted'
AND     [resource_class] is not null
AND     [session_id]     <> session_id()
;
```

`sys.dm_pdw_wait_stats` DMV kan användas för historisk trendanalys av väntetider.

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

Mer information om hur du hanterar databasanvändare och säkerhet finns [i Skydda en databas i SQL Analytics](sql-data-warehouse-overview-manage-security.md). Mer information om hur större resursklasser kan förbättra indexkvaliteten i klustrade columnstore finns i [Återskapa index för att förbättra segmentkvaliteten](sql-data-warehouse-tables-index.md#rebuilding-indexes-to-improve-segment-quality).
