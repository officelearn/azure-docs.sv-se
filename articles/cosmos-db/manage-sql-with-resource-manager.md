---
title: Skapa och hantera Azure Cosmos DB med Azure Resource Manager-mallar
description: Använd Azure Resource Manager mallar för att skapa och konfigurera Azure Cosmos DB för SQL-API (Core)
author: TheovanKraay
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 11/12/2019
ms.author: thvankra
ms.openlocfilehash: 0cb6e80bafca3bb0bfc339552facae5bd16aced4
ms.sourcegitcommit: 39da2d9675c3a2ac54ddc164da4568cf341ddecf
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/12/2019
ms.locfileid: "73960539"
---
# <a name="manage-azure-cosmos-db-sql-core-api-resources-using-azure-resource-manager-templates"></a>Hantera Azure Cosmos DB SQL (Core) API-resurser med Azure Resource Manager-mallar

Den här artikeln beskriver hur du utför olika åtgärder för att automatisera hanteringen av dina Azure Cosmos DB-konton, databaser och behållare med hjälp av Azure Resource Manager mallar. Den här artikeln innehåller exempel på endast SQL API-konton, för att hitta exempel på andra typer av API-typer finns i: använda Azure Resource Manager mallar med Azure Cosmos DB s API för [Cassandra](manage-cassandra-with-resource-manager.md), [Gremlin](manage-gremlin-with-resource-manager.md), [MongoDB](manage-mongodb-with-resource-manager.md), [tabell](manage-table-with-resource-manager.md) artiklar.

hur du skapar och hanterar Cosmos DB konton, databaser och behållare för MongoDB, Gremlin, Cassandra och Tabell-API.

## Skapa ett Azure Cosmos-konto, en databas och en behållare<a id="create-resource"></a>

Skapa Azure Cosmos DB-resurser med en Azure Resource Manager-mall. Den här mallen skapar ett Azure Cosmos-konto med två behållare som delar 400 RU/s-genomflöde på databas nivå och en enda behållare med dedikerat 400 RU/s-genomflöde. Kopiera mallen och distribuera på det sätt som visas nedan eller gå till [Azure snabb starts galleriet](https://azure.microsoft.com/resources/templates/101-cosmosdb-sql/) och distribuera från Azure Portal. Du kan också hämta mallen till den lokala datorn eller skapa en ny mall och ange den lokala sökvägen med parametern `--template-file`.

> [!NOTE]
>
> - Du kan inte lägga till eller ta bort platser samtidigt i ett Azure Cosmos-konto och ändra andra egenskaper. Dessa måste göras som separata åtgärder.
> - Konto namn måste bestå av gemener och 44 eller färre tecken.
> - Om du vill uppdatera RU/s skickar du om mallen med uppdaterade egenskaper för data flödes värden.

[!code-json[create-cosmosdb-sql](~/quickstart-templates/101-cosmosdb-sql/azuredeploy.json)]

> [!NOTE]
> Om du vill skapa en behållare med stor partitionsnyckel tar du med egenskapen `"version":2` i `partitionKey`-objektet i föregående mall.

### <a name="deploy-via-powershell"></a>Distribuera via PowerShell

Om du vill distribuera Azure Resource Manager-mallen med hjälp av PowerShell **kopierar** du skriptet och väljer **försök** att öppna Azure Cloud Shell. Om du vill klistra in skriptet högerklickar du på gränssnittet och väljer **Klistra in**:

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

Om du väljer att använda en lokalt installerad version av PowerShell i stället för från Azure Cloud Shell måste du [installera](/powershell/azure/install-az-ps) Azure PowerShell-modulen. Kör `Get-Module -ListAvailable Az` för att hitta versionen.

### <a name="deploy-via-azure-cli"></a>Distribuera via Azure CLI

Om du vill distribuera Azure Resource Manager-mallen med hjälp av Azure CLI väljer du **testa den** för att öppna Azure Cloud Shell. Om du vill klistra in skriptet högerklickar du på gränssnittet och väljer **Klistra in**:

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

Kommandot `az cosmosdb show` visar det nyligen skapade Azure Cosmos-kontot efter att det har etablerats. Om du väljer att använda en lokalt installerad version av Azure CLI i stället för att använda CloudShell, kan du läsa artikeln om [kommando rads gränssnittet i Azure (CLI)](/cli/azure/) .

## Skapa en Azure Cosmos DB-behållare med Server sidans funktioner<a id="create-sproc"></a>

Skapa en Azure Cosmos DB-behållare med en lagrad procedur, utlösare och användardefinierad funktion med hjälp av en Azure Resource Manager mall. Kopiera mallen och distribuera på det sätt som visas nedan eller gå till [Azure snabb starts galleriet](https://azure.microsoft.com/resources/templates/101-cosmosdb-sql-container-sprocs/) och distribuera från Azure Portal. Du kan också hämta mallen till den lokala datorn eller skapa en ny mall och ange den lokala sökvägen med parametern `--template-file`.

[!code-json[create-cosmosdb-sql-sprocs](~/quickstart-templates/101-cosmosdb-sql-container-sprocs/azuredeploy.json)]

### <a name="deploy-stored-procedure-template-via-powershell"></a>Distribuera lagrad procedur-mall via PowerShell

Om du vill distribuera Resource Manager-mallen med hjälp av PowerShell **kopierar** du skriptet och väljer **försök** att öppna Azure Cloud Shell. Om du vill klistra in skriptet högerklickar du på gränssnittet och väljer **Klistra in**:

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

Om du väljer att använda en lokalt installerad version av PowerShell i stället för från Azure Cloud Shell måste du [installera](/powershell/azure/install-az-ps) Azure PowerShell-modulen. Kör `Get-Module -ListAvailable Az` för att hitta versionen.

### <a name="deploy-stored-procedure-template-via-azure-cli"></a>Distribuera lagrad procedur mal len via Azure CLI

Om du vill distribuera Azure Resource Manager-mallen med hjälp av Azure CLI väljer du **testa den** för att öppna Azure Cloud Shell. Om du vill klistra in skriptet högerklickar du på gränssnittet och väljer **Klistra in**:

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

- [Azure Resource Manager dokumentation](/azure/azure-resource-manager/)
- [Schema för Azure Cosmos DB Resource Provider](/azure/templates/microsoft.documentdb/allversions)
- [Azure Cosmos DB Snabb starts mallar](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.DocumentDB&pageNumber=1&sort=Popular)
- [Felsök vanliga Azure Resource Manager distributions fel](../azure-resource-manager/resource-manager-common-deployment-errors.md)
