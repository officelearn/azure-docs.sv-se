---
title: Resource Manager-mallar för Azure Cosmos DB API för MongoDB
description: Använd Azure Resource Manager-mallar för att skapa och konfigurera Azure Cosmos DB API för MongoDB.
author: TheovanKraay
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 11/12/2019
ms.author: thvankra
ms.openlocfilehash: 531f122679c463b11c84eba2fca9f30b09e0935f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80063642"
---
# <a name="manage-azure-cosmos-db-mongodb-api-resources-using-azure-resource-manager-templates"></a>Hantera Azure Cosmos DB MongoDB API-resurser med Azure Resource Manager-mallar

I den här artikeln beskrivs hur du utför olika åtgärder för att automatisera hanteringen av dina Azure Cosmos DB-konton, databaser och behållare med Hjälp av Azure Resource Manager-mallar. Den här artikeln innehåller exempel för Azure Cosmos DB:s API för MongoDB endast för att hitta exempel på andra API-typkonton se: använd Azure Resource Manager-mallar med Azure Cosmos DB:s API för [Cassandra](manage-cassandra-with-resource-manager.md), [Gremlin](manage-gremlin-with-resource-manager.md), [SQL](manage-sql-with-resource-manager.md), [Tabellartiklar.](manage-table-with-resource-manager.md)

## <a name="create-azure-cosmos-db-api-for-mongodb-account-database-and-collection"></a>Skapa Azure Cosmos DB API för MongoDB-konto, databas och samling<a id="create-resource"></a>

Skapa Azure Cosmos DB-resurser med hjälp av en Azure Resource Manager-mall. Den här mallen skapar ett Azure Cosmos-konto för MongoDB API med två samlingar som delar 400 RU/s dataflöde på databasnivå. Kopiera mallen och distribuera enligt nedan eller besök [Azure Quickstart Gallery](https://azure.microsoft.com/resources/templates/101-cosmosdb-mongodb/) och distribuera från Azure-portalen. Du kan också hämta mallen till den lokala datorn eller skapa `--template-file` en ny mall och ange den lokala sökvägen med parametern.

> [!NOTE]
> Kontonamnen måste vara gemener och 44 eller färre tecken.
> Om du vill uppdatera RU/s skickar du in mallen igen med uppdaterade egenskapsvärden för dataflöde.
>
> För närvarande kan du bara skapa 3.2-version (det `*.documents.azure.com`vill säga konton som använder slutpunkten i formatet) för Azure Cosmos DB:s API för MongoDB-konton med hjälp av PowerShell och CLI. Om du vill skapa 3.6-version av konton använder du Resource Manager-mallar (nedan) eller Azure-portal i stället.

:::code language="json" source="~/quickstart-templates/101-cosmosdb-mongodb/azuredeploy.json":::

### <a name="deploy-via-the-azure-cli"></a>Distribuera via Azure CLI

Om du vill distribuera Azure Resource Manager-mallen med Azure CLI **kopierar du** skriptet och väljer **Prova det** för att öppna Azure Cloud Shell. Om du vill klistra in skriptet högerklickar du på skalet och väljer sedan **Klistra in:**

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

Kommandot `az cosmosdb show` visar det nyligen skapade Azure Cosmos-kontot när det har etablerats. Om du väljer att använda en lokalt installerad version av Azure CLI i stället för att använda Cloud Shell läser du [Azure CLI-artikeln.](/cli/azure/)

## <a name="next-steps"></a>Nästa steg

Här följer några ytterligare resurser:

- [Azure Resource Manager-dokumentation](/azure/azure-resource-manager/)
- [Azure Cosmos DB-resursproviderschema](/azure/templates/microsoft.documentdb/allversions)
- [Snabbstartsmallar för Azure Cosmos DB](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.DocumentDB&pageNumber=1&sort=Popular)
- [Felsöka vanliga distributionsfel i Azure Resource Manager](../azure-resource-manager/templates/common-deployment-errors.md)
