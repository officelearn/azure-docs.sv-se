---
title: Azure CLI-skript – Skapa ett Azure Cosmos DB SQL API-konto, databas och container
description: Exempel på Azure CLI-skript – Skapa ett Azure Cosmos DB SQL API-konto, databas och container
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.component: cosmosdb-sql
ms.topic: sample
ms.date: 10/26/2018
ms.openlocfilehash: 0a39786d2ccc8b0b57f539b10ee12d7b55bba381
ms.sourcegitcommit: 5d837a7557363424e0183d5f04dcb23a8ff966bb
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/06/2018
ms.locfileid: "52964602"
---
# <a name="azure-cosmos-db-create-a-sql-api-account-using-cli"></a>Azure Cosmos DB: Skapa ett SQL API-konto med hjälp av CLI

Det här CLI-skriptexemplet skapar ett Azure Cosmos DB SQL API-konto, databas och container.  

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Om du väljer att installera och använda CLI lokalt måste du köra Azure CLI version 2.0 eller senare. Kör `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI](/cli/azure/install-azure-cli).

## <a name="sample-script"></a>Exempelskript

[!code-azurecli-interactive[main](../../../cli_scripts/cosmosdb/create-cosmosdb-account-database/create-cosmosdb-account-database.sh "Create an Azure Cosmos DB SQL API account, database, and container.")]

## <a name="clean-up-deployment"></a>Rensa distribution

När exempelskriptet har körts kan följande kommando användas för att ta bort resursgruppen och alla resurser som är kopplade till den.

```azurecli-interactive
az group delete --name $resourceGroupName
```

## <a name="script-explanation"></a>Förklaring av skript

Det här skriptet använder följande kommandon. Varje kommando i tabellen länkar till kommandospecifik dokumentation.

| Kommando | Anteckningar |
|---|---|
| [az group create](/cli/azure/group#az-group-create) | Skapar en resursgrupp där alla resurser lagras. |
| [az cosmosdb create](/cli/azure/cosmosdb#az-cosmosdb-create) | Skapar ett Azure Cosmos DB-konto. |
| [az cosmosdb database create](/cli/azure/cosmosdb/database#az-cosmosdb-database-create) | Skapar en Azure Cosmos DB-databas. |
| [az cosmosdb collection create](/cli/azure/cosmosdb/collection#az-cosmosdb-collection-create) | Skapar en Azure Cosmos DB-container. |
| [az group delete](/cli/azure/resource#az-resource-delete) | Tar bort en resursgrupp, inklusive alla kapslade resurser. |

## <a name="next-steps"></a>Nästa steg

Mer information om Azure CLI finns i [Azure CLI-dokumentationen](/cli/azure).

Ytterligare Azure Cosmos DB CLI-skriptexempel finns i [Azure Cosmos DB CLI-dokumentationen](../cli-samples.md).
