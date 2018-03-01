---
title: "Azure CLI-skript: Återställa en Azure Database for PostgreSQL-server"
description: "Det här Azure CLI-exempelskriptet visar hur du återställer en Azure Database for MySQL-server och dess databaser till en tidigare tidpunkt."
services: postgresql
author: v-chenyh
ms.author: v-chenyh
manager: jhubbard
editor: jasonwhowell
ms.service: postgresql
ms.devlang: azure-cli
ms.topic: sample
ms.custom: mvc
ms.date: 01/12/2018
ms.openlocfilehash: 242dd836a629d3accd0c43a72b4549e93145168f
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/01/2018
---
# <a name="restore-an-azure-database-for-postgresql-server-using-azure-cli"></a>Återställ en Azure Database for PostgreSQL-server med hjälp av Azure CLI
Det här CLI-exempelskriptet återställer en enda Azure Database for PostgreSQL-server till en tidigare tidpunkt.

[!INCLUDE [cloud-shell-try-it](../../../includes/cloud-shell-try-it.md)]

Om du väljer att installera och använda CLI lokalt måste du köra Azure CLI version 2.0 eller senare. Kör `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI 2.0]( /cli/azure/install-azure-cli). 

## <a name="sample-script"></a>Exempelskript
I det här exempelskriptet ändrar du markerade rader om du vill anpassa administratörens användarnamn och lösenord. Ersätt prenumerations-ID som används i az monitor-kommandon med ditt eget prenumerations-ID.
[!code-azurecli-interactive[main](../../../cli_scripts/postgresql/backup-restore/backup-restore.sh?highlight=15-16 "Restore Azure Database for PostgreSQL.")]

## <a name="clean-up-deployment"></a>Rensa distribution
När exempelskriptet har körts kan följande kommando användas för att ta bort resursgruppen och alla resurser som är kopplade till den.
[!code-azurecli-interactive[main](../../../cli_scripts/postgresql/backup-restore/delete-postgresql.sh  "Delete the resource group.")]

## <a name="script-explanation"></a>Förklaring av skript
Det här skriptet använder följande kommandon. Varje kommando i tabellen länkar till kommandospecifik dokumentation.

| **Kommando** | **Anteckningar** |
|---|---|
| [az group create](/cli/azure/group#az_group_create) | Skapar en resursgrupp där alla resurser lagras. |
| [az postgresql server create](/cli/azure/postgresql/server#az_msql_server_create) | Skapar en PostgreSQL-server som är värd för databaserna. |
| [az postgresql server restore](/cli/azure/postgresql/server#az_msql_server_restore) | Återställ en server från en säkerhetskopia. |
| [az group delete](/cli/azure/group#az_group_delete) | Tar bort en resursgrupp, inklusive alla kapslade resurser. |

## <a name="next-steps"></a>Nästa steg
- Läs mer om Azure CLI: [Azure CLI-dokumentation](/cli/azure/overview).
- Prova ytterligare skript: [Azure CLI-exempel för Azure Database for PostgreSQL](../sample-scripts-azure-cli.md)
- [Säkerhetskopiera och återställa en server i Azure Database for PostgreSQL med Azure-portalen](../howto-restore-server-portal.md)
