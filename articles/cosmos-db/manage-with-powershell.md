---
title: Skapa och hantera Azure Cosmos DB med PowerShell
description: Använd Azure PowerShell för att hantera dina Azure Cosmos DB-konton, databaser, behållare och data flöde.
author: markjbrown
ms.service: cosmos-db
ms.topic: sample
ms.date: 08/05/2019
ms.author: mjbrown
ms.custom: seodec18
ms.openlocfilehash: e8f943ebaa5dfc06e0bfb04dc1097d6794ec6d05
ms.sourcegitcommit: e42c778d38fd623f2ff8850bb6b1718cdb37309f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/19/2019
ms.locfileid: "69616837"
---
# <a name="manage-azure-cosmos-db-sql-api-resources-using-powershell"></a>Hantera Azure Cosmos DB SQL API-resurser med hjälp av PowerShell

I följande guide beskrivs hur du använder PowerShell för att skripta och automatisera hanteringen av Azure Cosmos DB resurser, inklusive konto, databas, behållare och data flöde. Hantering av Azure Cosmos DB hanteras via AzResource-cmdleten direkt till Azure Cosmos DB Resource Provider. Om du vill visa alla egenskaper som kan hanteras med PowerShell för Azure Cosmos DB Resource Provider, se [Azure Cosmos DB Resource Provider-schema](/azure/templates/microsoft.documentdb/allversions)

För plattforms oberoende hantering av Azure Cosmos DB kan du använda [Azure CLI](manage-with-cli.md), [REST API][rp-rest-api]eller [Azure Portal](create-sql-api-dotnet.md#create-account).

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="getting-started"></a>Komma igång

Följ anvisningarna i [så här installerar och konfigurerar du Azure PowerShell][powershell-install-configure] för att installera och logga in på ditt Azure-konto i PowerShell.

* Om du vill köra följande kommandon utan att användaren ska bekräfta, Lägg till den `-Force` flaggan till kommandot.
* Följande kommandon är synkrona.

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
* [Visa en lista med anslutnings strängar för ett Azure Cosmos-konto](#list-connection-strings)
* [Ändra prioritet för redundans för ett Azure Cosmos-konto](#modify-failover-priority)

### <a id="create-account"></a>Skapa ett Azure Cosmos-konto

Det här kommandot skapar ett Azure Cosmos Database-konto med [flera regioner][distribute-data-globally], begränsade inaktuella [konsekvens principer](consistency-levels.md).

```azurepowershell-interactive
# Create an Azure Cosmos Account for Core (SQL) API
$resourceGroupName = "myResourceGroup"
$location = "West US 2"
$accountName = "mycosmosaccount" # must be lowercase and < 31 characters .

$locations = @(
    @{ "locationName"="West US 2"; "failoverPriority"=0 },
    @{ "locationName"="East US 2"; "failoverPriority"=1 }
)

$consistencyPolicy = @{
    "defaultConsistencyLevel"="BoundedStaleness";
    "maxIntervalInSeconds"=300;
    "maxStalenessPrefix"=100000
}

$CosmosDBProperties = @{
    "databaseAccountOfferType"="Standard";
    "locations"=$locations;
    "consistencyPolicy"=$consistencyPolicy;
    "enableMultipleWriteLocations"="false"
}

New-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName -Location $location `
    -Name $accountName -PropertyObject $CosmosDBProperties
```

* `$accountName`Namnet på Azure Cosmos-kontot. Måste vara gemener, accepterar alfanumeriska tecken och tecknet "-", och mellan 3 och 31 tecken.
* `$location`Platsen för Azure Cosmos-konto resursen.
* `$locations`Databas kontots replik regioner. Det måste finnas en Skriv region per databas konto med ett prioritets värde för redundans på 0.
* `$consistencyPolicy`Standard konsekvens nivån för Azure Cosmos-kontot. Mer information finns i [Konsekvensnivåer i Azure Cosmos DB](consistency-levels.md).
* `$CosmosDBProperties`Egenskaps värden som skickas till Cosmos DB Azure Resource Manager-providern för att etablera kontot.

Azure Cosmos-konton kan konfigureras med IP-brandvägg samt Virtual Network tjänst slut punkter. Information om hur du konfigurerar IP-brandväggen för Azure Cosmos DB finns i [Konfigurera IP-](how-to-configure-firewall.md)brandvägg.  Mer information om hur du aktiverar tjänstens slut punkter för Azure Cosmos DB finns i [Konfigurera åtkomst från virtuella nätverk](how-to-configure-vnet-service-endpoint.md).

### <a id="list-accounts"></a>Lista alla Azure Cosmos-konton i en prenumeration

Med det här kommandot kan du Visa en lista över alla Azure Cosmos-konton i en prenumeration.

```azurepowershell-interactive
# List Azure Cosmos Accounts

Get-AzResource -ResourceType Microsoft.DocumentDb/databaseAccounts | ft
```

### <a id="get-account"></a>Hämta egenskaperna för ett Azure Cosmos-konto

Med det här kommandot kan du hämta egenskaperna för ett befintligt Azure Cosmos-konto.

```azurepowershell-interactive
# Get the properties of an Azure Cosmos Account

$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"

Get-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName `
    -Name $accountName | Select-Object Properties
```

### <a id="update-account"></a>Uppdatera ett Azure Cosmos-konto

Med det här kommandot kan du uppdatera egenskaperna för ditt Azure Cosmos-databas konto. De egenskaper som kan uppdateras är följande:

* Lägga till eller ta bort regioner
* Ändra standard konsekvens princip
* Ändra filter för IP-intervall
* Ändra Virtual Network konfigurationer
* Aktivera flera huvud

> [!NOTE]
> Med det här kommandot kan du lägga till och ta bort regioner, men du kan inte ändra prioriteter för redundans eller `failoverPriority=0`ändra regionen med. Om du vill ändra prioriteten för redundans läser du [ändra prioritet för redundans för ett Azure Cosmos-konto](#modify-failover-priority).

```azurepowershell-interactive
# Get an Azure Cosmos Account (assume it has two regions currently West US 2 and East US 2) and add a third region

$resourceGroupName = "myResourceGroup"
$accountName = "myaccountname"

$account = Get-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName -Name $accountName

$locations = @(
    @{ "locationName"="West US 2"; "failoverPriority"=0 },
    @{ "locationName"="East US 2"; "failoverPriority"=1 },
    @{ "locationName"="South Central US"; "failoverPriority"=2 }
)

$account.Properties.locations = $locations
$CosmosDBProperties = $account.Properties

Set-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName `
    -Name $accountName -PropertyObject $CosmosDBProperties
```

### <a id="delete-account"></a>Ta bort ett Azure Cosmos-konto

Med det här kommandot kan du ta bort ett befintligt Azure Cosmos-konto.

```azurepowershell-interactive
# Delete an Azure Cosmos Account
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"

Remove-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName `
    -Name $accountName
```

### <a id="update-tags"></a>Uppdatera taggar för ett Azure Cosmos-konto

I följande exempel beskrivs hur du ställer in [Azures resurs Taggar][azure-resource-tags] för ett Azure Cosmos-konto.

> [!NOTE]
> Det här kommandot kan kombineras med kommandon för att skapa eller uppdatera genom att lägga till den `-Tags` flagga motsvarande parameter.

```azurepowershell-interactive
# Update tags for an Azure Cosmos Account

$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"

$tags = @{
    "dept" = "Finance";
    "environment" = "Production"
}

Set-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName `
    -Name $accountName -Tags $tags
```

### <a id="list-keys"></a> Lista nycklar

När du skapar ett Azure Cosmos DB-konto, genererar två master åtkomstnycklar som kan användas för autentisering när Azure Cosmos DB-kontot används i tjänsten. Genom att tillhandahålla två åtkomstnycklar för kan Azure Cosmos DB du återskapa nycklarna utan avbrott på ditt Azure Cosmos DB-konto. Det finns också skrivskyddade nycklar för att autentisera skrivskyddade åtgärder. Det finns två skrivskyddade nycklar (primära och sekundära) och två skrivskyddade nycklar (primär eller sekundär).

```azurepowershell-interactive
# List keys for an Azure Cosmos Account

$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"

$keys = Invoke-AzResourceAction -Action listKeys `
    -ResourceType "Microsoft.DocumentDb/databaseAccounts" -ApiVersion "2015-04-08" `
    -ResourceGroupName $resourceGroupName -Name $accountName

Select-Object $keys
```

### <a id="list-connection-strings"></a> Lista anslutningssträngar

För MongoDB-konton, kan anslutningssträngen för att ansluta din MongoDB-app till databaskontot hämtas med hjälp av följande kommando.

```azurepowershell-interactive
# List connection strings for an Azure Cosmos Account

$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"

$keys = Invoke-AzResourceAction -Action listConnectionStrings `
    -ResourceType "Microsoft.DocumentDb/databaseAccounts" -ApiVersion "2015-04-08" `
    -ResourceGroupName $resourceGroupName -Name $accountName

Select-Object $keys
```

### <a id="regenerate-keys"></a>Återskapa konto nycklar

Åtkomst nycklar till ett Azure Cosmos-konto bör återskapas regelbundet för att hjälpa till att hålla anslutningarna säkrare. Ett primärt och sekundärt åtkomst nycklar tilldelas till kontot. Detta gör att klienter kan upprätthålla åtkomst medan den andra återskapas. Det finns fyra typer av nycklar för ett Azure Cosmos-konto (primär, sekundär, PrimaryReadonly och SecondaryReadonly)

```azurepowershell-interactive
# Regenerate the primary key for an Azure Cosmos Account

$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"

$keyKind = @{ "keyKind"="Primary" }

$keys = Invoke-AzResourceAction -Action regenerateKey `
    -ResourceType "Microsoft.DocumentDb/databaseAccounts" -ApiVersion "2015-04-08" `
    -ResourceGroupName $resourceGroupName -Name $accountName -Parameters $keyKind

Select-Object $keys
```

### <a id="modify-failover-priority"></a>Ändra prioritet för redundans

För databas konton med flera regioner kan du ändra i vilken ordning ett Cosmos-konto ska befordra sekundära skrivskyddade repliker om en regional redundans inträffar på den primära Skriv repliken. Att `failoverPriority=0` ändra kan också användas för att initiera en haveri beredskap för att testa haveri beställnings planeringen.

I exemplet nedan antar du att kontot har en aktuell växlings prioritet för `West US 2 = 0` och `East US 2 = 1` och vänder regionerna.

> [!CAUTION]
> Om `locationName` du `failoverPriority=0` ändrar för aktive ras en manuell redundansväxling för ett Azure Cosmos-konto. Eventuella andra prioritets ändringar kommer inte att utlösa redundans.

```azurepowershell-interactive
# Change the failover priority for an Azure Cosmos Account
# Assume existing priority is "West US 2" = 0 and "East US 2" = 1

$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"

$failoverRegions = @(
    @{ "locationName"="East US 2"; "failoverPriority"=0 },
    @{ "locationName"="West US 2"; "failoverPriority"=1 }
)

$failoverPolicies = @{
    "failoverPolicies"= $failoverRegions
}

Invoke-AzResourceAction -Action failoverPriorityChange `
    -ResourceType "Microsoft.DocumentDb/databaseAccounts" -ApiVersion "2015-04-08" `
    -ResourceGroupName $resourceGroupName -Name $accountName -Parameters $failoverPolicies
```

## <a name="azure-cosmos-database"></a>Azure Cosmos-databas

Följande avsnitt visar hur du hanterar Azure Cosmos-databasen, inklusive:

* [Skapa en Azure Cosmos-databas](#create-db)
* [Skapa en Azure Cosmos-databas med delat data flöde](#create-db-ru)
* [Hämta data flödet för en Azure Cosmos-databas](#get-db-ru)
* [Lista alla Azure Cosmos-databaser i ett konto](#list-db)
* [Hämta en enda Azure Cosmos-databas](#get-db)
* [Ta bort en Azure Cosmos-databas](#delete-db)

### <a id="create-db"></a>Skapa en Azure Cosmos-databas

```azurepowershell-interactive
# Create an Azure Cosmos database
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$databaseName = "database1"
$resourceName = $accountName + "/sql/" + $databaseName

$DataBaseProperties = @{
    "resource"=@{"id"=$databaseName}
}

New-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts/apis/databases" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName `
    -Name $resourceName -PropertyObject $DataBaseProperties
```

### <a id="create-db-ru"></a>Skapa en Azure Cosmos-databas med delat data flöde

```azurepowershell-interactive
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$databaseName = "database2"
$resourceName = $accountName + "/sql/" + $databaseName

$DataBaseProperties = @{
    "resource"=@{ "id"=$databaseName };
    "options"=@{ "Throughput"="400" }
}

New-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts/apis/databases" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName `
    -Name $resourceName -PropertyObject $DataBaseProperties
```

### <a id="get-db-ru"></a>Hämta data flödet för en Azure Cosmos-databas

```azurepowershell-interactive
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$databaseName = "database1"
$containerName = "container1"
$databaseThroughputResourceType = "Microsoft.DocumentDb/databaseAccounts/apis/databases/settings"
$databaseThroughputResourceName = $accountName + "/sql/" + $databaseName + "/throughput"

Get-AzResource -ResourceType $databaseThroughputResourceType `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName `
    -Name $databaseThroughputResourceName  | Select-Object Properties
```

### <a id="list-db"></a>Hämta alla Azure Cosmos-databaser i ett konto

```azurepowershell-interactive
# Get all databases in an Azure Cosmos account
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$resourceName = $accountName + "/sql/"

Get-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts/apis/databases" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName `
    -Name $resourceName  | Select-Object Properties
```

### <a id="get-db"></a>Hämta en enda Azure Cosmos-databas

```azurepowershell-interactive
# Get a single database in an Azure Cosmos account
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$databaseName = "database1"
$resourceName = $accountName + "/sql/" + $databaseName

Get-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts/apis/databases" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName `
    -Name $resourceName | Select-Object Properties
```

### <a id="delete-db"></a>Ta bort en Azure Cosmos-databas

```azurepowershell-interactive
# Delete a database in an Azure Cosmos account
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$databaseName = "database1"
$resourceName = $accountName + "/sql/" + $databaseName
Remove-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts/apis/databases" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName -Name $resourceName
```

## <a name="azure-cosmos-container"></a>Azure Cosmos-behållare

Följande avsnitt visar hur du hanterar Azure Cosmos-behållaren, inklusive:

* [Skapa en Azure Cosmos-behållare](#create-container)
* [Skapa en Azure Cosmos-behållare med en stor partitionsnyckel](#create-container-big-pk)
* [Hämta data flödet för en Azure Cosmos-behållare](#get-container-ru)
* [Skapa en Azure Cosmos-behållare med delat data flöde](#create-container-ru)
* [Skapa en Azure Cosmos-behållare med anpassad indexering](#create-container-custom-index)
* [Skapa en Azure Cosmos-behållare med indexering inaktive rad](#create-container-no-index)
* [Skapa en Azure Cosmos-behållare med unik nyckel och TTL](#create-container-unique-key-ttl)
* [Skapa en Azure Cosmos-behållare med konflikt lösning](#create-container-lww)
* [Lista alla Azure Cosmos-behållare i en databas](#list-containers)
* [Hämta en enda Azure Cosmos-behållare i en databas](#get-container)
* [Ta bort en Azure Cosmos-behållare](#delete-container)

### <a id="create-container"></a>Skapa en Azure Cosmos-behållare

```azurepowershell-interactive
# Create an Azure Cosmos container with default indexes and throughput at 400 RU
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$databaseName = "database1"
$containerName = "container1"
$resourceName = $accountName + "/sql/" + $databaseName + "/" + $containerName

$ContainerProperties = @{
    "resource"=@{
        "id"=$containerName;
        "partitionKey"=@{
            "paths"=@("/myPartitionKey");
            "kind"="Hash"
        }
    };
    "options"=@{ "Throughput"="400" }
}

New-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts/apis/databases/containers" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName `
    -Name $resourceName -PropertyObject $ContainerProperties
```

### <a id="create-container-big-pk"></a>Skapa en Azure Cosmos-behållare med en stor nyckel storlek

```azurepowershell-interactive
# Create an Azure Cosmos container with a large partition key value (version = 2)
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$databaseName = "database1"
$containerName = "container1"
$resourceName = $accountName + "/sql/" + $databaseName + "/" + $containerName

$ContainerProperties = @{
    "resource"=@{
        "id"=$containerName;
        "partitionKey"=@{
            "paths"=@("/myPartitionKey");
            "kind"="Hash";
            "version" = 2
        }
    };
    "options"=@{ "Throughput"="400" }
}

New-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts/apis/databases/containers" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName `
    -Name $resourceName -PropertyObject $ContainerProperties
```

### <a id="get-container-ru"></a>Hämta data flödet för en Azure Cosmos-behållare

```azurepowershell-interactive
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$databaseName = "database1"
$containerName = "container1"
$containerThroughputResourceType = "Microsoft.DocumentDb/databaseAccounts/apis/databases/containers/settings"
$containerThroughputResourceName = $accountName + "/sql/" + $databaseName + "/" + $containerName + "/throughput"

Get-AzResource -ResourceType $containerThroughputResourceType `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName `
    -Name $containerThroughputResourceName  | Select-Object Properties
```

### <a id="create-container-ru"></a>Skapa en Azure Cosmos-behållare med delat data flöde

```azurepowershell-interactive
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$databaseName = "database1"
$containerName = "container2"
$resourceName = $accountName + "/sql/" + $databaseName + "/" + $containerName

$ContainerProperties = @{
    "resource"=@{
        "id"=$containerName; 
        "partitionKey"=@{
            "paths"=@("/myPartitionKey");
            "kind"="Hash"
        }
    };
    "options"=@{}
}

New-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts/apis/databases/containers" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName `
    -Name $resourceName -PropertyObject $ContainerProperties 
```

### <a id="create-container-custom-index"></a>Skapa en Azure Cosmos-behållare med en anpassad index princip

```azurepowershell-interactive
# Create a container with a custom indexing policy
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$databaseName = "database1"
$containerName = "container3"
$resourceName = $accountName + "/sql/" + $databaseName + "/" + $containerName

$ContainerProperties = @{
    "resource"=@{
        "id"=$containerName; 
        "partitionKey"=@{
            "paths"=@("/myPartitionKey"); 
            "kind"="Hash"
        }; 
        "indexingPolicy"=@{
            "indexingMode"="Consistent";
            "includedPaths"= @(@{
                "path"="/*";
                "indexes"= @()
            });
            "excludedPaths"= @(@{
                "path"="/myPathToNotIndex/*"
            })
        }
    };
    "options"=@{ "Throughput"="400" }
}

New-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts/apis/databases/containers" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName `
    -Name $resourceName -PropertyObject $ContainerProperties
```

### <a id="create-container-no-index"></a>Skapa en Azure Cosmos-behållare med indexering inaktive rad

```azurepowershell-interactive
# Create an Azure Cosmos container with no indexing
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$databaseName = "database1"
$containerName = "container4"
$resourceName = $accountName + "/sql/" + $databaseName + "/" + $containerName

$ContainerProperties = @{
    "resource"=@{
        "id"=$containerName; 
        "partitionKey"=@{
            "paths"=@("/myPartitionKey");
            "kind"="Hash"
        };
        "indexingPolicy"=@{
            "indexingMode"="none"
        }
    };
    "options"=@{ "Throughput"="400" }
} 

New-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts/apis/databases/containers" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName `
    -Name $resourceName -PropertyObject $ContainerProperties
```

### <a id="create-container-unique-key-ttl"></a>Skapa en Azure Cosmos-behållare med unik nyckel princip och TTL

```azurepowershell-interactive
# Create a container with a unique key policy and TTL
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$databaseName = "database1"
$containerName = "container5"
$resourceName = $accountName + "/sql/" + $databaseName + "/" + $containerName

$ContainerProperties = @{
    "resource"=@{
        "id"=$containerName;
        "partitionKey"=@{
            "paths"=@("/myPartitionKey");
            "kind"="Hash"
        }; 
        "indexingPolicy"=@{
            "indexingMode"="Consistent";
            "includedPaths"= @(@{
                "path"="/*";
                "indexes"= @()
            });
            "excludedPaths"= @()
        };
        "uniqueKeyPolicy"= @{
            "uniqueKeys"= @(@{
                "paths"= @(
                    "/myUniqueKey1";
                    "/myUniqueKey2"
                )
            })
        };
        "defaultTtl"= 100;
    };
    "options"=@{ "Throughput"="400" }
}

New-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts/apis/databases/containers" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName `
    -Name $resourceName -PropertyObject $ContainerProperties
```

### <a id="create-container-lww"></a>Skapa en Azure Cosmos-behållare med konflikt lösning

Om du vill skapa en konflikt lösnings princip för att använda en `"mode"="custom"` lagrad procedur ställer du in och anger matchnings Sök vägen som `"conflictResolutionPath"="myResolverStoredProcedure"`namnet på den lagrade proceduren. För att skriva alla konflikter till ConflictsFeed och hantera separat, ange `"mode"="custom"` och`"conflictResolutionPath"=""`

```azurepowershell-interactive
# Create container with last-writer-wins conflict resolution policy
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$databaseName = "database1"
$containerName = "container6"
$resourceName = $accountName + "/sql/" + $databaseName + "/" + $containerName

$ContainerProperties = @{
    "resource"=@{
        "id"=$containerName;
        "partitionKey"=@{
            "paths"=@("/myPartitionKey");
            "kind"="Hash"
        };
        "conflictResolutionPolicy"=@{
            "mode"="lastWriterWins";
            "conflictResolutionPath"="/myResolutionPath"
        }
    };
    "options"=@{ "Throughput"="400" }
}

New-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts/apis/databases/containers" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName `
    -Name $resourceName -PropertyObject $ContainerProperties
```

### <a id="list-containers"></a>Lista alla Azure Cosmos-behållare i en databas

```azurepowershell-interactive
# List all Azure Cosmos containers in a database
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$databaseName = "database1"
$resourceName = $accountName + "/sql/" + $databaseName

Get-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts/apis/databases/containers" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName `
    -Name $resourceName | Select-Object Properties
```

### <a id="get-container"></a>Hämta en enda Azure Cosmos-behållare i en databas

```azurepowershell-interactive
# Get a single Azure Cosmos container in a database
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$databaseName = "database1"
$containerName = "container5"
$resourceName = $accountName + "/sql/" + $databaseName + "/" + $containerName

Get-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts/apis/databases/containers" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName `
    -Name $resourceName | Select-Object Properties
```

### <a id="delete-container"></a>Ta bort en Azure Cosmos-behållare

```azurepowershell-interactive
# Delete an Azure Cosmos container
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$databaseName = "database1"
$containerName = "container1"
$resourceName = $accountName + "/sql/" + $databaseName + "/" + $containerName

Remove-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts/apis/databases/containers" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName -Name $resourceName
```

## <a name="next-steps"></a>Nästa steg

* [Alla PowerShell-exempel](powershell-samples.md)
* [Hantera Azure Cosmos-konto](how-to-manage-database-account.md)
* [Skapa en Azure Cosmos-behållare](how-to-create-container.md)
* [Konfigurera Time-to-Live i Azure Cosmos DB](how-to-time-to-live.md)

<!--Reference style links - using these makes the source content way more readable than using inline links-->

[powershell-install-configure]: https://docs.microsoft.com/azure/powershell-install-configure
[scaling-globally]: distribute-data-globally.md#EnableGlobalDistribution
[distribute-data-globally]: distribute-data-globally.md
[azure-resource-groups]: https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview#resource-groups
[azure-resource-tags]: https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags
[rp-rest-api]: https://docs.microsoft.com/rest/api/cosmos-db-resource-provider/
