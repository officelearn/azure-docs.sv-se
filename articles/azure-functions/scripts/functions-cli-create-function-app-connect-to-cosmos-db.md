---
title: Skapa en Azure-funktion som ansluter till Azure Cosmos DB | Microsoft Docs
description: Exempel på Azure CLI-skript – Skapa en Azure-funktion som ansluter till Azure Cosmos DB
services: functions
documentationcenter: functions
author: ggailey777
manager: cfowler
editor: ''
tags: functions
ms.assetid: ''
ms.service: functions
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: na
ms.workload: ''
ms.date: 01/22/2018
ms.author: glenga
ms.custom: mvc
ms.openlocfilehash: 092e0681b0491fc1c54c19e234aafdac6d428fd1
ms.sourcegitcommit: 8c3267c34fc46c681ea476fee87f5fb0bf858f9e
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/09/2018
ms.locfileid: "29843229"
---
# <a name="create-an-azure-function-that-connects-to-an-azure-cosmos-db"></a>Skapa en Azure-funktion som ansluter till Azure Cosmos DB

Det här exempelskriptet för Azure Functions skapar en funktionsapp och ansluter funktionen till en Azure Cosmos DB-databas. Appinställningen som skapas och som innehåller anslutningen kan användas med en [lagringsutlösare eller bindning för Azure Cosmos DB](..\functions-bindings-cosmosdb.md).

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Om du använder CLI lokalt kontrollerar du att du har Azure CLI version 2.0 eller senare. Kör `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI 2.0](/cli/azure/install-azure-cli). 

## <a name="sample-script"></a>Exempelskript

Det här exemplet skapar en Azure-funktionsapp och lägger till en Cosmos DB-slutpunkt och åtkomstnyckel i appinställningar.

[!code-azurecli-interactive[main](../../../cli_scripts/azure-functions/create-function-app-connect-to-cosmos-db/create-function-app-connect-to-cosmos-db.sh "Create an Azure Function that connects to an Azure Cosmos DB")]

[!INCLUDE [cli-script-clean-up](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>Förklaring av skript

Det här skriptet använder följande kommandon: varje kommando i tabellen länkar till kommandospecifik dokumentation.

| Kommando | Anteckningar |
|---|---|
| [az login](https://docs.microsoft.com/cli/azure/reference-index#az_login) | Logga in på Azure. |
| [az group create](https://docs.microsoft.com/cli/azure/group#az_group_create) | Skapa en resursgrupp med plats |
| [az storage accounts create](https://docs.microsoft.com/cli/azure/storage/account) | skapar ett lagringskonto |
| [az functionapp create](https://docs.microsoft.com/cli/azure/functionapp#az_functionapp_create) | Skapa en ny funktionsapp |
| [az cosmosdb create](https://docs.microsoft.com/cli/azure/cosmosdb#az_cosmosdb_create) | Skapa cosmosdb-databas |
| [az group delete](https://docs.microsoft.com/cli/azure/group#az_group_delete) | Rensa |

## <a name="next-steps"></a>Nästa steg

Mer information om Azure CLI finns i [Azure CLI-dokumentationen](https://docs.microsoft.com/cli/azure).

Ytterligare CLI-skriptexempel för Azure Functions finns i [Azure Functions-dokumentationen](../functions-cli-samples.md).




