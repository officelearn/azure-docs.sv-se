---
title: Skapa en Function-app med Azure Cosmos DB – Azure CLI
description: Exempel på Azure CLI-skript – Skapa en Azure-funktion som ansluter till Azure Cosmos DB
ms.topic: sample
ms.date: 07/03/2018
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: 440767159ec1321d9b157f53408dbff8f9706eec
ms.sourcegitcommit: 11e2521679415f05d3d2c4c49858940677c57900
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/31/2020
ms.locfileid: "87498573"
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
| [az storage accounts create](/cli/azure/storage/account#az-storage-account-create) | skapar ett lagringskonto |
| [az functionapp create](/cli/azure/functionapp#az-functionapp-create) | Skapar en Function-app i [förbruknings planen](../functions-scale.md#consumption-plan)utan server. |
| [az cosmosdb create](/cli/azure/cosmosdb#az-cosmosdb-create) | Skapa en Azure Cosmos DB-databas. |
| [AZ cosmosdb show](/cli/azure/cosmosdb#az-cosmosdb-show)| Hämtar databas konto anslutningen. |
| [az cosmosdb list-keys](/cli/azure/cosmosdb#az-cosmosdb-list-keys)| Hämtar nycklar för databasen. |
| [AZ functionapp config appSettings set](/cli/azure/functionapp/config/appsettings#az-functionapp-config-appsettings-set) | Anger anslutnings strängen som en app-inställning i Function-appen. |

## <a name="next-steps"></a>Nästa steg

Mer information om Azure CLI finns i [Azure CLI-dokumentationen](/cli/azure).

Ytterligare CLI-skriptexempel för Azure Functions finns i [Azure Functions-dokumentationen](../functions-cli-samples.md).




