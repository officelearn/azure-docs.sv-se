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
ms.date: 05/31/2017
ms.openlocfilehash: b847abb336cce5dd5516469dca58002d3ba265f0
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="monitor-and-scale-a-single-postgresql-server-using-azure-cli"></a>Övervaka och skala en enskild PostgreSQL-server med hjälp av Azure CLI
Det här exempelskriptet CLI skalar en Azure-databas för PostgreSQL-server till en annan prestandanivå efter frågar mätvärdena. 

[!INCLUDE [cloud-shell-try-it](../../../includes/cloud-shell-try-it.md)]

Om du väljer att installera och använda CLI lokalt måste du köra Azure CLI version 2.0 eller senare. Kör `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI 2.0]( /cli/azure/install-azure-cli). 

## <a name="sample-script"></a>Exempelskript
I det här exempelskriptet ändrar du markerade rader om du vill anpassa admin användarnamn och lösenord. Ersätt prenumerations-id som används i az övervakaren kommandon med din egen prenumerations-id.[!code-azurecli-interactive[main](../../../cli_scripts/postgresql/scale-postgresql-server/scale-postgresql-server.sh?highlight=15-16 "Create and scale Azure Database for PostgreSQL.")]

## <a name="clean-up-deployment"></a>Rensa distribution
Följande kommando kan användas för att ta bort resursgruppen och alla resurser som är associerade med den efter skriptexempel har körts.
[!code-azurecli-interactive[main](../../../cli_scripts/postgresql/scale-postgresql-server/delete-postgresql.sh "Delete the resource group.")]

## <a name="script-explanation"></a>Skriptet förklaring
Det här skriptet använder följande kommandon. Varje kommando i tabellen länkar till kommandot viss dokumentation.

| **Kommandot** | **Anteckningar** |
|---|---|
| [Skapa AZ grupp](/cli/azure/group#create) | Skapar en resursgrupp som är lagrade i alla resurser. |
| [Skapa AZ postgres server](/cli/azure/postgres/server#create) | Skapar en PostgreSQL-server som är värd för databaserna. |
| [AZ övervakaren mått lista](/cli/azure/monitor/metrics#list) | Visa en lista med måttet för resurser. |
| [ta bort grupp AZ](/cli/azure/group#delete) | Tar bort en resursgrupp, inklusive alla kapslade resurser. |

## <a name="next-steps"></a>Nästa steg
- Läs mer om Azure CLI: [Azure CLI-dokumentation](/cli/azure/overview)
- Försök ytterligare skript: [Azure CLI-exempel för Azure-databas för PostgreSQL](../sample-scripts-azure-cli.md)
- Mer information om att skala: [tjänstnivåer](../concepts-service-tiers.md) och [Compute enheter och lagringsenheter](../concepts-compute-unit-and-storage.md)
