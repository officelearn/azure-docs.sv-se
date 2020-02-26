---
title: Resource Manager-mallar för Azure Cosmos DB Tabell-API
description: Använd Azure Resource Manager mallar för att skapa och konfigurera Azure Cosmos DB Tabell-API.
author: TheovanKraay
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 11/12/2019
ms.author: thvankra
ms.openlocfilehash: 6ab54e56368e7e26e807e4d1dc0592536dc9374a
ms.sourcegitcommit: 7f929a025ba0b26bf64a367eb6b1ada4042e72ed
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/25/2020
ms.locfileid: "77588767"
---
# <a name="manage-azure-cosmos-db-table-api-resources-using-azure-resource-manager-templates"></a>Hantera Azure Cosmos DB Tabell-API resurser med Azure Resource Manager mallar

Den här artikeln beskriver hur du utför olika åtgärder för att automatisera hanteringen av dina Azure Cosmos DB-konton, databaser och behållare med hjälp av Azure Resource Manager mallar. Den här artikeln innehåller exempel på Tabell-API-konton, för att hitta exempel på andra typer av API-typer: Använd Azure Resource Manager mallar med Azure Cosmos DBs API för [Cassandra](manage-cassandra-with-resource-manager.md), [Gremlin](manage-gremlin-with-resource-manager.md), [MongoDB](manage-mongodb-with-resource-manager.md), [SQL](manage-sql-with-resource-manager.md) -artiklar.

## Skapa ett Azure Cosmos-konto och-tabell<a id="create-resource"></a>

Skapa Azure Cosmos DB-resurser med en Azure Resource Manager-mall. Den här mallen skapar ett Azure Cosmos-konto för Tabell-API med en tabell med 400 RU/s-genomflöde. Kopiera mallen och distribuera på det sätt som visas nedan eller gå till [Azure snabb starts galleriet](https://azure.microsoft.com/resources/templates/101-cosmosdb-table/) och distribuera från Azure Portal. Du kan också hämta mallen till den lokala datorn eller skapa en ny mall och ange den lokala sökvägen med parametern `--template-file`.

> [!NOTE]
> Konto namn måste bestå av gemener och 44 eller färre tecken.
> Om du vill uppdatera RU/s skickar du om mallen med uppdaterade egenskaper för data flödes värden.

:::code language="json" source="~/quickstart-templates/101-cosmosdb-table/azuredeploy.json":::

### <a name="deploy-via-powershell"></a>Distribuera via PowerShell

Om du vill distribuera Resource Manager-mallen med hjälp av PowerShell **kopierar** du skriptet och väljer **försök** att öppna Azure Cloud Shell. Om du vill klistra in skriptet högerklickar du på gränssnittet och väljer **Klistra in**:

```azurepowershell-interactive

$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
$accountName = Read-Host -Prompt "Enter the account name"
$location = Read-Host -Prompt "Enter the location (i.e. westus2)"
$primaryRegion = Read-Host -Prompt "Enter the primary region (i.e. westus2)"
$secondaryRegion = Read-Host -Prompt "Enter the secondary region (i.e. eastus2)"
$tableName = Read-Host -Prompt "Enter the table name"

New-AzResourceGroup -Name $resourceGroupName -Location $location
New-AzResourceGroupDeployment `
    -ResourceGroupName $resourceGroupName `
    -TemplateUri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-cosmosdb-table/azuredeploy.json" `
    -primaryRegion $primaryRegion `
    -secondaryRegion $secondaryRegion `
    -tableName $tableName

 (Get-AzResource --ResourceType "Microsoft.DocumentDb/databaseAccounts" --ApiVersion "2015-04-08" --ResourceGroupName $resourceGroupName).name
```

Om du väljer att använda en lokalt installerad version av PowerShell i stället för från Azure Cloud Shell måste du [installera](/powershell/azure/install-az-ps) Azure PowerShell-modulen. Kör `Get-Module -ListAvailable Az` för att hitta versionen.

### <a name="deploy-via-the-azure-cli"></a>Distribuera via Azure CLI

Om du vill distribuera Azure Resource Manager-mallen med hjälp av Azure CLI **kopierar** du skriptet och väljer **försök** att öppna Azure Cloud Shell. Om du vill klistra in skriptet högerklickar du på gränssnittet och väljer **Klistra in**:

```azurecli-interactive
read -p 'Enter the Resource Group name: ' resourceGroupName
read -p 'Enter the location (i.e. westus2): ' location
read -p 'Enter the account name: ' accountName
read -p 'Enter the primary region (i.e. westus2): ' primaryRegion
read -p 'Enter the secondary region (i.e. eastus2): ' secondaryRegion
read -p 'Enter the table name: ' tableName

az group create --name $resourceGroupName --location $location
az group deployment create --resource-group $resourceGroupName \
   --template-uri https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/101-cosmosdb-table/azuredeploy.json \
   --parameters accountName=$accountName primaryRegion=$primaryRegion secondaryRegion=$secondaryRegion tableName=$tableName

az cosmosdb show --resource-group $resourceGroupName --name accountName --output tsv
```

Kommandot `az cosmosdb show` visar det nyligen skapade Azure Cosmos-kontot efter att det har etablerats. Om du väljer att använda en lokalt installerad version av Azure CLI i stället för att använda Cloud Shell, se artikeln [Azure CLI](/cli/azure/) .

## <a name="next-steps"></a>Nästa steg

Här följer några ytterligare resurser:

- [Azure Resource Manager dokumentation](/azure/azure-resource-manager/)
- [Schema för Azure Cosmos DB Resource Provider](/azure/templates/microsoft.documentdb/allversions)
- [Azure Cosmos DB Snabb starts mallar](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.DocumentDB&pageNumber=1&sort=Popular)
- [Felsök vanliga Azure Resource Manager distributions fel](../azure-resource-manager/templates/common-deployment-errors.md)