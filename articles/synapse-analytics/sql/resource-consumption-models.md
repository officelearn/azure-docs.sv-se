---
title: Synapse Analytics SQL-resursförbrukning
description: Lär dig mer om Synapse SQL-förbrukningsmodeller i Azure Synapse Analytics.
services: synapse analytics
author: vvasic-msft
ms.service: synapse-analytics
ms.topic: overview
ms.date: 04/15/2020
ms.author: vvasic
ms.reviewer: jrasnick
ms.openlocfilehash: e078893b3bbe0ef5661cd87bad62b320f78ceb5d
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2020
ms.locfileid: "81424862"
---
# <a name="azure-synapse-analytics-sql-resource-consumption"></a>Azure Synapse Analytics SQL-resursförbrukning

I den här artikeln beskrivs resursförbrukningsmodeller för Synapse SQL (förhandsversion).

## <a name="sql-on-demand"></a>SQL på begäran

SQL on-demand är en pay per frågetjänst som inte kräver att du väljer rätt storlek. Systemet justeras automatiskt baserat på dina krav, vilket frigör dig från att hantera din infrastruktur och välja rätt storlek för din lösning.

## <a name="sql-pool---data-warehouse-units-dwus-and-compute-data-warehouse-units-cdwus"></a>SQL-pool - Data Warehouse Units (DWUs) och beräkna datalagerenheter (cDWUs)

Rekommendationer om hur du väljer det perfekta antalet informationslagerenheter (DWUs) för att optimera pris och prestanda och hur du ändrar antalet enheter.

### <a name="what-are-data-warehouse-units"></a>Vad är datalagerenheter

En Synapse SQL-pool representerar en samling analytiska resurser som etableras. Analytiska resurser definieras som en kombination av CPU, minne och IO. Dessa tre resurser buntas in i enheter av beräkningsskala som kallas Data Warehouse Units (DWUs). En DWU representerar ett abstrakt, normaliserat mått för beräkningsresurser och prestanda. En ändring av din servicenivå ändrar antalet DWUs som är tillgängliga för systemet, vilket i sin tur justerar prestanda och kostnad för ditt system.

För högre prestanda kan du öka antalet informationslagerenheter. Minska datalagerenheter för mindre prestanda. Lagrings- och beräkningskostnader faktureras separat, så ändringar av informationslagerenheter påverkar inte lagringskostnaderna.

Prestanda för informationslagerenheter baseras på dessa arbetsbelastningsmått för informationslager:

- Hur snabbt en standarddatalagringsfråga kan skanna ett stort antal rader och sedan utföra en komplex aggregering. Den här åtgärden är I/O- och CPU-intensiv.
- Hur snabbt informationslagret kan inta data från Azure Storage Blobbar eller Azure Data Lake. Den här åtgärden är nätverks- och CPU-intensiv.
- Hur snabbt [`CREATE TABLE AS SELECT`](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) T-SQL-kommandot kan kopiera en tabell. Den här åtgärden innebär att läsa data från lagring, distribuera dem över noderna på enheten och skriva till lagring igen. Den här åtgärden är CPU, IO och nätverksintensiv.

Öka nas:

- Linjärt ändrar systemets prestanda för skanningar, aggregeringar och CTAS-satser
- Ökar antalet läsare och författare för PolyBase-belastningsåtgärder
- Ökar det maximala antalet samtidiga frågor och samtidighetsplatser.

### <a name="service-level-objective"></a>Servicenivåmål

Servicenivåmålet (SLO) är skalbarhetsinställningen som bestämmer kostnads- och prestandanivån för ditt informationslager. Servicenivåerna för Gen2 mäts i beräkningsdatalagerenheter (cDWU), till exempel DW2000c. Gen1-servicenivåer mäts i DWUs, till exempel DW2000.

Servicenivåmålet (SLO) är skalbarhetsinställningen som bestämmer kostnads- och prestandanivån för ditt informationslager. Tjänstnivåerna för Gen2 SQL-pool mäts i datalagerenheter (DWU), till exempel DW2000c.

> [!NOTE]
> Azure SQL Data Warehouse Gen2 har nyligen lagt till ytterligare skalningsfunktioner för att stödja beräkningsnivåer så låga som 100 cDWU. Befintliga informationslager som för närvarande finns på Gen1 och som kräver de lägre beräkningsnivåerna kan nu uppgradera till Gen2 i de regioner som för närvarande är tillgängliga utan extra kostnad.  Om din region ännu inte stöds kan du fortfarande uppgradera till en region som stöds. Mer information finns i [Uppgradera till Gen2](../sql-data-warehouse/upgrade-to-latest-generation.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json).

I T-SQL bestämmer inställningen SERVICE_OBJECTIVE tjänstnivån och prestandanivån för din SQL-pool.

```sql
CREATE DATABASE mySQLDW
(Edition = 'Datawarehouse'
 ,SERVICE_OBJECTIVE = 'DW1000c'
)
;
```

### <a name="performance-tiers-and-data-warehouse-units"></a>Prestandanivåer och informationslagerenheter

Varje prestandanivå använder en något annorlunda måttenhet för sina informationslagerenheter. Den här skillnaden återspeglas på fakturan när skalenheten direkt översätts till fakturering.

- Gen1-datalager mäts i datalagerenheter (DWUs).
- Gen2-datalager mäts i beräkningsdatalagerenheter (cDWUs).

Både DWUs och cDWUs stöder skalning av beräkning upp eller ned och pausar beräkning när du inte behöver använda informationslagret. Dessa operationer är alla on-demand. Gen2 använder en lokal diskbaserad cache på beräkningsnoderna för att förbättra prestanda. När du skalar eller pausar systemet är cacheminnet ogiltigt och därför krävs en period av cacheuppvärmning innan optimal prestanda uppnås.  

När du ökar informationslagerenheter ökar du linjärt datorresurser. Gen2 ger bästa frågeprestanda och högsta skala. Gen2-system gör också det mesta av cachen.

#### <a name="capacity-limits"></a>Kapacitetsbegränsningar

Varje SQL-server (till exempel myserver.database.windows.net) har en [DTU-kvot (Database Transaction Unit)](../../sql-database/sql-database-service-tiers-dtu.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) som tillåter ett visst antal informationslagerenheter. Mer information finns i [kapacitetsgränserna för arbetsbelastningshantering](../sql-data-warehouse/sql-data-warehouse-service-capacity-limits.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json#workload-management).

### <a name="how-many-data-warehouse-units-do-i-need"></a>Hur många informationslagerenheter behöver jag

Det idealiska antalet informationslagerenheter beror i hög grad på din arbetsbelastning och hur mycket data du har läst in i systemet.

Steg för att hitta den bästa DWU för din arbetsbelastning:

1. Börja med att välja en mindre DWU.
2. Övervaka programmets prestanda när du testar databelastningar i systemet och observera antalet DWUs som valts jämfört med den prestanda du observerar.
3. Identifiera eventuella ytterligare krav för periodiska perioder av toppaktivitet. Arbetsbelastningar som visar betydande toppar och dalar i aktivitet kan behöva skalas ofta.

SQL-pool är ett skalningssystem som kan etablera stora mängder beräkning och fråga stora mängder data. Om du vill se dess verkliga funktioner för skalning, särskilt vid större DWUs, rekommenderar vi att du skalar datauppsättningen när du skalar för att säkerställa att du har tillräckligt med data för att mata processorerna. För skalningstestning rekommenderar vi att du använder minst 1 TB.

> [!NOTE]
>
> Frågeprestanda ökar bara med mer parallellisering om arbetet kan delas mellan beräkningsnoder. Om du upptäcker att skalning inte ändrar dina resultat kan du behöva ställa in tabelldesignen och/eller dina frågor. Vägledning för frågejustering finns i [Hantera användarfrågor](../overview-cheat-sheet.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json).

### <a name="permissions"></a>Behörigheter

Om du ändrar informationslagerenheterna krävs de behörigheter som beskrivs i [ALTER DATABASE](/sql/t-sql/statements/alter-database-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest).

Inbyggda roller för Azure-resurser som SQL DB Contributor och SQL Server Contributor kan ändra DWU-inställningar.

#### <a name="view-current-dwu-settings"></a>Visa aktuella DWU-inställningar

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

### <a name="change-data-warehouse-units"></a>Ändra lagerenheter

#### <a name="azure-portal"></a>Azure Portal

Så här ändrar du DWUs:

1. Öppna [Azure-portalen,](https://portal.azure.com)öppna databasen och klicka på **Skala**.

2. Under **Skala**flyttar du skjutreglaget åt vänster eller höger för att ändra DWU-inställningen.

3. Klicka på **Spara**. Ett bekräftelsemeddelande visas. Klicka på **Ja** för att bekräfta eller **Nej** för att avbryta.

#### <a name="powershell"></a>PowerShell

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Om du vill ändra DWUs använder du cmdleten [Set-AzSqlDatabase](/powershell/module/az.sql/set-azsqldatabase?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) PowerShell. I följande exempel anges servicenivåmålet för DW1000 för databasen MySQLDW som finns på servern MyServer.

```Powershell
Set-AzSqlDatabase -DatabaseName "MySQLDW" -ServerName "MyServer" -RequestedServiceObjectiveName "DW1000c"
```

Mer information finns i [PowerShell-cmdlets för SQL Data Warehouse](../sql-data-warehouse/sql-data-warehouse-reference-powershell-cmdlets.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)

#### <a name="t-sql"></a>T-SQL

Med T-SQL kan du visa aktuella DWUsettings, ändra inställningarna och kontrollera förloppet.

Så här ändrar du DWUs:

1. Anslut till huvuddatabasen som är associerad med den logiska SQL Database-servern.
2. Använd [ALTER DATABASE](/sql/t-sql/statements/alter-database-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) TSQL-satsen. I följande exempel anges servicenivåmålet för DW1000c för databasen MySQLDW.

```Sql
ALTER DATABASE MySQLDW
MODIFY (SERVICE_OBJECTIVE = 'DW1000c')
;
```

#### <a name="rest-apis"></a>REST API:er

Om du vill ändra DWUs använder du REST-API:et [Skapa eller uppdatera databas.](/sql/t-sql/statements/alter-database-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) I följande exempel anges servicenivåmålet för DW1000c för databasen MySQLDW, som finns på servern MyServer. Servern finns i en Azure-resursgrupp med namnet ResourceGroup1.

```
PUT https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Sql/servers/{server-name}/databases/{database-name}?api-version=2014-04-01-preview HTTP/1.1
Content-Type: application/json; charset=UTF-8

{
    "properties": {
        "requestedServiceObjectiveName": DW1000
    }
}
```

Fler REST API-exempel finns i [REST API:er för SQL Data Warehouse](../sql-data-warehouse/sql-data-warehouse-manage-compute-rest-api.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json).

### <a name="check-status-of-dwu-changes"></a>Kontrollera status för DWU-ändringar

DWU-ändringar kan ta flera minuter att slutföra. Om du skalar automatiskt bör du överväga att implementera logik för att säkerställa att vissa åtgärder har slutförts innan du fortsätter med en annan åtgärd.

Genom att kontrollera databastillståndet via olika slutpunkter kan du implementera automatisering på rätt sätt. Portalen meddelar när en åtgärd har slutförts och databasernas aktuella tillstånd men tillåter inte programmatisk kontroll av tillstånd.

Du kan inte kontrollera databastillståndet för utskalningsåtgärder med Azure-portalen.

Så här kontrollerar du statusen för DWU-ändringar:

1. Anslut till huvuddatabasen som är associerad med den logiska SQL Database-servern.
2. Skicka följande fråga för att kontrollera databastillståndet.

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

### <a name="the-scaling-workflow"></a>Skalningsarbetsflödet

När du startar en skalningsåtgärd dödar systemet först alla öppna sessioner och rullar tillbaka alla öppna transaktioner för att säkerställa ett konsekvent tillstånd. För skalningsåtgärder sker skalning endast när den här transaktionsåterfällningen har slutförts.  

- För en uppskalningsåtgärd kopplar systemet bort alla beräkningsnoder, etablerar ytterligare beräkningsnoder och sätter sedan tillbaka till lagringslagret.
- För en nedskalningsåtgärd kopplar systemet bort alla beräkningsnoder och sätter sedan tillbaka endast de nödvändiga noderna i lagringslagret.

## <a name="next-steps"></a>Nästa steg

Mer information om hur du hanterar prestanda finns i [Resursklasser för arbetsbelastningshantering](../sql-data-warehouse/resource-classes-for-workload-management.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) och [Begränsningar för minne och samtidighet](../sql-data-warehouse/memory-concurrency-limits.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json).
