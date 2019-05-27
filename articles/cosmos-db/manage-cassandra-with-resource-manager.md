---
title: Azure Resource Manager-mallar för Azure Cosmos DB Cassandra-API
description: Använd Azure Resource Manager-mallar för att skapa och konfigurera Azure Cosmos DB Cassandra API.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/06/2019
ms.author: mjbrown
ms.openlocfilehash: db754adbe60acfa155400910c47de556db793eef
ms.sourcegitcommit: e9a46b4d22113655181a3e219d16397367e8492d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/21/2019
ms.locfileid: "65968906"
---
# <a name="manage-azure-cosmos-db-cassandra-api-resources-using-azure-resource-manager-templates"></a>Hantera Azure Cosmos DB Cassandra API med Azure Resource Manager-mallar

## Skapa Azure Cosmos-konto, keyspace och tabell <a id="create-resource"></a>

Skapa Azure Cosmos DB-resurser med hjälp av en Azure Resource Manager-mall. Den här mallen skapar ett Azure Cosmos-konto för Cassandra API med två tabeller som delar 400 RU/s genomströmning på keyspace-nivå. Kopiera mallen och distribuera enligt nedan eller gå till [Azure Snabbstartsgalleriet](https://azure.microsoft.com/resources/templates/101-cosmosdb-cassandra/) och distribuera från Azure-portalen. Du kan också ladda ned mallen till din lokala dator eller skapa en ny mall och ange den lokala sökvägen med den `--template-file` parametern.

[!code-json[create-cosmos-Cassandra](~/quickstart-templates/101-cosmosdb-cassandra/azuredeploy.json)]

## <a name="deploy-with-azure-cli"></a>Distribuera med Azure CLI

Att distribuera Resource Manager-mallen med hjälp av Azure CLI, **kopia** skript och välj **prova** att öppna Azure Cloud shell. Högerklicka i gränssnittet för att klistra in skriptet, och välj sedan **klistra in**:

```azurecli-interactive

read -p 'Enter the Resource Group name: ' resourceGroupName
read -p 'Enter the location (i.e. westus2): ' location
read -p 'Enter the account name: ' accountName
read -p 'Enter the primary region (i.e. westus2): ' primaryRegion
read -p 'Enter the secondary region (i.e. eastus2): ' secondaryRegion
read -p 'Enter the keyset name: ' keysetName
read -p 'Enter the first table name: ' table1Name
read -p 'Enter the second table name: ' table2Name

az group create --name $resourceGroupName --location $location
az group deployment create --resource-group $resourceGroupName \
   --template-uri https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/101-cosmosdb-cassandra/azuredeploy.json \
   --parameters accountName=$accountName primaryRegion=$primaryRegion secondaryRegion=$secondaryRegion keysetName=$keysetName \
   table1Name=$table1Name table2Name=$table2Name

az cosmosdb show --resource-group $resourceGroupName --name accountName --output tsv
```

Den `az cosmosdb show` -kommando visar det nya Azure Cosmos-kontot när den har etablerats. Om du väljer att använda ett lokalt installerade versionen av Azure CLI istället för att använda CloudShell, se [Azure kommandoradsgränssnitt (CLI)](/cli/azure/) artikeln.

## Uppdatera dataflöde (RU/s) på ett keyspace <a id="keyspace-ru-update"></a>

Följande mall uppdateras dataflödet för ett keyspace. Kopiera mallen och distribuera enligt nedan eller gå till [Azure Snabbstartsgalleriet](https://azure.microsoft.com/resources/templates/101-cosmosdb-cassandra-keyspace-ru-update/) och distribuera från Azure-portalen. Du kan också ladda ned mallen till din lokala dator eller skapa en ny mall och ange den lokala sökvägen med den `--template-file` parametern.

[!code-json[cosmosdb-cassandra-keyspace-ru-update](~/quickstart-templates/101-cosmosdb-cassandra-keyspace-ru-update/azuredeploy.json)]

### <a name="deploy-keyspace-template-via-azure-cli"></a>Distribuera keyspace mall via Azure CLI

För att distribuera Resource Manager-mallen med hjälp av Azure CLI, Välj **prova** att öppna Azure Cloud shell. Högerklicka i gränssnittet för att klistra in skriptet, och välj sedan **klistra in**:

```azurecli-interactive
read -p 'Enter the Resource Group name: ' resourceGroupName
read -p 'Enter the account name: ' accountName
read -p 'Enter the keyspace name: ' keyspaceName
read -p 'Enter the new throughput: ' throughput

az group deployment create --resource-group $resourceGroupName \
   --template-uri https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/101-cosmosdb-cassandra-keyspace-ru-update/azuredeploy.json \
   --parameters accountName=$accountName keyspaceName=$keyspaceName throughput=$throughput
```

## Uppdatera dataflöde (RU/s) för en tabell <a id="table-ru-update"></a>

Följande mall uppdateras dataflödet för en tabell. Kopiera mallen och distribuera enligt nedan eller gå till [Azure Snabbstartsgalleriet](https://azure.microsoft.com/resources/templates/101-cosmosdb-cassandra-table-ru-update/) och distribuera från Azure-portalen. Du kan också ladda ned mallen till din lokala dator eller skapa en ny mall och ange den lokala sökvägen med den `--template-file` parametern.

[!code-json[cosmosdb-cassandra-table-ru-update](~/quickstart-templates/101-cosmosdb-cassandra-table-ru-update/azuredeploy.json)]

### <a name="deploy-table-template-via-azure-cli"></a>Distribuera mall via Azure CLI

För att distribuera Resource Manager-mallen med hjälp av Azure CLI, Välj **prova** att öppna Azure Cloud shell. Högerklicka i gränssnittet för att klistra in skriptet, och välj sedan **klistra in**:

```azurecli-interactive
read -p 'Enter the Resource Group name: ' resourceGroupName
read -p 'Enter the account name: ' accountName
read -p 'Enter the keyspace name: ' keyspaceName
read -p 'Enter the table name: ' tableName
read -p 'Enter the new throughput: ' throughput

az group deployment create --resource-group $resourceGroupName \
   --template-uri https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/101-cosmosdb-cassandra-table-ru-update/azuredeploy.json \
   --parameters accountName=$accountName keyspaceName=$keyspaceName tableName=$tableName throughput=$throughput
```

## <a name="next-steps"></a>Nästa steg

Här följer några ytterligare resurser:

- [Dokumentation om Azure Resource Manager](/azure/azure-resource-manager/)
- [Providerschema för Azure Cosmos DB-resurs](/azure/templates/microsoft.documentdb/allversions)
- [Azure Cosmos DB-Quickstart-mallar](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.DocumentDB&pageNumber=1&sort=Popular)
- [Felsök vanliga distributionsfel för Azure Resource Manager](../azure-resource-manager/resource-manager-common-deployment-errors.md)