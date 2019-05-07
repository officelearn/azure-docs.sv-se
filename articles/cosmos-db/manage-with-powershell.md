---
title: Skapa och hantera Azure Cosmos DB med PowerShell
description: Använd Azure Powershell hantera ditt Azure Cosmos DB-konton, databaser, behållare och dataflöde.
author: markjbrown
ms.service: cosmos-db
ms.topic: samples
ms.date: 05/06/2019
ms.author: mjbrown
ms.custom: seodec18
ms.openlocfilehash: 347c3a1c6cabca9532e5ada1237f2933841d5094
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/06/2019
ms.locfileid: "65077597"
---
# <a name="manage-azure-cosmos-db-sql-api-resources-using-powershell"></a>Hantera Azure Cosmos DB SQL API-resurser med hjälp av PowerShell

Enligt följande anvisningar beskriver hur du använder PowerShell till skript och automatisera hanteringen av Azure Cosmos DB, såsom konto, databas, behållare och dataflöde. Hantering av Azure Cosmos DB är inte via Azure Cosmos DB specifika cmdletar men med resursprovidern direkt med hjälp av cmdleten AzResource. Om du vill visa alla egenskaper som kan hanteras med hjälp av PowerShell för Azure Cosmos DB-resursprovidern, se [providerschemat i Azure Cosmos DB-resurs](/azure/templates/microsoft.documentdb/allversions)

Du kan använda för plattformsoberoende hantering av Azure Cosmos DB, [Azure CLI](manage-with-cli.md), [REST API][rp-rest-api], eller [Azure-portalen](create-sql-api-dotnet.md#create-account).

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="getting-started"></a>Komma igång

Följ instruktionerna i [hur du installerar och konfigurerar du Azure PowerShell] [ powershell-install-configure] att installera och logga in på ditt Azure-konto i Powershell.

* Om du vill köra följande kommandon utan att användaren ska bekräfta, Lägg till den `-Force` flaggan till kommandot.
* Följande kommandon är synkrona.

## <a name="azure-cosmos-accounts"></a>Azure Cosmos-konton

I följande avsnitt visar hur du kan hantera Azure Cosmos-konto, inklusive:

* [Skapa ett Azure Cosmos-konto](#create-account)
* [Uppdatera ett Azure Cosmos-konto](#update-account)
* [Skaffa en Azure Cosmos-konto](#get-account)
* [Ta bort ett Azure Cosmos-konto](#delete-account)
* [Uppdatera taggar för ett Azure Cosmos-konto](#update-tags)
* [Lista över nycklar för ett Azure Cosmos-konto](#list-keys)
* [Återskapa nycklar för ett Azure Cosmos-konto](#regenerate-keys)
* [Lista anslutningssträngar för ett Azure Cosmos-konto](#list-connection-strings)
* [Ändra redundansprioritet för ett Azure Cosmos-konto](#modify-failover-priority)

### <a id="create-account"></a> Skapa ett Azure Cosmos-konto

Det här kommandot kan du skapa ett Azure Cosmos DB-databaskonto. Konfigurera ditt nya databaskonto som antingen en region eller [flera regioner] [ distribute-data-globally] med en viss [konsekvens princip](consistency-levels.md).

```azurepowershell-interactive
# Create an Azure Cosmos Account for Core (SQL) API
$resourceGroupName = "myResourceGroup"
$location = "West US"
$accountName = "mycosmosaccount" # must be lower case.

$locations = @(
    @{ "locationName"="West US"; "failoverPriority"=0 },
    @{ "locationName"="East US"; "failoverPriority"=1 }
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
    "enableMultipleWriteLocations"="true"
}

New-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName -Location $location `
    -Name $accountName -PropertyObject $CosmosDBProperties
```

* `$accountName` Namn för Azure Cosmos-kontot. Måste vara i gemener, accepterar alfanumeriska och '-' tecknet, och mellan 3 och 50 tecken.
* `$location` Platsen för Azure Cosmos-kontot.
* `$locations` Repliken regioner för kontot. Det måste finnas en skrivregion per konto med ett prioritetsvärde för växling vid fel på 0.
* `$consistencyPolicy` Standard-konsekvensnivå på Azure Cosmos-kontot. Mer information finns i [Konsekvensnivåer i Azure Cosmos DB](consistency-levels.md).
* `$CosmosDBProperties` Värden har överförts till Cosmos DB Azure Resource Manager-providern att tillhandahålla kontot.

Azure Cosmos konton kan konfigureras med IP-brandvägg och virtuellt nätverk tjänstens slutpunkter. Information om hur du konfigurerar IP-Brandvägg för Azure Cosmos DB finns i [konfigurera brandväggen för IP-](how-to-configure-firewall.md).  Mer information om hur du aktiverar Tjänsteslutpunkter för Azure Cosmos DB finns i [Konfigurera åtkomst från virtuella nätverk](how-to-configure-vnet-service-endpoint.md) .

### <a id="get-account"></a> Hämta egenskaperna för ett Azure Cosmos-konto

Det här kommandot kan du hämta egenskaperna för ett befintligt Azure Cosmos-konto.

```azurepowershell-interactive
# Get the properties of an Azure Cosmos Account

$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"

Get-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName `
    -Name $accountName | Select-Object Properties
```

### <a id="update-account"></a> Uppdatera ett Azure Cosmos-konto

Det här kommandot kan du uppdatera egenskaperna för databasen ditt Azure Cosmos DB. Följande: egenskaper som kan uppdateras

* Att lägga till eller ta bort regioner
* Ändra standardprincipen för konsekvens
* Ändra redundansprincip
* Ändra IP-Adressintervallfilter
* Ändra konfigurationer av virtuella
* Aktivera multimaster

> [!NOTE]
> Det här kommandot kan du lägga till och ta bort regioner, men tillåter inte att ändra prioriteter för redundans. Ändring av redundansprioriteringar finns i [ändra redundansprioritet för ett Azure Cosmos-konto](#modify-failover-priority).

```azurepowershell-interactive
# Update an Azure Cosmos Account and set Consistency level to Session

$resourceGroupName = "myResourceGroup"
$accountName = "myaccountname"

$account = Get-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName -Name $accountName

$consistencyPolicy = @{ "defaultConsistencyLevel"="Session" }

$account.Properties.consistencyPolicy = $consistencyPolicy
$CosmosDBProperties = $account.Properties

Set-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName `
    -Name $accountName -PropertyObject $CosmosDBProperties
```

### <a id="delete-account"></a> Ta bort ett Azure Cosmos-konto

Det här kommandot kan du ta bort ett befintligt Azure Cosmos-konto.

```azurepowershell-interactive
# Delete an Azure Cosmos Account
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"

Remove-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName `
    -Name $accountName
```

### <a id="update-tags"></a> Uppdatera taggar för ett Azure Cosmos-konto

I följande exempel beskrivs hur du ställer in [Azure-resurstaggar] [ azure-resource-tags] för ett Azure Cosmos-konto.

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

### <a id="regenerate-keys"></a> Återskapa nycklar

Åtkomstnycklar till ett Azure Cosmos-konto ska återskapas med jämna mellanrum för att skydda anslutningar. En primär och sekundär åtkomstnycklar tilldelas till kontot. På så sätt kan klienter att upprätthålla åtkomsten medan den andra återskapas. Det finns fyra typer av nycklar för ett Azure Cosmos-konto (primär, sekundär, PrimaryReadonly och SecondaryReadonly)

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

### <a id="modify-failover-priority"></a> Ändra Redundansprioritet

För databaskonton, kan du ändra redundansprioritet av de olika regioner som Azure Cosmos DB-databaskontot finns i. Läs mer på redundans i ditt Azure Cosmos DB-databaskonto [distribuera data globalt med Azure Cosmos DB][distribute-data-globally].

För exemplet nedan, förutsätter att kontot har en aktuell redundansprioritet av westus = 0 och eastus = 1. Det här exemplet nedan ska vända regionerna.

> [!CAUTION]
> Den här åtgärden utlöser en manuell redundans för ditt konto till den nya regionen med en failoverPriority noll.

```azurepowershell-interactive
# Change the failover priority for an Azure Cosmos Account

$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"

$failoverPolicies = @(
    @{ "locationName"="East US"; "failoverPriority"=0 },
    @{ "locationName"="West US"; "failoverPriority"=1 }
)

Invoke-AzResourceAction -Action failoverPriorityChange `
    -ResourceType "Microsoft.DocumentDb/databaseAccounts" -ApiVersion "2015-04-08" `
    -ResourceGroupName $resourceGroupName -Name $accountName -Parameters $failoverPolicies
```

## <a name="azure-cosmos-database"></a>Azure Cosmos Database

I följande avsnitt visar hur du kan hantera Azure Cosmos-databasen, inklusive:

* [Skapa en Azure Cosmos-databas](#create-db)
* [Skapa en Azure Cosmos-databas med delade dataflöde](#create-db-ru)
* [Lista över alla Azure Cosmos-databaser i ett konto](#get-all-db)
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

### <a id="create-db-ru"></a>Skapa en Azure Cosmos-databas med delade dataflöde

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

### <a id="get-all-db"></a>Hämta alla Azure Cosmos-databaser i ett konto

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

## <a name="azure-cosmos-container"></a>Azure Cosmos Container

I följande avsnitt visar hur du kan hantera Azure Cosmos-behållaren, inklusive:

* [Skapa en Azure Cosmos-behållare](#create-container)
* [Skapa en Azure Cosmos-behållare med delade dataflöde](#create-container-ru)
* [Skapa ett Azure Cosmos-behållare med anpassad indexprincip](#create-container-custom-index)
* [Skapa ett Azure Cosmos-behållare med indexering stängs av](#create-container-no-index)
* [Skapa ett Azure Cosmos-behållare med unik nyckel och TTL](#create-container-unique-key-ttl)
* [Skapa ett Azure Cosmos-behållare med konfliktlösning](#create-container-lww)
* [Lista över alla Azure Cosmos-behållare i en databas](#list-all-container)
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

### <a id="create-container-ru"></a>Skapa en Azure Cosmos-behållare med delade dataflöde

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

### <a id="create-container-custom-index"></a>Skapa en Azure Cosmos-behållare med anpassat index princip

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
                "indexes"= @(@{
                        "kind"="Range";
                        "dataType"="number";
                        "precision"=-1
                    },
                    @{
                        "kind"="Range";
                        "dataType"="string";
                        "precision"=-1
                    }
                )
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

### <a id="create-container-no-index"></a>Skapa ett Azure Cosmos-behållare med indexering stängs av

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

### <a id="create-container-unique-key-ttl"></a>Skapa ett Azure Cosmos-behållare med unika policy och TTL

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
                "indexes"= @(@{
                        "kind"="Range";
                        "dataType"="number";
                        "precision"=-1
                    },
                    @{
                        "kind"="Range";
                        "dataType"="string";
                        "precision"=-1
                    }
                )
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

### <a id="create-container-lww"></a>Skapa ett Azure Cosmos-behållare med konfliktlösning

Så här skapar du en principen för konfliktlösning för att använda en lagrad procedur `"mode"="custom"` och ange sökvägen upplösning som namnet på den lagrade proceduren `"conflictResolutionPath"="myResolverStoredProcedure"`. Om du vill skriva alla konflikter till ConflictsFeed och hantera separat, ange `"mode"="custom"` och `"conflictResolutionPath"=""`

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

### <a id="list-all-container"></a>Lista över alla Azure Cosmos-behållare i en databas

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
* [Konfigurera time-to-live i Azure Cosmos DB](how-to-time-to-live.md)

<!--Reference style links - using these makes the source content way more readable than using inline links-->

[powershell-install-configure]: https://docs.microsoft.com/azure/powershell-install-configure
[scaling-globally]: distribute-data-globally.md#EnableGlobalDistribution
[distribute-data-globally]: distribute-data-globally.md
[azure-resource-groups]: https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview#resource-groups
[azure-resource-tags]: https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags
[rp-rest-api]: https://docs.microsoft.com/rest/api/cosmos-db-resource-provider/