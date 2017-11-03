---
title: "Skriptexempel Azure CLI - skala en webbapp över hela världen med en hög availabilty-arkitektur | Microsoft Docs"
description: "Skriptexempel Azure CLI - skala en webbapp över hela världen med en hög availabilty-arkitektur"
services: appservice
documentationcenter: appservice
author: syntaxc4
manager: erikre
editor: 
tags: azure-service-management
ms.assetid: e4033a50-0e05-4505-8ce8-c876204b2acc
ms.service: app-service
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: na
ms.workload: web
ms.date: 06/19/2017
ms.author: cfowler
ms.custom: mvc
ms.openlocfilehash: 52679af9cd8f6b455f68b9307ed52202ff74349d
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="scale-a-web-app-worldwide-with-a-high-availability-architecture"></a>Skala ett webbprogram över hela världen med en arkitektur med hög tillgänglighet

I det här scenariot skapar du en resursgrupp, två apptjänstplaner, två webbappar, en traffic manager-profil och två traffic manager-slutpunkter. När den här övningen är klar har du en hög tillgänglighet arkitektur som gör att tillhandahåller globala tillgängligheten hos ditt webbprogram baserat på den lägsta Nätverksfördröjningen.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Om du väljer att installera och använda CLI lokalt måste du köra Azure CLI version 2.0 eller senare. Kör `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI 2.0]( /cli/azure/install-azure-cli). 


## <a name="sample-script"></a>Exempelskript

[!code-azurecli-interactive[main](../../../cli_scripts/app-service/scale-geographic/scale-geographic.sh "Geographic Scale")]

[!INCLUDE [cli-script-clean-up](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>Skriptet förklaring

Det här skriptet använder följande kommandon för att skapa en resursgrupp, webbprogram, trafikhanterarprofilen och alla relaterade resurser. Varje kommando i tabellen länkar till kommandot viss dokumentation.

| Kommando | Anteckningar |
|---|---|
| [Skapa AZ grupp](https://docs.microsoft.com/cli/azure/group#az_group_create) | Skapar en resursgrupp som är lagrade i alla resurser. |
| [Skapa AZ programtjänstplan](https://docs.microsoft.com/cli/azure/appservice/plan#az_appservice_plan_create) | Skapar en App Service-plan. Detta påminner om en servergrupp för din Azure webbapp. |
| [Skapa AZ webapp](https://docs.microsoft.com/cli/azure/webapp#az_webapp_create) | Skapar ett Azure-webbapp. |
| [Skapa AZ network traffic manager-profilen](https://docs.microsoft.com/cli/azure/network/traffic-manager/profile#az_network_traffic_manager_profile_create) | Skapar en Azure Traffic Manager-profil. |
| [Skapa AZ network traffic manager-slutpunkt](https://docs.microsoft.com/cli/azure/network/traffic-manager/endpoint#az_network_traffic_manager_endpoint_create) | Lägger till en slutpunkt för en Azure Traffic Manager-profilen. |

## <a name="next-steps"></a>Nästa steg

Mer information om Azure CLI finns [Azure CLI dokumentationen](https://docs.microsoft.com/cli/azure/overview).

Ytterligare App Service CLI skriptexempel finns i den [dokumentation för Azure App Service](../app-service-cli-samples.md).
