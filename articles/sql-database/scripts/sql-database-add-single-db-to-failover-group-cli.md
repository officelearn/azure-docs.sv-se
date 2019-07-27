---
title: CLI-exempel – Lägg till en enskild databas i gruppen redundans-Azure SQL Database
description: Exempel skript för Azure CLI för att skapa en Azure SQL Database enskild databas, lägga till den i en grupp för redundans och testa redundans.
services: sql-database
ms.service: sql-database
ms.subservice: high-availability
ms.custom: ''
ms.devlang: azurecli
ms.topic: sample
author: MashaMSFT
ms.author: mathoma
ms.reviewer: carlrab
ms.date: 07/16/2019
ms.openlocfilehash: b80be0db41ea8bd3996a7b8843c80f510289545f
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/26/2019
ms.locfileid: "68570047"
---
# <a name="use-cli-to-move-an-azure-sql-database-in-a-sql-elastic-pool"></a>Använda CLI för flytt av en Azure SQL-databas inom en elastisk SQL-databaspool

Detta exempel på PowerShell-skript skapar en enda databas, skapar en grupp för växling vid fel, lägger till databasen i den och testar redundansväxlingen. 

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Om du väljer att installera och använda CLI lokalt måste du köra Azure CLI version 2.0 eller senare. Kör `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa informationen i [Installera Azure CLI]( /cli/azure/install-azure-cli).

## <a name="sample-script"></a>Exempelskript

[!code-azurecli-interactive[main](../../../cli_scripts/sql-database/failover-groups/add-single-db-to-failover-group-az-cli.sh "Add single database to failover group")]

## <a name="clean-up-deployment"></a>Rensa distribution

Använd följande kommando för att ta bort resurs gruppen och alla resurser som är kopplade till den.

```azurecli-interactive
az group delete --name $resourceGroupName
```

## <a name="script-explanation"></a>Förklaring av skript

Det här skriptet använder följande kommandon. Varje kommando i tabellen länkar till kommandospecifik dokumentation.

| Kommando | Anteckningar |
|---|---|
| [AZ-konto uppsättning](/cli/azure/account?view=azure-cli-latest#az-account-set) | Anger att en prenumeration är den aktuella aktiva prenumerationen. | 
| [az group create](/cli/azure/group#az-group-create) | Skapar en resursgrupp där alla resurser lagras. |
| [az sql server create](/cli/azure/sql/server#az-sql-server-create) | Skapar en SQL Database-server som är värd för enkla databaser och elastiska pooler. |
| [AZ SQL Server-brandvägg-regel skapa](/cli/azure/sql/server/firewall-rule) | Skapar en servers brand Väggs regler. | 
| [AZ SQL-redundans – grupp skapa](/cli/azure/sql/failover-group?view=azure-cli-latest#az-sql-failover-group-create) | Skapar en grupp för redundans. | 
| [AZ SQL-redundans – grupp lista](/cli/azure/sql/failover-group?view=azure-cli-latest#az-sql-failover-group-list) | Visar en lista över failover-grupper på en server. |
| [AZ SQL-redundans – grupp uppsättning – primär](/cli/azure/sql/failover-group?view=azure-cli-latest#az-sql-failover-group-set-primary) | Ange den primära gruppen för redundans genom att redundansväxla alla databaser från den aktuella primära servern. | 
| [az group delete](https://docs.microsoft.com/cli/azure/vm/extension#az-vm-extension-set) | Tar bort en resursgrupp, inklusive alla kapslade resurser. |

## <a name="next-steps"></a>Nästa steg

Mer information om Azure CLI finns i [Azure CLI-dokumentationen](https://docs.microsoft.com/cli/azure).

Ytterligare CLI-skriptexempel för SQL Database finns i [Azure SQL Database-dokumentationen](../sql-database-cli-samples.md).
