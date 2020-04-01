---
title: Arbeta med kontonycklar och anslutningssträngar för ett Azure Cosmos-konto
description: Arbeta med kontonycklar och anslutningssträngar för ett Azure Cosmos-konto
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: sample
ms.date: 9/25/2019
ms.openlocfilehash: e766f52d729a4f916eefd2b148d926d929b4f540
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "71275558"
---
# <a name="work-with-account-keys-and-connection-strings-for-an-azure-cosmos-account-using-azure-cli"></a>Arbeta med kontonycklar och anslutningssträngar för ett Azure Cosmos-konto med Azure CLI

[!INCLUDE [cloud-shell-try-it.md](../../../../../includes/cloud-shell-try-it.md)]

Om du väljer att installera och använda CLI lokalt kräver det här avsnittet att du kör Azure CLI version 2.0.73 eller senare. Kör `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI](/cli/azure/install-azure-cli).

## <a name="sample-script"></a>Exempelskript

Det här skriptet visar fyra åtgärder.

- Lista alla kontonycklar
- Lista skrivskyddade kontonycklar
- Lista anslutningssträngar
- Återskapa kontonycklar

> [!NOTE]
> Det här exemplet visas med hjälp av ett SQL-API-konto (Core), men kontonyckeln och anslutningssträngåtgärderna är identiska i alla databas-API:er i Cosmos DB.

[!code-azurecli-interactive[main](../../../../../cli_scripts/cosmosdb/common/keys.sh "Keys and connection string operations for Cosmos DB.")]

## <a name="clean-up-deployment"></a>Rensa distribution

När skriptexemplet har körts kan följande kommando användas för att ta bort resursgruppen och alla resurser som är kopplade till den.

```azurecli-interactive
az group delete --name $resourceGroupName
```

## <a name="script-explanation"></a>Förklaring av skript

Det här skriptet använder följande kommandon. Varje kommando i tabellen länkar till kommandospecifik dokumentation.

| Kommando | Anteckningar |
|---|---|
| [az group create](/cli/azure/group#az-group-create) | Skapar en resursgrupp där alla resurser lagras. |
| [az cosmosdb create](/cli/azure/cosmosdb#az-cosmosdb-create) | Skapar ett Azure Cosmos DB-konto. |
| [az cosmosdb nycklar lista](/cli/azure/cosmosdb/keys#az-cosmosdb-keys-list) | Lista nycklarna för ett Azure Cosmos DB-konto. |
| [az cosmosdb list-skrivskyddade nycklar](/cli/azure/cosmosdb#az-cosmosdb-list-read-only-keys) | Lista de skrivskyddade nycklarna för ett Azure Cosmos DB-konto. |
| [az cosmosdb list-connection-strings](/cli/azure/cosmosdb#az-cosmosdb-list-connection-strings) | Lista anslutningssträngarna för ett Azure Cosmos DB-konto. |
| [az cosmosdb regenerate-key](/cli/azure/cosmosdb#az-cosmosdb-regenerate-key) | Återskapa nycklar för ett Azure Cosmos DB-konto. |
| [az group delete](/cli/azure/resource#az-resource-delete) | Tar bort en resursgrupp, inklusive alla kapslade resurser. |

## <a name="next-steps"></a>Nästa steg

Mer information om Azure Cosmos DB CLI finns i [Azure Cosmos DB CLI-dokumentation](/cli/azure/cosmosdb).

Alla Azure Cosmos DB CLI-skriptexempel finns i [Azure Cosmos DB CLI GitHub Repository](https://github.com/Azure-Samples/azure-cli-samples/tree/master/cosmosdb).
