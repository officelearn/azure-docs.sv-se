---
title: "Azure CLI-skript – Återställa en Azure Database for PostgreSQL-server"
description: "Det här Azure CLI-exempelskriptet visar hur du återställer en Azure Database for PostgreSQL-server och dess databaser till en tidigare tidpunkt."
services: postgresql
author: rachel-msft
ms.author: raagyema
manager: kfile
editor: jasonwhowell
ms.service: postgresql
ms.devlang: azure-cli
ms.topic: sample
ms.custom: mvc
ms.date: 02/28/2018
ms.openlocfilehash: c8b099e859e9ab418fd121a987920d037ed3f36d
ms.sourcegitcommit: 83ea7c4e12fc47b83978a1e9391f8bb808b41f97
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/28/2018
---
# <a name="restore-an-azure-database-for-postgresql-server-using-azure-cli"></a>Återställ en Azure Database for PostgreSQL-server med hjälp av Azure CLI
Det här CLI-exempelskriptet återställer en enda Azure Database for PostgreSQL-server till en tidigare tidpunkt.

[!INCLUDE [cloud-shell-try-it](../../../includes/cloud-shell-try-it.md)]

Om du väljer att köra CLI lokalt måste du ha Azure CLI version 2.0 eller senare. Kontrollera versionen genom att köra `az --version`. Se [Installera Azure CLI 2.0]( /cli/azure/install-azure-cli) för att installera eller uppgradera din version av Azure CLI.

## <a name="sample-script"></a>Exempelskript
I det här exempelskriptet ändrar du de markerade raderna om du vill uppdatera administratörens användarnamn och lösenord till dina egna. Ersätt prenumerations-ID som används i `az monitor`-kommandon med ditt eget prenumerations-ID.
[!code-azurecli-interactive[main](../../../cli_scripts/postgresql/backup-restore/backup-restore.sh?highlight=18-19 "Restore Azure Database for PostgreSQL.")]

## <a name="clean-up-deployment"></a>Rensa distribution
När skriptet har körts kan följande kommando användas för att ta bort resursgruppen och alla resurser som är kopplade till den. 
[!code-azurecli-interactive[main](../../../cli_scripts/postgresql/backup-restore/delete-postgresql.sh  "Delete the resource group.")]

## <a name="script-explanation"></a>Förklaring av skript
Det här skriptet använder de kommandon som beskrivs i följande tabell:

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
