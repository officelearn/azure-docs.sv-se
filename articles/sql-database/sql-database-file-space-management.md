---
title: Azure SQL Database utrymme filhantering | Microsoft Docs
description: Den här sidan beskriver hur du hanterar filutrymme med Azure SQL Database och innehåller kodexempel att avgöra om du behöver krympa en databas samt hur du utför en databas för att minska åtgärden.
services: sql-database
author: oslake
manager: craigg
ms.service: sql-database
ms.custom: how-to
ms.topic: conceptual
ms.date: 08/15/2018
ms.author: moslake
ms.openlocfilehash: 498e83e7c312480af6d2eff7d44bd13aee9c55fd
ms.sourcegitcommit: d2f2356d8fe7845860b6cf6b6545f2a5036a3dd6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/16/2018
ms.locfileid: "42054391"
---
# <a name="manage-file-space-in-azure-sql-database"></a>Hantera utrymmet i Azure SQL Database
Den här artikeln beskrivs olika typer av lagringsutrymme i Azure SQL Database och steg som kan utföras när utrymmet som allokerats för databaser och elastiska pooler måste hanteras uttryckligen.

## <a name="overview"></a>Översikt

De flesta mätvärden i storage utrymme visas i Azure portal och följande API: er mäta antalet sidor som inte används för databaser och elastiska pooler i Azure SQL Database:
- Azure Resource Manager baserade mått API: er, inklusive PowerShell [get-mått](https://docs.microsoft.com/powershell/module/azurerm.insights/get-azurermmetric)
- T-SQL: [sys.dm_db_resource_stats](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-db-resource-stats-azure-sql-database)
- T-SQL: [sys.resource_stats](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-resource-stats-azure-sql-database)
- T-SQL: [sys.elastic_pool_resource_stats](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-elastic-pool-resource-stats-azure-sql-database)

Det finns arbetsbelastningmönster där allokeringen av underliggande datafiler för databaser kan bli större än mängden data som används sidor.  Detta kan inträffa när utrymme används ökar och data raderas.  Det beror på att utrymme allokeras inte frigörs automatiskt när data tas bort.  I sådana scenarier kan allokerat utrymme för en databas eller en pool överstiga gränserna för stöds förhindra datatillväxt eller förhindra att prestanda nivåändringar och kräver att minska storleken på datafilerna som ska minska.

SQL DB-tjänsten Komprimera inte automatiskt filer för att frigöra oanvänt allokerade utrymme på grund av den möjliga inverkan på prestanda för databasen.  Kunder kan dock minska datafiler via självbetjäning i taget de önskar genom att följa stegen som beskrivs i [frigöra oanvänt allokerat utrymme](#reclaim-unused-allocated-space). 

> [!NOTE]
> Till skillnad från datafiler och SQL Database-tjänsten automatiskt minskar storleken på loggfiler eftersom åtgärden inte påverkar databasprestanda. 

## <a name="understanding-types-of-storage-space-for-a-database"></a>Förstå typer av lagringsutrymme för en databas

Förstå följande storage utrymme kvantiteter är viktiga för att hantera filutrymme för en databas.

|Databasen kvantitet|Definition|Kommentarer|
|---|---|---|
|**Datautrymme som används**|Mängden utrymme som används för att lagra databasdata på 8 KB sidor.|I allmänhet används utrymme ökar (minskningar) på infogningar (ta bort). I vissa fall, hur mycket diskutrymme ändras inte på infogningar eller tar bort beroende på omfattningen av och mönstret för de data som ingår i åtgärden och den fragmenterade. Till exempel tar bort en rad från varje datasida inte nödvändigtvis minska utrymmet som används.|
|**Data som allokerats**|Mängden formaterad filutrymme som gjorts tillgängliga för att lagra databasdata.|Mängden utrymme som allokerats växer automatiskt, men aldrig minskar när du tar bort. Detta säkerställer att framtida infogningar är snabbare eftersom utrymme inte behöver formateras.|
|**Data som har allokerats men används inte**|Skillnaden mellan mängden data som allokerats och datautrymme som används.|Den här datamängden representerar den maximala mängden ledigt utrymme som kan vara frigöras genom att minska storleken på databasfiler för data.|
|**Maximal datastorlek**|Den maximala mängden utrymme som kan användas för att lagra databasdata.|Mängden data som allokerats kan inte växer dig större än maxstorleken för data.|
||||

Följande diagram illustrerar förhållandet mellan de olika typerna av lagringsutrymme för en databas.

![utrymme lagringstyper och relationer](./media/sql-database-file-space-management/storage-types.png) 

## <a name="query-a-database-for-storage-space-information"></a>Fråga en databas för information om diskutrymme

Följande frågor kan användas för att fastställa storage utrymme kvantiteter för en databas.  

### <a name="database-data-space-used"></a>Databasutrymme data som används
Ändra följande fråga om du vill returnera mängden utrymme i databas data används.  Enheter av frågeresultatet är i MB.

```sql
-- Connect to master
-- Database data space used in MB
SELECT TOP 1 storage_in_megabytes AS DatabaseDataSpaceUsedInMB
FROM sys.resource_stats
WHERE database_name = 'db1'
ORDER BY end_time DESC
```

### <a name="database-data-space-allocated-and-unused-allocated-space"></a>Data databasutrymme allokeras och oanvända allokerat utrymme
Använd följande fråga om du vill returnera mängden data databasutrymme allokeras och mängden outnyttjat utrymme som allokerats.  Enheter av frågeresultatet är i MB.

```sql
-- Connect to database
-- Database data space allocated in MB and database data space allocated unused in MB
SELECT SUM(size/128.0) AS DatabaseDataSpaceAllocatedInMB, 
SUM(size/128.0 - CAST(FILEPROPERTY(name, 'SpaceUsed') AS int)/128.0) AS DatabaseDataSpaceAllocatedUnusedInMB 
FROM sys.database_files
GROUP BY type_desc
HAVING type_desc = 'ROWS'
```
 
### <a name="database-data-max-size"></a>Högsta tillåtna databasstorleken
Ändra följande fråga för att returnera den högsta tillåtna databasstorleken.  Enheter av frågeresultatet är i byte.

```sql
-- Connect to database
-- Database data max size in bytes
SELECT DATABASEPROPERTYEX('db1', 'MaxSizeInBytes') AS DatabaseDataMaxSizeInBytes
```

## <a name="understanding-types-of-storage-space-for-an-elastic-pool"></a>Förstå typer av lagringsutrymme för en elastisk pool

Förstå följande storage utrymme kvantiteter är viktiga för att hantera filutrymme för en elastisk pool.

|Elastisk pool kvantitet|Definition|Kommentarer|
|---|---|---|
|**Datautrymme som används**|Summering av datautrymme som används av alla databaser i den elastiska poolen.||
|**Data som allokerats**|Summan av data som har allokerats av alla databaser i den elastiska poolen.||
|**Data som har allokerats men används inte**|Skillnaden mellan mängden data som allokerats och datautrymme som används av alla databaser i den elastiska poolen.|Den här datamängden representerar den maximala mängden utrymme som allokerats för den elastiska poolen som kan vara frigöras genom att minska storleken på databasfiler för data.|
|**Maximal datastorlek**|Högsta mängden utrymme som kan användas av den elastiska poolen för alla dess databaser.|Det tilldelade utrymmet för den elastiska poolen får inte överskrida den maximala storleken för elastisk pool.  Om detta inträffar kan allokerat utrymme som är oanvända återtas genom att minska storleken på databasfiler för data.|
||||

## <a name="query-an-elastic-pool-for-storage-space-information"></a>Fråga en elastisk pool för information om diskutrymme

Följande frågor kan användas för att fastställa storage utrymme kvantiteter för en elastisk pool.  

### <a name="elastic-pool-data-space-used"></a>Elastisk pool datautrymme används
Ändra följande fråga om du vill returnera mängden utrymme som data i en elastisk pool och används.  Enheter av frågeresultatet är i MB.

```sql
-- Connect to master
-- Elastic pool data space used in MB  
SELECT TOP 1 avg_storage_percent * elastic_pool_storage_limit_mb AS ElasticPoolDataSpaceUsedInMB
FROM sys.elastic_pool_resource_stats
WHERE elastic_pool_name = 'ep1'
ORDER BY end_time DESC
```

### <a name="elastic-pool-data-space-allocated-and-unused-allocated-space"></a>Elastisk pool datautrymme allokeras och oanvända allokerat utrymme

Ändra följande PowerShell-skript för att returnera en tabell som listar det tilldelade utrymmet och oanvända allokerat utrymme för varje databas i en elastisk pool. Tabellen beställningar databaser från de som har störst oanvända allokerat utrymme för att den minsta uppsättningen oanvända allokerat utrymme.  Enheter av frågeresultatet är i MB.  

Resultatet av frågan för att fastställa det tilldelade utrymmet för varje databas i poolen kan läggas till tillsammans att fastställa det totala utrymmet som allokerats för den elastiska poolen. Det tilldelade utrymmet för elastisk pool får inte överskrida den maximala storleken för elastisk pool.  

PowerShell-skriptet kräver SQL Server PowerShell-modulen – Se [ladda ned PowerShell-modulen](https://docs.microsoft.com/sql/powershell/download-sql-server-ps-module?view=sql-server-2017) att installera.

```powershell
# Resource group name
$resourceGroupName = "rg1" 
# Server name
$serverName = "ls2" 
# Elastic pool name
$poolName = "ep1"
# User name for server
$userName = "name"
# Password for server
$password = "password"

# Get list of databases in elastic pool
$databasesInPool = Get-AzureRmSqlElasticPoolDatabase `
    -ResourceGroupName $resourceGroupName `
    -ServerName $serverName `
    -ElasticPoolName $poolName
$databaseStorageMetrics = @()

# For each database in the elastic pool,
# get its space allocated in MB and space allocated unused in MB.
  
foreach ($database in $databasesInPool)
{
    $sqlCommand = "SELECT DB_NAME() as DatabaseName, `
    SUM(size/128.0) AS DatabaseDataSpaceAllocatedInMB, `
    SUM(size/128.0 - CAST(FILEPROPERTY(name, 'SpaceUsed') AS int)/128.0) AS DatabaseDataSpaceAllocatedUnusedInMB `
    FROM sys.database_files `
    GROUP BY type_desc `
    HAVING type_desc = 'ROWS'"
    $serverFqdn = "tcp:" + $serverName + ".database.windows.net,1433"
    $databaseStorageMetrics = $databaseStorageMetrics + 
        (Invoke-Sqlcmd -ServerInstance $serverFqdn `
        -Database $database.DatabaseName `
        -Username $userName `
        -Password $password `
        -Query $sqlCommand)
}
# Display databases in descending order of space allocated unused
Write-Output "`n" "ElasticPoolName: $poolName"
Write-Output $databaseStorageMetrics | Sort `
    -Property DatabaseDataSpaceAllocatedUnusedInMB `
    -Descending | Format-Table
```

Skärmbilden nedan är ett exempel på utdata från skriptet:

![elastisk pool allokerat utrymme och exempel för oanvända allokerat utrymme](./media/sql-database-file-space-management/elastic-pool-allocated-unused.png)

### <a name="elastic-pool-data-max-size"></a>Maxstorlek för elastisk pool-data

Ändra följande T-SQL-fråga för att returnera den maximala datastorleken för elastisk pool.  Enheter av frågeresultatet är i MB.

```sql
-- Connect to master
-- Elastic pools max size in MB
SELECT TOP 1 elastic_pool_storage_limit_mb AS ElasticPoolMaxSizeInMB
FROM sys.elastic_pool_resource_stats
WHERE elastic_pool_name = 'ep1'
ORDER BY end_time DESC
```

## <a name="reclaim-unused-allocated-space"></a>Frigöra oanvänt allokerade utrymme

När databaser har identifierats för att frigöra oanvänt allokerade utrymme, ändrar du följande kommando för att minska datafiler för varje databas.

```sql
-- Shrink database data space allocated.
DBCC SHRINKDATABASE (N'db1')
```

Mer information om det här kommandot finns i [SHRINKDATABASE](https://docs.microsoft.com/sql/t-sql/database-console-commands/dbcc-shrinkdatabase-transact-sql). 

> [!IMPORTANT] 
> Överväg att återskapande databasindex efter databasfiler för data är krympas, index kan bli fragmenterad och förlora effektiviteten prestanda optimering. Om detta inträffar bör index återskapas. Mer information om fragmentering och bygga om index finns i [Reorganize och bygg om index](https://docs.microsoft.com/sql/relational-databases/indexes/reorganize-and-rebuild-indexes).

## <a name="next-steps"></a>Nästa steg

- Information om databasernas maximala storlek finns i:
  - [Azure SQL Database vCore-baserade köpa modellen gränser för en enskild databas](https://docs.microsoft.com/azure/sql-database/sql-database-vcore-resource-limits-single-databases)
  - [Resursgränser för enskilda databaser med hjälp av den DTU-baserade inköpsmodellen](https://docs.microsoft.com/azure/sql-database/sql-database-dtu-resource-limits-single-databases)
  - [Azure SQL Database vCore-baserade köpa modellen lagringsgränser för elastiska pooler](https://docs.microsoft.com/azure/sql-database/sql-database-vcore-resource-limits-elastic-pools)
  - [Resurser-gränser för elastiska pooler med den DTU-baserade inköpsmodellen](https://docs.microsoft.com/azure/sql-database/sql-database-dtu-resource-limits-elastic-pools)
- Mer information om den `SHRINKDATABASE` kommandot, se [SHRINKDATABASE](https://docs.microsoft.com/sql/t-sql/database-console-commands/dbcc-shrinkdatabase-transact-sql). 
- Mer information om fragmentering och bygga om index finns i [Reorganize och bygg om index](https://docs.microsoft.com/sql/relational-databases/indexes/reorganize-and-rebuild-indexes).
