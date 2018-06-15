---
title: Azure CLI-skript – Ändra serverkonfigurationer
description: Det här CLI-skriptexemplet visar en lista med alla tillgängliga serverkonfigurationer och uppdaterar värdet för innodb_lock_wait_timeout.
services: mysql
author: ajlam
ms.author: andrela
manager: kfile
editor: jasonwhowell
ms.service: mysql
ms.devlang: azure-cli
ms.topic: sample
ms.custom: mvc
ms.date: 02/28/2018
ms.openlocfilehash: 8a119d611d4b2817ad85ebe30b88d11cce6fca9f
ms.sourcegitcommit: 1b8665f1fff36a13af0cbc4c399c16f62e9884f3
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/11/2018
ms.locfileid: "35266337"
---
# <a name="list-and-update-configurations-of-an-azure-database-for-mysql-server-using-azure-cli"></a>Visa och uppdatera konfigurationer av en Azure Database for MySQL-server med Azure CLI
Det här CLI-exempelskriptet visar alla tillgängliga konfigurationsparametrar samt tillåtna värden för Azure Database for MySQL-servern och anger *innodb_lock_wait_timeout* till ett annat värde än standardvärdet.

[!INCLUDE [cloud-shell-try-it](../../../includes/cloud-shell-try-it.md)]

Om du väljer att köra CLI lokalt måste du ha Azure CLI version 2.0 eller senare. Kontrollera versionen genom att köra `az --version`. [Installera Azure CLI 2.0]( /cli/azure/install-azure-cli) innehåller information om hur du installerar eller uppgraderar din version av Azure CLI. 

## <a name="sample-script"></a>Exempelskript
I det här exempelskriptet ändrar du de markerade raderna om du vill uppdatera administratörens användarnamn och lösenord till dina egna.
[!code-azurecli-interactive[main](../../../cli_scripts/mysql/change-server-configurations/change-server-configurations.sh?highlight=18-19 "List and update configurations of Azure Database for MySQL.")]

## <a name="clean-up-deployment"></a>Rensa distribution
När skriptet har körts kan följande kommando användas för att ta bort resursgruppen och alla resurser som är kopplade till den. 
[!code-azurecli-interactive[main](../../../cli_scripts/mysql/change-server-configurations/delete-mysql.sh  "Delete the resource group.")]

## <a name="script-explanation"></a>Förklaring av skript
Det här skriptet använder de kommandon som beskrivs i följande tabell:

| **Kommando** | **Anteckningar** |
|---|---|
| [az group create](/cli/azure/group#az_group_create) | Skapar en resursgrupp där alla resurser lagras. |
| [az mysql server create](/cli/azure/mysql/server#az_msql_server_create) | Skapar en MySQL-server som är värd för databaserna. |
| [az mysql server configuration list](/cli/azure/mysql/server/configuration#az_msql_server_configuration_list) | Skapar en lista med konfigurationerna för en Azure Database for MySQL-server. |
| [az mysql server configuration set](/cli/azure/mysql/server/configuration#az_msql_server_configuration_set) | Uppdaterar konfigurationen för en Azure Database for MySQL-server. |
| [az mysql server configuration show](/cli/azure/mysql/server/configuration#az_msql_server_configuration_show) | Visar konfigurationen för en Azure Database for MySQL-server. |
| [az group delete](/cli/azure/group#az_group_delete) | Tar bort en resursgrupp, inklusive alla kapslade resurser. |

## <a name="next-steps"></a>Nästa steg
- Läs mer om Azure CLI: [Azure CLI-dokumentation](/cli/azure).
- Prova fler skript: [Azure CLI-exempel för Azure Database for MySQL](../sample-scripts-azure-cli.md)
- Mer information om serverparametrar finns i [Konfigurera serverparametrar i Azure Database for MySQL](../howto-server-parameters.md).
