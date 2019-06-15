---
title: Azure Resource Manager-mallar för Azure Cosmos DB Table API
description: Använd Azure Resource Manager-mallar för att skapa och konfigurera Azure Cosmos DB Table API.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/20/2019
ms.author: mjbrown
ms.openlocfilehash: 82e2a436bf6b25b6164d845d234896390a262292
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/13/2019
ms.locfileid: "65968819"
---
# <a name="manage-azure-cosmos-db-table-api-resources-using-azure-resource-manager-templates"></a>Hantera Azure Cosmos DB Table API med Azure Resource Manager-mallar

## Skapa Azure Cosmos-konto och en tabell <a id="create-resource"></a>

Skapa Azure Cosmos DB-resurser med hjälp av en Azure Resource Manager-mall. Den här mallen skapar ett Azure Cosmos-konto för tabell-API med en tabell med 400 RU/s-dataflöde. Kopiera mallen och distribuera enligt nedan eller gå till [Azure Snabbstartsgalleriet](https://azure.microsoft.com/resources/templates/101-cosmosdb-table/) och distribuera från Azure-portalen. Du kan också ladda ned mallen till din lokala dator eller skapa en ny mall och ange den lokala sökvägen med den `--template-file` parametern.

[!code-json[create-cosmos-table](~/quickstart-templates/101-cosmosdb-table/azuredeploy.json)]

### <a name="deploy-via-powershell"></a>Distribuera via PowerShell

Att distribuera Resource Manager-mallen med hjälp av PowerShell, **kopia** skript och välj **prova** att öppna Azure Cloud shell. Högerklicka i gränssnittet för att klistra in skriptet, och välj sedan **klistra in**:

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

Om du vill använda ett lokalt installerade versionen av PowerShell i stället för från Azure Cloud shell kan du behöva [installera](/powershell/azure/install-az-ps) Azure PowerShell-modulen. Kör `Get-Module -ListAvailable Az` för att hitta versionen.

### <a name="deploy-via-azure-cli"></a>Distribuera via Azure CLI

Att distribuera Resource Manager-mallen med hjälp av Azure CLI, **kopia** skript och välj **prova** att öppna Azure Cloud shell. Högerklicka i gränssnittet för att klistra in skriptet, och välj sedan **klistra in**:

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

Den `az cosmosdb show` -kommando visar det nya Azure Cosmos-kontot när den har etablerats. Om du väljer att använda ett lokalt installerade versionen av Azure CLI istället för att använda CloudShell, se [Azure kommandoradsgränssnitt (CLI)](/cli/azure/) artikeln.

## Uppdatera dataflöde (RU/s) för en tabell <a id="table-ru-update"></a>

Följande mall uppdateras dataflödet för en tabell. Kopiera mallen och distribuera enligt nedan eller gå till [Azure Snabbstartsgalleriet](https://azure.microsoft.com/resources/templates/101-cosmosdb-table-ru-update/) och distribuera från Azure-portalen. Du kan också ladda ned mallen till din lokala dator eller skapa en ny mall och ange den lokala sökvägen med den `--template-file` parametern.

[!code-json[cosmosdb-table-ru-update](~/quickstart-templates/101-cosmosdb-table-ru-update/azuredeploy.json)]

### <a name="deploy-table-throughput-via-powershell"></a>Distribuera tabell dataflöde via PowerShell

Att distribuera Resource Manager-mallen med hjälp av PowerShell, **kopia** skript och välj **prova** att öppna Azure Cloud shell. Högerklicka i gränssnittet för att klistra in skriptet, och välj sedan **klistra in**:

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
$accountName = Read-Host -Prompt "Enter the account name"
$tableName = Read-Host -Prompt "Enter the table name"
$throughput = Read-Host -Prompt "Enter new throughput for table"

New-AzResourceGroupDeployment `
    -ResourceGroupName $resourceGroupName `
    -TemplateUri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-cosmosdb-table-ru-update/azuredeploy.json" `
    -accountName $accountName `
    -tableName $tableName `
    -throughput $throughput
```

### <a name="deploy-table-template-via-azure-cli"></a>Distribuera mall via Azure CLI

För att distribuera Resource Manager-mallen med hjälp av Azure CLI, Välj **prova** att öppna Azure Cloud shell. Högerklicka i gränssnittet för att klistra in skriptet, och välj sedan **klistra in**:

```azurecli-interactive
read -p 'Enter the Resource Group name: ' resourceGroupName
read -p 'Enter the account name: ' accountName
read -p 'Enter the table name: ' tableName
read -p 'Enter the new throughput: ' throughput

az group deployment create --resource-group $resourceGroupName \
   --template-uri https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/101-cosmosdb-table-ru-update/azuredeploy.json \
   --parameters accountName=$accountName tableName=$tableName throughput=$throughput
```

## <a name="next-steps"></a>Nästa steg

Här följer några ytterligare resurser:

- [Dokumentation om Azure Resource Manager](/azure/azure-resource-manager/)
- [Providerschema för Azure Cosmos DB-resurs](/azure/templates/microsoft.documentdb/allversions)
- [Azure Cosmos DB-Quickstart-mallar](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.DocumentDB&pageNumber=1&sort=Popular)
- [Felsök vanliga distributionsfel för Azure Resource Manager](../azure-resource-manager/resource-manager-common-deployment-errors.md)