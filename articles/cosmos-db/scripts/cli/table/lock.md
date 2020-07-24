---
title: Skapa resurs lås för en Azure Cosmos DB Tabell-API tabell
description: Skapa resurs lås för en Azure Cosmos DB Tabell-API tabell
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-table
ms.topic: sample
ms.date: 06/03/2020
ms.openlocfilehash: bb66f215241755ae0fbcef3c95f5ea67a686a4fd
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/23/2020
ms.locfileid: "87092583"
---
# <a name="create-resource-lock-for-a-azure-cosmos-db-table-api-table-using-azure-cli"></a>Skapa resurs lås för en Azure Cosmos DB Tabell-API tabell med Azure CLI

[!INCLUDE [cloud-shell-try-it.md](../../../../../includes/cloud-shell-try-it.md)]

Om du väljer att installera och använda CLI lokalt, kräver det här avsnittet att du kör Azure CLI-version 2.6.0 eller senare. Kör `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI](/cli/azure/install-azure-cli).

> [!IMPORTANT]
> Resurs lås fungerar inte för ändringar som gjorts av användare som ansluter Cosmos DB Table SDK, Azure Storage Table SDK, alla verktyg som ansluter via konto nycklar eller Azure Portal om inte Cosmos DB-kontot först är låst med `disableKeyBasedMetadataWriteAccess` egenskapen aktive rad. Mer information om hur du aktiverar den här egenskapen finns i [förhindra ändringar från SDK](../../../role-based-access-control.md#prevent-sdk-changes): er.

## <a name="sample-script"></a>Exempelskript

[!code-azurecli-interactive[main](../../../../../cli_scripts/cosmosdb/table/lock.sh "Create a resource lock for an Azure Cosmos DB Table API table.")]

## <a name="script-explanation"></a>Förklaring av skript

Det här skriptet använder följande kommandon. Varje kommando i tabellen länkar till kommandospecifik dokumentation.

| Kommando | Kommentarer |
|---|---|
| [AZ lås skapa](/cli/azure/lock#az-lock-create) | Skapar ett lås. |
| [AZ lås lista](/cli/azure/lock#az-lock-list) | Visa information om lås. |
| [AZ lås show](/cli/azure/lock#az-lock-show) | Visa lås egenskaper. |
| [AZ lås borttagning](/cli/azure/lock#az-lock-delete) | Tar bort ett lås. |

## <a name="next-steps"></a>Nästa steg

-[Lås resurser för att förhindra oväntade ändringar](../../../../azure-resource-manager/management/lock-resources.md)

-[Azure Cosmos DB CLI-dokumentation](/cli/azure/cosmosdb).

-[Azure Cosmos DB CLI GitHub-lagringsplatsen](https://github.com/Azure-Samples/azure-cli-samples/tree/master/cosmosdb).
