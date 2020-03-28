---
title: Skapa och hantera Azure Cosmos DB med PowerShell
description: Använd Azure Powershell hantera dina Azure Cosmos-konton, databaser, behållare och dataflöde.
author: markjbrown
ms.service: cosmos-db
ms.topic: sample
ms.date: 03/26/2020
ms.author: mjbrown
ms.custom: seodec18
ms.openlocfilehash: c8e833a4ba18520d8e354398cfd0d00525594d15
ms.sourcegitcommit: 07d62796de0d1f9c0fa14bfcc425f852fdb08fb1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "80365753"
---
# <a name="manage-azure-cosmos-db-sql-api-resources-using-powershell"></a>Hantera Azure Cosmos DB SQL API-resurser med PowerShell

I följande guide beskrivs hur du använder PowerShell för att skripta och automatisera hanteringen av Azure Cosmos DB-resurser, som konto, databas, container och dataflöde.

> [!NOTE]
> Exempel i den `Get-AzResource` `Set-AzResource` här artikeln använder och Powershell-cmdlets för Azure-resursåtgärder samt [Az.CosmosDB-hanteringscmdlets.](https://docs.microsoft.com/powershell/module/az.cosmosdb) `Az.CosmosDB`cmdlets är fortfarande i förhandsgranskning och kan ändras innan de är allmänt tillgängliga. Se [az.cosmosdb](https://docs.microsoft.com/powershell/module/az.cosmosdb) API-referenssidan för eventuella uppdateringar av kommandona.

Information om hur du visar alla `Get-Resource` / `Set-AzResource` egenskaper som kan hanteras med PowerShell-cmdlets finns i [Azure Cosmos DB-resursproviderschema](/azure/templates/microsoft.documentdb/allversions)

För plattformsoberoende hantering av Azure Cosmos `Az` DB `Az.CosmosDB` kan du använda och cmdlets med [plattformsoberoende Powershell](https://docs.microsoft.com/powershell/scripting/install/installing-powershell), samt [Azure CLI](manage-with-cli.md), [REST API][rp-rest-api]eller [Azure-portalen](create-sql-api-dotnet.md#create-account).

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="getting-started"></a>Komma igång

Följ instruktionerna i [Så här installerar och konfigurerar du Azure PowerShell][powershell-install-configure] för att installera och logga in på ditt Azure-konto i Powershell.

* `Set-AzureResource`används nedan. Det kommer att be om användarbekräftelse.  Om du föredrar att köra utan att `-Force` kräva användarbekräftelse lägger du till flaggan i kommandot.

## <a name="azure-cosmos-accounts"></a>Azure Cosmos-konton

Följande avsnitt visar hur du hanterar Azure Cosmos-kontot, inklusive:

* [Skapa ett Azure Cosmos-konto](#create-account)
* [Uppdatera ett Azure Cosmos-konto](#update-account)
* [Lista alla Azure Cosmos-konton i en prenumeration](#list-accounts)
* [Skaffa ett Azure Cosmos-konto](#get-account)
* [Ta bort ett Azure Cosmos-konto](#delete-account)
* [Uppdatera taggar för ett Azure Cosmos-konto](#update-tags)
* [Lista nycklar för ett Azure Cosmos-konto](#list-keys)
* [Återskapa nycklar för ett Azure Cosmos-konto](#regenerate-keys)
* [Lista anslutningssträngar för ett Azure Cosmos-konto](#list-connection-strings)
* [Ändra redundansprioritet för ett Azure Cosmos-konto](#modify-failover-priority)
* [Utlösa en manuell redundans för ett Azure Cosmos-konto](#trigger-manual-failover)

### <a name="create-an-azure-cosmos-account"></a><a id="create-account"></a>Skapa ett Azure Cosmos-konto

Det här kommandot skapar ett Azure Cosmos DB-databaskonto med [flera regioner,][distribute-data-globally] [automatisk redundans](how-to-manage-database-account.md#automatic-failover) och [konsekvensprincip](consistency-levels.md)för begränsad inaktuellhet .

```azurepowershell-interactive
$resourceGroupName = "myResourceGroup"
$locations = @("West US 2", "East US 2")
$accountName = "mycosmosaccount"
$apiKind = "GlobalDocumentDB"
$consistencyLevel = "BoundedStaleness"
$maxStalenessInterval = 300
$maxStalenessPrefix = 100000

New-AzCosmosDBAccount -ResourceGroupName $resourceGroupName `
    -Location $locations -Name $accountName `
    -ApiKind $apiKind -EnableAutomaticFailover:$true `
    -DefaultConsistencyLevel $consistencyLevel `
    -MaxStalenessIntervalInSeconds $maxStalenessInterval `
    -MaxStalenessPrefix $maxStalenessPrefix
```

* `$resourceGroupName`Azure-resursgruppen som du vill distribuera Cosmos-kontot till. Det måste redan finnas.
* `$locations`Regionerna för databaskontot, som börjar med skrivregionen och ordnas efter redundansprioritet.
* `$accountName`Namnet på Azure Cosmos-kontot. Måste vara unika, gemener, innehåller endast alfanumeriska och "-" tecken, och mellan 3 och 31 tecken i längd.
* `$apiKind`Den typ av Cosmos-konto som ska skapas. Mer information finns [i API:er i Cosmos DB](introduction.md#develop-applications-on-cosmos-db-using-popular-open-source-software-oss-apis).
* `$consistencyPolicy`, `$maxStalenessInterval`och `$maxStalenessPrefix` standardkonsekvensnivå och inställningar för Azure Cosmos-kontot. Mer information finns [i Konsekvensnivåer i Azure Cosmos DB](consistency-levels.md).

Azure Cosmos-konton kan konfigureras med IP-brandvägg, slutpunkter för tjänsten För virtuella nätverk och privata slutpunkter. Information om hur du konfigurerar IP-brandväggen för Azure Cosmos DB finns i [Konfigurera IP-brandväggen](how-to-configure-firewall.md). Information om hur du aktiverar tjänstslutpunkter för Azure Cosmos DB finns i [Konfigurera åtkomst från virtuella nätverk](how-to-configure-vnet-service-endpoint.md). Information om hur du aktiverar privata slutpunkter för Azure Cosmos DB finns i [Konfigurera åtkomst från privata slutpunkter](how-to-configure-private-endpoints.md).

### <a name="list-all-azure-cosmos-accounts-in-a-resource-group"></a><a id="list-accounts"></a>Lista alla Azure Cosmos-konton i en resursgrupp

Det här kommandot listar alla Azure Cosmos-konton i en resursgrupp.

```azurepowershell-interactive
$resourceGroupName = "myResourceGroup"

Get-AzCosmosDBAccount -ResourceGroupName $resourceGroupName
```

### <a name="get-the-properties-of-an-azure-cosmos-account"></a><a id="get-account"></a>Hämta egenskaperna för ett Azure Cosmos-konto

Med det här kommandot kan du hämta egenskaperna för ett befintligt Azure Cosmos-konto.

```azurepowershell-interactive
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"

Get-AzCosmosDBAccount -ResourceGroupName $resourceGroupName -Name $accountName
```

### <a name="update-an-azure-cosmos-account"></a><a id="update-account"></a>Uppdatera ett Azure Cosmos-konto

Med det här kommandot kan du uppdatera egenskaperna för azure cosmos DB-databaskonton. Egenskaper som kan uppdateras är följande:

* Lägga till eller ta bort regioner
* Ändra standardprincip för konsekvens
* Ändra filter för IP-intervall
* Ändra konfigurationer för virtuella nätverk
* Aktivera Multi-master

> [!NOTE]
> Du kan inte samtidigt `locations` lägga till eller ta bort regioner och ändra andra egenskaper för ett Azure Cosmos-konto. Ändringsregioner måste utföras som en separat åtgärd från alla andra ändringar av kontot.
> [!NOTE]
> Med det här kommandot kan du lägga till och ta bort regioner, men du kan inte ändra redundansprioriteringar eller utlösa en manuell redundans. Se [Ändra redundansprioritet](#modify-failover-priority) [och utlösa manuell redundans](#trigger-manual-failover).

```azurepowershell-interactive
# Create account with two regions
$resourceGroupName = "myResourceGroup"
$locations = @("West US 2", "East US 2")
$accountName = "mycosmosaccount"
$apiKind = "GlobalDocumentDB"
$consistencyLevel = "Session"
$enableAutomaticFailover = $true

New-AzCosmosDBAccount `
    -ResourceGroupName $resourceGroupName `
    -Location $locations -Name $accountName `
    -ApiKind $apiKind -EnableAutomaticFailover:$enableAutomaticFailover `
    -DefaultConsistencyLevel $consistencyLevel

# Region operations
$resourceType = "Microsoft.DocumentDb/databaseAccounts"
$apiVersion = "2020-03-01"

# Add a region to the account
$locations2 = @("West US 2", "East US 2", "South Central US")
$locationObjects2 = @()
$i = 0
ForEach ($location in $locations2) { $locationObjects2 += @{ locationName = "$location"; failoverPriority = $i++ } }
$accountProperties = @{
    databaseAccountOfferType = "Standard";
    locations = $locationObjects2;
    enableAutomaticFailover = $enableAutomaticFailover;
}

Set-AzResource -ResourceType $resourceType `
    -ResourceGroupName $resourceGroupName `
    -ApiVersion $apiVersion -Name $accountName `
    -PropertyObject $accountProperties

Write-Host "Set-AzResource returns before the region update is complete."
Write-Host "Check account in Azure portal or using Get-AzCosmosDBAccount for region status."
Write-Host "When region was added, press any key to continue."
$HOST.UI.RawUI.ReadKey("NoEcho,IncludeKeyDown") | OUT-NULL
$HOST.UI.RawUI.Flushinputbuffer()

# Remove a region from the account
$locations3 = @("West US 2", "South Central US")
$locationObjects3 = @()
$i = 0
ForEach ($location in $locations3) { $locationObjects3 += @{ locationName = "$location"; failoverPriority = $i++ } }
$accountProperties = @{
    databaseAccountOfferType = "Standard";
    locations = $locationObjects3;
    enableAutomaticFailover = $enableAutomaticFailover;
}

Set-AzResource -ResourceType $resourceType `
    -ResourceGroupName $resourceGroupName `
    -ApiVersion $apiVersion -Name $accountName `
    -PropertyObject $accountProperties

Write-Host "Set-AzResource returns before the region update is complete."
Write-Host "Check account in Azure portal or using Get-AzCosmosDBAccount for region status."
```
### <a name="enable-multiple-write-regions-for-an-azure-cosmos-account"></a><a id="multi-master"></a>Aktivera flera skrivregioner för ett Azure Cosmos-konto

```azurepowershell-interactive
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$enableAutomaticFailover = $false
$enableMultiMaster = $true

# First disable automatic failover - cannot have both automatic
# failover and multi-master on an account
Update-AzCosmosDBAccount `
    -ResourceGroupName $resourceGroupName `
    -Name $accountName `
    -EnableAutomaticFailover:$enableAutomaticFailover

# Now enable multi-master
Update-AzCosmosDBAccount `
    -ResourceGroupName $resourceGroupName `
    -Name $accountName `
    -EnableMultipleWriteLocations:$enableMultiMaster
```

### <a name="delete-an-azure-cosmos-account"></a><a id="delete-account"></a>Ta bort ett Azure Cosmos-konto

Det här kommandot tar bort ett befintligt Azure Cosmos-konto.

```azurepowershell-interactive
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"

Remove-AzCosmosDBAccount `
    -ResourceGroupName $resourceGroupName `
    -Name $accountName -PassThru
```

### <a name="update-tags-of-an-azure-cosmos-account"></a><a id="update-tags"></a>Uppdatera taggar för ett Azure Cosmos-konto

Det här kommandot anger [Azure-resurstaggarna][azure-resource-tags] för ett Azure Cosmos-konto. Taggar kan ställas in både `New-AzCosmosDBAccount` vid skapandet av `Update-AzCosmosDBAccount`konton med hjälp av och på konto uppdatering med .

```azurepowershell-interactive
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$tags = @{dept = "Finance"; environment = "Production";}

Update-AzCosmosDBAccount `
    -ResourceGroupName $resourceGroupName `
    -Name $accountName -Tag $tags
```

### <a name="list-account-keys"></a><a id="list-keys"></a>Lista kontonycklar

När du skapar ett Azure Cosmos-konto genererar tjänsten två huvudåtkomstnycklar som kan användas för autentisering när Azure Cosmos-kontot används. Skrivskyddade nycklar för autentisering av skrivskyddade åtgärder genereras också.
Genom att tillhandahålla två åtkomstnycklar kan du med Azure Cosmos DB återskapa och rotera en nyckel i taget utan avbrott i ditt Azure Cosmos-konto.
Cosmos DB-konton har två läs-skriv nycklar (primära och sekundära) och två skrivskyddade nycklar (primära och sekundära).

```azurepowershell-interactive
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"

Get-AzCosmosDBAccountKey `
    -ResourceGroupName $resourceGroupName `
    -Name $accountName -Type "Keys"
```

### <a name="list-connection-strings"></a><a id="list-connection-strings"></a>Lista anslutningssträngar

Följande kommando hämtar anslutningssträngar för att ansluta appar till Cosmos DB-kontot.

```azurepowershell-interactive
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"

Get-AzCosmosDBAccountKey `
    -ResourceGroupName $resourceGroupName `
    -Name $accountName -Type "ConnectionStrings"
```

### <a name="regenerate-account-keys"></a><a id="regenerate-keys"></a>Återskapa kontonycklar

Åtkomstnycklar till ett Azure Cosmos-konto bör regelbundet återskapas för att skydda anslutningarna. Kontot tilldelas en primär och sekundär åtkomstnycklar. Detta gör det möjligt för klienter att behålla åtkomsten medan en nyckel i taget återskapas.
Det finns fyra typer av nycklar för ett Azure Cosmos-konto (Primär, Sekundär, PrimaryReadonly och SecondaryReadonly)

```azurepowershell-interactive
$resourceGroupName = "myResourceGroup" # Resource Group must already exist
$accountName = "mycosmosaccount" # Must be all lower case
$keyKind = "primary" # Other key kinds: secondary, primaryReadOnly, secondaryReadOnly

New-AzCosmosDBAccountKey `
    -ResourceGroupName $resourceGroupName `
    -Name $accountName -KeyKind $keyKind
```

### <a name="enable-automatic-failover"></a><a id="enable-automatic-failover"></a>Aktivera automatisk redundans

Följande kommando anger att ett Cosmos DB-konto ska växlas över automatiskt till den sekundära regionen om den primära regionen blir otillgänglig.

```azurepowershell-interactive
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$enableAutomaticFailover = $true
$enableMultiMaster = $false

# First disable multi-master - cannot have both automatic
# failover and multi-master on an account
Update-AzCosmosDBAccount `
    -ResourceGroupName $resourceGroupName `
    -Name $accountName `
    -EnableMultipleWriteLocations:$enableMultiMaster

# Now enable automatic failover
Update-AzCosmosDBAccount `
    -ResourceGroupName $resourceGroupName `
    -Name $accountName `
    -EnableAutomaticFailover:$enableAutomaticFailover
```

### <a name="modify-failover-priority"></a><a id="modify-failover-priority"></a>Ändra redundansprioritet

För konton som konfigurerats med Automatisk redundans kan du ändra i vilken ordning Cosmos befordrar sekundära repliker till primära om den primära blir otillgänglig.

I exemplet nedan antar du att `West US 2 = 0`den `East US 2 = 1` `South Central US = 2`aktuella redundansprioriteten är , , . Kommandot ändrar detta `West US 2 = 0` `South Central US = 1`till `East US 2 = 2`, , .

> [!CAUTION]
> Om du `failoverPriority=0` ändrar platsen för utlöses en manuell redundans för ett Azure Cosmos-konto. Andra prioritetsändringar utlöser inte en redundansväxling.

```azurepowershell-interactive
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$locations = @("West US 2", "South Central US", "East US 2") # Regions ordered by UPDATED failover priority

Update-AzCosmosDBAccountFailoverPriority `
    -ResourceGroupName $resourceGroupName `
    -Name $accountName `
    -FailoverPolicy $locations
```

### <a name="trigger-manual-failover"></a><a id="trigger-manual-failover"></a>Manuell redundans för utlösare

För konton som konfigurerats med Manuell redundans kan du växla `failoverPriority=0`över och befordra en sekundär replik till primär genom att ändra till . Den här åtgärden kan användas för att initiera en åtgärd för haveriberedskap för att testa katastrofåterställningsplanering.

I exemplet nedan antar du att kontot har `West US 2 = 0` `East US 2 = 1` en aktuell redundansprioritet för och och vänder på regionerna.

> [!CAUTION]
> Om `locationName` `failoverPriority=0` du ändrar för utlöses en manuell redundans för ett Azure Cosmos-konto. Alla andra prioritetsändringar utlöser inte en redundansväxling.

```azurepowershell-interactive
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$locations = @("East US 2", "West US 2") # Regions ordered by UPDATED failover priority

Update-AzCosmosDBAccountFailoverPriority `
    -ResourceGroupName $resourceGroupName `
    -Name $accountName `
    -FailoverPolicy $locations
```

## <a name="azure-cosmos-db-database"></a>Azure Cosmos DB-databas

Följande avsnitt visar hur du hanterar Azure Cosmos DB-databasen, inklusive:

* [Skapa en Azure Cosmos DB-databas](#create-db)
* [Skapa en Azure Cosmos DB-databas med delat dataflöde](#create-db-ru)
* [Hämta dataflödet för en Azure Cosmos DB-databas](#get-db-ru)
* [Lista alla Azure Cosmos DB-databaser i ett konto](#list-db)
* [Skaffa en enda Azure Cosmos DB-databas](#get-db)
* [Ta bort en Azure Cosmos DB-databas](#delete-db)

### <a name="create-an-azure-cosmos-db-database"></a><a id="create-db"></a>Skapa en Azure Cosmos DB-databas

```azurepowershell-interactive
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$databaseName = "myDatabase"

Set-AzCosmosDBSqlDatabase `
    -ResourceGroupName $resourceGroupName `
    -AccountName $accountName `
    -Name $databaseName
```

### <a name="create-an-azure-cosmos-db-database-with-shared-throughput"></a><a id="create-db-ru"></a>Skapa en Azure Cosmos DB-databas med delat dataflöde

```azurepowershell-interactive
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$databaseName = "myDatabase"
$databaseRUs = 400

Set-AzCosmosDBSqlDatabase `
    -ResourceGroupName $resourceGroupName `
    -AccountName $accountName `
    -Name $databaseName `
    -Throughput $databaseRUs
```

### <a name="get-the-throughput-of-an-azure-cosmos-db-database"></a><a id="get-db-ru"></a>Hämta dataflödet för en Azure Cosmos DB-databas

```azurepowershell-interactive
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$databaseName = "myDatabase"

Get-AzCosmosDBSqlDatabaseThroughput `
    -ResourceGroupName $resourceGroupName `
    -AccountName $accountName `
    -Name $databaseName
```

### <a name="get-all-azure-cosmos-db-databases-in-an-account"></a><a id="list-db"></a>Hämta alla Azure Cosmos DB-databaser i ett konto

```azurepowershell-interactive
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"

Get-AzCosmosDBSqlDatabase `
    -ResourceGroupName $resourceGroupName `
    -AccountName $accountName
```

### <a name="get-a-single-azure-cosmos-db-database"></a><a id="get-db"></a>Skaffa en enda Azure Cosmos DB-databas

```azurepowershell-interactive
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$databaseName = "myDatabase"

Get-AzCosmosDBSqlDatabase `
    -ResourceGroupName $resourceGroupName `
    -AccountName $accountName `
    -Name $databaseName
```

### <a name="delete-an-azure-cosmos-db-database"></a><a id="delete-db"></a>Ta bort en Azure Cosmos DB-databas

```azurepowershell-interactive
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$databaseName = "myDatabase"

Remove-AzCosmosDBSqlDatabase `
    -ResourceGroupName $resourceGroupName `
    -AccountName $accountName `
    -Name $databaseName
```

## <a name="azure-cosmos-db-container"></a>Azure Cosmos DB-behållare

Följande avsnitt visar hur du hanterar Azure Cosmos DB-behållaren, inklusive:

* [Skapa en Azure Cosmos DB-behållare](#create-container)
* [Skapa en Azure Cosmos DB-behållare med en stor partitionsnyckel](#create-container-big-pk)
* [Hämta dataflödet för en Azure Cosmos DB-behållare](#get-container-ru)
* [Skapa en Azure Cosmos DB-behållare med anpassad indexering](#create-container-custom-index)
* [Skapa en Azure Cosmos DB-behållare med indexering inaktiverad](#create-container-no-index)
* [Skapa en Azure Cosmos DB-behållare med unik nyckel och TTL](#create-container-unique-key-ttl)
* [Skapa en Azure Cosmos DB-behållare med konfliktlösning](#create-container-lww)
* [Lista alla Azure Cosmos DB-behållare i en databas](#list-containers)
* [Skaffa en enda Azure Cosmos DB-behållare i en databas](#get-container)
* [Ta bort en Azure Cosmos DB-behållare](#delete-container)

### <a name="create-an-azure-cosmos-db-container"></a><a id="create-container"></a>Skapa en Azure Cosmos DB-behållare

```azurepowershell-interactive
# Create an Azure Cosmos DB container with default indexes and throughput at 400 RU
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$databaseName = "myDatabase"
$containerName = "myContainer"
$partitionKeyPath = "/myPartitionKey"

Set-AzCosmosDBSqlContainer `
    -ResourceGroupName $resourceGroupName `
    -AccountName $accountName `
    -DatabaseName $databaseName `
    -Name $containerName `
    -PartitionKeyKind Hash `
    -PartitionKeyPath $partitionKeyPath
```

### <a name="create-an-azure-cosmos-db-container-with-a-large-partition-key-size"></a><a id="create-container-big-pk"></a>Skapa en Azure Cosmos DB-behållare med en stor partitionsnyckelstorlek

```azurepowershell-interactive
# Create an Azure Cosmos DB container with a large partition key value (version = 2)
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$databaseName = "myDatabase"
$containerName = "myContainer"
$partitionKeyPath = "/myPartitionKey"

Set-AzCosmosDBSqlContainer `
    -ResourceGroupName $resourceGroupName `
    -AccountName $accountName `
    -DatabaseName $databaseName `
    -Name $containerName `
    -PartitionKeyKind Hash `
    -PartitionKeyPath $partitionKeyPath `
    -PartitionKeyVersion 2
```

### <a name="get-the-throughput-of-an-azure-cosmos-db-container"></a><a id="get-container-ru"></a>Hämta dataflödet för en Azure Cosmos DB-behållare

```azurepowershell-interactive
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$databaseName = "myDatabase"
$containerName = "myContainer"

Get-AzCosmosDBSqlContainerThroughput `
    -ResourceGroupName $resourceGroupName `
    -AccountName $accountName `
    -DatabaseName $databaseName `
    -Name $containerName
```

### <a name="create-an-azure-cosmos-db-container-with-custom-index-policy"></a><a id="create-container-custom-index"></a>Skapa en Azure Cosmos DB-behållare med anpassad indexprincip

```azurepowershell-interactive
# Create a container with a custom indexing policy
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$databaseName = "myDatabase"
$containerName = "myContainer"
$partitionKeyPath = "/myPartitionKey"
$indexPathIncluded = "/*"
$indexPathExcluded = "/myExcludedPath/*"

$includedPathIndex = New-AzCosmosDBSqlIncludedPathIndex -DataType String -Kind Range
$includedPath = New-AzCosmosDBSqlIncludedPath -Path $indexPathIncluded -Index $includedPathIndex

$indexingPolicy = New-AzCosmosDBSqlIndexingPolicy `
    -IncludedPath $includedPath `
    -ExcludedPath $indexPathExcluded `
    -IndexingMode Consistent `
    -Automatic $true

Set-AzCosmosDBSqlContainer `
    -ResourceGroupName $resourceGroupName `
    -AccountName $accountName `
    -DatabaseName $databaseName `
    -Name $containerName `
    -PartitionKeyKind Hash `
    -PartitionKeyPath $partitionKeyPath `
    -IndexingPolicy $indexingPolicy
```

### <a name="create-an-azure-cosmos-db-container-with-indexing-turned-off"></a><a id="create-container-no-index"></a>Skapa en Azure Cosmos DB-behållare med indexering inaktiverad

```azurepowershell-interactive
# Create an Azure Cosmos DB container with no indexing
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$databaseName = "myDatabase"
$containerName = "myContainer"
$partitionKeyPath = "/myPartitionKey"

$indexingPolicy = New-AzCosmosDBSqlIndexingPolicy `
    -IndexingMode None

Set-AzCosmosDBSqlContainer `
    -ResourceGroupName $resourceGroupName `
    -AccountName $accountName `
    -DatabaseName $databaseName `
    -Name $containerName `
    -PartitionKeyKind Hash `
    -PartitionKeyPath $partitionKeyPath `
    -IndexingPolicy $indexingPolicy
```

### <a name="create-an-azure-cosmos-db-container-with-unique-key-policy-and-ttl"></a><a id="create-container-unique-key-ttl"></a>Skapa en Azure Cosmos DB-behållare med unik nyckelprincip och TTL

```azurepowershell-interactive
# Create a container with a unique key policy and TTL of one day
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$databaseName = "myDatabase"
$containerName = "myContainer"
$partitionKeyPath = "/myPartitionKey"
$uniqueKeyPath = "/myUniqueKeyPath"
$ttlInSeconds = 86400 # Set this to -1 (or don't use it at all) to never expire

$uniqueKey = New-AzCosmosDBSqlUniqueKey `
    -Path $uniqueKeyPath

$uniqueKeyPolicy = New-AzCosmosDBSqlUniqueKeyPolicy `
    -UniqueKey $uniqueKey

Set-AzCosmosDBSqlContainer `
    -ResourceGroupName $resourceGroupName `
    -AccountName $accountName `
    -DatabaseName $databaseName `
    -Name $containerName `
    -PartitionKeyKind Hash `
    -PartitionKeyPath $partitionKeyPath `
    -UniqueKeyPolicy $uniqueKeyPolicy `
    -TtlInSeconds $ttlInSeconds
```

### <a name="create-an-azure-cosmos-db-container-with-conflict-resolution"></a><a id="create-container-lww"></a>Skapa en Azure Cosmos DB-behållare med konfliktlösning

Om du vill skapa en konfliktlösningsprincip för att använda en lagrad procedur anger `"mode"="custom"` och anger du lösningssökvägen som namnet på den lagrade proceduren `"conflictResolutionPath"="myResolverStoredProcedure"`. Om du vill skriva alla konflikter till ConflictsFeed och hantera separat ställer du in `"mode"="custom"` och`"conflictResolutionPath"=""`

```azurepowershell-interactive
# Create container with last-writer-wins conflict resolution policy
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$databaseName = "myDatabase"
$containerName = "myContainer"
$partitionKeyPath = "/myPartitionKey"
$conflictResolutionPath = "/myResolutionPath"

$conflictResolutionPolicy = New-AzCosmosDBSqlConflictResolutionPolicy `
    -Type LastWriterWins `
    -Path $conflictResolutionPath

Set-AzCosmosDBSqlContainer `
    -ResourceGroupName $resourceGroupName `
    -AccountName $accountName `
    -DatabaseName $databaseName `
    -Name $containerName `
    -PartitionKeyKind Hash `
    -PartitionKeyPath $partitionKeyPath `
    -ConflictResolutionPolicy $conflictResolutionPolicy
```

### <a name="list-all-azure-cosmos-db-containers-in-a-database"></a><a id="list-containers"></a>Lista alla Azure Cosmos DB-behållare i en databas

```azurepowershell-interactive
# List all Azure Cosmos DB containers in a database
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$databaseName = "myDatabase"

Get-AzCosmosDBSqlContainer `
    -ResourceGroupName $resourceGroupName `
    -AccountName $accountName `
    -DatabaseName $databaseName
```

### <a name="get-a-single-azure-cosmos-db-container-in-a-database"></a><a id="get-container"></a>Skaffa en enda Azure Cosmos DB-behållare i en databas

```azurepowershell-interactive
# Get a single Azure Cosmos DB container in a database
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$databaseName = "myDatabase"
$containerName = "myContainer"

Get-AzCosmosDBSqlContainer `
    -ResourceGroupName $resourceGroupName `
    -AccountName $accountName `
    -DatabaseName $databaseName `
    -Name $containerName
```

### <a name="delete-an-azure-cosmos-db-container"></a><a id="delete-container"></a>Ta bort en Azure Cosmos DB-behållare

```azurepowershell-interactive
# Delete an Azure Cosmos DB container
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$databaseName = "myDatabase"
$containerName = "myContainer"

Remove-AzCosmosDBSqlContainer `
    -ResourceGroupName $resourceGroupName `
    -AccountName $accountName `
    -DatabaseName $databaseName `
    -Name $containerName
```

## <a name="next-steps"></a>Nästa steg

* [Alla PowerShell-exempel](powershell-samples.md)
* [Hantera Azure Cosmos-konto](how-to-manage-database-account.md)
* [Skapa en Azure Cosmos DB-behållare](how-to-create-container.md)
* [Konfigurera tid att leva i Azure Cosmos DB](how-to-time-to-live.md)

<!--Reference style links - using these makes the source content way more readable than using inline links-->

[powershell-install-configure]: https://docs.microsoft.com/azure/powershell-install-configure
[scaling-globally]: distribute-data-globally.md#EnableGlobalDistribution
[distribute-data-globally]: distribute-data-globally.md
[azure-resource-groups]: https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview#resource-groups
[azure-resource-tags]: https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags
[rp-rest-api]: https://docs.microsoft.com/rest/api/cosmos-db-resource-provider/
