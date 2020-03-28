---
title: Skapa en funktionsapp med Azure Cosmos DB - Azure CLI
description: Exempel på Azure CLI-skript – Skapa en Azure-funktion som ansluter till Azure Cosmos DB
ms.topic: sample
ms.date: 07/03/2018
ms.custom: mvc
ms.openlocfilehash: 5ee80283ed39789eabb702a48aa97f678a6409f9
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "75922717"
---
# <a name="create-an-azure-function-that-connects-to-an-azure-cosmos-db"></a>Skapa en Azure-funktion som ansluter till Azure Cosmos DB

Det här exempelskriptet för Azure Functions skapar en funktionsapp och ansluter funktionen till en Azure Cosmos DB-databas. Appinställningen som skapas och som innehåller anslutningen kan användas med en [lagringsutlösare eller bindning för Azure Cosmos DB](../functions-bindings-cosmosdb.md).

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Om du använder CLI lokalt kontrollerar du att du har Azure CLI version 2.0 eller senare. Kör `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa informationen i [Installera Azure CLI](/cli/azure/install-azure-cli). 

## <a name="sample-script"></a>Exempelskript

Det här exemplet skapar en Azure-funktionsapp och lägger till en Cosmos DB-slutpunkt och åtkomstnyckel i appinställningar.

[!code-azurecli-interactive[main](../../../cli_scripts/azure-functions/create-function-app-connect-to-cosmos-db/create-function-app-connect-to-cosmos-db.sh "Create an Azure Function that connects to an Azure Cosmos DB")]

[!INCLUDE [cli-script-clean-up](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>Förklaring av skript

Det här skriptet använder följande kommandon: varje kommando i tabellen länkar till kommandospecifik dokumentation.

| Kommando | Anteckningar |
|---|---|
| [az group create](/cli/azure/group#az-group-create) | Skapa en resursgrupp med plats |
| [az storage accounts create](/cli/azure/storage/account#az-storage-account-create) | Skapa ett lagringskonto |
| [az functionapp create](/cli/azure/functionapp#az-functionapp-create) | Skapar en funktionsapp i den serverlösa [förbrukningsplanen](../functions-scale.md#consumption-plan). |
| [az cosmosdb create](/cli/azure/cosmosdb#az-cosmosdb-create) | Skapa en Azure Cosmos DB-databas. |
| [az kosmosdb visa](/cli/azure/cosmosdb#az-cosmosdb-show)| Hämtar databaskontoanslutningen. |
| [az cosmosdb list-keys](/cli/azure/cosmosdb#az-cosmosdb-list-keys)| Hämtar nycklarna till databasen. |
| [az functionapp config appsettings set az functionapp config appsettings set az functionapp config appsettings set az function](/cli/azure/functionapp/config/appsettings#az-functionapp-config-appsettings-set) | Ställer in anslutningssträngen som en appinställning i funktionsappen. |

## <a name="next-steps"></a>Nästa steg

Mer information om Azure CLI finns i [Azure CLI-dokumentationen](/cli/azure).

Ytterligare CLI-skriptexempel för Azure Functions finns i [Azure Functions-dokumentationen](../functions-cli-samples.md).




