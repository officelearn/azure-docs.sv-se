---
title: Hantering av filutrymme i en-/poolade databaser
description: Den här sidan beskriver hur du hanterar filutrymme med enstaka och poolade databaser i Azure SQL Database och innehåller kodexempel för hur du tar reda på om du behöver krympa en enskild eller en poolad databas samt hur du utför en databasförminskaråtgärd.
services: sql-database
ms.service: sql-database
ms.subservice: operations
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: oslake
ms.author: moslake
ms.reviewer: jrasnick, carlrab
ms.date: 03/12/2019
ms.openlocfilehash: 007bbffbd7c4fcad339f88eb78991eb39fb829e6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74420984"
---
# <a name="manage-file-space-for-single-and-pooled-databases-in-azure-sql-database"></a>Hantera filutrymme för enstaka och poolade databaser i Azure SQL Database

I den här artikeln beskrivs olika typer av lagringsutrymme för enskilda och poolerade databaser i Azure SQL Database och åtgärder som kan vidtas när filutrymmet som allokerats för databaser och elastiska pooler måste hanteras uttryckligen.

> [!NOTE]
> Den här artikeln gäller inte för distributionsalternativet för hanterade instanser i Azure SQL Database.

## <a name="overview"></a>Översikt

Med enstaka och poolade databaser i Azure SQL Database finns det arbetsbelastningsmönster där allokeringen av underliggande datafiler för databaser kan bli större än mängden använda datasidor. Den här situationen kan uppstå när mängden utnyttjat utrymme ökar och data därefter raderas. Orsaken är att det tilldelade filutrymmet inte automatiskt hämtas när data tas bort.

I följande scenarier kan det vara nödvändigt att övervaka användningen av filutrymmet och att krympa datafiler:

- Tillåt datatillväxt i en elastisk pool när filutrymmet som tilldelats dess databaser når poolens maxstorlek.
- Tillåt att maxstorleken för en enskild databas eller elastisk pool minskas.
- Tillåt att en enskild databas eller elastisk pool ändras till en annan tjänstnivå eller prestandanivå med en mindre maxstorlek.

### <a name="monitoring-file-space-usage"></a>Övervaka användning av filutrymme

De flesta lagringslagringslagringsmått som visas i Azure-portalen och följande API:er mäter bara storleken på använda datasidor:

- Azure Resource Manager-baserade api:er för mått, inklusive [Get-mått för](https://docs.microsoft.com/powershell/module/az.monitor/get-azmetric) PowerShell
- T-SQL: [sys.dm_db_resource_stats](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-db-resource-stats-azure-sql-database)

Följande API:er mäter dock också storleken på det utrymme som allokerats för databaser och elastiska pooler:

- T-SQL: [sys.resource_stats](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-resource-stats-azure-sql-database)
- T-SQL: [sys.elastic_pool_resource_stats](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-elastic-pool-resource-stats-azure-sql-database)

### <a name="shrinking-data-files"></a>Krympande datafiler

SQL Database-tjänsten förminsar inte automatiskt datafiler för att frigöra oanvänt allokerat utrymme på grund av den potentiella effekten på databasens prestanda.  Kunder kan dock krympa datafiler via självbetjäning vid en tidpunkt de själva väljer genom att följa stegen som beskrivs i [återta oanvänt allokerat utrymme](#reclaim-unused-allocated-space).

> [!NOTE]
> Till skillnad från datafiler förminskar SQL Database-tjänsten automatiskt loggfiler eftersom åtgärden inte påverkar databasens prestanda.

## <a name="understanding-types-of-storage-space-for-a-database"></a>Förstå typer av lagringsutrymme för en databas

Att förstå följande lagringsutrymmesmängder är viktigt för att hantera filutrymmet i en databas.

|Databaskvantitet|Definition|Kommentarer|
|---|---|---|
|**Datautrymme som används**|Mängden utrymme som används för att lagra databasdata på 8 KB-sidor.|I allmänhet ökar (minskar) utrymmet på skär (borttagningar). I vissa fall ändras inte det utrymme som används på skär eller borttagningar beroende på mängden och mönstret för data som ingår i åtgärden och eventuell fragmentering. Om du till exempel tar bort en rad från varje datasida minskar inte nödvändigtvis det utrymme som används.|
|**Tilldelat datautrymme**|Mängden formaterat filutrymme som gjorts tillgängligt för lagring av databasdata.|Mängden tilldelade utrymme ökar automatiskt, men minskar aldrig när borttagningarna har tas bort. Detta säkerställer att framtida infogningar är snabbare eftersom utrymmet inte behöver formateras om.|
|**Datautrymme allokerat men oanvänt men oanvänt**|Skillnaden mellan mängden tilldelade datautrymme och datautrymme som används.|Den här kvantiteten representerar den maximala mängden ledigt utrymme som kan återanvändas genom att databasdatafiler förminskas.|
|**Data max storlek**|Den maximala mängden utrymme som kan användas för att lagra databasdata.|Mängden tilldelade datautrymme kan inte växa utöver data maxstorleken.|

Följande diagram illustrerar förhållandet mellan de olika typerna av lagringsutrymme för en databas.

![typer och relationer för lagringsutrymme](./media/sql-database-file-space-management/storage-types.png)

## <a name="query-a-single-database-for-storage-space-information"></a>Fråga en enskild databas efter information om lagringsutrymme

Följande frågor kan användas för att bestämma lagringsutrymmesmängder för en enskild databas.  

### <a name="database-data-space-used"></a>Databasdatautrymme som används

Ändra följande fråga för att returnera mängden databasdatautrymme som används.  Enheter i frågeresultatet finns i MB.

```sql
-- Connect to master
-- Database data space used in MB
SELECT TOP 1 storage_in_megabytes AS DatabaseDataSpaceUsedInMB
FROM sys.resource_stats
WHERE database_name = 'db1'
ORDER BY end_time DESC
```

### <a name="database-data-space-allocated-and-unused-allocated-space"></a>Databasdatautrymme allokerat och oanvänt allokerat utrymme

Använd följande fråga för att returnera mängden databasdatautrymme som allokerats och mängden oanvänt utrymme som allokerats.  Enheter i frågeresultatet finns i MB.

```sql
-- Connect to database
-- Database data space allocated in MB and database data space allocated unused in MB
SELECT SUM(size/128.0) AS DatabaseDataSpaceAllocatedInMB,
SUM(size/128.0 - CAST(FILEPROPERTY(name, 'SpaceUsed') AS int)/128.0) AS DatabaseDataSpaceAllocatedUnusedInMB
FROM sys.database_files
GROUP BY type_desc
HAVING type_desc = 'ROWS'
```

### <a name="database-data-max-size"></a>Databasdata maxstorlek

Ändra följande fråga för att returnera databasdatas maximala storlek.  Enheter i frågeresultatet är i byte.

```sql
-- Connect to database
-- Database data max size in bytes
SELECT DATABASEPROPERTYEX('db1', 'MaxSizeInBytes') AS DatabaseDataMaxSizeInBytes
```

## <a name="understanding-types-of-storage-space-for-an-elastic-pool"></a>Förstå typer av lagringsutrymme för en elastisk pool

Att förstå följande lagringsutrymmesmängder är viktigt för att hantera filutrymmet i en elastisk pool.

|Elastisk poolkvantitet|Definition|Kommentarer|
|---|---|---|
|**Datautrymme som används**|Summeringen av datautrymme som används av alla databaser i den elastiska poolen.||
|**Tilldelat datautrymme**|Summeringen av datautrymme som allokeras av alla databaser i den elastiska poolen.||
|**Datautrymme allokerat men oanvänt men oanvänt**|Skillnaden mellan mängden datautrymme som allokerats och datautrymme som används av alla databaser i den elastiska poolen.|Den här kvantiteten representerar den maximala mängden utrymme som allokerats för den elastiska poolen som kan återanvändas genom att databasdatafiler förminskas.|
|**Data max storlek**|Den maximala mängden datautrymme som kan användas av den elastiska poolen för alla dess databaser.|Det utrymme som allokerats för den elastiska poolen bör inte överstiga den elastiska poolen max storlek.  Om det här villkoret inträffar kan utrymme som allokerats som inte används återanvändas genom att databasdatafiler förminskas.|

## <a name="query-an-elastic-pool-for-storage-space-information"></a>Fråga en elastisk pool efter information om lagringsutrymme

Följande frågor kan användas för att bestämma lagringsutrymmesmängder för en elastisk pool.  

### <a name="elastic-pool-data-space-used"></a>Elastiskt pooldatautrymme som används

Ändra följande fråga för att returnera mängden elastiskt pooldatautrymme som används.  Enheter i frågeresultatet finns i MB.

```sql
-- Connect to master
-- Elastic pool data space used in MB  
SELECT TOP 1 avg_storage_percent / 100.0 * elastic_pool_storage_limit_mb AS ElasticPoolDataSpaceUsedInMB
FROM sys.elastic_pool_resource_stats
WHERE elastic_pool_name = 'ep1'
ORDER BY end_time DESC
```

### <a name="elastic-pool-data-space-allocated-and-unused-allocated-space"></a>Elastiskt pooldatautrymme allokerat och oanvänt allokerat utrymme

Ändra följande exempel för att returnera en tabell med det tilldelade och oandelade utrymmet för varje databas i en elastisk pool. Tabellen beställer databaser från de databaser som har den största mängden oanvänt allokerat utrymme till den minsta mängden oanvänt allokerat utrymme.  Enheter i frågeresultatet finns i MB.  

Frågeresultaten för att bestämma det utrymme som allokerats för varje databas i poolen kan läggas ihop för att bestämma det totala utrymme som allokerats för den elastiska poolen. Det elastiska poolutrymmet som allokerats bör inte överstiga den elastiska poolen maxstorlek.  

> [!IMPORTANT]
> PowerShell Azure Resource Manager (RM) stöds fortfarande av Azure SQL Database, men all framtida utveckling är för Az.Sql-modulen. AzureRM-modulen fortsätter att ta emot buggfixar fram till åtminstone december 2020.  Argumenten för kommandona i Az-modulen och i AzureRm-modulerna är i stort sett identiska. Mer information om deras kompatibilitet finns i [Introduktion till den nya Azure PowerShell Az-modulen](/powershell/azure/new-azureps-module-az).

PowerShell-skriptet kräver SQL Server PowerShell-modul – se [Hämta PowerShell-modul](https://docs.microsoft.com/sql/powershell/download-sql-server-ps-module) för att installera.

```powershell
$resourceGroupName = "<resourceGroupName>"
$serverName = "<serverName>"
$poolName = "<poolName>"
$userName = "<userName>"
$password = "<password>"

# get list of databases in elastic pool
$databasesInPool = Get-AzSqlElasticPoolDatabase -ResourceGroupName $resourceGroupName `
    -ServerName $serverName -ElasticPoolName $poolName
$databaseStorageMetrics = @()

# for each database in the elastic pool, get space allocated in MB and space allocated unused in MB
foreach ($database in $databasesInPool) {
    $sqlCommand = "SELECT DB_NAME() as DatabaseName, `
    SUM(size/128.0) AS DatabaseDataSpaceAllocatedInMB, `
    SUM(size/128.0 - CAST(FILEPROPERTY(name, 'SpaceUsed') AS int)/128.0) AS DatabaseDataSpaceAllocatedUnusedInMB `
    FROM sys.database_files `
    GROUP BY type_desc `
    HAVING type_desc = 'ROWS'"
    $serverFqdn = "tcp:" + $serverName + ".database.windows.net,1433"
    $databaseStorageMetrics = $databaseStorageMetrics + 
        (Invoke-Sqlcmd -ServerInstance $serverFqdn -Database $database.DatabaseName `
            -Username $userName -Password $password -Query $sqlCommand)
}

# display databases in descending order of space allocated unused
Write-Output "`n" "ElasticPoolName: $poolName"
Write-Output $databaseStorageMetrics | Sort -Property DatabaseDataSpaceAllocatedUnusedInMB -Descending | Format-Table
```

Följande skärmdump är ett exempel på utskriften av skriptet:

![elastisk pool allokerat utrymme och oanvänt allokerat utrymme exempel](./media/sql-database-file-space-management/elastic-pool-allocated-unused.png)

### <a name="elastic-pool-data-max-size"></a>Elastiska pooldata maxstorlek

Ändra följande T-SQL-fråga för att returnera den elastiska pooldata maxstorlek.  Enheter i frågeresultatet finns i MB.

```sql
-- Connect to master
-- Elastic pools max size in MB
SELECT TOP 1 elastic_pool_storage_limit_mb AS ElasticPoolMaxSizeInMB
FROM sys.elastic_pool_resource_stats
WHERE elastic_pool_name = 'ep1'
ORDER BY end_time DESC
```

## <a name="reclaim-unused-allocated-space"></a>Återta oanvänt allokerat utrymme

> [!NOTE]
> Det här kommandot kan påverka databasens prestanda medan den körs och bör om möjligt köras under perioder med låg användning.

### <a name="dbcc-shrink"></a>DBCC krympa

När databaser har identifierats för att återvinna oanvänt allokerat utrymme ändrar du namnet på databasen i följande kommando för att förminska datafilerna för varje databas.

```sql
-- Shrink database data space allocated.
DBCC SHRINKDATABASE (N'db1')
```

Det här kommandot kan påverka databasens prestanda medan den körs och bör om möjligt köras under perioder med låg användning.  

Mer information om det här kommandot finns i [SHRINKDATABASE](https://docs.microsoft.com/sql/t-sql/database-console-commands/dbcc-shrinkdatabase-transact-sql).

### <a name="auto-shrink"></a>Automatisk krympning

Alternativt kan automatisk förminskande aktiveras för en databas.  Automatisk krympning minskar filhanteringskomplexiteten och är `SHRINKDATABASE` mindre `SHRINKFILE`effektkänslig för databasens prestanda än eller .  Automatisk krympning kan vara särskilt användbart för att hantera elastiska pooler med många databaser.  Automatisk krympning kan dock vara mindre effektiv `SHRINKDATABASE` `SHRINKFILE`när det gäller att återvinna filutrymme än och .
Om du vill aktivera automatisk förminskande ändrar du namnet på databasen i följande kommando.

```sql
-- Enable auto-shrink for the database.
ALTER DATABASE [db1] SET AUTO_SHRINK ON
```

Mer information om det här kommandot finns i [databasuppsättningsalternativ.](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql-set-options?view=azuresqldb-current)

### <a name="rebuild-indexes"></a>Återskapa index

När databasdatafiler krymps kan index fragmenteras och förlora sin effektivitet i prestandaoptimering. Om prestandaförsämring inträffar bör du överväga att återskapa databasindex. Mer information om fragmentering och återskapande index finns i [Ordna om och återskapa index](https://docs.microsoft.com/sql/relational-databases/indexes/reorganize-and-rebuild-indexes).

## <a name="next-steps"></a>Nästa steg

- Information om databasens maxstorlekar finns i:
  - [Azure SQL Database vCore-baserade inköpsmodellgränser för en enskild databas](sql-database-vcore-resource-limits-single-databases.md)
  - [Resursgränser för enskilda databaser med hjälp av den DTU-baserade inköpsmodellen](sql-database-dtu-resource-limits-single-databases.md)
  - [Azure SQL Database vCore-baserade inköpsmodellgränser för elastiska pooler](sql-database-vcore-resource-limits-elastic-pools.md)
  - [Resursgränser för elastiska pooler med hjälp av den DTU-baserade inköpsmodellen](sql-database-dtu-resource-limits-elastic-pools.md)
- Mer information om `SHRINKDATABASE` kommandot finns i [SHRINKDATABASE](https://docs.microsoft.com/sql/t-sql/database-console-commands/dbcc-shrinkdatabase-transact-sql).
- Mer information om fragmentering och återskapande index finns i [Ordna om och återskapa index](https://docs.microsoft.com/sql/relational-databases/indexes/reorganize-and-rebuild-indexes).
