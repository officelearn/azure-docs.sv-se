---
title: "Azure CLI-skript Sample - skapa en webbapp med distribution från GitHub | Microsoft Docs"
description: "Azure CLI-skript Sample - skapa en webbapp med distribution från GitHub"
services: app-service\web
documentationcenter: 
author: cephalin
manager: erikre
editor: 
tags: azure-service-management
ms.assetid: 0205c991-0989-4ca3-bb41-237dcc964460
ms.service: app-service-web
ms.workload: web
ms.devlang: azurecli
ms.tgt_pltfrm: sample
ms.topic: sample
ms.date: 06/19/2017
ms.author: cephalin
ms.custom: mvc
ms.openlocfilehash: 1b97faa1216b5dc0e80a743d69bdf94fa6a9c58d
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="create-a-web-app-with-deployment-from-github"></a>Skapa en webbapp med distribution från GitHub

Det här exempelskriptet skapar en webbapp i App Service med dess relaterade resurser och distribuerar sedan web app-kod från en offentlig GitHub-databas (utan kontinuerlig distribution). GitHub-distribution med kontinuerlig distribution finns [skapa ett webbprogram med kontinuerlig distribution från GitHub](app-service-cli-continuous-deployment-github.md).

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Om du väljer att installera och använda CLI lokalt måste du köra Azure CLI version 2.0 eller senare. Kör `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI 2.0]( /cli/azure/install-azure-cli). 

## <a name="sample-script"></a>Exempelskript

[!code-azurecli-interactive[main](../../../cli_scripts/app-service/deploy-github/deploy-github.sh?highlight=3 "Create a web app with deployment from GitHub")]

[!INCLUDE [cli-script-clean-up](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>Skriptet förklaring 

Det här skriptet använder följande kommandon. Varje kommando i tabellen länkar till kommandot viss dokumentation.

| Kommando | Anteckningar |
|---|---|
| [Skapa AZ grupp](https://docs.microsoft.com/cli/azure/group#az_group_create) | Skapar en resursgrupp som är lagrade i alla resurser. |
| [Skapa AZ programtjänstplan](https://docs.microsoft.com/cli/azure/appservice/plan#az_appservice_plan_create) | Skapar en App Service-plan. |
| [Skapa AZ webapp](https://docs.microsoft.com/cli/azure/webapp#az_webapp_create) | Skapar ett Azure-webbapp. |
| [AZ webapp distributionskonfiguration källa](https://docs.microsoft.com/cli/azure/webapp/deployment/source#az_webapp_deployment_source_config) | Associerar en Azure-app med en Git eller ett lagringsplats. |
| [AZ webapp Bläddra](https://docs.microsoft.com/cli/azure/webapp#az_webapp_browse) | Öppna en Azure-app i en webbläsare. |

## <a name="next-steps"></a>Nästa steg

Mer information om Azure CLI finns [Azure CLI dokumentationen](https://docs.microsoft.com/cli/azure/overview).

Ytterligare App Service CLI skriptexempel finns i den [dokumentation för Azure App Service](../app-service-cli-samples.md).
