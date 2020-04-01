---
title: Ansluta ett befintligt Azure Cosmos-konto med slutpunkter för virtuella nätverkstjänster
description: Ansluta ett befintligt Azure Cosmos-konto med slutpunkter för virtuella nätverkstjänster
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: sample
ms.date: 9/25/2019
ms.openlocfilehash: 46e93e864034c451e1da1848a318ab176a292b6e
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "71275565"
---
# <a name="connect-an-existing-azure-cosmos-account-with-virtual-network-service-endpoints-using-azure-cli"></a>Ansluta ett befintligt Azure Cosmos-konto med slutpunkter för virtuella nätverkstjänster med Azure CLI

[!INCLUDE [cloud-shell-try-it.md](../../../../../includes/cloud-shell-try-it.md)]

Om du väljer att installera och använda CLI lokalt kräver det här avsnittet att du kör Azure CLI version 2.0.73 eller senare. Kör `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI](/cli/azure/install-azure-cli).

## <a name="sample-script"></a>Exempelskript

Det här exemplet är avsett att visa hur du ansluter ett befintligt Azure Cosmos-konto till ett befintligt `ignore-missing-vnet-service-endpoint` nytt virtuellt nätverk där undernätet ännu inte är konfigurerat för tjänstslutpunkter med hjälp av parametern. Detta gör att konfigurationen för Cosmos-kontot kan slutföras utan fel innan konfigurationen till det virtuella nätverkets undernät har slutförts. När undernätskonfigurationen är klar kommer Cosmos-kontot att vara tillgängligt via det konfigurerade undernätet.

> [!NOTE]
> Det här exemplet visas med hjälp av ett SQL-API-konto (Core). Om du vill använda det här `enable-virtual-network` `virtual-network-rules` exemplet för andra API:er använder du och parametrarna i skriptet nedan på ditt API-specifika skript.

[!code-azurecli-interactive[main](../../../../../cli_scripts/cosmosdb/common/service-endpoints-ignore-missing-vnet.sh "Create an Azure Cosmos account with service endpoints.")]

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
| [az network vnet create](/cli/azure/network/vnet#az-network-vnet-create) | Skapar ett virtuellt Azure-nätverk. |
| [az network vnet subnet create](/cli/azure/network/vnet/subnet#az-network-vnet-subnet-create) | Skapar ett undernät för ett virtuellt Azure-nätverk. |
| [az nätverk vnet undernät visar](/cli/azure/network/vnet/subnet#az-network-vnet-subnet-show) | Returnerar ett undernät för ett virtuellt Azure-nätverk. |
| [az cosmosdb create](/cli/azure/cosmosdb#az-cosmosdb-create) | Skapar ett Azure Cosmos DB-konto. |
| [az network vnet subnet update](/cli/azure/network/vnet/subnet#az-network-vnet-subnet-update) | Uppdaterar ett undernät för ett virtuellt Azure-nätverk. |
| [az group delete](/cli/azure/resource#az-resource-delete) | Tar bort en resursgrupp, inklusive alla kapslade resurser. |

## <a name="next-steps"></a>Nästa steg

Mer information om Azure Cosmos DB CLI finns i [Azure Cosmos DB CLI-dokumentation](/cli/azure/cosmosdb).

Alla Azure Cosmos DB CLI-skriptexempel finns i [Azure Cosmos DB CLI GitHub Repository](https://github.com/Azure-Samples/azure-cli-samples/tree/master/cosmosdb).
