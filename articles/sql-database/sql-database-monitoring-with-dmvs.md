---
title: "Övervaka Azure SQL Database med dynamiska hanteringsvyer | Microsoft Docs"
description: "Lär dig att identifiera och diagnostisera vanliga prestandaproblem med dynamiska hanteringsvyer för att övervaka Microsoft Azure SQL Database."
services: sql-database
author: CarlRabeler
manager: craigg
ms.service: sql-database
ms.custom: monitor & tune
ms.topic: article
ms.date: 01/10/2017
ms.author: carlrab
ms.openlocfilehash: 8185e2748f4aeec4343fa649d8b54958cdcb262a
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/16/2018
---
# <a name="monitoring-azure-sql-database-using-dynamic-management-views"></a>Övervaka Azure SQL Database med dynamiska hanteringsvyer
Microsoft Azure SQL Database gör det möjligt för en delmängd av dynamiska hanteringsvyer att diagnostisera prestandaproblem som orsakas av blockerade eller långvariga frågor, flaskhalsar för resurser, dåliga frågeplaner och så vidare. Det här avsnittet innehåller information om hur du identifierar vanliga prestandaproblem med dynamiska hanteringsvyer.

SQL Database stöder delvis tre kategorier av dynamiska hanteringsvyer:

* Databasrelaterade dynamiska hanteringsvyer.
* Körningen-relaterade dynamiska hanteringsvyer.
* Relaterad dynamiska hanteringsvyer.

Detaljerad information om dynamiska hanteringsvyer finns [vyer för hantering av dynamiska och funktioner (Transact-SQL)](https://msdn.microsoft.com/library/ms188754.aspx) i SQL Server Books Online.

## <a name="permissions"></a>Behörigheter
I SQL-databas, frågar en dynamisk hanteringsvy kräver **databasen VISNINGSSTATUS** behörigheter. Den **databasen VISNINGSSTATUS** behörighet returnerar information om alla objekt i den aktuella databasen.
Att bevilja de **databasen VISNINGSSTATUS** för en viss databasanvändare kör följande fråga:

```GRANT VIEW DATABASE STATE TO database_user; ```

Dynamiska hanteringsvyer returnera information om tillstånd i en lokal SQL Server-instans. De returnera information om logiska databasen bara i SQL-databas.

## <a name="calculating-database-size"></a>Beräkning av databasens storlek
Följande fråga returnerar storleken på databasen (i megabyte):

```
-- Calculates the size of the database.
SELECT SUM(reserved_page_count)*8.0/1024
FROM sys.dm_db_partition_stats;
GO
```

Följande fråga returnerar storleken på enskilda objekt (i megabyte) i databasen:

```
-- Calculates the size of individual database objects.
SELECT sys.objects.name, SUM(reserved_page_count) * 8.0 / 1024
FROM sys.dm_db_partition_stats, sys.objects
WHERE sys.dm_db_partition_stats.object_id = sys.objects.object_id
GROUP BY sys.objects.name;
GO
```

## <a name="monitoring-connections"></a>Övervakar anslutningar
Du kan använda den [sys.dm_exec_connections](https://msdn.microsoft.com/library/ms181509.aspx) vyn för att hämta information om anslutningar till en specifik Azure SQL Database-server och information om varje anslutning. Dessutom kan den [sys.dm_exec_sessions](https://msdn.microsoft.com/library/ms176013.aspx) vy är användbar när du hämtar information om alla aktiva användaranslutningar och interna aktiviteter.
Följande fråga hämtar information om den aktuella anslutningen:

```
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
> När du kör den **sys.dm_exec_requests** och **sys.dm_exec_sessions vyer**, om du har **databasen VISNINGSSTATUS** behörighet på databasen, hittar du alla verkställande sessioner på databasen, annars kan du se endast den aktuella sessionen.
> 
> 

## <a name="monitoring-query-performance"></a>Övervaka prestanda för frågor
Långsamt eller länge kör frågor kan du använda betydande systemresurser. Det här avsnittet beskrivs hur du använder dynamiska hanteringsvyer för att identifiera några vanliga problem med frågeprestanda. En äldre men ändå bra referens för felsökning, är den [felsökning av problem med prestanda i SQL Server 2008](http://download.microsoft.com/download/D/B/D/DBDE7972-1EB9-470A-BA18-58849DB3EB3B/TShootPerfProbs2008.docx) artikel på Microsoft TechNet.

### <a name="finding-top-n-queries"></a>Hitta främsta frågor
I följande exempel returnerar information om de översta fem frågor som rangordnas av Genomsnittlig CPU-tid. Det här exemplet aggregerar frågor enligt deras fråga-hash, så att logiskt motsvarande frågorna grupperas efter deras kumulativa resursförbrukning.

```
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
Långsam eller långvariga frågor kan bidra till hög resursförbrukning och en följd av blockerade frågor. Orsak till spärr kan vara dålig programdesign, felaktig frågeplaner, bristande användbar index och så vidare. Du kan använda vyn sys.dm_tran_locks för att få information om den aktuella låsning aktiviteten i Azure SQL-databasen. Exempelkod finns i avsnittet [sys.dm_tran_locks (Transact-SQL)](https://msdn.microsoft.com/library/ms190345.aspx) i SQL Server Books Online.

### <a name="monitoring-query-plans"></a>Övervakningsprogram för frågan
En ineffektiv frågeplan kan också öka processoranvändningen. I följande exempel används den [sys.dm_exec_query_stats](https://msdn.microsoft.com/library/ms189741.aspx) vyn för att avgöra vilka frågan använder mest kumulativa Processorn.

```
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
[Introduktion till SQL-databas](sql-database-technical-overview.md)

