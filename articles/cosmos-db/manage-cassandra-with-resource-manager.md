---
title: Resource Manager-mallar för Azure Cosmos DB API för Cassandra
description: Använd Azure Resource Manager mallar för att skapa och konfigurera Azure Cosmos DB API för Cassandra.
author: TheovanKraay
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 11/12/2019
ms.author: thvankra
ms.openlocfilehash: c4dc97453fe50865db74f8918ef3dffdb4013b4f
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/13/2020
ms.locfileid: "79251900"
---
# <a name="manage-azure-cosmos-db-cassandra-api-resources-using-azure-resource-manager-templates"></a>Hantera Azure Cosmos DB API för Cassandra resurser med Azure Resource Manager mallar

Den här artikeln beskriver hur du utför olika åtgärder för att automatisera hanteringen av dina Azure Cosmos DB-konton, databaser och behållare med hjälp av Azure Resource Manager mallar. Den här artikeln innehåller exempel på endast SQL API-konton, för att hitta exempel på andra typer av API-typer finns i: använda Azure Resource Manager mallar med Azure Cosmos DB s API för [SQL](manage-sql-with-resource-manager.md), [Gremlin](manage-gremlin-with-resource-manager.md), [MongoDB](manage-mongodb-with-resource-manager.md), [tabell](manage-table-with-resource-manager.md) artiklar.

## Skapa ett Azure Cosmos-konto, ett tomt utrymme och en tabell<a id="create-resource"></a>

Skapa Azure Cosmos DB-resurser med en Azure Resource Manager-mall. Den här mallen skapar ett Azure Cosmos-konto för API för Cassandra med två tabeller som delar 400 RU/s-genomflöde på den på den här sidan. Kopiera mallen och distribuera på det sätt som visas nedan eller gå till [Azure snabb starts galleriet](https://azure.microsoft.com/resources/templates/101-cosmosdb-cassandra/) och distribuera från Azure Portal. Du kan också hämta mallen till den lokala datorn eller skapa en ny mall och ange den lokala sökvägen med parametern `--template-file`.

> [!NOTE]
> Konto namn måste bestå av gemener och 44 eller färre tecken.
> Om du vill uppdatera RU/s skickar du om mallen med uppdaterade egenskaper för data flödes värden.

:::code language="json" source="~/quickstart-templates/101-cosmosdb-cassandra/azuredeploy.json":::

## <a name="deploy-with-the-azure-cli"></a>Distribuera med Azure CLI

Om du vill distribuera Azure Resource Manager-mallen med hjälp av Azure CLI **kopierar** du skriptet och väljer **försök** att öppna Azure Cloud Shell. Om du vill klistra in skriptet högerklickar du på gränssnittet och väljer **Klistra in**:

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

Kommandot `az cosmosdb show` visar det nyligen skapade Azure Cosmos-kontot efter att det har etablerats. Om du väljer att använda en lokalt installerad version av Azure CLI i stället för att använda Cloud Shell, se artikeln [Azure CLI](/cli/azure/) .


## <a name="next-steps"></a>Nästa steg

Här följer några ytterligare resurser:

- [Azure Resource Manager dokumentation](/azure/azure-resource-manager/)
- [Schema för Azure Cosmos DB Resource Provider](/azure/templates/microsoft.documentdb/allversions)
- [Azure Cosmos DB Snabb starts mallar](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.DocumentDB&pageNumber=1&sort=Popular)
- [Felsök vanliga Azure Resource Manager distributions fel](../azure-resource-manager/templates/common-deployment-errors.md)
