---
title: "Azure CLI skriptexempel - skapa en webbapp och distribuera kod från en lokal Git-lagringsplats | Microsoft Docs"
description: "Azure CLI skriptexempel - skapa en webbapp och distribuera kod från en lokal Git-lagringsplats"
services: app-service\web
documentationcenter: 
author: cephalin
manager: erikre
editor: 
tags: azure-service-management
ms.assetid: 048f98aa-f708-44cb-9b9e-953f67dc6da8
ms.service: app-service-web
ms.workload: web
ms.devlang: azurecli
ms.tgt_pltfrm: na
ms.topic: sample
ms.date: 06/19/2017
ms.author: cephalin
ms.custom: mvc
ms.openlocfilehash: 71cd1aeaa336d484d76e9a51511abcd38a878f00
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="create-a-web-app-and-deploy-code-from-a-local-git-repository"></a>Skapa en webbapp och distribuera kod från en lokal Git-lagringsplats

Det här exempelskriptet skapar en webbapp i App Service med dess relaterade resurser och distribuerar sedan web app-kod i en lokal Git-lagringsplats.


[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Om du väljer att installera och använda CLI lokalt måste du köra Azure CLI version 2.0 eller senare. Kör `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI 2.0]( /cli/azure/install-azure-cli). 

## <a name="sample-script"></a>Exempelskript

[!code-azurecli-interactive[main](../../../cli_scripts/app-service/deploy-local-git/deploy-local-git.sh?highlight=3-5 "Create a web app and deploy code from a local Git repository")]

[!INCLUDE [cli-script-clean-up](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>Skriptet förklaring

Det här skriptet använder följande kommandon. Varje kommando i tabellen länkar till kommandot viss dokumentation.

| Kommando | Anteckningar |
|---|---|
| [Skapa AZ grupp](https://docs.microsoft.com/cli/azure/group#az_group_create) | Skapar en resursgrupp som är lagrade i alla resurser. |
| [Skapa AZ programtjänstplan](https://docs.microsoft.com/cli/azure/appservice/plan#az_appservice_plan_create) | Skapar en App Service-plan. |
| [Skapa AZ webapp](https://docs.microsoft.com/cli/azure/webapp#az_webapp_create) | Skapar ett Azure-webbapp. |
| [AZ webapp distributionsanvändare har angetts](https://review.docs.microsoft.com/cli/azure/webapp/deployment/user#set) | Anger autentiseringsuppgifter för kontonivå distribution för Apptjänst. |
| [AZ webapp distribution källa config-lokal-git](https://review.docs.microsoft.com/cli/azure/webapp/deployment/source#config-local-git) | Skapar en konfiguration för kontroll av källan för en lokal Git-lagringsplats. |
| [AZ webapp Bläddra](https://docs.microsoft.com/cli/azure/webapp#az_webapp_browse) | Öppna en Azure-app i en webbläsare. |

## <a name="next-steps"></a>Nästa steg

Mer information om Azure CLI finns [Azure CLI dokumentationen](https://docs.microsoft.com/cli/azure/overview).

Ytterligare App Service CLI skriptexempel finns i den [dokumentation för Azure App Service](../app-service-cli-samples.md).
