---
title: Resource Manager-mallar för Azure Cosmos DB Table API
description: Använd Azure Resource Manager-mallar för att skapa och konfigurera Azure Cosmos DB Table API.
author: TheovanKraay
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 11/12/2019
ms.author: thvankra
ms.openlocfilehash: 6ab54e56368e7e26e807e4d1dc0592536dc9374a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79246713"
---
# <a name="manage-azure-cosmos-db-table-api-resources-using-azure-resource-manager-templates"></a>Hantera Azure Cosmos DB Table API-resurser med Azure Resource Manager-mallar

I den här artikeln beskrivs hur du utför olika åtgärder för att automatisera hanteringen av dina Azure Cosmos DB-konton, databaser och behållare med Hjälp av Azure Resource Manager-mallar. Den här artikeln innehåller exempel för Tabell API-konton, för att hitta exempel för andra API-typ konton se: använd Azure Resource Manager mallar med Azure Cosmos DB API för [Cassandra](manage-cassandra-with-resource-manager.md), [Gremlin](manage-gremlin-with-resource-manager.md), [MongoDB](manage-mongodb-with-resource-manager.md), [SQL-artiklar.](manage-sql-with-resource-manager.md)

## <a name="create-azure-cosmos-account-and-table"></a>Skapa Azure Cosmos-konto och -tabell<a id="create-resource"></a>

Skapa Azure Cosmos DB-resurser med hjälp av en Azure Resource Manager-mall. Den här mallen skapar ett Azure Cosmos-konto för tabell-API med en tabell på 400 RU/s dataflöde. Kopiera mallen och distribuera enligt nedan eller besök [Azure Quickstart Gallery](https://azure.microsoft.com/resources/templates/101-cosmosdb-table/) och distribuera från Azure-portalen. Du kan också hämta mallen till den lokala datorn eller skapa `--template-file` en ny mall och ange den lokala sökvägen med parametern.

> [!NOTE]
> Kontonamnen måste vara gemener och 44 eller färre tecken.
> Om du vill uppdatera RU/s skickar du in mallen igen med uppdaterade egenskapsvärden för dataflöde.

:::code language="json" source="~/quickstart-templates/101-cosmosdb-table/azuredeploy.json":::

### <a name="deploy-via-powershell"></a>Distribuera via PowerShell

Om du vill distribuera Resource Manager-mallen med PowerShell **kopierar du** skriptet och väljer **Prova** det för att öppna Azure Cloud Shell. Om du vill klistra in skriptet högerklickar du på skalet och väljer sedan **Klistra in:**

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

Om du väljer att använda en lokalt installerad version av PowerShell i stället för från Azure Cloud-skalet måste du [installera](/powershell/azure/install-az-ps) Azure PowerShell-modulen. Kör `Get-Module -ListAvailable Az` för att hitta versionen.

### <a name="deploy-via-the-azure-cli"></a>Distribuera via Azure CLI

Om du vill distribuera Azure Resource Manager-mallen med Azure CLI **kopierar du** skriptet och väljer **Prova det** för att öppna Azure Cloud Shell. Om du vill klistra in skriptet högerklickar du på skalet och väljer sedan **Klistra in:**

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

Kommandot `az cosmosdb show` visar det nyligen skapade Azure Cosmos-kontot när det har etablerats. Om du väljer att använda en lokalt installerad version av Azure CLI i stället för att använda Cloud Shell läser du [Azure CLI-artikeln.](/cli/azure/)

## <a name="next-steps"></a>Nästa steg

Här följer några ytterligare resurser:

- [Azure Resource Manager-dokumentation](/azure/azure-resource-manager/)
- [Azure Cosmos DB-resursproviderschema](/azure/templates/microsoft.documentdb/allversions)
- [Snabbstartsmallar för Azure Cosmos DB](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.DocumentDB&pageNumber=1&sort=Popular)
- [Felsöka vanliga distributionsfel i Azure Resource Manager](../azure-resource-manager/templates/common-deployment-errors.md)