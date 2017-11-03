---
title: "Azure CLI skript-hämta Azure Cosmos DB-anslutningssträngen för MongoDB-appar | Microsoft Docs"
description: "Skriptexempel Azure CLI - hämta Azure Cosmos DB-anslutningssträngen för MongoDB-appar"
services: cosmos-db
documentationcenter: cosmosdb
author: mimig1
manager: jhubbard
editor: 
tags: azure-service-management
ms.assetid: 
ms.service: cosmos-db
ms.custom: mvc
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: cosmosdb
ms.workload: database
ms.date: 06/02/2017
ms.author: mimig
ms.openlocfilehash: 12e51922db4e9a2ff9b176186a4180ae2238be7d
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="get-an-azure-cosmos-db-connection-string-for-mongodb-apps-using-the-azure-cli"></a>Hämta en Azure Cosmos DB-anslutningssträngen för MongoDB-appar som använder Azure CLI

Det här exemplet hämtar en Azure Cosmos DB-anslutningssträng för MongoDB-appar som använder Azure CLI. 

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Om du väljer att installera och använda CLI lokalt måste du köra Azure CLI version 2.0 eller senare. Kör `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI 2.0]( /cli/azure/install-azure-cli). 

## <a name="sample-script"></a>Exempelskript

[!code-azurecli-interactive[main](../../../cli_scripts/cosmosdb/secure-cosmosdb-get-mongodb-connection-string/secure-cosmosdb-get-mongodb-connection-string.sh?highlight=36-39 "Get Azure Cosmos DB connection string for MongoDB apps")]

## <a name="clean-up-deployment"></a>Rensa distribution

Följande kommando kan användas för att ta bort resursgruppen och alla resurser som är associerade med den efter skriptexempel har körts.

```azurecli-interactive
az group delete --name myResourceGroup
```

## <a name="script-explanation"></a>Skriptet förklaring

Det här skriptet använder följande kommandon. Varje kommando i tabellen länkar till kommandot viss dokumentation.

| Kommando | Anteckningar |
|---|---|
| [Skapa AZ grupp](https://docs.microsoft.com/cli/azure/group#az_group_create) | Skapar en resursgrupp som är lagrade i alla resurser. |
| [AZ cosmosdb uppdatering](https://docs.microsoft.com/cli/azure/cosmosdb#az_cosmosdb_update) | Uppdaterar ett Azure DB som Cosmos-konto. |
| [AZ cosmosdb lista--anslutningssträngar](https://docs.microsoft.com/cli/azure/cosmosdb#az_cosmosdb_list_connection_strings) | Hämtar anslutningssträngen för kontot.|
| [ta bort grupp AZ](https://docs.microsoft.com/cli/azure/group#az_group_delete) | Tar bort en resursgrupp, inklusive alla kapslade resurser. |

## <a name="next-steps"></a>Nästa steg

Mer information om Azure CLI finns [Azure CLI dokumentationen](https://docs.microsoft.com/cli/azure/overview).

Ytterligare Azure Cosmos DB CLI skriptexempel finns i den [Azure Cosmos DB CLI dokumentationen](../cli-samples.md).
