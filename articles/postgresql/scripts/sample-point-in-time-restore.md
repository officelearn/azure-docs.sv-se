---
title: "Azure CLI-skript: återställa en Azure-databas för PostgreSQL-server"
description: "Det här exemplet Azure CLI visar hur du återställer en Azure-databas för MySQL-servern och dess databaser till en tidigare tidpunkt."
services: postgresql
author: v-chenyh
ms.author: v-chenyh
manager: jhubbard
editor: jasonwhowell
ms.service: postgresql-database
ms.devlang: azure-cli
ms.topic: sample
ms.custom: mvc
ms.date: 01/12/2018
ms.openlocfilehash: dfc53ae10055b0e8583fb0c705e605bbbb999760
ms.sourcegitcommit: 384d2ec82214e8af0fc4891f9f840fb7cf89ef59
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/16/2018
---
# <a name="restore-an-azure-database-for-postgresql-server-using-azure-cli"></a>Återställa en Azure-databas för PostgreSQL-server med Azure CLI
Det här exempelskriptet CLI återställer en Azure-databas för PostgreSQL-servern till en tidigare tidpunkt.

[!INCLUDE [cloud-shell-try-it](../../../includes/cloud-shell-try-it.md)]

Om du väljer att installera och använda CLI lokalt kräver i det här exemplet att du använder Azure CLI version 2.0 eller senare. Kör `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI 2.0]( /cli/azure/install-azure-cli). 

## <a name="sample-script"></a>Exempelskript
I det här exempelskriptet ändrar du markerade rader om du vill anpassa admin användarnamn och lösenord. Ersätt prenumerations-ID som används i az övervakaren kommandon med din egen prenumerations-ID.
[!code-azurecli-interactive[main](../../../cli_scripts/postgresql/backup-restore/backup-restore.sh?highlight=15-16 "Restore Azure Database for PostgreSQL.")]

## <a name="clean-up-deployment"></a>Rensa distribution
Följande kommando kan användas för att ta bort resursgruppen och alla resurser som är associerade med den efter skriptexempel har körts.
[!code-azurecli-interactive[main](../../../cli_scripts/postgresql/backup-restore/delete-postgresql.sh  "Delete the resource group.")]

## <a name="script-explanation"></a>Skriptet förklaring
Det här skriptet använder följande kommandon. Varje kommando i tabellen länkar till kommandot viss dokumentation.

| **Kommandot** | **Anteckningar** |
|---|---|
| [Skapa AZ grupp](/cli/azure/group#az_group_create) | Skapar en resursgrupp som är lagrade i alla resurser. |
| [Skapa AZ postgresql-server](/cli/azure/postgresql/server#az_msql_server_create) | Skapar en PostgreSQL-server som är värd för databaserna. |
| [återställning av AZ postgresql server](/cli/azure/postgresql/server#az_msql_server_restore) | Återställa en server från en säkerhetskopia. |
| [ta bort grupp AZ](/cli/azure/group#az_group_delete) | Tar bort en resursgrupp, inklusive alla kapslade resurser. |

## <a name="next-steps"></a>Nästa steg
- Läs mer om Azure CLI: [Azure CLI dokumentationen](/cli/azure/overview).
- Försök ytterligare skript: [Azure CLI-exempel för Azure-databas för PostgreSQL](../sample-scripts-azure-cli.md)
- [Säkerhetskopiera och återställa en server i Azure-databas för PostgreSQL med Azure-portalen](../howto-restore-server-portal.md)
