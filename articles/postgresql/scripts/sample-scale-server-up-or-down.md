---
title: "Azure CLI skript skala Azure-databas för PostgreSQL | Microsoft Docs"
description: "Azure CLI skriptexempel - skala Azure-databas för PostgreSQL-server till en annan prestandanivå efter frågar mätvärdena."
services: postgresql
author: salonisonpal
ms.author: salonis
manager: jhubbard
editor: jasonwhowell
ms.service: postgresql
ms.devlang: azure-cli
ms.custom: mvc
ms.topic: sample
ms.date: 11/27/2017
ms.openlocfilehash: a5a24e9aeea193df28bd49d5c428a72b5ec75d1b
ms.sourcegitcommit: 310748b6d66dc0445e682c8c904ae4c71352fef2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/28/2017
---
# <a name="monitor-and-scale-a-single-postgresql-server-using-azure-cli"></a>Övervaka och skala en enskild PostgreSQL-server med hjälp av Azure CLI
Det här exempelskriptet CLI skalar en Azure-databas för PostgreSQL-server till en annan prestandanivå efter frågar mätvärdena. 

[!INCLUDE [cloud-shell-try-it](../../../includes/cloud-shell-try-it.md)]

Om du väljer att installera och använda CLI lokalt måste du köra Azure CLI version 2.0 eller senare. Kör `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI 2.0]( /cli/azure/install-azure-cli). 

## <a name="sample-script"></a>Exempelskript
I det här exempelskriptet ändrar du markerade rader om du vill anpassa admin användarnamn och lösenord. Ersätt prenumerations-ID som används i az övervakaren kommandon med din egen prenumerations-ID.
[!code-azurecli-interactive[main](../../../cli_scripts/postgresql/scale-postgresql-server/scale-postgresql-server.sh?highlight=15-16 "Create and scale Azure Database for PostgreSQL.")]

## <a name="clean-up-deployment"></a>Rensa distribution
Följande kommando kan användas för att ta bort resursgruppen och alla resurser som är associerade med den efter skriptexempel har körts.
[!code-azurecli-interactive[main](../../../cli_scripts/postgresql/scale-postgresql-server/delete-postgresql.sh "Delete the resource group.")]

## <a name="script-explanation"></a>Skriptet förklaring
Det här skriptet använder följande kommandon. Varje kommando i tabellen länkar till kommandot viss dokumentation.

| **Kommandot** | **Anteckningar** |
|---|---|
| [Skapa AZ grupp](/cli/azure/group#az_group_create) | Skapar en resursgrupp som är lagrade i alla resurser. |
| [Skapa AZ postgres server](/cli/azure/postgres/server#az_postgres_server_create) | Skapar en PostgreSQL-server som är värd för databaserna. |
| [AZ övervakaren mått lista](/cli/azure/monitor/metrics#az_monitor_metrics_list) | Visa en lista med måttet för resurser. |
| [ta bort grupp AZ](/cli/azure/group#az_group_delete) | Tar bort en resursgrupp, inklusive alla kapslade resurser. |

## <a name="next-steps"></a>Nästa steg
- Läs mer om Azure CLI: [Azure CLI-dokumentation](/cli/azure/overview)
- Försök ytterligare skript: [Azure CLI-exempel för Azure-databas för PostgreSQL](../sample-scripts-azure-cli.md)
- Mer information om att skala: [tjänstnivåer](../concepts-service-tiers.md) och [Compute enheter och lagringsenheter](../concepts-compute-unit-and-storage.md)
