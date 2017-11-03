---
title: "Pausa, återuppta, skala med T-SQL i Azure SQL Data Warehouse | Microsoft Docs"
description: "Transact-SQL (T-SQL) uppgifter till skalbar prestanda genom att justera dwu: er. Spara kostnader genom att skala tillbaka under tider med låg belastning."
services: sql-data-warehouse
documentationcenter: NA
author: hirokib
manager: jhubbard
editor: 
ms.assetid: a970d939-2adf-4856-8a78-d4fe8ab2cceb
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.date: 03/30/2017
ms.author: elbutter;barbkess
ms.openlocfilehash: 9221d72ecf8ab2ba8b04e4bc97eeef7157817cca
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="manage-compute-power-in-azure-sql-data-warehouse-t-sql"></a>Hantera datorkraft i Azure SQL Data Warehouse (T-SQL)
> [!div class="op_single_selector"]
> * [Översikt](sql-data-warehouse-manage-compute-overview.md)
> * [Portal](sql-data-warehouse-manage-compute-portal.md)
> * [PowerShell](sql-data-warehouse-manage-compute-powershell.md)
> * [REST](sql-data-warehouse-manage-compute-rest-api.md)
> * [TSQL](sql-data-warehouse-manage-compute-tsql.md)
>
>

<a name="current-dwu-bk"></a>

## <a name="view-current-dwu-settings"></a>Visa aktuella DWU-inställningar
Visa det aktuella DWU-inställningen för dina databaser:

1. Öppna SQL Server Object Explorer i Visual Studio.
2. Ansluta till master-databasen som är kopplade till den logiska SQL Database-servern.
3. Välj vyn sys.database_service_objectives dynamisk hantering. Här är ett exempel: 

```sql
SELECT
    db.name [Database]
,   ds.edition [Edition]
,   ds.service_objective [Service Objective]
FROM
    sys.database_service_objectives ds
JOIN
    sys.databases db ON ds.database_id = db.database_id
```

<a name="scale-dwu-bk"></a>
<a name="scale-compute-bk"></a>

## <a name="scale-compute"></a>Skala bearbetning
[!INCLUDE [SQL Data Warehouse scale DWUs description](../../includes/sql-data-warehouse-scale-dwus-description.md)]

Ändra de dwu: er:

1. Ansluta till master-databasen som är associerade med din logiska SQL Database-server.
2. Använd den [ALTER DATABASE] [ ALTER DATABASE] TSQL-instruktion. I följande exempel anger servicenivåmålet till DW1000 för MySQLDW-databasen. 

```Sql
ALTER DATABASE MySQLDW
MODIFY (SERVICE_OBJECTIVE = 'DW1000')
;
```

<a name="check-database-state-bk"></a>

## <a name="check-database-state-and-operation-progress"></a>Kontrollera databasen tillstånd och åtgärden pågår

1. Ansluta till master-databasen som är associerade med din logiska SQL Database-server.
2. Skicka fråga om du vill kontrollera databasens status

```sql
SELECT *
FROM
sys.databases
```

3. Skicka fråga om du vill kontrollera status för åtgärden

```sql
SELECT *
FROM
    sys.dm_operation_status
WHERE
    resource_type_desc = 'Database'
AND 
    major_resource_id = 'MySQLDW'
```

Den här DMV returnerar information om olika hanteringsåtgärder på ditt SQL Data Warehouse, till exempel igen och tillstånd för processen som ska antingen vara IN_PROGRESS eller SLUTFÖRTS.



<a name="next-steps-bk"></a>

## <a name="next-steps"></a>Nästa steg
Andra hanteringsåtgärder finns [översikt över][Management overview].

<!--Image references-->

<!--Article references-->
[Service capacity limits]: ./sql-data-warehouse-service-capacity-limits.md
[Management overview]: ./sql-data-warehouse-overview-manage.md
[Manage compute power overview]: ./sql-data-warehouse-manage-compute-overview.md

<!--MSDN references-->

[ALTER DATABASE]: https://msdn.microsoft.com/library/mt204042.aspx


<!--Other Web references-->

[Azure portal]: http://portal.azure.com/
