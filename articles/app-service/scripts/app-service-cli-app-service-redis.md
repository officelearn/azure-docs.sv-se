---
title: "Azure CLI-skript exempel – ansluta en webbapp till ett redis-cache | Microsoft Docs"
description: "Azure CLI-skript exempel – ansluta en webbapp till ett redis-cache"
services: appservice
documentationcenter: appservice
author: syntaxc4
manager: erikre
editor: 
tags: azure-service-management
ms.assetid: bc8345b2-8487-40c6-a91f-77414e8688e6
ms.service: app-service
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: na
ms.workload: web
ms.date: 08/30/2017
ms.author: cfowler
ms.custom: mvc
ms.openlocfilehash: c1277a04e3a63c09b2ed7f9304bfc482ab9189a8
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="connect-a-web-app-to-a-redis-cache"></a>Ansluta en webbapp till ett redis-cache

I det här scenariot du lära dig hur du skapar ett Azure redis-cache och en Azure-webbapp. Du kan länka redis-cache till det webbprogram som använder app-inställningar.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Exempelskript

[!code-azurecli-interactive[main](../../../cli_scripts/app-service/connect-to-redis/connect-to-redis.sh "Azure Redis Cache")]

[!INCLUDE [cli-script-clean-up](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>Skriptet förklaring

Skriptet använder följande kommandon för att skapa en resursgrupp, webbprogram, redis-cache och alla relaterade resurser. Varje kommando i tabellen länkar till kommandot viss dokumentation.

| Kommando | Anteckningar |
|---|---|
| [Skapa AZ grupp](https://docs.microsoft.com/cli/azure/group#az_group_create) | Skapar en resursgrupp som är lagrade i alla resurser. |
| [Skapa AZ programtjänstplan](https://docs.microsoft.com/cli/azure/appservice/plan#az_appservice_plan_create) | Skapar en App Service-plan. Detta påminner om en servergrupp för din Azure webbapp. |
| [Skapa AZ webapp](https://docs.microsoft.com/cli/azure/webapp#az_webapp_create) | Skapar ett Azure-webbapp. |
| [Skapa AZ redis](https://docs.microsoft.com/en-us/cli/azure/redis#az_redis_create) | Skapa en ny Redis-Cache-instans. Detta är där data ska lagras. |
| [AZ redis lista nycklar](https://docs.microsoft.com/en-us/cli/azure/redis#az_redis_list_keys) | Visar åtkomstnycklarna för redis-cacheinstansen. |
| [AZ webapp appsettings konfigurationsuppsättning](https://docs.microsoft.com/cli/azure/webapp/config/appsettings#az_webapp_config_appsettings_set) | Skapar eller uppdaterar en appinställning för ett Azure-webbapp. App-inställningar visas som miljövariabler för din app. |

## <a name="next-steps"></a>Nästa steg

Mer information om Azure CLI finns [Azure CLI dokumentationen](https://docs.microsoft.com/cli/azure/overview).

Ytterligare App Service CLI skriptexempel finns i den [dokumentation för Azure App Service](../app-service-cli-samples.md).
