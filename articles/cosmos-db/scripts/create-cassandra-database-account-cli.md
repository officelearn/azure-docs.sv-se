---
title: Azure CLI-skript – Skapa ett konto, en databas och en tabell för API för Cassandra i Azure Cosmos DB | Microsoft Docs
description: Exempel på Azure CLI-skript – Skapa ett konto, en databas och en tabell för API för Cassandra i Azure Cosmos DB
author: markjbrown
ms.service: cosmos-db
ms.component: cosmosdb-cassandra
ms.topic: sample
ms.date: 10/26/2018
ms.author: mjbrown
ms.openlocfilehash: 72eb26599ce2f57e7178d5741851b6e442a62e23
ms.sourcegitcommit: 00dd50f9528ff6a049a3c5f4abb2f691bf0b355a
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/05/2018
ms.locfileid: "51019603"
---
# <a name="azure-cosmos-db-create-a-cassandra-api-account-using-azure-cli"></a>Azure Cosmos DB: Skapa ett Cassandra API-konto med hjälp av Azure CLI

Det här exemplet på CLI-skript skapar ett konto, en databas och en tabell för API för Cassandra i Azure Cosmos DB.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Om du väljer att installera och använda CLI lokalt måste du köra Azure CLI version 2.0 eller senare. Kör `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI](/cli/azure/install-azure-cli).

## <a name="sample-script"></a>Exempelskript

[!code-azurecli-interactive[main](../../../cli_scripts/cosmosdb/create-cosmosdb-cassandra-account/create-cosmosdb-cassandra-account.sh "Create an Azure Cosmos DB Cassandra API account, database, and table.")]

> [!Note]
> Azure CLI stöder inte direkt att skapa en Cassandra-tabell. Använd Azure-portalen eller CQL Shell.

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
| [az group delete](/cli/azure/resource#az-resource-delete) | Tar bort en resursgrupp, inklusive alla kapslade resurser. |

## <a name="next-steps"></a>Nästa steg

Mer information om Azure CLI finns i [Azure CLI-dokumentationen](/cli/azure).

Ytterligare Azure Cosmos DB CLI-skriptexempel finns i [Azure Cosmos DB CLI-dokumentationen](../cli-samples.md).
