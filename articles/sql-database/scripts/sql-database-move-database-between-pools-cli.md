---
title: CLI exempel flytta Azure SQL database-elastisk SQL-pool | Microsoft Docs
description: "Azure CLI-exempelskript för att flytta en SQL-databas i en elastisk SQL-pool"
services: sql-database
documentationcenter: sql-database
author: janeng
manager: jstrauss
editor: carlrab
tags: azure-service-management
ms.assetid: 
ms.service: sql-database
ms.custom: monitor & tune
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: sql-database
ms.workload: database
ms.date: 07/05/2017
ms.author: janeng
ms.openlocfilehash: 946492e1d4b784a2a70b553826b4ec837799ff6d
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="use-cli-to-move-an-azure-sql-database-in-a-sql-elastic-pool"></a>Använda CLI för att flytta en Azure SQL database i en elastisk SQL-pool

Detta exempel på Azure CLI-skript skapar två elastiska pooler flyttar en Azure SQL-databas från en elastiska SQL-poolen till en annan elastisk SQL-pool och flyttas sedan databasen utanför elastiska poolen till en enda Azure database prestandanivå. 

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Om du väljer att installera och använda CLI lokalt måste du köra Azure CLI version 2.0 eller senare. Kör `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI 2.0]( /cli/azure/install-azure-cli). 

## <a name="sample-script"></a>Exempelskript

[!code-azurecli-interactive[main](../../../cli_scripts/sql-database/move-database-between-pools/move-database-between-pools.sh "Move database between pools")]

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
| [Skapa AZ SQLServer](https://docs.microsoft.com/cli/azure/sql/server#az_sql_server_create) | Skapar en logisk server som är värd för en databas eller elastisk pool. |
| [Skapa AZ sql elastisk-pooler](https://docs.microsoft.com/cli/azure/sql/elastic-pool#az_sql_elastic_pool_create) | Skapar en elastisk pool i den logiska servern. |
| [Skapa AZ sql-databas](https://docs.microsoft.com/cli/azure/sql/db#az_sql_db_create) | Skapar en databas i en logisk server som en enda eller en delad databas. |
| [AZ sql db-uppdateringen](https://docs.microsoft.com/cli/azure/sql/db#az_sql_db_update) | Uppdaterar Databasegenskaper eller flyttar en databas i, slut på eller mellan elastiska pooler. |
| [ta bort grupp AZ](https://docs.microsoft.com/cli/azure/vm/extension#az_vm_extension_set) | Tar bort en resursgrupp, inklusive alla kapslade resurser. |

## <a name="next-steps"></a>Nästa steg

Mer information om Azure CLI finns [Azure CLI dokumentationen](https://docs.microsoft.com/cli/azure/overview).

Ytterligare SQL-databas CLI skriptexempel finns i den [dokumentation för Azure SQL Database](../sql-database-cli-samples.md).


