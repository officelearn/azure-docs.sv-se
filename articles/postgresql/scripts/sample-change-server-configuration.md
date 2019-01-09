---
title: Azure CLI-skript – Ändra serverkonfigurationer
description: Det här CLI-exempelskriptet visar en lista över alla tillgängliga serverkonfigurationsalternativ och uppdaterar värdet för ett av alternativen.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.devlang: azurecli
ms.topic: sample
ms.custom: mvc
ms.date: 02/28/2018
ms.openlocfilehash: c570982fed746b01f5a8c15edb94bdeac679b248
ms.sourcegitcommit: 71ee622bdba6e24db4d7ce92107b1ef1a4fa2600
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/17/2018
ms.locfileid: "53545623"
---
# <a name="list-and-update-configurations-of-an-azure-database-for-postgresql-server-using-azure-cli"></a>Visa och uppdatera konfigurationer av en Azure Database for PostgreSQL-server med Azure CLI
Det här CLI-exempelskriptet visar alla tillgängliga konfigurationsparametrar samt de tillåtna värdena för Azure Database for PostgreSQL-servern och anger *log_retention_days* till ett annat värde än standardvärdet.

[!INCLUDE [cloud-shell-try-it](../../../includes/cloud-shell-try-it.md)]

Om du väljer att köra CLI lokalt måste du ha Azure CLI version 2.0 eller senare. Kontrollera versionen genom att köra `az --version`. [Installera Azure CLI]( /cli/azure/install-azure-cli) innehåller information om hur du installerar eller uppgraderar din version av Azure CLI. 

## <a name="sample-script"></a>Exempelskript
I det här exempelskriptet ändrar du de markerade raderna om du vill uppdatera administratörens användarnamn och lösenord till dina egna.
[!code-azurecli-interactive[main](../../../cli_scripts/postgresql/change-server-configurations/change-server-configurations.sh?highlight=15-16 "List and update configurations of Azure Database for PostgreSQL.")]

## <a name="clean-up-deployment"></a>Rensa distribution
När skriptet har körts kan följande kommando användas för att ta bort resursgruppen och alla resurser som är kopplade till den. 
[!code-azurecli-interactive[main](../../../cli_scripts/postgresql/change-server-configurations/delete-postgresql.sh  "Delete the resource group.")]

## <a name="script-explanation"></a>Förklaring av skript
Det här skriptet använder de kommandon som beskrivs i följande tabell:

| **Kommando** | **Anteckningar** |
|---|---|
| [az group create](/cli/azure/group#az_group_create) | Skapar en resursgrupp där alla resurser lagras. |
| [az postgres server create](/cli/azure/postgres/server#az_postgres_server_create) | Skapar en PostgreSQL-server som är värd för databaserna. |
| [az postgres server configuration list](/cli/azure/postgres/server/configuration#az_postgres_server_configuration_list) | Visa en lista med konfigurationerna av en Azure Database for PostgreSQL-server. |
| [az postgres server configuration set](/cli/azure/postgres/server/configuration#az_postgres_server_configuration_set) | Uppdatera konfigurationen av en Azure Database for PostgreSQL-server. |
| [az postgres server configuration show](/cli/azure/postgres/server/configuration#az_postgres_server_configuration_show) | Visa konfigurationen av en Azure Database for PostgreSQL-server. |
| [az group delete](/cli/azure/group#az_group_delete) | Tar bort en resursgrupp, inklusive alla kapslade resurser. |

## <a name="next-steps"></a>Nästa steg
- Läs mer om Azure CLI: [Azure CLI-dokumentation](/cli/azure).
- Prova ytterligare skript: [Azure CLI-exempel för Azure Database for PostgreSQL](../sample-scripts-azure-cli.md)
- Läs mer om serverparametrar i informationen om [hur du konfigurerar serverparametrar på Azure-portalen](../howto-configure-server-parameters-using-portal.md).
