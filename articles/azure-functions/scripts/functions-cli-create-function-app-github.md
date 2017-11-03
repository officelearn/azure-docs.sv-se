---
title: "Skapa en Funktionsapp och distribuera Funktionskoden från GitHub | Microsoft Docs"
description: "Azure CLI skriptexempel - skapa en Funktionsapp och distribuera Funktionskoden från GitHub"
services: functions
keywords: 
author: syntaxc4
ms.author: cfowler
ms.date: 04/27/2017
ms.topic: sample
ms.service: functions
ms.custom: mvc
ms.openlocfilehash: 39e26ba6c3ae0927fbf6c62af0bd3b48d16657ad
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="create-a-function-app-and-deploy-function-code-from-github"></a>Skapa en funktionsapp och distribuera Funktionskoden från GitHub

Det här exempelskriptet skapar en funktion app med hjälp av den [förbrukning plan](../functions-scale.md#consumption-plan) med dess relaterade resurser, och distribuerar Funktionskoden från en offentlig GitHub-databas (utan kontinuerlig distribution). Kontinuerlig leverans av Funktionskoden från GitHub läsa [skapa en funktionsapp och distribuera kontinuerligt från GitHub](functions-cli-create-function-app-github-continuous.md)

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Om du väljer att installera och använda CLI lokalt måste du köra Azure CLI version 2.0 eller senare. Kör `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI 2.0]( /cli/azure/install-azure-cli). 

## <a name="sample-script"></a>Exempelskript

Det här exemplet skapar en app i Azure-funktion och distribuerar Funktionskoden från GitHub.

[!code-azurecli-interactive[main](../../../cli_scripts/azure-functions/deploy-function-app-with-function-github/deploy-function-app-with-function-github.sh?highlight=3 "Create a function app with deployment from GitHub")]

[!INCLUDE [cli-script-clean-up](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>Skriptet förklaring

Varje kommando i tabellen länkar till kommandot viss dokumentation. Det här skriptet använder följande kommandon:

| Kommando | Anteckningar |
|---|---|
| [Skapa AZ grupp](https://docs.microsoft.com/cli/azure/group#az_group_create) | Skapar en resursgrupp som är lagrade i alla resurser. |
| [Skapa AZ storage-konto](https://docs.microsoft.com/cli/azure/appservice/plan#az_appservice_plan_create) | Skapar en App Service-plan. |
| [Skapa AZ functionapp](https://docs.microsoft.com/cli/azure/appservice/web#az_appservice_web_delete) | Skapar en funktionsapp i Azure. |
| [AZ apptjänst Webbkonfiguration källkontroll](https://docs.microsoft.com/cli/azure/appservice/web/source-control#az_appservice_web_source_control_config) | Associerar en funktionsapp med en Git eller ett. |

## <a name="next-steps"></a>Nästa steg

Mer information om Azure CLI finns [Azure CLI dokumentationen](https://docs.microsoft.com/cli/azure/overview).

Ytterligare Azure Functions CLI skriptexempel finns i den [Azure Functions dokumentationen](../functions-cli-samples.md).
