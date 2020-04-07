---
title: Data Warehouse Units (DWUs) i Azure Synapse Analytics (tidigare SQL DW)
description: Rekommendationer om hur du väljer det perfekta antalet informationslagerenheter (DWUs) för att optimera pris och prestanda och hur du ändrar antalet enheter.
services: synapse-analytics
author: mlee3gsd
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 11/22/2019
ms.author: martinle
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: 62cf1f369cbde372e82e7c3ffe26473f09668bc7
ms.sourcegitcommit: bd5fee5c56f2cbe74aa8569a1a5bce12a3b3efa6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/06/2020
ms.locfileid: "80742552"
---
# <a name="data-warehouse-units-dwus"></a>Data warehouseenheter (DWUs)

Rekommendationer om hur du väljer det perfekta antalet informationslagerenheter (DWUs) för att optimera pris och prestanda och hur du ändrar antalet enheter.

## <a name="what-are-data-warehouse-units"></a>Vad är datalagerenheter

En [Synapse SQL-pool](sql-data-warehouse-overview-what-is.md#synapse-sql-pool-in-azure-synapse) representerar en samling analytiska resurser som etableras. Analytiska resurser definieras som en kombination av CPU, minne och IO.

Dessa tre resurser buntas in i enheter av beräkningsskala som kallas Data Warehouse Units (DWUs). En DWU representerar ett abstrakt, normaliserat mått för beräkningsresurser och prestanda.

En ändring av din servicenivå ändrar antalet DWUs som är tillgängliga för systemet, vilket i sin tur justerar prestanda och kostnad för ditt system.

För högre prestanda kan du öka antalet informationslagerenheter. Minska datalagerenheter för mindre prestanda. Lagrings- och beräkningskostnader faktureras separat, så ändringar av informationslagerenheter påverkar inte lagringskostnaderna.

Prestanda för informationslagerenheter baseras på dessa arbetsbelastningsmått:

- Hur snabbt en vanlig SQL-poolfråga kan skanna ett stort antal rader och sedan utföra en komplex aggregering. Den här åtgärden är I/O- och CPU-intensiv.
- Hur snabbt SQL-poolen kan få in data från Azure Storage Blobbar eller Azure Data Lake. Den här åtgärden är nätverks- och CPU-intensiv.
- Hur snabbt [`CREATE TABLE AS SELECT`](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse) T-SQL-kommandot kan kopiera en tabell. Den här åtgärden innebär att läsa data från lagring, distribuera dem över noderna på enheten och skriva till lagring igen. Den här åtgärden är CPU, IO och nätverksintensiv.

Öka nas:

- Linjärt ändrar systemets prestanda för skanningar, aggregeringar och CTAS-satser
- Ökar antalet läsare och författare för PolyBase-belastningsåtgärder
- Ökar det maximala antalet samtidiga frågor och samtidighetsplatser.

## <a name="service-level-objective"></a>Servicenivåmål

Servicenivåmålet (SLO) är skalbarhetsinställningen som bestämmer kostnads- och prestandanivån för DIN SQL-pool. Tjänstnivåerna för Gen2 SQL-pool mäts i datalagerenheter (DWU), till exempel DW2000c.

I T-SQL bestämmer inställningen SERVICE_OBJECTIVE tjänstnivån för DIN SQL-pool.

```sql
CREATE DATABASE mySQLDW
( EDITION = 'Datawarehouse'
 ,SERVICE_OBJECTIVE = 'DW1000c'
)
;
```

## <a name="capacity-limits"></a>Kapacitetsbegränsningar

Varje SQL-server (till exempel myserver.database.windows.net) har en [DTU-kvot (Database Transaction Unit)](../../sql-database/sql-database-service-tiers-dtu.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) som tillåter ett visst antal informationslagerenheter. Mer information finns i [kapacitetsgränserna för arbetsbelastningshantering](sql-data-warehouse-service-capacity-limits.md#workload-management).

## <a name="how-many-data-warehouse-units-do-i-need"></a>Hur många informationslagerenheter behöver jag

Det idealiska antalet informationslagerenheter beror i hög grad på din arbetsbelastning och hur mycket data du har läst in i systemet.

Steg för att hitta den bästa DWU för din arbetsbelastning:

1. Börja med att välja en mindre DWU.
2. Övervaka programmets prestanda när du testar databelastningar i systemet och observera antalet DWUs som valts jämfört med den prestanda du observerar.
3. Identifiera eventuella ytterligare krav för periodiska perioder av toppaktivitet. Arbetsbelastningar som visar betydande toppar och dalar i aktivitet kan behöva skalas ofta.

SQL-pool är ett skalningssystem som kan etablera stora mängder beräkning och fråga stora mängder data.

Om du vill se dess verkliga funktioner för skalning, särskilt vid större DWUs, rekommenderar vi att du skalar datauppsättningen när du skalar för att säkerställa att du har tillräckligt med data för att mata processorerna. För skalningstestning rekommenderar vi att du använder minst 1 TB.

> [!NOTE]
>
> Frågeprestanda ökar bara med mer parallellisering om arbetet kan delas mellan beräkningsnoder. Om du upptäcker att skalning inte ändrar dina resultat kan du behöva ställa in tabelldesignen och/eller dina frågor. Vägledning för frågejustering finns i [Hantera användarfrågor](cheat-sheet.md).

## <a name="permissions"></a>Behörigheter

Om du ändrar informationslagerenheterna krävs de behörigheter som beskrivs i [ALTER DATABASE](/sql/t-sql/statements/alter-database-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest).

Inbyggda roller för Azure-resurser som SQL DB Contributor och SQL Server Contributor kan ändra DWU-inställningar.

## <a name="view-current-dwu-settings"></a>Visa aktuella DWU-inställningar

Så här visar du den aktuella DWU-inställningen:

1. Öppna SQL Server Object Explorer i Visual Studio.
2. Anslut till huvuddatabasen som är associerad med den logiska SQL Database-servern.
3. Välj i vyn sys.database_service_objectives dynamisk hantering. Här är ett exempel:

```sql
SELECT  db.name [Database]
,        ds.edition [Edition]
,        ds.service_objective [Service Objective]
FROM    sys.database_service_objectives   AS ds
JOIN    sys.databases                     AS db ON ds.database_id = db.database_id
;
```

## <a name="change-data-warehouse-units"></a>Ändra lagerenheter

### <a name="azure-portal"></a>Azure Portal

Så här ändrar du DWUs:

1. Öppna [Azure-portalen,](https://portal.azure.com)öppna databasen och klicka på **Skala**.

2. Under **Skala**flyttar du skjutreglaget åt vänster eller höger för att ändra DWU-inställningen.

3. Klicka på **Spara**. Ett bekräftelsemeddelande visas. Klicka på **Ja** för att bekräfta eller **Nej** för att avbryta.

### <a name="powershell"></a>PowerShell

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Om du vill ändra DWUs använder du cmdleten [Set-AzSqlDatabase](/powershell/module/az.sql/set-azsqldatabase) PowerShell. I följande exempel anges servicenivåmålet för DW1000c för databasen MySQLDW som finns på servern MyServer.

```Powershell
Set-AzSqlDatabase -DatabaseName "MySQLDW" -ServerName "MyServer" -RequestedServiceObjectiveName "DW1000c"
```

Mer information finns i [PowerShell-cmdlets för SQL Data Warehouse](sql-data-warehouse-reference-powershell-cmdlets.md)

### <a name="t-sql"></a>T-SQL

Med T-SQL kan du visa de aktuella DWU-inställningarna, ändra inställningarna och kontrollera förloppet.

Så här ändrar du DWUs:

1. Anslut till huvuddatabasen som är associerad med den logiska SQL Database-servern.
2. Använd [ALTER DATABASE](/sql/t-sql/statements/alter-database-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) TSQL-satsen. I följande exempel anges servicenivåmålet för DW1000c för databasen MySQLDW.

```Sql
ALTER DATABASE MySQLDW
MODIFY (SERVICE_OBJECTIVE = 'DW1000c')
;
```

### <a name="rest-apis"></a>REST API:er

Om du vill ändra DWUs använder du REST-API:et [Skapa eller uppdatera databas.](/rest/api/sql/databases/createorupdate) I följande exempel anges servicenivåmålet för DW1000c för databasen MySQLDW, som finns på servern MyServer. Servern finns i en Azure-resursgrupp med namnet ResourceGroup1.

```
PUT https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Sql/servers/{server-name}/databases/{database-name}?api-version=2014-04-01-preview HTTP/1.1
Content-Type: application/json; charset=UTF-8

{
    "properties": {
        "requestedServiceObjectiveName": DW1000c
    }
}
```

Fler REST API-exempel finns i [REST API:er för SQL Data Warehouse](sql-data-warehouse-manage-compute-rest-api.md).

## <a name="check-status-of-dwu-changes"></a>Kontrollera status för DWU-ändringar

DWU-ändringar kan ta flera minuter att slutföra. Om du skalar automatiskt bör du överväga att implementera logik för att säkerställa att vissa åtgärder har slutförts innan du fortsätter med en annan åtgärd.

Genom att kontrollera databastillståndet via olika slutpunkter kan du implementera automatisering på rätt sätt. Portalen meddelar när en åtgärd har slutförts och databasernas aktuella tillstånd men tillåter inte programmatisk kontroll av tillstånd.

Du kan inte kontrollera databastillståndet för utskalningsåtgärder med Azure-portalen.

Så här kontrollerar du statusen för DWU-ändringar:

1. Anslut till huvuddatabasen som är associerad med den logiska SQL Database-servern.

1. Skicka följande fråga för att kontrollera databastillståndet.

    ```sql
    SELECT    *
    FROM      sys.databases
    ;
    ```

1. Skicka följande fråga för att kontrollera status för åtgärden

    ```sql
    SELECT    *
    FROM      sys.dm_operation_status
    WHERE     resource_type_desc = 'Database'
    AND       major_resource_id = 'MySQLDW'
    ;
    ```

Den här DMV returnerar information om olika hanteringsåtgärder på din SQL-pool, till exempel åtgärden och tillståndet för åtgärden, som antingen är IN_PROGRESS eller SLUTFÖRD.

## <a name="the-scaling-workflow"></a>Skalningsarbetsflödet

När du startar en skalningsåtgärd dödar systemet först alla öppna sessioner och rullar tillbaka alla öppna transaktioner för att säkerställa ett konsekvent tillstånd. För skalningsåtgärder sker skalning endast när den här transaktionsåterfällningen har slutförts.  

- För en uppskalningsåtgärd kopplar systemet bort alla beräkningsnoder, etablerar ytterligare beräkningsnoder och sätter sedan tillbaka till lagringslagret.
- För en nedskalningsåtgärd kopplar systemet bort alla beräkningsnoder och sätter sedan tillbaka endast de nödvändiga noderna i lagringslagret.

## <a name="next-steps"></a>Nästa steg

Mer information om hur du hanterar prestanda finns i [Resursklasser för arbetsbelastningshantering](resource-classes-for-workload-management.md) och [Begränsningar för minne och samtidighet](memory-concurrency-limits.md).
