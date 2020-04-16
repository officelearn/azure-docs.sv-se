---
title: Skapa och hantera Azure Cosmos DB med Resource Manager-mallar
description: Använda Azure Resource Manager-mallar för att skapa och konfigurera Azure Cosmos DB för SQL (Core) API
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 04/14/2020
ms.author: mjbrown
ms.openlocfilehash: 3514b3e77781010fd56b43229f87854ea2d591e8
ms.sourcegitcommit: d6e4eebf663df8adf8efe07deabdc3586616d1e4
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/15/2020
ms.locfileid: "81390894"
---
# <a name="manage-azure-cosmos-db-sql-core-api-resources-with-azure-resource-manager-templates"></a>Hantera Azure Cosmos DB SQL(Core) API-resurser med Azure Resource Manager-mallar

I den här artikeln får du lära dig hur du använder Azure Resource Manager-mallar för att automatisera hanteringen av dina Azure Cosmos DB-konton, databaser och containrar.

Den här artikeln visar bara exempel på Azure Resource Manager-mall för SQL API-konton. Du kan också hitta mallexempel för [Cassandra,](manage-cassandra-with-resource-manager.md) [Gremlin,](manage-gremlin-with-resource-manager.md) [MongoDB](manage-mongodb-with-resource-manager.md)och Tabell-API:er. [Table](manage-table-with-resource-manager.md)

<a id="create-resource"></a>

## <a name="create-an-azure-cosmos-account-database-and-container"></a>Skapa ett Azure Cosmos-konto, databas och behållare

Följande Azure Resource Manager-mall skapar ett Azure Cosmos-konto med:

* Två behållare som delar 400 begärda enheter per sekund (RU/s) dataflöde på databasnivå.
* En behållare med dedikerade 400 RU/s dataflöde.

Om du vill skapa Azure Cosmos DB-resurser kopierar du följande exempelmall och distribuerar den enligt beskrivningen, antingen via [PowerShell](#deploy-via-powershell) eller [Azure CLI](#deploy-via-azure-cli).

* Du kan också besöka [Azure Quickstart Gallery](https://azure.microsoft.com/resources/templates/101-cosmosdb-sql/) och distribuera mallen från Azure-portalen.
* Du kan också hämta mallen till den lokala datorn eller skapa `--template-file` en ny mall och ange den lokala sökvägen med parametern.

> [!IMPORTANT]
>
> * När du lägger till eller tar bort platser i ett Azure Cosmos-konto kan du inte samtidigt ändra andra egenskaper. Dessa åtgärder måste utföras separat.
> * Kontonamn är begränsade till 44 tecken, alla gemener.
> * Om du vill ändra dataflödesvärdena skickar du in mallen igen med uppdaterade RU/s.

:::code language="json" source="~/quickstart-templates/101-cosmosdb-sql/azuredeploy.json":::

> [!NOTE]
> Om du vill skapa en behållare med stor `"version":2` partitionsnyckel `partitionKey` ändrar du den tidigare mallen så att den innehåller egenskapen i objektet.

### <a name="deploy-via-powershell"></a>Distribuera via PowerShell

Så här använder du PowerShell för att distribuera Azure Resource Manager-mallen:

1. **Kopiera** skriptet.
2. Välj **Prova det** för att öppna Azure Cloud Shell.
3. Högerklicka i Azure Cloud Shell-fönstret och välj sedan **Klistra in**.

```azurepowershell-interactive

$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
$accountName = Read-Host -Prompt "Enter the account name"
$location = Read-Host -Prompt "Enter the location (i.e. westus2)"
$primaryRegion = Read-Host -Prompt "Enter the primary region (i.e. westus2)"
$secondaryRegion = Read-Host -Prompt "Enter the secondary region (i.e. eastus2)"
$databaseName = Read-Host -Prompt "Enter the database name"
$sharedThroughput = Read-Host -Prompt "Enter the shared database throughput (i.e. 400)"
$sharedContainer1Name = Read-Host -Prompt "Enter the first shared container name"
$sharedContainer2Name = Read-Host -Prompt "Enter the second shared container name"
$dedicatedContainer1Name = Read-Host -Prompt "Enter the dedicated container name"
$dedicatedThroughput = Read-Host -Prompt "Enter the dedicated container throughput (i.e. 400)"

New-AzResourceGroup -Name $resourceGroupName -Location $location
New-AzResourceGroupDeployment `
    -ResourceGroupName $resourceGroupName `
    -TemplateUri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-cosmosdb-sql/azuredeploy.json" `
    -accountName $accountName `
    -location $location `
    -primaryRegion $primaryRegion `
    -secondaryRegion $secondaryRegion `
    -databaseName $databaseName `
    -sharedThroughput $ $sharedThroughput `
    -sharedContainer1Name $sharedContainer1Name `
    -sharedContainer2Name $sharedContainer2Name `
    -dedicatedContainer1Name $dedicatedContainer1Name `
    -dedicatedThroughput $dedicatedThroughput

 (Get-AzResource --ResourceType "Microsoft.DocumentDb/databaseAccounts" --ApiVersion "2019-08-01" --ResourceGroupName $resourceGroupName).name
```

Du kan välja att distribuera mallen med en lokalt installerad version av PowerShell i stället för Azure Cloud Shell. Du måste [installera Azure PowerShell-modulen](/powershell/azure/install-az-ps). Kör `Get-Module -ListAvailable Az` för att hitta den version som krävs.

### <a name="deploy-via-azure-cli"></a>Distribuera via Azure CLI

Så här använder du Azure CLI för att distribuera Azure Resource Manager-mallen:

1. **Kopiera** skriptet.
2. Välj **Prova det** för att öppna Azure Cloud Shell.
3. Högerklicka i Azure Cloud Shell-fönstret och välj sedan **Klistra in**.

```azurecli-interactive
read -p 'Enter the Resource Group name: ' resourceGroupName
read -p 'Enter the location (i.e. westus2): ' location
read -p 'Enter the account name: ' accountName
read -p 'Enter the primary region (i.e. westus2): ' primaryRegion
read -p 'Enter the secondary region (i.e. eastus2): ' secondaryRegion
read -p 'Enter the database name: ' databaseName
read -p 'Enter the shared database throughput: sharedThroughput
read -p 'Enter the first shared container name: ' sharedContainer1Name
read -p 'Enter the second shared container name: ' sharedContainer2Name
read -p 'Enter the dedicated container name: ' dedicatedContainer1Name
read -p 'Enter the dedicated container throughput: dedicatedThroughput

az group create --name $resourceGroupName --location $location
az group deployment create --resource-group $resourceGroupName \
   --template-uri https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/101-cosmosdb-sql/azuredeploy.json \
   --parameters accountName=$accountName \
   primaryRegion=$primaryRegion \
   secondaryRegion=$secondaryRegion \
   databaseName=$databaseName \
   sharedThroughput=$sharedThroughput \
   sharedContainer1Name=$sharedContainer1Name \
   sharedContainer2Name=$sharedContainer2Name \
   dedicatedContainer1Name=$dedicatedContainer1Name \
   dedicatedThroughput=$dedicatedThroughput

az cosmosdb show --resource-group $resourceGroupName --name accountName --output tsv
```

Kommandot `az cosmosdb show` visar det nyligen skapade Azure Cosmos-kontot när det har etablerats. Du kan välja att distribuera mallen med en lokalt installerad version av Azure CLI istället Azure Cloud Shell. Mer information finns i artikeln [(AZURE Command-Line Interface) (CLI).](/cli/azure/)

<a id="create-sproc"></a>

## <a name="create-an-azure-cosmos-db-container-with-server-side-functionality"></a>Skapa en Azure Cosmos DB-behållare med funktioner på serversidan

Du kan använda en Azure Resource Manager-mall för att skapa en Azure Cosmos DB-behållare med en lagrad procedur, utlösare och användardefinierad funktion.

Kopiera följande exempelmall och distribuera den enligt beskrivningen, antingen med [PowerShell](#deploy-with-powershell) eller [Azure CLI](#deploy-with-azure-cli).

* Du kan också besöka [Azure Quickstart Gallery](https://azure.microsoft.com/resources/templates/101-cosmosdb-sql-container-sprocs/) och distribuera mallen från Azure-portalen.
* Du kan också hämta mallen till den lokala datorn eller skapa `--template-file` en ny mall och ange den lokala sökvägen med parametern.

:::code language="json" source="~/quickstart-templates/101-cosmosdb-sql-container-sprocs/azuredeploy.json":::

### <a name="deploy-with-powershell"></a>Distribuera med PowerShell

Så här använder du PowerShell för att distribuera Azure Resource Manager-mallen:

1. **Kopiera** skriptet.
1. Välj **Prova det** för att öppna Azure Cloud Shell.
1. Högerklicka på Azure Cloud Shell-fönstret och välj sedan **Klistra in**.

```azurepowershell-interactive

$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
$accountName = Read-Host -Prompt "Enter the account name"
$location = Read-Host -Prompt "Enter the location (i.e. westus2)"
$primaryRegion = Read-Host -Prompt "Enter the primary region (i.e. westus2)"
$secondaryRegion = Read-Host -Prompt "Enter the secondary region (i.e. eastus2)"
$databaseName = Read-Host -Prompt "Enter the database name"
$containerName = Read-Host -Prompt "Enter the container name"

New-AzResourceGroup -Name $resourceGroupName -Location $location
New-AzResourceGroupDeployment `
    -ResourceGroupName $resourceGroupName `
    -TemplateUri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-cosmosdb-sql-container-sprocs/azuredeploy.json" `
    -accountName $accountName `
    -location $location `
    -primaryRegion $primaryRegion `
    -secondaryRegion $secondaryRegion `
    -databaseName $databaseName `
    -containerName $containerName

 (Get-AzResource --ResourceType "Microsoft.DocumentDb/databaseAccounts" --ApiVersion "2019-08-01" --ResourceGroupName $resourceGroupName).name
```

Du kan välja att distribuera mallen med en lokalt installerad version av PowerShell i stället för Azure Cloud Shell. Du måste [installera Azure PowerShell-modulen](/powershell/azure/install-az-ps). Kör `Get-Module -ListAvailable Az` för att hitta den version som krävs.

### <a name="deploy-with-azure-cli"></a>Distribuera med Azure CLI

Så här använder du Azure CLI för att distribuera Azure Resource Manager-mallen:

1. **Kopiera** skriptet.
2. Välj **Prova det** för att öppna Azure Cloud Shell.
3. Högerklicka i Azure Cloud Shell-fönstret och välj sedan **Klistra in**.

```azurecli-interactive
read -p 'Enter the Resource Group name: ' resourceGroupName
read -p 'Enter the location (i.e. westus2): ' location
read -p 'Enter the account name: ' accountName
read -p 'Enter the primary region (i.e. westus2): ' primaryRegion
read -p 'Enter the secondary region (i.e. eastus2): ' secondaryRegion
read -p 'Enter the database name: ' databaseName
read -p 'Enter the container name: ' containerName

az group create --name $resourceGroupName --location $location
az group deployment create --resource-group $resourceGroupName \
   --template-uri https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/101-cosmosdb-sql-container-sprocs/azuredeploy.json \
   --parameters accountName=$accountName primaryRegion=$primaryRegion secondaryRegion=$secondaryRegion databaseName=$databaseName \
   containerName=$containerName

az cosmosdb show --resource-group $resourceGroupName --name accountName --output tsv
```

## <a name="next-steps"></a>Nästa steg

Här följer några ytterligare resurser:

* [Azure Resource Manager-dokumentation](/azure/azure-resource-manager/)
* [Azure Cosmos DB-resursproviderschema](/azure/templates/microsoft.documentdb/allversions)
* [Snabbstartsmallar för Azure Cosmos DB](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Documentdb&pageNumber=1&sort=Popular)
* [Felsöka vanliga distributionsfel i Azure Resource Manager](../azure-resource-manager/templates/common-deployment-errors.md)
