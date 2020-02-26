---
title: Skapa och hantera Azure Cosmos DB med Resource Manager-mallar
description: Använd Azure Resource Manager mallar för att skapa och konfigurera Azure Cosmos DB för SQL-API (Core)
author: TheovanKraay
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 11/12/2019
ms.author: thvankra
ms.openlocfilehash: 72a87c3b23e0eed6cfbf1614388702443f4e99d0
ms.sourcegitcommit: 7f929a025ba0b26bf64a367eb6b1ada4042e72ed
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/25/2020
ms.locfileid: "77588795"
---
# <a name="manage-azure-cosmos-db-sql-core-api-resources-with-azure-resource-manager-templates"></a>Hantera Azure Cosmos DB SQL (Core) API-resurser med Azure Resource Manager mallar

I den här artikeln får du lära dig hur du använder Azure Resource Manager-mallar för att automatisera hanteringen av dina Azure Cosmos DB-konton, databaser och containrar.

Den här artikeln innehåller bara exempel på Azure Resource Manager mallar för SQL API-konton. Du kan också hitta mall exempel för API: er för [Cassandra](manage-cassandra-with-resource-manager.md), [Gremlin](manage-gremlin-with-resource-manager.md), [MongoDB](manage-mongodb-with-resource-manager.md)och [Table](manage-table-with-resource-manager.md) .

<a id="create-resource"></a>

## <a name="create-an-azure-cosmos-account-database-and-container"></a>Skapa ett Azure Cosmos-konto, en databas och en behållare

Följande Azure Resource Manager mall skapar ett Azure Cosmos-konto med:

* Två behållare som delar 400 begärda enheter per sekund (RU/s) genom strömning på databas nivå.
* En behållare med dedikerade 400 RU/s-genomflöde.

Om du vill skapa Azure Cosmos DB resurser kopierar du följande exempel mall och distribuerar den enligt beskrivningen, antingen via [PowerShell](#deploy-via-powershell) eller [Azure CLI](#deploy-via-azure-cli).

* Du kan också gå till [Azure snabb starts galleriet](https://azure.microsoft.com/resources/templates/101-cosmosdb-sql/) och distribuera mallen från Azure Portal.
* Du kan också hämta mallen till den lokala datorn eller skapa en ny mall och ange den lokala sökvägen med parametern `--template-file`.

> [!IMPORTANT]
>
> * När du lägger till eller tar bort platser i ett Azure Cosmos-konto kan du ändra andra egenskaper samtidigt. Dessa åtgärder måste utföras separat.
> * Konto namn är begränsade till 44 tecken, alla gemener.
> * Om du vill ändra data flödes värden skickar du mallen igen med uppdaterade RU/s.

:::code language="json" source="~/quickstart-templates/101-cosmosdb-sql/azuredeploy.json":::

> [!NOTE]
> Om du vill skapa en behållare med en stor partitionsnyckel ändrar du den tidigare mallen så att den inkluderar egenskapen `"version":2` i `partitionKey`-objektet.

### <a name="deploy-via-powershell"></a>Distribuera via PowerShell

Så här använder du PowerShell för att distribuera Azure Resource Manager-mallen:

1. **Kopiera** skriptet.
2. Välj **försök** att öppna Azure Cloud Shell.
3. Högerklicka på fönstret Azure Cloud Shell och välj **Klistra in**.

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

Du kan välja att distribuera mallen med en lokalt installerad version av PowerShell i stället för att Azure Cloud Shell. Du måste [installera Azure PowerShell-modulen](/powershell/azure/install-az-ps). Kör `Get-Module -ListAvailable Az` för att hitta den version som krävs.

### <a name="deploy-via-azure-cli"></a>Distribuera via Azure CLI

Så här använder du Azure CLI för att distribuera Azure Resource Manager-mallen:

1. **Kopiera** skriptet.
2. Välj **försök** att öppna Azure Cloud Shell.
3. Högerklicka på fönstret Azure Cloud Shell och välj **Klistra in**.

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

Kommandot `az cosmosdb show` visar det nyligen skapade Azure Cosmos-kontot efter att det har tillhandahållits. Du kan välja att distribuera mallen med en lokalt installerad version av Azure CLI i stället Azure Cloud Shell. Mer information finns i artikeln [kommando rads gränssnitt för Azure (CLI)](/cli/azure/) .

<a id="create-sproc"></a>

## <a name="create-an-azure-cosmos-db-container-with-server-side-functionality"></a>Skapa en Azure Cosmos DB-behållare med Server sidans funktioner

Du kan använda en Azure Resource Manager mall för att skapa en Azure Cosmos DB behållare med en lagrad procedur, utlösare och användardefinierad funktion.

Kopiera följande exempel mall och distribuera den enligt beskrivningen, antingen med [PowerShell](#deploy-with-powershell) eller [Azure CLI](#deploy-with-azure-cli).

* Du kan också gå till [Azure snabb starts galleriet](https://azure.microsoft.com/resources/templates/101-cosmosdb-sql-container-sprocs/) och distribuera mallen från Azure Portal.
* Du kan också hämta mallen till den lokala datorn eller skapa en ny mall och ange den lokala sökvägen med parametern `--template-file`.

:::code language="json" source="~/quickstart-templates/101-cosmosdb-sql-container-sprocs/azuredeploy.json":::

### <a name="deploy-with-powershell"></a>Distribuera med PowerShell

Så här använder du PowerShell för att distribuera Azure Resource Manager-mallen:

1. **Kopiera** skriptet.
1. Välj **försök** att öppna Azure Cloud Shell.
1. Högerklicka på fönstret Azure Cloud Shell och välj sedan **Klistra in**.

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

Du kan välja att distribuera mallen med en lokalt installerad version av PowerShell i stället för att Azure Cloud Shell. Du måste [installera Azure PowerShell-modulen](/powershell/azure/install-az-ps). Kör `Get-Module -ListAvailable Az` för att hitta den version som krävs.

### <a name="deploy-with-azure-cli"></a>Distribuera med Azure CLI

Så här använder du Azure CLI för att distribuera Azure Resource Manager-mallen:

1. **Kopiera** skriptet.
2. Välj **försök** att öppna Azure Cloud Shell.
3. Högerklicka på fönstret Azure Cloud Shell och välj **Klistra in**.

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

* [Azure Resource Manager dokumentation](/azure/azure-resource-manager/)
* [Schema för Azure Cosmos DB Resource Provider](/azure/templates/microsoft.documentdb/allversions)
* [Azure Cosmos DB Snabb starts mallar](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Documentdb&pageNumber=1&sort=Popular)
* [Felsök vanliga Azure Resource Manager distributions fel](../azure-resource-manager/templates/common-deployment-errors.md)
