---
title: "Azure CLI skript-Get-konto nycklar för Azure Cosmos DB | Microsoft Docs"
description: "Skriptexempel Azure CLI - Get nycklar för Azure Cosmos DB"
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
ms.openlocfilehash: 5a2881904d85895940be0517052cf2713a360465
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="get-account-keys-for-azure-cosmos-db-using-the-azure-cli"></a>Hämta nycklar för Azure Cosmos-databasen med Azure CLI

Det här exemplet hämtar nycklar för alla typer av Azure DB som Cosmos-konto.  

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Om du väljer att installera och använda CLI lokalt måste du köra Azure CLI version 2.0 eller senare. Kör `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI 2.0]( /cli/azure/install-azure-cli). 

## <a name="sample-script"></a>Exempelskript

[!code-azurecli-interactive[main](../../../cli_scripts/cosmosdb/scale-cosmosdb-get-account-key/secure-cosmosdb-get-account-key.sh?highlight=22-25 "Get Azure Cosmos DB account keys")]

## <a name="clean-up-deployment"></a>Rensa distribution

Följande kommando kan användas för att ta bort resursgruppen och alla resurser som är associerade med den efter skriptexempel har körts.

```azurecli-interactive
az group delete --name myResourceGroup
```

## <a name="script-explanation"></a>Skriptet förklaring

Det här skriptet använder följande kommandon. Varje kommando i tabellen länkar till kommandot viss dokumentation.

| Kommando | Anteckningar |
|---|---|
| [Skapa AZ grupp](/cli/azure/group#create) | Skapar en resursgrupp som är lagrade i alla resurser. |
| [AZ cosmosdb uppdatering](https://docs.microsoft.com/cli/azure/cosmosdb#az_cosmosdb_update) | Uppdaterar ett Azure DB som Cosmos-konto. |
| [AZ cosmosdb lista nycklar](https://docs.microsoft.com/cli/azure/cosmosdb#az_cosmosdb_list_keys) | Skapar en logisk server som är värd för SQL-databasen. |
| [ta bort grupp AZ](https://docs.microsoft.com/cli/azure/group#az_group_delete) | Tar bort en resursgrupp, inklusive alla kapslade resurser. |

## <a name="next-steps"></a>Nästa steg

Mer information om Azure CLI finns [Azure CLI dokumentationen](https://docs.microsoft.com/cli/azure/overview).

Ytterligare Azure Cosmos DB CLI skriptexempel finns i den [Azure Cosmos DB CLI dokumentationen](../cli-samples.md).
