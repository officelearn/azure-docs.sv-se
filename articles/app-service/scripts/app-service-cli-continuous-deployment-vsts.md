---
title: "Azure CLI-skript Sample - skapa en webbapp med kontinuerlig distribution från Visual Studio Team Services | Microsoft Docs"
description: "Azure CLI-skript Sample - skapa en webbapp med kontinuerlig distribution från Visual Studio Team Services"
services: app-service\web
documentationcenter: 
author: syntaxc4
manager: erikre
editor: 
tags: azure-service-management
ms.assetid: 389d3bd3-cd8e-4715-a3a1-031ec061d385
ms.service: app-service-web
ms.workload: web
ms.devlang: azurecli
ms.tgt_pltfrm: na
ms.topic: sample
ms.date: 06/19/2017
ms.author: cfowler
ms.custom: mvc
ms.openlocfilehash: 6c10b7654c02fae631e34572f12482928b39ceb6
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="create-a-web-app-with-continuous-deployment-from-visual-studio-team-services"></a>Skapa en webbapp med kontinuerlig distribution från Visual Studio Team Services

Det här exempelskriptet skapar en webbapp i App Service med dess relaterade resurser och ställer sedan in kontinuerlig distribution från Visual Studio Team Services-databasen. För det här exemplet behöver du:

* Ett Visual Studio Team Services-databas med programkod som du har administrativa behörigheter för.
* En [personlig åtkomst-Token (PATRIK)](https://www.visualstudio.com/docs/setup-admin/team-services/use-personal-access-tokens-to-authenticate) för Visual Studio Team Services-konto.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]


[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Om du väljer att installera och använda CLI lokalt måste du köra Azure CLI version 2.0 eller senare. Kör `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI 2.0]( /cli/azure/install-azure-cli). 

## <a name="sample-script"></a>Exempelskript

[!code-azurecli-interactive[main](../../../cli_scripts/app-service/deploy-vsts-continuous/deploy-vsts-continuous.sh?highlight=3-4 "Create a web app with continuous deployment from Visual Studio Team Services")]

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
