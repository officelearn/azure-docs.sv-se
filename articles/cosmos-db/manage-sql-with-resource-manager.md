---
title: Skapa och hantera Azure Cosmos DB med Azure Resource Manager-mallar
description: Använd Azure Resource Manager mallar för att skapa och konfigurera Azure Cosmos DB för SQL-API (Core)
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 08/05/2019
ms.author: mjbrown
ms.openlocfilehash: b4d121e0628512f7bbd6aedc0a9067b31d46d0ed
ms.sourcegitcommit: c8a102b9f76f355556b03b62f3c79dc5e3bae305
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/06/2019
ms.locfileid: "68814971"
---
# <a name="manage-azure-cosmos-db-sql-core-api-resources-using-azure-resource-manager-templates"></a>Hantera Azure Cosmos DB SQL (Core) API-resurser med Azure Resource Manager-mallar

## Skapa ett Azure Cosmos-konto, en databas och en behållare<a id="create-resource"></a>

Skapa Azure Cosmos DB-resurser med en Azure Resource Manager-mall. Den här mallen skapar ett Azure Cosmos-konto med två behållare som delar 400 RU/s-genomflöde på databas nivå. Kopiera mallen och distribuera på det sätt som visas nedan eller gå till [Azure snabb starts galleriet](https://azure.microsoft.com/resources/templates/101-cosmosdb-sql/) och distribuera från Azure Portal. Du kan också hämta mallen till den lokala datorn eller skapa en ny mall och ange den lokala sökvägen med `--template-file` parametern.

> [!NOTE]
>
> - För närvarande går det inte att distribuera användardefinierade funktioner (UDF: er), lagrade procedurer och utlösare med hjälp av Resource Manager-mallar.
> - Du kan inte lägga till eller ta bort platser samtidigt i ett Azure Cosmos-konto och ändra andra egenskaper. Dessa måste göras som separata åtgärder.
> - Konto namn måste innehålla gemener och < 31 tecken.

[!code-json[create-cosmosdb-sql](~/quickstart-templates/101-cosmosdb-sql/azuredeploy.json)]

### <a name="deploy-via-powershell"></a>Distribuera via PowerShell

Om du vill distribuera Resource Manager-mallen med hjälp av PowerShell **kopierar** du skriptet och väljer **prova** att öppna Azure Cloud Shell. Om du vill klistra in skriptet högerklickar du på gränssnittet och väljer **Klistra in**:

```azurepowershell-interactive

$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
$accountName = Read-Host -Prompt "Enter the account name"
$location = Read-Host -Prompt "Enter the location (i.e. westus2)"
$primaryRegion = Read-Host -Prompt "Enter the primary region (i.e. westus2)"
$secondaryRegion = Read-Host -Prompt "Enter the secondary region (i.e. eastus2)"
$databaseName = Read-Host -Prompt "Enter the database name"
$container1Name = Read-Host -Prompt "Enter the first container name"
$container2Name = Read-Host -Prompt "Enter the second container name"

New-AzResourceGroup -Name $resourceGroupName -Location $location
New-AzResourceGroupDeployment `
    -ResourceGroupName $resourceGroupName `
    -TemplateUri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-cosmosdb-sql/azuredeploy.json" `
    -accountName $accountName `
    -location $location `
    -primaryRegion $primaryRegion `
    -secondaryRegion $secondaryRegion `
    -databaseName $databaseName `
    -container1Name $container1Name `
    -container2Name $container2Name

 (Get-AzResource --ResourceType "Microsoft.DocumentDb/databaseAccounts" --ApiVersion "2015-04-08" --ResourceGroupName $resourceGroupName).name
```

Om du väljer att använda en lokalt installerad version av PowerShell i stället för från Azure Cloud Shell måste du [installera](/powershell/azure/install-az-ps) Azure PowerShell-modulen. Kör `Get-Module -ListAvailable Az` för att hitta versionen.

### <a name="deploy-via-azure-cli"></a>Distribuera via Azure CLI

Om du vill distribuera Resource Manager-mallen med Azure CLI väljer du **prova** att öppna Azure Cloud Shell. Om du vill klistra in skriptet högerklickar du på gränssnittet och väljer **Klistra in**:

```azurecli-interactive
read -p 'Enter the Resource Group name: ' resourceGroupName
read -p 'Enter the location (i.e. westus2): ' location
read -p 'Enter the account name: ' accountName
read -p 'Enter the primary region (i.e. westus2): ' primaryRegion
read -p 'Enter the secondary region (i.e. eastus2): ' secondaryRegion
read -p 'Enter the database name: ' databaseName
read -p 'Enter the first container name: ' container1Name
read -p 'Enter the second container name: ' container2Name

az group create --name $resourceGroupName --location $location
az group deployment create --resource-group $resourceGroupName \
   --template-uri https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/101-cosmosdb-sql/azuredeploy.json \
   --parameters accountName=$accountName primaryRegion=$primaryRegion secondaryRegion=$secondaryRegion databaseName=$databaseName \
   container1Name=$container1Name container2Name=$container2Name

az cosmosdb show --resource-group $resourceGroupName --name accountName --output tsv
```

`az cosmosdb show` Kommandot visar det nyligen skapade Azure Cosmos-kontot efter att det har etablerats. Om du väljer att använda en lokalt installerad version av Azure CLI i stället för att använda CloudShell, kan du läsa artikeln om [kommando rads gränssnittet i Azure (CLI)](/cli/azure/) .

## Uppdatera data flöde (RU/s) på en databas<a id="database-ru-update"></a>

Följande mall kommer att uppdatera data flödet för en databas. Kopiera mallen och distribuera på det sätt som visas nedan eller gå till [Azure snabb starts galleriet](https://azure.microsoft.com/resources/templates/101-cosmosdb-sql-database-ru-update/) och distribuera från Azure Portal. Du kan också hämta mallen till den lokala datorn eller skapa en ny mall och ange den lokala sökvägen med `--template-file` parametern.

[!code-json[cosmosdb-sql-database-ru-update](~/quickstart-templates/101-cosmosdb-sql-database-ru-update/azuredeploy.json)]

### <a name="deploy-database-template-via-powershell"></a>Distribuera databas mal len via PowerShell

Om du vill distribuera Resource Manager-mallen med hjälp av PowerShell **kopierar** du skriptet och väljer **prova** att öppna Azure Cloud Shell. Om du vill klistra in skriptet högerklickar du på gränssnittet och väljer **Klistra in**:

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
$accountName = Read-Host -Prompt "Enter the account name"
$databaseName = Read-Host -Prompt "Enter the database name"
$throughput = Read-Host -Prompt "Enter new throughput for database"

New-AzResourceGroupDeployment `
    -ResourceGroupName $resourceGroupName `
    -TemplateUri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-cosmosdb-sql-database-ru-update/azuredeploy.json" `
    -accountName $accountName `
    -databaseName $databaseName `
    -throughput $throughput
```

### <a name="deploy-database-template-via-azure-cli"></a>Distribuera databas mal len via Azure CLI

Om du vill distribuera Resource Manager-mallen med Azure CLI väljer du **prova** att öppna Azure Cloud Shell. Om du vill klistra in skriptet högerklickar du på gränssnittet och väljer **Klistra in**:

```azurecli-interactive
read -p 'Enter the Resource Group name: ' resourceGroupName
read -p 'Enter the account name: ' accountName
read -p 'Enter the database name: ' databaseName
read -p 'Enter the new throughput: ' throughput

az group deployment create --resource-group $resourceGroupName \
   --template-uri https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/101-cosmosdb-sql-database-ru-update/azuredeploy.json \
   --parameters accountName=$accountName databaseName=$databaseName throughput=$throughput
```

## Uppdatera data flöde (RU/s) på en behållare<a id="container-ru-update"></a>

Följande mall kommer att uppdatera data flödet för en behållare. Kopiera mallen och distribuera på det sätt som visas nedan eller gå till [Azure snabb starts galleriet](https://azure.microsoft.com/resources/templates/101-cosmosdb-sql-container-ru-update/) och distribuera från Azure Portal. Du kan också hämta mallen till den lokala datorn eller skapa en ny mall och ange den lokala sökvägen med `--template-file` parametern.

[!code-json[cosmosdb-sql-container-ru-update](~/quickstart-templates/101-cosmosdb-sql-container-ru-update/azuredeploy.json)]

### <a name="deploy-container-template-via-powershell"></a>Distribuera container-mall via PowerShell

Om du vill distribuera Resource Manager-mallen med hjälp av PowerShell **kopierar** du skriptet och väljer **prova** att öppna Azure Cloud Shell. Om du vill klistra in skriptet högerklickar du på gränssnittet och väljer **Klistra in**:

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
$accountName = Read-Host -Prompt "Enter the account name"
$databaseName = Read-Host -Prompt "Enter the database name"
$containerName = Read-Host -Prompt "Enter the container name"
$throughput = Read-Host -Prompt "Enter new throughput for container"

New-AzResourceGroupDeployment `
    -ResourceGroupName $resourceGroupName `
    -TemplateUri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-cosmosdb-sql-container-ru-update/azuredeploy.json" `
    -accountName $accountName `
    -databaseName $databaseName `
    -containerName $containerName `
    -throughput $throughput
```

### <a name="deploy-container-template-via-azure-cli"></a>Distribuera container-mall via Azure CLI

Om du vill distribuera Resource Manager-mallen med Azure CLI väljer du **prova** att öppna Azure Cloud Shell. Om du vill klistra in skriptet högerklickar du på gränssnittet och väljer **Klistra in**:

```azurecli-interactive
read -p 'Enter the Resource Group name: ' resourceGroupName
read -p 'Enter the account name: ' accountName
read -p 'Enter the database name: ' databaseName
read -p 'Enter the container name: ' containerName
read -p 'Enter the new throughput: ' throughput

az group deployment create --resource-group $resourceGroupName \
   --template-uri https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/101-cosmosdb-sql-container-ru-update/azuredeploy.json \
   --parameters accountName=$accountName databaseName=$databaseName containerName=$containerName throughput=$throughput
```

## <a name="next-steps"></a>Nästa steg

Här följer några ytterligare resurser:

- [Azure Resource Manager dokumentation](/azure/azure-resource-manager/)
- [Schema för Azure Cosmos DB Resource Provider](/azure/templates/microsoft.documentdb/allversions)
- [Azure Cosmos DB Snabb starts mallar](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.DocumentDB&pageNumber=1&sort=Popular)
- [Felsök vanliga Azure Resource Manager distributions fel](../azure-resource-manager/resource-manager-common-deployment-errors.md)