---
title: Skapa en Azure-funktion som ansluter till en Azure-Cosmos-databas | Microsoft Docs
description: Azure CLI-skript Sample - skapa en Azure-funktion som ansluter till en Azure-Cosmos-databas
services: functions
documentationcenter: functions
author: rachelappel
manager: cfowler
editor: 
tags: functions
ms.assetid: 
ms.service: functions
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: na
ms.workload: 
ms.date: 04/20/2017
ms.author: rachelap
ms.custom: mvc
ms.openlocfilehash: c2c3530df62a1f291be51739a7918f7b8ab08487
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="create-an-azure-function-that-connects-to-an-azure-cosmos-db"></a>Skapa en Azure-funktion som ansluter till en Azure-Cosmos-databas

Det här exempelskriptet skapar en App för Azure-funktion och ansluter till en Azure Cosmos-DB-databas.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Om du väljer att installera och använda CLI lokalt måste du köra Azure CLI version 2.0 eller senare. Kör `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI 2.0]( /cli/azure/install-azure-cli). 

## <a name="sample-script"></a>Exempelskript

Det här exemplet skapar en app i Azure-funktion och lägger till en Cosmos-DB-slutpunkten och åtkomstnyckel app-inställningar.

[!code-azurecli-interactive[main](../../../cli_scripts/azure-functions/create-function-app-connect-to-cosmos-db/create-function-app-connect-to-cosmos-db.sh "Create an Azure Function that connects to an Azure Cosmos DB")]

## <a name="clean-up-deployment"></a>Rensa distribution

Efter skriptexempel har körts, följ-kommando kan användas för att ta bort resursgruppen och alla relaterade resurser.

[!INCLUDE [cli-script-clean-up](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>Skriptet förklaring

Det här skriptet använder följande kommandon. Varje kommando i tabellen länkar till kommandot viss dokumentation.

| Kommando | Anteckningar |
|---|---|
| [AZ inloggning](https://docs.microsoft.com/cli/azure/#login) | Logga in på Azure. |
| [Skapa AZ grupp](https://docs.microsoft.com/cli/azure/group#az_group_create) | Skapa en resursgrupp med platsen |
| [Skapa AZ storage-konto](https://docs.microsoft.com/cli/azure/storage/account) | skapar ett lagringskonto |
| [Skapa AZ functionapp](https://docs.microsoft.com/cli/azure/functionapp#az_functionapp_create) | Skapa en ny funktionsapp |
| [Skapa AZ documentdb](https://docs.microsoft.com/cli/azure/documentdb#az_documentdb_create) | Skapa documentdb-databas |
| [ta bort grupp AZ](https://docs.microsoft.com/cli/azure/group#az_group_delete) | Rensa |

## <a name="next-steps"></a>Nästa steg

Mer information om Azure CLI finns [Azure CLI dokumentationen](https://docs.microsoft.com/cli/azure/overview).

Ytterligare Azure Functions CLI skriptexempel finns i den [Azure Functions dokumentationen](../functions-cli-samples.md).




