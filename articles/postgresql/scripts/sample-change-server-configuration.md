---
title: "Azure CLI-skript: ändra serverkonfigurationer"
description: "Det här exempelskriptet CLI visar en lista över alla tillgängliga server-konfigurationsalternativ och uppdaterar värdet för ett av alternativen."
services: postgres
author: v-chenyh
ms.author: v-chenyh
manager: jhubbard
editor: jasonwhowell
ms.service: postgresql-database
ms.devlang: azure-cli
ms.topic: sample
ms.custom: mvc
ms.date: 01/12/2018
ms.openlocfilehash: 334322557c83d5576c9a11def6bb0dc0e3ad1fec
ms.sourcegitcommit: 384d2ec82214e8af0fc4891f9f840fb7cf89ef59
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/16/2018
---
# <a name="list-and-update-configurations-of-an-azure-database-for-postgresql-server-using-azure-cli"></a>Visa och uppdatera konfigurationer av en Azure-databas för PostgreSQL-server med Azure CLI
Det här exempelskriptet CLI listar alla tillgängliga konfigurationsparametrar samt de tillåtna värdena för Azure-databas för PostgreSQL-server och anger den *log_retention_days* till ett värde som är än standardvärdet.

[!INCLUDE [cloud-shell-try-it](../../../includes/cloud-shell-try-it.md)]

Om du väljer att installera och använda CLI lokalt måste du köra Azure CLI version 2.0 eller senare. Kör `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI 2.0]( /cli/azure/install-azure-cli). 

## <a name="sample-script"></a>Exempelskript
I det här exempelskriptet ändrar du markerade rader om du vill anpassa admin användarnamn och lösenord.
[!code-azurecli-interactive[main](../../../cli_scripts/postgresql/change-server-configurations/change-server-configurations.sh?highlight=15-16 "List and update configurations of Azure Database for PostgreSQL.")]

## <a name="clean-up-deployment"></a>Rensa distribution
Följande kommando kan användas för att ta bort resursgruppen och alla resurser som är associerade med den efter skriptexempel har körts.
[!code-azurecli-interactive[main](../../../cli_scripts/postgresql/change-server-configurations/delete-postgresql.sh  "Delete the resource group.")]

## <a name="script-explanation"></a>Skriptet förklaring
Det här skriptet använder följande kommandon. Varje kommando i tabellen länkar till kommandot viss dokumentation.

| **Kommandot** | **Anteckningar** |
|---|---|
| [Skapa AZ grupp](/cli/azure/group#az_group_create) | Skapar en resursgrupp som är lagrade i alla resurser. |
| [Skapa AZ postgres server](/cli/azure/postgres/server#az_postgres_server_create) | Skapar en PostgreSQL-server som är värd för databaserna. |
| [AZ postgres configuration serverlista](/cli/azure/postgres/server/configuration#az_postgres_server_configuration_list) | Lista över konfigurationer av en Azure-databas för PostgreSQL-servern. |
| [AZ postgres server configuration set](/cli/azure/postgres/server/configuration#az_postgres_server_configuration_set) | Uppdatera konfigurationen av en Azure-databas för PostgreSQL-servern. |
| [AZ postgres server configuration visa](/cli/azure/postgres/server/configuration#az_postgres_server_configuration_show) | Visa konfigurationen av en Azure-databas för PostgreSQL-servern. |
| [ta bort grupp AZ](/cli/azure/group#az_group_delete) | Tar bort en resursgrupp, inklusive alla kapslade resurser. |

## <a name="next-steps"></a>Nästa steg
- Läs mer om Azure CLI: [Azure CLI dokumentationen](/cli/azure/overview).
- Försök ytterligare skript: [Azure CLI-exempel för Azure-databas för PostgreSQL](../sample-scripts-azure-cli.md)
- Mer information om serverparametrar finns [hur att konfigurera parametrar för server i Azure-portalen](../howto-configure-server-parameters-using-portal.md).
