---
title: Skapa och hantera Azure Cosmos DB med Azure Resource Manager-mallar
description: Använd Azure Resource Manager-mallar för att skapa och konfigurera Azure Cosmos DB för SQL (kärna) API
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/20/2019
ms.author: mjbrown
ms.openlocfilehash: a3798ac0c73c7bc6c4012dbb089275254f4c3504
ms.sourcegitcommit: e9a46b4d22113655181a3e219d16397367e8492d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/21/2019
ms.locfileid: "65968830"
---
# <a name="manage-azure-cosmos-db-sql-core-api-resources-using-azure-resource-manager-templates"></a>Hantera Azure Cosmos DB SQL (kärna) API med Azure Resource Manager-mallar

## Skapa ett Azure Cosmos-konto, databas och behållare <a id="create-resource"></a>

Skapa Azure Cosmos DB-resurser med hjälp av en Azure Resource Manager-mall. Den här mallen skapar ett Azure Cosmos-konto med två behållare som delar 400 RU/s genomströmning på databasnivå. Kopiera mallen och distribuera enligt nedan eller gå till [Azure Snabbstartsgalleriet](https://azure.microsoft.com/resources/templates/101-cosmosdb-sql/) och distribuera från Azure-portalen. Du kan också ladda ned mallen till din lokala dator eller skapa en ny mall och ange den lokala sökvägen med den `--template-file` parametern.

[!code-json[create-cosmosdb-sql](~/quickstart-templates/101-cosmosdb-sql/azuredeploy.json)]

### <a name="deploy-via-powershell"></a>Distribuera via PowerShell

Att distribuera Resource Manager-mallen med hjälp av PowerShell, **kopia** skript och välj **prova** att öppna Azure Cloud shell. Högerklicka i gränssnittet för att klistra in skriptet, och välj sedan **klistra in**:

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

Om du vill använda ett lokalt installerade versionen av PowerShell i stället för från Azure Cloud shell kan du behöva [installera](/powershell/azure/install-az-ps) Azure PowerShell-modulen. Kör `Get-Module -ListAvailable Az` för att hitta versionen.

### <a name="deploy-via-azure-cli"></a>Distribuera via Azure CLI

För att distribuera Resource Manager-mallen med hjälp av Azure CLI, Välj **prova** att öppna Azure Cloud shell. Högerklicka i gränssnittet för att klistra in skriptet, och välj sedan **klistra in**:

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

Den `az cosmosdb show` -kommando visar det nya Azure Cosmos-kontot när den har etablerats. Om du väljer att använda ett lokalt installerade versionen av Azure CLI istället för att använda CloudShell, se [Azure kommandoradsgränssnitt (CLI)](/cli/azure/) artikeln.

## Uppdatera dataflöde (RU/s) på en databas <a id="database-ru-update"></a>

Följande mall uppdateras dataflödet för en databas. Kopiera mallen och distribuera enligt nedan eller gå till [Azure Snabbstartsgalleriet](https://azure.microsoft.com/resources/templates/101-cosmosdb-sql-database-ru-update/) och distribuera från Azure-portalen. Du kan också ladda ned mallen till din lokala dator eller skapa en ny mall och ange den lokala sökvägen med den `--template-file` parametern.

[!code-json[cosmosdb-sql-database-ru-update](~/quickstart-templates/101-cosmosdb-sql-database-ru-update/azuredeploy.json)]

### <a name="deploy-database-template-via-powershell"></a>Distribuera databasmall via PowerShell

Att distribuera Resource Manager-mallen med hjälp av PowerShell, **kopia** skript och välj **prova** att öppna Azure Cloud shell. Högerklicka i gränssnittet för att klistra in skriptet, och välj sedan **klistra in**:

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

### <a name="deploy-database-template-via-azure-cli"></a>Distribuera databasmall via Azure CLI

För att distribuera Resource Manager-mallen med hjälp av Azure CLI, Välj **prova** att öppna Azure Cloud shell. Högerklicka i gränssnittet för att klistra in skriptet, och välj sedan **klistra in**:

```azurecli-interactive
read -p 'Enter the Resource Group name: ' resourceGroupName
read -p 'Enter the account name: ' accountName
read -p 'Enter the database name: ' databaseName
read -p 'Enter the new throughput: ' throughput

az group deployment create --resource-group $resourceGroupName \
   --template-uri https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/101-cosmosdb-sql-database-ru-update/azuredeploy.json \
   --parameters accountName=$accountName databaseName=$databaseName throughput=$throughput
```

## Uppdatera dataflöde (RU/s) för en behållare <a id="container-ru-update"></a>

Följande mall uppdateras dataflödet för en behållare. Kopiera mallen och distribuera enligt nedan eller gå till [Azure Snabbstartsgalleriet](https://azure.microsoft.com/resources/templates/101-cosmosdb-sql-container-ru-update/) och distribuera från Azure-portalen. Du kan också ladda ned mallen till din lokala dator eller skapa en ny mall och ange den lokala sökvägen med den `--template-file` parametern.

[!code-json[cosmosdb-sql-container-ru-update](~/quickstart-templates/101-cosmosdb-sql-container-ru-update/azuredeploy.json)]

### <a name="deploy-container-template-via-powershell"></a>Distribuera behållare mall via PowerShell

Att distribuera Resource Manager-mallen med hjälp av PowerShell, **kopia** skript och välj **prova** att öppna Azure Cloud shell. Högerklicka i gränssnittet för att klistra in skriptet, och välj sedan **klistra in**:

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

### <a name="deploy-container-template-via-azure-cli"></a>Distribuera behållare mall via Azure CLI

För att distribuera Resource Manager-mallen med hjälp av Azure CLI, Välj **prova** att öppna Azure Cloud shell. Högerklicka i gränssnittet för att klistra in skriptet, och välj sedan **klistra in**:

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

- [Dokumentation om Azure Resource Manager](/azure/azure-resource-manager/)
- [Providerschema för Azure Cosmos DB-resurs](/azure/templates/microsoft.documentdb/allversions)
- [Azure Cosmos DB-Quickstart-mallar](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.DocumentDB&pageNumber=1&sort=Popular)
- [Felsök vanliga distributionsfel för Azure Resource Manager](../azure-resource-manager/resource-manager-common-deployment-errors.md)