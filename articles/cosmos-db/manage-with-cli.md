---
title: Hantera Azure Cosmos DB-resurser med Azure CLI
description: Använd Azure CLI för att hantera ditt Azure Cosmos DB konto, databas och behållare.
author: markjbrown
ms.service: cosmos-db
ms.topic: how-to
ms.date: 06/03/2020
ms.author: mjbrown
ms.openlocfilehash: fe348c2bbd901934c6365be6efefafb44ef8d875
ms.sourcegitcommit: 635114a0f07a2de310b34720856dd074aaf4f9cd
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/23/2020
ms.locfileid: "85262420"
---
# <a name="manage-azure-cosmos-resources-using-azure-cli"></a>Hantera Azure Cosmos-resurser med Azure CLI

I följande guide beskrivs vanliga kommandon för att automatisera hanteringen av dina Azure Cosmos DB-konton, databaser och containrar med hjälp av Azure CLI. Referens sidor för alla Azure Cosmos DB CLI-kommandon är tillgängliga i [Azure CLI-referensen](https://docs.microsoft.com/cli/azure/cosmosdb). Du kan också hitta fler exempel i [Azure CLI-exempel för Azure Cosmos DB](cli-samples.md), inklusive hur du skapar och hanterar Cosmos DB-konton, databaser och behållare för MongoDB, Gremlin, Cassandra och tabell-API.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Om du väljer att installera och använda CLI lokalt, kräver det här avsnittet att du kör Azure CLI-version 2.6.0 eller senare. Kör `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI](/cli/azure/install-azure-cli).

## <a name="azure-cosmos-accounts"></a>Azure Cosmos-konton

Följande avsnitt visar hur du hanterar Azure Cosmos-kontot, inklusive:

* [Skapa ett Azure Cosmos-konto](#create-an-azure-cosmos-db-account)
* [Lägga till eller ta bort regioner](#add-or-remove-regions)
* [Aktivera skrivningar i flera regioner](#enable-multiple-write-regions)
* [Ange regional redundans prioritet](#set-failover-priority)
* [Aktivera automatisk redundans](#enable-automatic-failover)
* [Utlös manuell redundans](#trigger-manual-failover)
* [Lista konto nycklar](#list-account-keys)
* [Lista med skrivskyddade konto nycklar](#list-read-only-account-keys)
* [Lista anslutnings strängar](#list-connection-strings)
* [Återskapa konto nyckel](#regenerate-account-key)

### <a name="create-an-azure-cosmos-db-account"></a>Skapa ett Azure Cosmos DB-konto

Skapa ett Azure Cosmos DB-konto med SQL API, konsekvens i sessioner i USA, västra 2 och USA, östra 2 regioner:

> [!IMPORTANT]
> Namnet på Azure Cosmos-kontot måste innehålla gemener och färre än 44 tecken.

```azurecli-interactive
resourceGroupName='MyResourceGroup'
accountName='mycosmosaccount' #needs to be lower case and less than 44 characters

az cosmosdb create \
    -n $accountName \
    -g $resourceGroupName \
    --default-consistency-level Session \
    --locations regionName='West US 2' failoverPriority=0 isZoneRedundant=False \
    --locations regionName='East US 2' failoverPriority=1 isZoneRedundant=False
```

### <a name="add-or-remove-regions"></a>Lägga till eller ta bort regioner

Skapa ett Azure Cosmos-konto med två regioner, Lägg till en region och ta bort en region.

> [!NOTE]
> Du kan inte lägga till eller ta bort regioner samtidigt `locations` och ändra andra egenskaper för ett Azure Cosmos-konto. Ändra regioner måste utföras som en separat åtgärd än andra ändringar i konto resursen.
> [!NOTE]
> Med det här kommandot kan du lägga till och ta bort regioner, men du kan inte ändra prioriteter för redundans eller utlösa en manuell redundansväxling. Se [ange prioritet för redundans](#set-failover-priority) och [utlösa manuell redundans](#trigger-manual-failover).

```azurecli-interactive
resourceGroupName='myResourceGroup'
accountName='mycosmosaccount'

# Create an account with 2 regions
az cosmosdb create --name $accountName --resource-group $resourceGroupName \
    --locations regionName="West US 2" failoverPriority=0 isZoneRedundant=False \
    --locations regionName="East US 2" failoverPriority=1 isZoneRedundant=False

# Add a region
az cosmosdb update --name $accountName --resource-group $resourceGroupName \
    --locations regionName="West US 2" failoverPriority=0 isZoneRedundant=False \
    --locations regionName="East US 2" failoverPriority=1 isZoneRedundant=False \
    --locations regionName="South Central US" failoverPriority=2 isZoneRedundant=False

# Remove a region
az cosmosdb update --name $accountName --resource-group $resourceGroupName \
    --locations regionName="West US 2" failoverPriority=0 isZoneRedundant=False \
    --locations regionName="East US 2" failoverPriority=1 isZoneRedundant=False
```

### <a name="enable-multiple-write-regions"></a>Aktivera flera Skriv regioner

Aktivera multi-master för ett Cosmos-konto

```azurecli-interactive
# Update an Azure Cosmos account from single to multi-master
resourceGroupName='myResourceGroup'
accountName='mycosmosaccount'

# Get the account resource id for an existing account
accountId=$(az cosmosdb show -g $resourceGroupName -n $accountName --query id -o tsv)

az cosmosdb update --ids $accountId --enable-multiple-write-locations true
```

### <a name="set-failover-priority"></a>Ange prioritet för redundans

Ange prioritet för redundans för ett Azure Cosmos-konto som kon figurer ATS för automatisk redundans

```azurecli-interactive
# Assume region order is initially 'West US 2'=0 'East US 2'=1 'South Central US'=2 for account
resourceGroupName='myResourceGroup'
accountName='mycosmosaccount'

# Get the account resource id for an existing account
accountId=$(az cosmosdb show -g $resourceGroupName -n $accountName --query id -o tsv)

# Make South Central US the next region to fail over to instead of East US 2
az cosmosdb failover-priority-change --ids $accountId \
    --failover-policies 'West US 2=0' 'South Central US=1' 'East US 2=2'
```

### <a name="enable-automatic-failover"></a>Aktivera automatisk redundans

```azurecli-interactive
# Enable automatic failover on an existing account
resourceGroupName='myResourceGroup'
accountName='mycosmosaccount'

# Get the account resource id for an existing account
accountId=$(az cosmosdb show -g $resourceGroupName -n $accountName --query id -o tsv)

az cosmosdb update --ids $accountId --enable-automatic-failover true
```

### <a name="trigger-manual-failover"></a>Utlös manuell redundans

> [!CAUTION]
> Om du ändrar region med prioritet = 0 utlöses en manuell redundans för ett Azure Cosmos-konto. Alla andra prioritets ändringar kommer inte att utlösa redundans.

```azurecli-interactive
# Assume region order is initially 'West US 2=0' 'East US 2=1' 'South Central US=2' for account
resourceGroupName='myResourceGroup'
accountName='mycosmosaccount'

# Get the account resource id for an existing account
accountId=$(az cosmosdb show -g $resourceGroupName -n $accountName --query id -o tsv)

# Trigger a manual failover to promote East US 2 as new write region
az cosmosdb failover-priority-change --ids $accountId \
    --failover-policies 'East US 2=0' 'South Central US=1' 'West US 2=2'
```

### <a name="list-all-account-keys"></a><a id="list-account-keys"></a>Lista alla konto nycklar

Hämta alla nycklar för ett Cosmos-konto.

```azurecli-interactive
# List all account keys
resourceGroupName='MyResourceGroup'
accountName='mycosmosaccount'

az cosmosdb keys list \
   -n $accountName \
   -g $resourceGroupName
```

### <a name="list-read-only-account-keys"></a>Lista med skrivskyddade konto nycklar

Hämta skrivskyddade nycklar för ett Cosmos-konto.

```azurecli-interactive
# List read-only account keys
resourceGroupName='MyResourceGroup'
accountName='mycosmosaccount'

az cosmosdb keys list \
    -n $accountName \
    -g $resourceGroupName \
    --type read-only-keys
```

### <a name="list-connection-strings"></a>Lista anslutnings strängar

Hämta anslutnings strängarna för ett Cosmos-konto.

```azurecli-interactive
# List connection strings
resourceGroupName='MyResourceGroup'
accountName='mycosmosaccount'

az cosmosdb keys list \
    -n $accountName \
    -g $resourceGroupName \
    --type connection-strings
```

### <a name="regenerate-account-key"></a>Återskapa konto nyckel

Återskapa en ny nyckel för ett Cosmos-konto.

```azurecli-interactive
# Regenerate secondary account keys
# key-kind values: primary, primaryReadonly, secondary, secondaryReadonly
az cosmosdb keys regenerate \
    -n $accountName \
    -g $resourceGroupName \
    --key-kind secondary
```

## <a name="azure-cosmos-db-database"></a>Azure Cosmos DB databas

Följande avsnitt visar hur du hanterar Azure Cosmos DB-databasen, inklusive:

* [Skapa en databas](#create-a-database)
* [Skapa en databas med delat data flöde](#create-a-database-with-shared-throughput)
* [Ändra databas data flöde](#change-database-throughput)
* [Hantera lås på en databas](#manage-lock-on-a-database)

### <a name="create-a-database"></a>Skapa en databas

Skapa en Cosmos-databas.

```azurecli-interactive
resourceGroupName='MyResourceGroup'
accountName='mycosmosaccount'
databaseName='database1'

az cosmosdb sql database create \
    -a $accountName \
    -g $resourceGroupName \
    -n $databaseName
```

### <a name="create-a-database-with-shared-throughput"></a>Skapa en databas med delat data flöde

Skapa en Cosmos-databas med delat data flöde.

```azurecli-interactive
resourceGroupName='MyResourceGroup'
accountName='mycosmosaccount'
databaseName='database1'
throughput=400

az cosmosdb sql database create \
    -a $accountName \
    -g $resourceGroupName \
    -n $databaseName \
    --throughput $throughput
```

### <a name="change-database-throughput"></a>Ändra databas data flöde

Öka data flödet för en Cosmos-databas med 1000 RU/s.

```azurecli-interactive
resourceGroupName='MyResourceGroup'
accountName='mycosmosaccount'
databaseName='database1'
newRU=1000

# Get minimum throughput to make sure newRU is not lower than minRU
minRU=$(az cosmosdb sql database throughput show \
    -g $resourceGroupName -a $accountName -n $databaseName \
    --query resource.minimumThroughput -o tsv)

if [ $minRU -gt $newRU ]; then
    newRU=$minRU
fi

az cosmosdb sql database throughput update \
    -a $accountName \
    -g $resourceGroupName \
    -n $databaseName \
    --throughput $newRU
```

### <a name="manage-lock-on-a-database"></a>Hantera lås på en databas

Lägg ett borttagnings lås på en databas. Mer information om hur du aktiverar detta finns i, [förhindra ändringar från SDK](role-based-access-control.md#preventing-changes-from-cosmos-sdk): er.

```azurecli-interactive
resourceGroupName='myResourceGroup'
accountName='my-cosmos-account'
databaseName='myDatabase'

lockType='CanNotDelete' # CanNotDelete or ReadOnly
databaseParent="databaseAccounts/$accountName"
databaseLockName="$databaseName-Lock"

# Create a delete lock on database
az lock create --name $databaseLockName \
    --resource-group $resourceGroupName \
    --resource-type Microsoft.DocumentDB/sqlDatabases \
    --lock-type $lockType \
    --parent $databaseParent \
    --resource $databaseName

# Delete lock on database
lockid=$(az lock show --name $databaseLockName \
        --resource-group $resourceGroupName \
        --resource-type Microsoft.DocumentDB/sqlDatabases \
        --resource $databaseName \
        --parent $databaseParent \
        --output tsv --query id)
az lock delete --ids $lockid
```

## <a name="azure-cosmos-db-container"></a>Azure Cosmos DB behållare

Följande avsnitt visar hur du hanterar Azure Cosmos DB container, inklusive:

* [Skapa en container](#create-a-container)
* [Skapa en behållare med TTL aktiverat](#create-a-container-with-ttl)
* [Skapa en behållare med en anpassad index princip](#create-a-container-with-a-custom-index-policy)
* [Ändra behållarens data flöde](#change-container-throughput)
* [Hantera lås på en behållare](#manage-lock-on-a-container)

### <a name="create-a-container"></a>Skapa en container

Skapa en Cosmos-behållare med standard index policy, partitionsnyckel och RU/s av 400.

```azurecli-interactive
# Create a SQL API container
resourceGroupName='MyResourceGroup'
accountName='mycosmosaccount'
databaseName='database1'
containerName='container1'
partitionKey='/myPartitionKey'
throughput=400

az cosmosdb sql container create \
    -a $accountName -g $resourceGroupName \
    -d $databaseName -n $containerName \
    -p $partitionKey --throughput $throughput
```

### <a name="create-a-container-with-ttl"></a>Skapa en behållare med TTL

Skapa en Cosmos-behållare med TTL aktiverat.

```azurecli-interactive
# Create an Azure Cosmos container with TTL of one day
resourceGroupName='myResourceGroup'
accountName='mycosmosaccount'
databaseName='database1'
containerName='container1'

az cosmosdb sql container update \
    -g $resourceGroupName \
    -a $accountName \
    -d $databaseName \
    -n $containerName \
    --ttl=86400
```

### <a name="create-a-container-with-a-custom-index-policy"></a>Skapa en behållare med en anpassad index princip

Skapa en Cosmos-behållare med en anpassad index princip, ett rums index, sammansatt index, en partitionsnyckel och RU/s av 400.

```azurecli-interactive
# Create a SQL API container
resourceGroupName='MyResourceGroup'
accountName='mycosmosaccount'
databaseName='database1'
containerName='container1'
partitionKey='/myPartitionKey'
throughput=400

# Generate a unique 10 character alphanumeric string to ensure unique resource names
uniqueId=$(env LC_CTYPE=C tr -dc 'a-z0-9' < /dev/urandom | fold -w 10 | head -n 1)

# Define the index policy for the container, include spatial and composite indexes
idxpolicy=$(cat << EOF
{
    "indexingMode": "consistent",
    "includedPaths": [
        {"path": "/*"}
    ],
    "excludedPaths": [
        { "path": "/headquarters/employees/?"}
    ],
    "spatialIndexes": [
        {"path": "/*", "types": ["Point"]}
    ],
    "compositeIndexes":[
        [
            { "path":"/name", "order":"ascending" },
            { "path":"/age", "order":"descending" }
        ]
    ]
}
EOF
)
# Persist index policy to json file
echo "$idxpolicy" > "idxpolicy-$uniqueId.json"


az cosmosdb sql container create \
    -a $accountName -g $resourceGroupName \
    -d $databaseName -n $containerName \
    -p $partitionKey --throughput $throughput \
    --idx @idxpolicy-$uniqueId.json

# Clean up temporary index policy file
rm -f "idxpolicy-$uniqueId.json"
```

### <a name="change-container-throughput"></a>Ändra behållarens data flöde

Öka data flödet för en Cosmos-behållare genom att 1000 RU/s.

```azurecli-interactive
resourceGroupName='MyResourceGroup'
accountName='mycosmosaccount'
databaseName='database1'
containerName='container1'
newRU=1000

# Get minimum throughput to make sure newRU is not lower than minRU
minRU=$(az cosmosdb sql container throughput show \
    -g $resourceGroupName -a $accountName -d $databaseName \
    -n $containerName --query resource.minimumThroughput -o tsv)

if [ $minRU -gt $newRU ]; then
    newRU=$minRU
fi

az cosmosdb sql container throughput update \
    -a $accountName \
    -g $resourceGroupName \
    -d $databaseName \
    -n $containerName \
    --throughput $newRU
```

### <a name="manage-lock-on-a-container"></a>Hantera lås på en behållare

Lägg ett borttagnings lås på en behållare. Mer information om hur du aktiverar detta finns i, [förhindra ändringar från SDK](role-based-access-control.md#preventing-changes-from-cosmos-sdk): er.

```azurecli-interactive
resourceGroupName='myResourceGroup'
accountName='my-cosmos-account'
databaseName='myDatabase'
containerName='myContainer'

lockType='CanNotDelete' # CanNotDelete or ReadOnly
databaseParent="databaseAccounts/$accountName"
containerParent="databaseAccounts/$accountName/sqlDatabases/$databaseName"
containerLockName="$containerName-Lock"

# Create a delete lock on container
az lock create --name $containerLockName \
    --resource-group $resourceGroupName \
    --resource-type Microsoft.DocumentDB/containers \
    --lock-type $lockType \
    --parent $containerParent \
    --resource $containerName

# Delete lock on container
lockid=$(az lock show --name $containerLockName \
        --resource-group $resourceGroupName \
        --resource-type Microsoft.DocumentDB/containers \
        --resource-name $containerName \
        --parent $containerParent \
        --output tsv --query id)
az lock delete --ids $lockid
```

## <a name="next-steps"></a>Nästa steg

Mer information om Azure CLI finns i:

- [Installera Azure CLI](/cli/azure/install-azure-cli)
- [Azure CLI-referens](https://docs.microsoft.com/cli/azure/cosmosdb)
- [Ytterligare Azure CLI-exempel för Azure Cosmos DB](cli-samples.md)
