---
title: "Azure CLI-skript exempel – ansluta en webbapp till Cosmos DB | Microsoft Docs"
description: "Azure CLI-skript exempel – ansluta en webbapp till Cosmos DB"
services: appservice
documentationcenter: appservice
author: syntaxc4
manager: erikre
editor: 
tags: azure-service-management
ms.assetid: bbbdbc42-efb5-4b4f-8ba6-c03c9d16a7ea
ms.service: app-service
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: na
ms.workload: web
ms.date: 06/19/2017
ms.author: cfowler
ms.custom: mvc
ms.openlocfilehash: a59e0512308a85248a10a24c5951984040bedbcc
ms.sourcegitcommit: b07d06ea51a20e32fdc61980667e801cb5db7333
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/08/2017
---
# <a name="connect-a-web-app-to-cosmos-db"></a>Ansluta en webbapp till Cosmos DB

I det här scenariot du lära dig hur du skapar ett Azure DB som Cosmos-konto och ett Azure-webbapp. Du kan länka Cosmos-DB till det webbprogram som använder app-inställningar.


[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]


[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Om du väljer att installera och använda CLI lokalt måste du köra Azure CLI version 2.0 eller senare. Kör `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI 2.0]( /cli/azure/install-azure-cli). 

## <a name="sample-script"></a>Exempelskript

[!code-azurecli-interactive[main](../../../cli_scripts/app-service/connect-to-documentdb/connect-to-documentdb.sh "Azure Cosmos DB")]

[!INCLUDE [cli-script-clean-up](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>Skriptet förklaring

Det här skriptet använder följande kommandon för att skapa en resursgrupp, webbapp Cosmos DB och alla relaterade resurser. Varje kommando i tabellen länkar till kommandot viss dokumentation.

| Kommando | Anteckningar |
|---|---|
| [Skapa AZ grupp](https://docs.microsoft.com/cli/azure/group#az_group_create) | Skapar en resursgrupp som är lagrade i alla resurser. |
| [Skapa AZ programtjänstplan](https://docs.microsoft.com/cli/azure/appservice/plan#az_appservice_plan_create) | Skapar en App Service-plan. Detta påminner om en servergrupp för din Azure webbapp. |
| [Skapa AZ webapp](https://docs.microsoft.com/cli/azure/webapp#az_webapp_create) | Skapar ett Azure-webbapp. |
| [Skapa AZ cosmosdb](https://docs.microsoft.com/cli/azure/cosmosdb#az_cosmosdb_create) | Skapar en Cosmos-DB-konto. Detta är där data ska lagras. |
| [AZ cosmosdb lista nycklar](https://docs.microsoft.com/cli/azure/cosmosdb#az_cosmosdb_list_keys) | Visar en lista över åtkomstnycklarna för det angivna Cosmos-DB-kontot. |
| [AZ webapp appsettings konfigurationsuppsättning](https://docs.microsoft.com/cli/azure/webapp/config/appsettings#az_webapp_config_appsettings_set) | Skapar eller uppdaterar en appinställning för ett Azure-webbapp. App-inställningar visas som miljövariabler för din app. |

## <a name="next-steps"></a>Nästa steg

Mer information om Azure CLI finns [Azure CLI dokumentationen](https://docs.microsoft.com/cli/azure/overview).

Ytterligare App Service CLI skriptexempel finns i den [dokumentation för Azure App Service](../app-service-cli-samples.md).
