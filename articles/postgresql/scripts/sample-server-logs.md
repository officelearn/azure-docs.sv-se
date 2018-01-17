---
title: "Azure CLI-skript: hämta loggas i Azure-databas för PostgreSQL"
description: "Det här exemplet Azure CLI visar hur du aktiverar och hämta de server-loggarna i en Azure-databas för PostgreSQL-server."
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
ms.openlocfilehash: 8bd6e193ca52401b16e141162a76d39bdef840f1
ms.sourcegitcommit: 384d2ec82214e8af0fc4891f9f840fb7cf89ef59
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/16/2018
---
# <a name="enable-and-download-server-slow-query-logs-of-an-azure-database-for-postgresql-server-using-azure-cli"></a>Aktivera och hämta långsam fråga serverloggen för en Azure-databas för PostgreSQL-server med Azure CLI
Det här exempelskriptet CLI gör och hämtar långsam fråga-loggarna för en Azure-databas för PostgreSQL-servern.

[!INCLUDE [cloud-shell-try-it](../../../includes/cloud-shell-try-it.md)]

Om du väljer att installera och använda CLI lokalt kräver i det här exemplet att du använder Azure CLI version 2.0 eller senare. Kör `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI 2.0]( /cli/azure/install-azure-cli). 

## <a name="sample-script"></a>Exempelskript
I det här exempelskriptet ändrar du markerade rader om du vill anpassa admin användarnamn och lösenord. Ersätt < loggfilsnamn > i az övervakaren kommandon med din egen server loggfilens namn.
[!code-azurecli-interactive[main](../../../cli_scripts/postgresql/server-logs/server-logs.sh?highlight=15-16 "Manipulate with server logs.")]

## <a name="clean-up-deployment"></a>Rensa distribution
Följande kommando kan användas för att ta bort resursgruppen och alla resurser som är associerade med den efter skriptexempel har körts.
[!code-azurecli-interactive[main](../../../cli_scripts/postgresql/server-logs/delete-postgresql.sh  "Delete the resource group.")]

## <a name="script-explanation"></a>Skriptet förklaring
Det här skriptet använder följande kommandon. Varje kommando i tabellen länkar till kommandot viss dokumentation.

| **Kommandot** | **Anteckningar** |
|---|---|
| [Skapa AZ grupp](/cli/azure/group#az_group_create) | Skapar en resursgrupp som är lagrade i alla resurser. |
| [Skapa AZ postgres server](/cli/azure/postgres/server#az_msql_server_create) | Skapar en PostgreSQL-server som är värd för databaserna. |
| [AZ postgres configuration serverlista](/cli/azure/postgres/server/configuration#az_postgres_server_configuration_list) | Lista över konfigurationsvärden för en server. |
| [AZ postgres server configuration set](/cli/azure/postgres/server/configuration#az_postgres_server_configuration_set) | Uppdatera konfigurationen för en server. |
| [AZ postgres serverloggen lista](/cli/azure/postgres/server-logs#az_postgres_server_logs_list) | Listar de loggfiler för en server. |
| [Hämta AZ postgres server-loggar](/cli/azure/postgres/server-logs#az_postgres_server_logs_download) | Hämta loggfiler. |
| [ta bort grupp AZ](/cli/azure/group#az_group_delete) | Tar bort en resursgrupp, inklusive alla kapslade resurser. |

## <a name="next-steps"></a>Nästa steg
- Läs mer om Azure CLI: [Azure CLI dokumentationen](/cli/azure/overview).
- Försök ytterligare skript: [Azure CLI-exempel för Azure-databas för PostgreSQL](../sample-scripts-azure-cli.md)
- [Konfigurera och åtkomst loggas i Azure-portalen](../howto-configure-server-logs-in-portal.md)
