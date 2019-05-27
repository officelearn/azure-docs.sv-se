---
title: Azure Resource Manager-mallar för Azure Cosmos DB API för MongoDB
description: Använd Azure Resource Manager-mallar för att skapa och konfigurera Azure Cosmos DB API för MongoDB.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/20/2019
ms.author: mjbrown
ms.openlocfilehash: 99f1e41107c277c8b3f1b21f81952d5d5cadaa29
ms.sourcegitcommit: e9a46b4d22113655181a3e219d16397367e8492d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/21/2019
ms.locfileid: "65968867"
---
# <a name="manage-azure-cosmos-db-mongodb-api-resources-using-azure-resource-manager-templates"></a>Hantera Azure Cosmos DB MongoDB API-resurser med hjälp av Azure Resource Manager-mallar

## Skapa Azure Cosmos DB API för MongoDB-konto, databas och samling <a id="create-resource"></a>

Skapa Azure Cosmos DB-resurser med hjälp av en Azure Resource Manager-mall. Den här mallen skapar ett Azure Cosmos-konto för MongoDB-API med två samlingar som delar 400 RU/s genomströmning på databasnivå. Kopiera mallen och distribuera enligt nedan eller gå till [Azure Snabbstartsgalleriet](https://azure.microsoft.com/resources/templates/101-cosmosdb-mongodb/) och distribuera från Azure-portalen. Du kan också ladda ned mallen till din lokala dator eller skapa en ny mall och ange den lokala sökvägen med den `--template-file` parametern.

[!code-json[create-cosmos-mongo](~/quickstart-templates/101-cosmosdb-mongodb/azuredeploy.json)]

### <a name="deploy-via-azure-cli"></a>Distribuera via Azure CLI

Att distribuera Resource Manager-mallen med hjälp av Azure CLI, **kopia** skript och välj **prova** att öppna Azure Cloud shell. Högerklicka i gränssnittet för att klistra in skriptet, och välj sedan **klistra in**:

```azurecli-interactive

read -p 'Enter the Resource Group name: ' resourceGroupName
read -p 'Enter the location (i.e. westus2): ' location
read -p 'Enter the account name: ' accountName
read -p 'Enter the primary region (i.e. westus2): ' primaryRegion
read -p 'Enter the secondary region (i.e. eastus2): ' secondaryRegion
read -p 'Enter the database name: ' databaseName
read -p 'Enter the first collection name: ' collection1Name
read -p 'Enter the second collection name: ' collection2Name

az group create --name $resourceGroupName --location $location
az group deployment create --resource-group $resourceGroupName \
  --template-uri https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/101-cosmosdb-mongodb/azuredeploy.json \
  --parameters accountName=$accountName primaryRegion=$primaryRegion secondaryRegion=$secondaryRegion \
  databaseName=$databaseName collection1Name=$collection1Name collection2Name=$collection2Name

az cosmosdb show --resource-group $resourceGroupName --name accountName --output tsv
```

Den `az cosmosdb show` -kommando visar det nya Azure Cosmos-kontot när den har etablerats. Om du väljer att använda ett lokalt installerade versionen av Azure CLI istället för att använda CloudShell, se [Azure kommandoradsgränssnitt (CLI)](/cli/azure/) artikeln.

## Uppdatera dataflöde (RU/s) på en databas <a id="database-ru-update"></a>

Följande mall uppdateras dataflödet för en databas. Kopiera mallen och distribuera enligt nedan eller gå till [Azure Snabbstartsgalleriet](https://azure.microsoft.com/resources/templates/101-cosmosdb-mongodb-database-ru-update/) och distribuera från Azure-portalen. Du kan också ladda ned mallen till din lokala dator eller skapa en ny mall och ange den lokala sökvägen med den `--template-file` parametern.

[!code-json[cosmosdb-mongodb-database-ru-update](~/quickstart-templates/101-cosmosdb-mongodb-database-ru-update/azuredeploy.json)]

### <a name="deploy-database-template-via-azure-cli"></a>Distribuera databasmall via Azure CLI

För att distribuera Resource Manager-mallen med hjälp av Azure CLI, Välj **prova** att öppna Azure Cloud shell. Högerklicka i gränssnittet för att klistra in skriptet, och välj sedan **klistra in**:

```azurecli-interactive
read -p 'Enter the Resource Group name: ' resourceGroupName
read -p 'Enter the account name: ' accountName
read -p 'Enter the database name: ' databaseName
read -p 'Enter the new throughput: ' throughput

az group deployment create --resource-group $resourceGroupName \
   --template-uri https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/101-cosmosdb-mongodb-database-ru-update/azuredeploy.json \
   --parameters accountName=$accountName databaseName=$databaseName throughput=$throughput
```

## Uppdatera dataflöde (RU/s) på en samling <a id="collection-ru-update"></a>

Följande mall uppdateras dataflödet för en samling. Kopiera mallen och distribuera enligt nedan eller gå till [Azure Snabbstartsgalleriet](https://azure.microsoft.com/resources/templates/101-cosmosdb-mongodb-collection-ru-update/) och distribuera från Azure-portalen. Du kan också ladda ned mallen till din lokala dator eller skapa en ny mall och ange den lokala sökvägen med den `--template-file` parametern.

[!code-json[cosmosdb-mongodb-collection-ru-update](~/quickstart-templates/101-cosmosdb-mongodb-collection-ru-update/azuredeploy.json)]

### <a name="deploy-collection-template-via-azure-cli"></a>Distribuera samling mall via Azure CLI

För att distribuera Resource Manager-mallen med hjälp av Azure CLI, Välj **prova** att öppna Azure Cloud shell. Högerklicka i gränssnittet för att klistra in skriptet, och välj sedan **klistra in**:

```azurecli-interactive
read -p 'Enter the Resource Group name: ' resourceGroupName
read -p 'Enter the account name: ' accountName
read -p 'Enter the database name: ' databaseName
read -p 'Enter the collection name: ' collectionName
read -p 'Enter the new throughput: ' throughput

az group deployment create --resource-group $resourceGroupName \
   --template-uri https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/101-cosmosdb-mongodb-collection-ru-update/azuredeploy.json \
   --parameters accountName=$accountName databaseName=$databaseName collectionName=$collectionName throughput=$throughput
```

## <a name="next-steps"></a>Nästa steg

Här följer några ytterligare resurser:

- [Dokumentation om Azure Resource Manager](/azure/azure-resource-manager/)
- [Providerschema för Azure Cosmos DB-resurs](/azure/templates/microsoft.documentdb/allversions)
- [Azure Cosmos DB-Quickstart-mallar](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.DocumentDB&pageNumber=1&sort=Popular)
- [Felsök vanliga distributionsfel för Azure Resource Manager](../azure-resource-manager/resource-manager-common-deployment-errors.md)