---
title: Azure SQL Database utrymme filhantering | Microsoft Docs
description: Den här sidan beskriver hur du hanterar filutrymme med Azure SQL Database och innehåller kodexempel att avgöra om du behöver krympa en databas samt hur du utför en databas för att minska åtgärden.
services: sql-database
author: CarlRabeler
manager: craigg
ms.service: sql-database
ms.custom: how-to
ms.topic: conceptual
ms.date: 08/01/2018
ms.author: carlrab
ms.openlocfilehash: 1ecc0ce08ef42f5f5935bca29e8269be2ea142f0
ms.sourcegitcommit: 96f498de91984321614f09d796ca88887c4bd2fb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/02/2018
ms.locfileid: "39415150"
---
# <a name="manage-file-space-in-azure-sql-database"></a>Hantera utrymmet i Azure SQL Database

Den här artikeln beskrivs olika typer av lagringsutrymme i Azure SQL Database och steg som kan utföras när utrymmet som allokerats för databaser och elastiska pooler måste hanteras av kunden.

## <a name="overview"></a>Översikt

Storage-mått visas i Azure portal och följande API: er mäta antalet sidor som inte används för databaser och elastiska pooler i Azure SQL Database:
- Azure Resource Manager baserade mått API: er, inklusive PowerShell [get-mått](https://docs.microsoft.com/powershell/module/azurerm.insights/get-azurermmetric)
- T-SQL: [sys.dm_db_resource_stats](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-db-resource-stats-azure-sql-database)
- T-SQL: [sys.resource_stats](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-resource-stats-azure-sql-database)
- T-SQL: [sys.elastic_pool_resource_stats](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-elastic-pool-resource-stats-azure-sql-database)

Det finns arbetsbelastningmönster där allokering av utrymme i de underliggande datafilerna för databaser blir större än antalet sidor som inte används i datafilerna. Det här scenariot kan inträffa när utrymme används ökar och sedan data raderas. När data tas bort, frigörs det tilldelade utrymmet för filen automatiskt inte när data tas bort. I sådana scenarier allokerat utrymme för en databas eller en pool kan överskrida högsta gränsen som stöds (eller som stöds) för databasen och därför kan förhindra datatillväxt eller på förhindra prestanda nivåändringar, även om databasutrymme som faktiskt används är mindre än det största värdet gräns för diskutrymme. För att lösa, kan du behöva minska databasen för att minska allokerade men oanvänt utrymme i databasen.

SQL Database-tjänsten Komprimera inte automatiskt databasfiler för att frigöra oanvänt allokerade utrymme på grund av den potentiella påverkan på prestanda för databasen. Men du kan minska filen i en databas i taget valfri genom att följa stegen som beskrivs i [frigöra oanvänt allokerat utrymme](#reclaim-unused-allocated-space). 

> [!NOTE]
> Till skillnad från datafiler och SQL Database-tjänsten automatiskt minskar storleken på loggfiler eftersom åtgärden inte påverkar databasprestanda.

## <a name="understanding-the-types-of-storage-space-for-a-database"></a>Förstå vilka typer av lagringsutrymme för en databas

För att hantera filutrymme, måste du förstå följande villkor som rör databaslagring för både en enkel databas och en elastisk pool.

|Storage utrymme termen|Definition|Kommentarer|
|---|---|---|
|**Datautrymme som används**|Mängden utrymme som används för att lagra databasdata på 8 KB sidor.|I allmänhet används här ökar (minskningar) på infogningar (ta bort). I vissa fall, hur mycket diskutrymme ändras inte på infogningar eller tar bort beroende på omfattningen av och mönstret för de data som ingår i åtgärden och den fragmenterade. Till exempel tar bort en rad från varje datasida inte nödvändigtvis minska utrymmet som används.|
|**Allokerat utrymme**|Mängden formaterad filutrymme som gjorts tillgängliga för att lagra databasdata|Det tilldelade utrymmet växer automatiskt, men aldrig minskar när du tar bort. Detta säkerställer att framtida infogningar är snabbare eftersom utrymme inte behöver formateras.|
|**Men används inte allokerat utrymme**|Mängden oanvända datautrymme allokerat för databasen.|Det här antalet är skillnaden mellan mängden utrymme som allokerats och använt utrymme och representerar den maximala mängden utrymme som kan vara frigöras genom att minska storleken på databasfiler.|
|**Maxstorlek**|Den maximala mängden datautrymme som kan användas av databasen.|Det tilldelade utrymmet för data kan inte växer dig större än maxstorleken för data.|
||||

Följande diagram illustrerar förhållandet mellan olika typer av lagringsutrymme.

![utrymme lagringstyper och relationer](./media/sql-database-file-space-management/storage-types.png)

## <a name="query-a-database-for-storage-space-information"></a>Fråga en databas för information om diskutrymme

För att avgöra om du har tilldelat men används inte datautrymme för en individuell databas att du kanske vill frigöra, använder du följande frågor:

### <a name="database-data-space-used"></a>Databasutrymme data som används
Ändra följande fråga om du vill returnera mängden utrymme i databas data används i MB.

```sql
-- Connect to master
-- Database data space used in MB
SELECT TOP 1 storage_in_megabytes AS DatabaseDataSpaceUsedInMB
FROM sys.resource_stats
WHERE database_name = 'db1'
ORDER BY end_time DESC
```

### <a name="database-data-allocated-and-allocated-space-unused"></a>Databasdata allokeras och tilldelas oanvänt utrymme
Ändra följande fråga om du vill returnera mängden databasdata allokeras och allokerat utrymme som är oanvända.

```sql
-- Connect to database
-- Database data space allocated in MB and database data space allocated unused in MB
SELECT SUM(size/128.0) AS DatabaseDataSpaceAllocatedInMB, 
SUM(size/128.0 - CAST(FILEPROPERTY(name, 'SpaceUsed') AS int)/128.0) AS DatabaseDataSpaceAllocatedUnusedInMB 
FROM sys.database_files
GROUP BY type_desc
HAVING type_desc = 'ROWS'
```
 
### <a name="database-max-size"></a>Maximal databasstorlek
Ändra följande fråga för att returnera den maximala databasstorleken i byte.

```sql
-- Connect to database
-- Database data max size in bytes
SELECT DATABASEPROPERTYEX('db1', 'MaxSizeInBytes') AS DatabaseDataMaxSizeInBytes
```

## <a name="query-an-elastic-pool-for-storage-space-information"></a>Fråga en elastisk pool för information om diskutrymme

För att avgöra om du har tilldelat men används inte datautrymme i en elastisk pool och för varje databas i pool att du kanske vill frigöra, använder du följande frågor:

### <a name="elastic-pool-data-space-used"></a>Elastisk pool datautrymme används
Ändra följande fråga om du vill returnera mängden utrymme som data i en elastisk pool och används i MB.

```sql
-- Connect to master
-- Elastic pool data space used in MB  
SELECT TOP 1 avg_storage_percent * elastic_pool_storage_limit_mb AS ElasticPoolDataSpaceUsedInMB
FROM sys.elastic_pool_resource_stats
WHERE elastic_pool_name = 'ep1'
ORDER BY end_time DESC
```

### <a name="elastic-pool-data-allocated-and-allocated-space-unused"></a>Elastisk pool data allokeras och tilldelas oanvänt utrymme

Ändra följande PowerShell-skript för att returnera en tabell som listar totalt utrymme som allokerats och oanvänt utrymme som allokerats för varje databas i en elastisk pool. Tabellen beställningar databaser från de som har störst allokerat utrymme oanvända till minsta mängd minne som allokeras inte används.  

Resultatet av frågan för att fastställa det tilldelade utrymmet för varje databas i poolen kan läggas tillsammans att fastställa allokerat utrymme för elastisk pool. Det tilldelade utrymmet för elastisk pool får inte överskrida den maximala storleken för elastisk pool.  

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
# Requires SQL Server PowerShell module – see here to install.  
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

### <a name="elastic-pool-max-size"></a>Maxstorlek för elastisk pool

Använd följande T-SQL-fråga för att returnera den maximala storleken för elastisk databas i MB.

```sql
-- Connect to master
-- Elastic pools max size in MB
SELECT TOP 1 elastic_pool_storage_limit_mb AS ElasticPoolMaxSizeInMB
FROM sys.elastic_pool_resource_stats
WHERE elastic_pool_name = 'ep1'
ORDER BY end_time DESC
```

## <a name="reclaim-unused-allocated-space"></a>Frigöra oanvänt allokerade utrymme

När du har fastställt att du har allokerat utrymme som du vill frigöra, använder du följande kommando för att minska det tilldelade databasutrymmet. 

> [!IMPORTANT]
> Databaser med de tilldelade utrymmet för databaser i en elastisk pool oanvända ska krympas först om du vill frigöra utrymme mest snabbt.  

Använd följande kommando för att minska alla datafiler i den angivna databasen:

```sql
-- Shrink database data space allocated.
DBCC SHRINKDATABASE (N'<database_name>')
```

Mer information om det här kommandot finns i [SHRINKDATABASE](https://docs.microsoft.com/sql/t-sql/database-console-commands/dbcc-shrinkdatabase-transact-sql).

> [!IMPORTANT] 
> Överväg att återskapande databasindex efter databasfiler för data är krympas, index kan bli fragmenterad och förlora effektiviteten prestanda optimering. Om detta inträffar bör index återskapas. Mer information om fragmentering och bygga om index finns i [Reorganize och bygg om index](https://docs.microsoft.com/sql/relational-databases/indexes/reorganize-and-rebuild-indexes).

## <a name="next-steps"></a>Nästa steg

- Information om max databasernas storlek finns i:
  - [Azure SQL Database vCore-baserade köpa modellen gränser för en enskild databas](https://docs.microsoft.com/azure/sql-database/sql-database-vcore-resource-limits-single-databases)
  - [Resursgränser för enskilda databaser med hjälp av den DTU-baserade inköpsmodellen](https://docs.microsoft.com/azure/sql-database/sql-database-dtu-resource-limits-single-databases)
  - [Azure SQL Database vCore-baserade köpa modellen lagringsgränser för elastiska pooler](https://docs.microsoft.com/azure/sql-database/sql-database-vcore-resource-limits-elastic-pools)
  - [Resurser-gränser för elastiska pooler med den DTU-baserade inköpsmodellen](https://docs.microsoft.com/azure/sql-database/sql-database-dtu-resource-limits-elastic-pools)
- Mer information om den `SHRINKDATABASE` kommandot, se [SHRINKDATABASE](https://docs.microsoft.com/sql/t-sql/database-console-commands/dbcc-shrinkdatabase-transact-sql). 
- Mer information om fragmentering och bygga om index finns i [Reorganize och bygg om index](https://docs.microsoft.com/sql/relational-databases/indexes/reorganize-and-rebuild-indexes).