---
title: Lär dig hur du hanterar databaskonton i Azure Cosmos DB
description: Lär dig hur du hanterar databaskonton i Azure Cosmos DB
author: markjbrown
ms.service: cosmos-db
ms.topic: sample
ms.date: 05/06/2019
ms.author: mjbrown
ms.openlocfilehash: 57116327168a76f971a22b61144850199cb0cbae
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/06/2019
ms.locfileid: "65068821"
---
# <a name="manage-an-azure-cosmos-account"></a>Hantera ett Azure Cosmos-konto

Den här artikeln beskriver hur du hanterar olika uppgifter på ett Azure Cosmos-konto med hjälp av Azure portal, Azure PowerShell, Azure CLI och Azure Resource Manager-mallar.

## <a name="create-an-account"></a>Skapa ett konto

### <a id="create-database-account-via-portal"></a>Azure-portalen

[!INCLUDE [cosmos-db-create-dbaccount](../../includes/cosmos-db-create-dbaccount.md)]

### <a id="create-database-account-via-cli"></a>Azure CLI

```azurecli-interactive
# Create an account
$resourceGroupName = 'myResourceGroup'
$accountName = 'myaccountname' # must be lower case.

az cosmosdb create \
   --name $accountName \
   --resource-group $resourceGroupName \
   --kind GlobalDocumentDB \
   --default-consistency-level Session \
   --locations WestUS=0 EastUS=1 \
   --enable-multiple-write-locations true
```

### <a id="create-database-account-via-ps"></a>Azure PowerShell
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

### <a id="create-database-account-via-arm-template"></a>Azure Resource Manager-mall

Azure Resource Manager-mallen skapar ett Azure Cosmos DB-konto för alla stöds API: er som har konfigurerats med två regioner och alternativ att välja konsekvensnivå, automatisk växling vid fel och Multi-Master. Klicka på Distribuera till Azure på sidan viktigt om du vill distribuera den här mallen [skapar Azure Cosmos DB-konto](https://github.com/Azure/azure-quickstart-templates/tree/master/101-cosmosdb-create-multi-region-account)

## <a name="addremove-regions-from-your-database-account"></a>Lägga till/ta bort regioner från ditt databaskonto

### <a id="add-remove-regions-via-portal"></a>Azure-portalen

1. Logga in på [Azure-portalen](https://portal.azure.com). 

1. Gå till ditt Azure Cosmos-konto och öppna den **replikera data globalt** menyn.

1. Om du vill lägga till regioner, Välj Sexhörningar på kartan med den **+** etiketten som motsvarar din önskade region. Även om du vill lägga till en region, välja den **+ Lägg till region** och väljer en region från den nedrullningsbara menyn.

1. Om du vill ta bort regioner avmarkerar du en eller flera regioner från kartan genom att välja de blå sexhörningarna med kryssmarkeringar. Eller välj ”papperskorgsikonen” (🗑) intill regionen på höger sida.

1. Spara ändringarna genom att välja **OK**.

   ![Lägga till eller ta bort regionsmenyn](./media/how-to-manage-database-account/add-region.png)

Skriv läge, du inte kan ta bort skrivregionen i en enskild region. Du måste växla över till en annan region innan du kan ta bort den aktuella skrivregionen.

Skriva-läge i flera regioner kan du lägga till eller ta bort valfri region om du har minst en region.

### <a id="add-remove-regions-via-cli"></a>Azure CLI

```azurecli-interactive
$resourceGroupName = 'myResourceGroup'
$accountName = 'myaccountname'

# Create an account with 1 region
az cosmosdb create --name $accountName --resource-group $resourceGroupName --locations westus=0

# Add a region
az cosmosdb update --name $accountName --resource-group $resourceGroupName --locations westus=0 eastus=1

# Remove a region
az cosmosdb update --name $accountName --resource-group $resourceGroupName --locations westus=0
```

### <a id="add-remove-regions-via-ps"></a>Azure PowerShell

```azurepowershell-interactive
# Create an account with 1 region
$resourceGroupName = "myResourceGroup"
$location = "West US"
$accountName = "mycosmosaccount" # must be lower case.

$locations = @( @{ "locationName"="West US"; "failoverPriority"=0 } )
$consistencyPolicy = @{ "defaultConsistencyLevel"="Session" }
$CosmosDBProperties = @{
    "databaseAccountOfferType"="Standard";
    "locations"=$locations;
    "consistencyPolicy"=$consistencyPolicy
}
New-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName -Location $location `
    -Name $accountName -PropertyObject $CosmosDBProperties

# Add a region
$account = Get-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName -Name $accountName

$locations = @( 
    @{ "locationName"="West US"; "failoverPriority"=0 },
    @{ "locationName"="East Us"; "failoverPriority"=1 } 
)

$account.Properties.locations = $locations
$CosmosDBProperties = $account.Properties

Set-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName `
    -Name $accountName -PropertyObject $CosmosDBProperties

# Azure Resource Manager does not wait on the resource update
Write-Host "Confirm region added before continuing..."

# Remove a region
$account = Get-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName -Name $accountName

$locations = @( @{ "locationName"="West US"; "failoverPriority"=0 } )

$account.Properties.locations = $locations
$CosmosDBProperties = $account.Properties

Set-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName `
    -Name $accountName -PropertyObject $CosmosDBProperties
```

## <a id="configure-multiple-write-regions"></a>Konfigurera flera Skriv-regioner

### <a id="configure-multiple-write-regions-portal"></a>Azure-portalen

Öppna den **replikera Data globalt** fliken och markera **aktivera** att aktivera flera regioner skrivningar. När du har aktiverat skrivningar flera regioner de skrivskyddade regioner som du har för närvarande på kontot ska bli läser och skriver regioner. 

> [!NOTE]
> När du har aktiverat skrivningar flera regioner kan du inte inaktivera den. 

![Azure Cosmos-konto konfigurerar flera huvudservrar skärmbild](./media/how-to-manage-database-account/single-to-multi-master.png)

Kontakta den askcosmosdb@microsoft.com alias för ytterligare frågor om den här funktionen. 

### <a id="configure-multiple-write-regions-cli"></a>Azure CLI

```azurecli-interactive
$resourceGroupName = 'myResourceGroup'
$accountName = 'myaccountname'
az cosmosdb update --name $accountName --resource-group $resourceGroupName --enable-multiple-write-locations true
```

### <a id="configure-multiple-write-regions-ps"></a>Azure PowerShell

```azurepowershell-interactive
# Update an Azure Cosmos Account from single to multi-master

$account = Get-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName -Name $accountName

$account.Properties.enableMultipleWriteLocations = "true"
$CosmosDBProperties = $account.Properties

Set-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName `
    -Name $accountName -PropertyObject $CosmosDBProperties
```

### <a id="configure-multiple-write-regions-arm"></a>Resource Manager-mall

Ett konto kan migreras från ett enda ställe till multimaster genom att distribuera Resource Manager-mallen som används för att skapa kontot och inställningen `enableMultipleWriteLocations: true`. Följande Azure Resource Manager-mallen är ett utan minsta mall som distribuerar ett Azure Cosmos DB-konto för SQL-API med en enda region och multimaster aktiverat.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "name": {
            "type": "String"
        },
        "location": {
            "type": "String",
            "defaultValue": "[resourceGroup().location]"
        }
    },
    "resources": [
        {
            "type": "Microsoft.DocumentDb/databaseAccounts",
            "kind": "GlobalDocumentDB",
            "name": "[parameters('name')]",
            "apiVersion": "2015-04-08",
            "location": "[parameters('location')]",
            "tags": {},
            "properties": {
                "databaseAccountOfferType": "Standard",
                "consistencyPolicy": { "defaultConsistencyLevel": "Session" },
                "locations": [
                    {
                        "locationName": "[parameters('location')]",
                        "failoverPriority": 0
                    }
                ],
                "enableMultipleWriteLocations": true
            }
        }
    ]
}
```

## <a id="automatic-failover"></a>Aktivera automatisk redundans för ditt Azure Cosmos DB-konto

Automatisk redundans-alternativet kan Azure Cosmos DB att redundansväxla till regionen som har högst redundansprioritet med inga användaråtgärder ska en region blir otillgänglig. När automatisk redundans är aktiverat, kan region prioritet ändras. Kontot måste ha två eller flera regioner för att aktivera automatisk redundans.

### <a id="enable-automatic-failover-via-portal"></a>Azure-portalen

1. Öppna fönsterrutan **Replikera data globalt** i ditt Azure Cosmos DB-konto.

2. Längst upp i fönsterrutan väljer du **Automatisk redundans**.

   ![Menyn Replikera data globalt](./media/how-to-manage-database-account/replicate-data-globally.png)

3. I fönsterrutan **Automatisk redundans** ser du till att **Aktivera automatisk redundans** är inställt på **PÅ**. 

4. Välj **Spara**.

   ![Menyn Automatisk redundans i portalen](./media/how-to-manage-database-account/automatic-failover.png)

### <a id="enable-automatic-failover-via-cli"></a>Azure CLI

```azurecli-interactive
# Enable automatic failover on an existing account
$resourceGroupName = 'myResourceGroup'
$accountName = 'myaccountname'

az cosmosdb update --name $accountName --resource-group $resourceGroupName --enable-automatic-failover true
```

### <a id="enable-automatic-failover-via-ps"></a>Azure PowerShell

```azurepowershell-interactive
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"

$account = Get-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName `
    -Name $accountName

$account.Properties.enableAutomaticFailover="true";
$CosmosDBProperties = $account.Properties;

Set-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName `
    -Name $accountName -PropertyObject $CosmosDBProperties
```

## <a name="set-failover-priorities-for-your-azure-cosmos-account"></a>Ange redundansprioritet för ditt Azure Cosmos-konto

När en Cosmos-konto har konfigurerats för automatisk redundans, kan redundansprioritet för regioner ändras.

> [!IMPORTANT]
> Du kan inte ändra skrivregion (redundansprioritet noll) när kontot har konfigurerats för automatisk redundans. Om du vill ändra skrivregion, måste du inaktivera automatisk växling vid fel och gör en manuell redundans.

### <a id="set-failover-priorities-via-portal"></a>Azure-portalen

1. Ditt Azure Cosmos-konto, öppna den **replikera data globalt** fönstret.

2. Längst upp i fönsterrutan väljer du **Automatisk redundans**.

   ![Menyn Replikera data globalt](./media/how-to-manage-database-account/replicate-data-globally.png)

3. I fönsterrutan **Automatisk redundans** ser du till att **Aktivera automatisk redundans** är inställt på **PÅ**.

4. Du ändrar redundansprioritet genom att dra läsregionerna via de tre punkterna till vänster om raden som visas när du hovrar över dem.

5. Välj **Spara**.

   ![Menyn Automatisk redundans i portalen](./media/how-to-manage-database-account/automatic-failover.png)

### <a id="set-failover-priorities-via-cli"></a>Azure CLI

```azurecli-interactive
# Assume region order is initially eastus=0 westus=1 southeastasia=2 on account creation
$resourceGroupName = 'myResourceGroup'
$accountName = 'myaccountname'

az cosmosdb failover-priority-change --name $accountName --resource-group $resourceGroupName --failover-policies eastus=0 southeastasia=1 westus=2
```

### <a id="set-failover-priorities-via-ps"></a>Azure PowerShell

```azurepowershell-interactive
# Assume account currently has regions with priority: West US = 0, East US = 1, Southeast Asia = 2
$resourceGroupName = "myResourceGroup"
$accountName = "myaccountname"

$failoverPolicies = @(
    @{ "locationName"="West US"; "failoverPriority"=0 },
    @{ "locationName"="Southeast Asia"; "failoverPriority"=1 },
    @{ "locationName"="East US"; "failoverPriority"=2 }
)

Invoke-AzResourceAction -Action failoverPriorityChange `
    -ResourceType "Microsoft.DocumentDb/databaseAccounts" -ApiVersion "2015-04-08" `
    -ResourceGroupName $resourceGroupName -Name $accountName -Parameters $failoverPolicies
```

## <a id="manual-failover"></a>Utföra manuell växling vid fel på ett Azure Cosmos-konto

> [!IMPORTANT]
> Azure Cosmos-konto måste konfigureras för manuell redundans för den här åtgärden ska lyckas.

Processen för att utföra en manuell redundans innebär att du ändrar kontots skrivregionen (redundansprioritet = 0) till en annan region som konfigurerats för kontot.

> [!NOTE]
> Flera huvudservrar konton redundansväxlas inte manuellt. För program som använder Azure Cosmos DB SDK, ska SDK: N identifiera när en region blir otillgänglig och automatisk omdirigering till nästa närmaste region om du använder flera API: et i SDK.

### <a id="enable-manual-failover-via-portal"></a>Azure-portalen

1. Gå till ditt Azure Cosmos-konto och öppna den **replikera data globalt** menyn.

2. Längst upp på menyn väljer du **Manuell redundans**.

   ![Menyn Replikera data globalt](./media/how-to-manage-database-account/replicate-data-globally.png)

3. På menyn **Manuell redundans** väljer du din nya skrivregion. Markera kryssrutan för att bekräfta att du förstår att det här alternativet ändrar din skrivregion.

4. Utlös redundansväxlingen genom att välja **OK**.

   ![Menyn Manuell redundans i portalen](./media/how-to-manage-database-account/manual-failover.png)

### <a id="enable-manual-failover-via-cli"></a>Azure CLI

```azurecli-interactive
# Assume account currently has regions with priority: eastus=0 westus=1
# Change the priority order to trigger a failover of the write region
$resourceGroupName = 'myResourceGroup'
$accountName = 'myaccountname'

az cosmosdb update --name $accountName --resource-group $resourceGroupName --locations westus=0 eastus=1
```

### <a id="enable-manual-failover-via-ps"></a>Azure PowerShell

```azurepowershell-interactive
# Assume account currently has regions with priority: West US = 0, East US = 1
# Change the priority order to trigger a failover of the write region
$resourceGroupName = "myResourceGroup"
$accountName = "myaccountname"

$account = Get-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName `
    -Name $accountName

$locations = @(
    @{ "locationName"="East US"; "failoverPriority"=0 },
    @{ "locationName"="West US"; "failoverPriority"=1 }
)

$account.Properties.locations=$locations;
$CosmosDBProperties = $account.Properties;

Set-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName `
    -Name $accountName -PropertyObject $CosmosDBProperties
```

## <a name="next-steps"></a>Nästa steg

Mer information och exempel på hur du hanterar Azure Cosmos-konto som databasen och behållare kan du läsa följande artiklar:

* [Hantera Azure Cosmos DB med hjälp av Azure PowerShell](manage-with-powershell.md)
* [Hantera Azure Cosmos DB med Azure CLI](manage-with-cli.md)
