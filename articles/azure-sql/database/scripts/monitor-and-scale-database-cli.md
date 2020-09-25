---
title: 'Azure CLI: övervaka och skala en enskild databas i Azure SQL Database'
description: Använd ett Azure CLI-exempel skript för att övervaka och skala en enskild databas i Azure SQL Database.
services: sql-database
ms.service: sql-database
ms.subservice: performance
ms.custom: sqldbrb=1
ms.devlang: azurecli
ms.topic: sample
author: juliemsft
ms.author: jrasnick
ms.reviewer: sstein
ms.date: 06/25/2019
ms.openlocfilehash: c4df9ecc025bbffb63730273be06f54cf46f613c
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/25/2020
ms.locfileid: "91319406"
---
# <a name="use-the-azure-cli-to-monitor-and-scale-a-single-database-in-azure-sql-database"></a>Använd Azure CLI för att övervaka och skala en enskild databas i Azure SQL Database

[!INCLUDE[appliesto-sqldb](../../includes/appliesto-sqldb.md)]

Detta exempel på Azure CLI-skript skalar en enskild databas i Azure SQL Database till en annan beräknings storlek efter att ha frågat om storleks informationen för databasen.

Om du väljer att installera och använda Azure CLI lokalt, kräver den här artikeln att du kör Azure CLI version 2,0 eller senare. Kör `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa informationen i [Installera Azure CLI](/cli/azure/install-azure-cli).

## <a name="sample-script"></a>Exempelskript

### <a name="sign-in-to-azure"></a>Logga in på Azure

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]

```azurecli-interactive
$subscription = "<subscriptionId>" # add subscription here

az account set -s $subscription # ...or use 'az login'
```

### <a name="run-the-script"></a>Kör skriptet

[!code-azurecli-interactive[main](../../../../cli_scripts/sql-database/monitor-and-scale-database/monitor-and-scale-database.sh "Monitor and scale a database in Azure SQL Database")]

> [!TIP]
> Använd [AZ SQL DB op List](/cli/azure/sql/db/op?#az-sql-db-op-list) för att hämta en lista över åtgärder som utförts i databasen och Använd [AZ SQL DB op Cancel](/cli/azure/sql/db/op#az-sql-db-op-cancel) för att avbryta en uppdaterings åtgärd på databasen.

### <a name="clean-up-deployment"></a>Rensa distribution

Använd följande kommando för att ta bort resurs gruppen och alla resurser som är kopplade till den.

```azurecli-interactive
az group delete --name $resource
```

## <a name="sample-reference"></a>Exempel referens

Det här skriptet använder följande kommandon. Varje kommando i tabellen länkar till kommandospecifik dokumentation.

| Skript | Description |
|---|---|
| [az sql server](/cli/azure/sql/server) | Server kommandon. |
| [az sql db show-usage](/cli/azure/sql#az-sql-show-usage) | Visar användningsinformation om storlek för en databas. |

## <a name="next-steps"></a>Nästa steg

Mer information om Azure CLI finns i [Azure CLI-dokumentationen](/cli/azure).

Ytterligare CLI-skript exempel finns i [skript för Azure CLI-exempel](../az-cli-script-samples-content-guide.md).
