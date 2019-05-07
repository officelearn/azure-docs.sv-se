---
title: Azure Resource Manager-mallar för Azure Cosmos DB API för MongoDB
description: Använd Azure Resource Manager-mallar för att skapa och konfigurera Azure Cosmos DB API för MongoDB.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/06/2019
ms.author: mjbrown
ms.openlocfilehash: bcf9e0492e58de79efbb16f9ee13adbd0f27b572
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/06/2019
ms.locfileid: "65077777"
---
# <a name="create-azure-cosmos-db-api-for-mongodb-resources-from-a-resource-manager-template"></a>Skapa Azure Cosmos DB API för MongoDB-resurser från en Resource Manager-mall

Lär dig hur du skapar ett Azure Cosmos DB-API för MongoDB-resurser med hjälp av en Azure Resource Manager-mall. I följande exempel skapas ett Azure Cosmos DB-konto för MongoDB-API från en [Azure-snabbstartsmall](https://aka.ms/mongodb-arm-qs). Den här mallen skapar ett Azure Cosmos-konto för MongoDB-API med två samlingar som delar 400 RU/s genomströmning på databasnivå.

Här är en kopia av mallen:

[!code-json[create-cosmos-mongo](~/quickstart-templates/101-cosmosdb-mongodb/azuredeploy.json)]

## <a name="deploy-via-azure-cli"></a>Distribuera via Azure CLI

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

I exemplet ovan har du refererar till en mall som lagras på GitHub. Du kan också ladda ned mallen till din lokala dator eller skapa en ny mall och ange den lokala sökvägen med den `--template-file` parametern.

## <a name="next-steps"></a>Nästa steg

Här följer några ytterligare resurser:

- [Dokumentation om Azure Resource Manager](/azure/azure-resource-manager/)
- [Providerschema för Azure Cosmos DB-resurs](/azure/templates/microsoft.documentdb/allversions)
- [Azure Cosmos DB-Quickstart-mallar](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.DocumentDB&pageNumber=1&sort=Popular)
- [Felsök vanliga distributionsfel för Azure Resource Manager](../azure-resource-manager/resource-manager-common-deployment-errors.md)