---
title: Skapa resurs lås för en Azure Cosmos DB kärna (SQL) API-databas och behållare
description: Skapa resurs lås för en Azure Cosmos DB kärna (SQL) API-databas och behållare
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: sample
ms.date: 06/03/2020
ms.openlocfilehash: 5a44376e0039e2783081785b06191f7c36c19f60
ms.sourcegitcommit: 5504d5a88896c692303b9c676a7d2860f36394c1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/08/2020
ms.locfileid: "84514708"
---
# <a name="create-resource-lock-for-a-azure-cosmos-db-core-sql-api-database-and-container-using-azure-cli"></a>Skapa resurs lås för en Azure Cosmos DB Core (SQL) API-databas och behållare med hjälp av Azure CLI

[!INCLUDE [cloud-shell-try-it.md](../../../../../includes/cloud-shell-try-it.md)]

Om du väljer att installera och använda CLI lokalt, kräver det här avsnittet att du kör Azure CLI-version 2.6.0 eller senare. Kör `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI](/cli/azure/install-azure-cli).

> [!IMPORTANT]
> Resurs lås fungerar inte för ändringar som gjorts av användare som ansluter med hjälp av någon Cosmos DB SDK, verktyg som ansluter via konto nycklar eller Azure Portal om inte Cosmos DB-kontot först är låst med `disableKeyBasedMetadataWriteAccess` egenskapen aktive rad. Mer information om hur du aktiverar den här egenskapen finns i [förhindra ändringar från SDK](../../../role-based-access-control.md#preventing-changes-from-cosmos-sdk): er.

## <a name="sample-script"></a>Exempelskript

[!code-azurecli-interactive[main](../../../../../cli_scripts/cosmosdb/sql/lock.sh "Create a resource lock for an Azure Cosmos DB Core (SQL) API database and container.")]

## <a name="script-explanation"></a>Förklaring av skript

Det här skriptet använder följande kommandon. Varje kommando i tabellen länkar till kommandospecifik dokumentation.

| Kommando | Obs! |
|---|---|
| [AZ lås skapa](/cli/azure/lock#az-lock-create) | Skapar ett lås. |
| [AZ lås lista](/cli/azure/lock#az-lock-list) | Visa information om lås. |
| [AZ lås show](/cli/azure/lock#az-lock-show) | Visa lås egenskaper. |
| [AZ lås borttagning](/cli/azure/lock#az-lock-delete) | Tar bort ett lås. |

## <a name="next-steps"></a>Nästa steg

-[Lås resurser för att förhindra oväntade ändringar](../../../../azure-resource-manager/management/lock-resources.md)

-[Azure Cosmos DB CLI-dokumentation](/cli/azure/cosmosdb).

-[Azure Cosmos DB CLI GitHub-lagringsplatsen](https://github.com/Azure-Samples/azure-cli-samples/tree/master/cosmosdb).
