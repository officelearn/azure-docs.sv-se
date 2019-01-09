---
title: Azure CLI-skript – Hämta serverloggar i Azure Database for PostgreSQL
description: Det här Azure CLI-exempelskriptet visar hur du aktiverar och hämtar serverloggarna för en Azure Database for PostgreSQL-server.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.devlang: azurecli
ms.topic: sample
ms.custom: mvc
ms.date: 02/28/2018
ms.openlocfilehash: cd4346c738e1c1c52ee181f764f827b1b1060650
ms.sourcegitcommit: 71ee622bdba6e24db4d7ce92107b1ef1a4fa2600
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/17/2018
ms.locfileid: "53547209"
---
# <a name="enable-and-download-server-slow-query-logs-of-an-azure-database-for-postgresql-server-using-azure-cli"></a>Aktivera och hämta serverns långsamma frågeloggar för en Azure Databas for PostgreSQL-server med Azure CLI
Det här CLI-exempelskriptet aktiverar och hämtar de långsamma frågeloggarna för en Azure Database for PostgreSQL-server.

[!INCLUDE [cloud-shell-try-it](../../../includes/cloud-shell-try-it.md)]

Om du väljer att köra CLI lokalt måste du ha Azure CLI version 2.0 eller senare. Kontrollera versionen genom att köra `az --version`. [Installera Azure CLI]( /cli/azure/install-azure-cli) innehåller information om hur du installerar eller uppgraderar din version av Azure CLI.

## <a name="sample-script"></a>Exempelskript
I det här exempelskriptet ändrar du de markerade raderna om du vill uppdatera administratörens användarnamn och lösenord till dina egna. Ersätt &lt;log_file_name&gt; i `az monitor`-kommandona med ditt eget filnamn för serverloggen.
[!code-azurecli-interactive[main](../../../cli_scripts/postgresql/server-logs/server-logs.sh?highlight=15-16 "Manipulate with server logs.")]

## <a name="clean-up-deployment"></a>Rensa distribution
När skriptet har körts kan följande kommando användas för att ta bort resursgruppen och alla resurser som är kopplade till den. 
[!code-azurecli-interactive[main](../../../cli_scripts/postgresql/server-logs/delete-postgresql.sh  "Delete the resource group.")]

## <a name="script-explanation"></a>Förklaring av skript
Det här skriptet använder de kommandon som beskrivs i följande tabell:

| **Kommando** | **Anteckningar** |
|---|---|
| [az group create](/cli/azure/group#az_group_create) | Skapar en resursgrupp där alla resurser lagras. |
| [az postgres server create](/cli/azure/postgres/server#az_msql_server_create) | Skapar en PostgreSQL-server som är värd för databaserna. |
| [az postgres server configuration list](/cli/azure/postgres/server/configuration#az_postgres_server_configuration_list) | Visar en lista över konfigurationsvärden för en server. |
| [az postgres server configuration set](/cli/azure/postgres/server/configuration#az_postgres_server_configuration_set) | Uppdatera konfigurationen för en server. |
| [az postgres server-logs list](/cli/azure/postgres/server-logs#az_postgres_server_logs_list) | Visar en lista över loggfilerna för en server. |
| [az postgres server-logs download](/cli/azure/postgres/server-logs#az_postgres_server_logs_download) | Hämta loggfiler. |
| [az group delete](/cli/azure/group#az_group_delete) | Tar bort en resursgrupp, inklusive alla kapslade resurser. |

## <a name="next-steps"></a>Nästa steg
- Läs mer om Azure CLI: [Azure CLI-dokumentation](/cli/azure).
- Prova ytterligare skript: [Azure CLI-exempel för Azure Database for PostgreSQL](../sample-scripts-azure-cli.md)
- [Konfigurera och öppna serverloggar i Azure-portalen](../howto-configure-server-logs-in-portal.md)
