---
title: Azure CLI-skript – Replikering i flera regioner för Azure Cosmos DB
description: Exempel på Azure CLI-skript – Replikering i flera regioner för Azure Cosmos DB
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: sample
ms.date: 10/26/2018
ms.openlocfilehash: 9981d41a259eeb48354002a98c9440156a7f2481
ms.sourcegitcommit: 5d837a7557363424e0183d5f04dcb23a8ff966bb
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/06/2018
ms.locfileid: "52962519"
---
# <a name="replicate-an-azure-cosmos-db-database-account-in-multiple-regions-and-configure-failover-priorities-using-the-azure-cli"></a>Replikera ett Azure Cosmos DB-databaskonto i flera regioner och konfigurera redundansprioriteringar med hjälp av Azure CLI

Det här exemplet replikerar ett Azure Cosmos DB-databaskonto i flera regioner och konfigurerar redundansprioriteringar med hjälp av Azure CLI.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Om du väljer att installera och använda CLI lokalt måste du köra Azure CLI version 2.0 eller senare. Kör `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI](/cli/azure/install-azure-cli).

## <a name="sample-script"></a>Exempelskript

[!code-azurecli-interactive[main](../../../cli_scripts/cosmosdb/scale-cosmosdb-replicate-multiple-regions/scale-cosmosdb-replicate-multiple-regions.sh "Scale Azure Cosmos DB into multiple regions, change fail-over regions")]

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
| [az cosmosdb update](/cli/azure/cosmosdb#az-cosmosdb-update) | Uppdaterar ett Azure Cosmos DB-konto. |
| [az group delete](/cli/azure/group#az-group-delete) | Tar bort en resursgrupp, inklusive alla kapslade resurser. |

## <a name="next-steps"></a>Nästa steg

Mer information om Azure CLI finns i [Azure CLI-dokumentationen](/cli/azure).

Ytterligare Azure Cosmos DB CLI-skriptexempel finns i [Azure Cosmos DB CLI-dokumentationen](../cli-samples.md).
