---
title: "Azure CLI: Hämta server-loggar i Azure-databas för MySQL"
description: "Det här exemplet Azure CLI visar hur du aktiverar och hämta de server-loggarna i en Azure-databas för MySQL-servern."
services: mysql
author: v-chenyh
ms.author: v-chenyh
manager: jhubbard
editor: jasonwhowell
ms.service: mysql-database
ms.devlang: azure-cli
ms.topic: sample
ms.custom: mvc
ms.date: 01/11/2018
ms.openlocfilehash: b0d34009d189ab136dcb6f28fdccc49b6da9e108
ms.sourcegitcommit: 562a537ed9b96c9116c504738414e5d8c0fd53b1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/12/2018
---
# <a name="enable-and-download-server-slow-query-logs-of-an-azure-database-for-mysql-server-using-azure-cli"></a>Aktivera och hämta långsam fråga serverloggen för en Azure-databas för MySQL-server med Azure CLI
Det här exempelskriptet CLI gör och hämtar långsam fråga-loggarna för en Azure-databas för MySQL-servern.

[!INCLUDE [cloud-shell-try-it](../../../includes/cloud-shell-try-it.md)]

Om du väljer att installera och använda CLI lokalt kräver i det här exemplet att du använder Azure CLI version 2.0 eller senare. Kör `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI 2.0]( /cli/azure/install-azure-cli). 

## <a name="sample-script"></a>Exempelskript
I det här exempelskriptet ändrar du markerade rader om du vill anpassa admin användarnamn och lösenord. Ersätt < loggfilsnamn > i az övervakaren kommandon med din egen server loggfilens namn.
[!code-azurecli-interactive[main](../../../cli_scripts/mysql/server-logs/server-logs.sh?highlight=15-16 "Manipulate with server logs.")]

## <a name="clean-up-deployment"></a>Rensa distribution
Följande kommando kan användas för att ta bort resursgruppen och alla resurser som är associerade med den efter skriptexempel har körts.
[!code-azurecli-interactive[main](../../../cli_scripts/mysql/server-logs/delete-mysql.sh  "Delete the resource group.")]

## <a name="script-explanation"></a>Skriptet förklaring
Det här skriptet använder följande kommandon. Varje kommando i tabellen länkar till kommandot viss dokumentation.

| **Kommandot** | **Anteckningar** |
|---|---|
| [Skapa AZ grupp](/cli/azure/group#az_group_create) | Skapar en resursgrupp som är lagrade i alla resurser. |
| [Skapa AZ mysql-server](/cli/azure/mysql/server#az_msql_server_create) | Skapar en MySQL-server som är värd för databaserna. |
| [AZ mysql configuration serverlista](/cli/azure/mysql/server/configuration#az_mysql_server_configuration_list) | Lista över konfigurationsvärden för en server. |
| [AZ mysql server configuration set](/cli/azure/mysql/server/configuration#az_mysql_server_configuration_set) | Uppdatera konfigurationen för en server. |
| [AZ mysql serverloggen lista](/cli/azure/mysql/server-logs#az_mysql_server_logs_list) | Listar de loggfiler för en server. |
| [Hämta AZ mysql server-loggar](/cli/azure/mysql/server-logs#az_mysql_server_logs_download) | Hämta loggfiler. |
| [ta bort grupp AZ](/cli/azure/group#az_group_delete) | Tar bort en resursgrupp, inklusive alla kapslade resurser. |

## <a name="next-steps"></a>Nästa steg
- Läs mer om Azure CLI: [Azure CLI dokumentationen](/cli/azure/overview).
- Försök ytterligare skript: [Azure CLI-exempel för Azure-databas för MySQL](../sample-scripts-azure-cli.md)
