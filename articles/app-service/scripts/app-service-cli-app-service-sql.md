---
title: "Azure CLI-skript exempel – ansluta en webbapp till en SQL-databas | Microsoft Docs"
description: "Azure CLI-skript exempel – ansluta en webbapp till en SQL-databas"
services: appservice
documentationcenter: appservice
author: syntaxc4
manager: erikre
editor: 
tags: azure-service-management
ms.assetid: 7c2efdd0-f553-4038-a77a-e953021b3f77
ms.service: app-service
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: na
ms.workload: web
ms.date: 12/11/2017
ms.author: cfowler
ms.custom: mvc
ms.openlocfilehash: e85b405e61460c7cde8dd8a6001add736c53c713
ms.sourcegitcommit: aaba209b9cea87cb983e6f498e7a820616a77471
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/12/2017
---
# <a name="connect-a-web-app-to-a-sql-database"></a>Ansluta en webbapp till en SQL-databas

Det här exempelskriptet skapar en Azure SQL database och en Azure-webbapp. Därefter länkar SQL-databasen till det webbprogram som använder app-inställningar.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Om du väljer att installera och använda CLI lokalt, måste Azure CLI version 2.0 eller senare. Kör `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI 2.0]( /cli/azure/install-azure-cli).

## <a name="sample-script"></a>Exempelskript

[!code-azurecli-interactive[main](../../../cli_scripts/app-service/connect-to-sql/connect-to-sql.sh?highlight=9-10 "SQL Database")]

[!INCLUDE [cli-script-clean-up](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>Skriptet förklaring

Det här skriptet använder följande kommandon för att skapa en resursgrupp, webbprogram, SQL-databasen och alla relaterade resurser. Varje kommando i tabellen länkar till kommandot viss dokumentation.

| Kommando | Anteckningar |
|---|---|
| [`az group create`](/cli/azure/group?view=azure-cli-latest#az_group_create) | Skapar en resursgrupp som är lagrade i alla resurser. |
| [`az appservice plan create`](/cli/azure/appservice/plan?view=azure-cli-latest#az_appservice_plan_create) | Skapar en App Service-plan. |
| [`az webapp create`](/cli/azure/webapp?view=azure-cli-latest#az_webapp_create) | Skapar ett Azure-webbapp. |
| [`az sql server create`](/cli/azure/sql/server?view=azure-cli-latest#az_sql_server_create) | Skapar en SQL-databasservern.  |
| [`az sql db create`](/cli/azure/sql/db?view=azure-cli-latest#az_sql_db_create) | Skapar en ny databas med SQL-databasservern. |
| [`az sql db show-connection-string`](/cli/azure/sql/db?view=azure-cli-latest#az_sql_db_show_connection_string) | Genererar en anslutningssträng för en databas. |
| [`az webapp config appsettings set`](/cli/azure/webapp/config/appsettings?view=azure-cli-latest#az_webapp_config_appsettings_set) | Skapar eller uppdaterar en appinställning för ett Azure-webbapp. App-inställningar visas som miljövariabler för din app. |

## <a name="next-steps"></a>Nästa steg

Mer information om Azure CLI finns [Azure CLI dokumentationen](https://docs.microsoft.com/cli/azure/overview).

Ytterligare App Service CLI skriptexempel finns i den [dokumentation för Azure App Service](../app-service-cli-samples.md).
