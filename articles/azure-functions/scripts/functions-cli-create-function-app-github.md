---
title: "Skapa en funktionsapp och distribuera funktionskod från GitHub | Microsoft Docs"
description: "Exempel på Azure CLI-skript – Skapa en funktionsapp och distribuera funktionskod från GitHub"
services: functions
keywords: 
author: syntaxc4
ms.author: cfowler
ms.date: 04/27/2017
ms.topic: sample
ms.service: functions
ms.custom: mvc
ms.openlocfilehash: 382fbe8919a031ca29a32e708b12070c584ed21f
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/09/2018
---
# <a name="create-a-function-app-and-deploy-function-code-from-github"></a>Skapa en funktionsapp och distribuera funktionskod från GitHub

Det här exempelskriptet skapar en funktionsapp med hjälp av [förbrukningsplanen](../functions-scale.md#consumption-plan) med dess relaterade resurser, och distribuerar funktionskoden från en offentlig GitHub-lagringsplats (utan kontinuerlig distribution). Information om kontinuerlig leverans av funktionskod från GitHub finns i [Skapa en funktionsapp och distribuera kontinuerligt från GitHub](functions-cli-create-function-app-github-continuous.md).

[!INCLUDE [upgrade runtime](../../../includes/functions-cli-version-note.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Om du väljer att installera och använda CLI lokalt måste du köra Azure CLI version 2.0 eller senare. Kör `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI 2.0]( /cli/azure/install-azure-cli). 

## <a name="sample-script"></a>Exempelskript

Det här exemplet skapar en funktionsapp och distribuerar funktionskod från GitHub.

[!code-azurecli-interactive[main](../../../cli_scripts/azure-functions/deploy-function-app-with-function-github/deploy-function-app-with-function-github.sh?highlight=3 "Create a function app with deployment from GitHub")]

[!INCLUDE [cli-script-clean-up](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>Förklaring av skript

Varje kommando i tabellen länkar till kommandospecifik dokumentation. Det här skriptet använder följande kommandon:

| Kommando | Anteckningar |
|---|---|
| [az group create](https://docs.microsoft.com/cli/azure/group#az_group_create) | Skapar en resursgrupp där alla resurser lagras. |
| [az storage account create](https://docs.microsoft.com/cli/azure/appservice/plan#az_appservice_plan_create) | Skapar en App Service-plan. |
| [az functionapp create](https://docs.microsoft.com/cli/azure/appservice/web#az_appservice_web_delete) | Skapar en Azure-funktionsapp. |
| [az appservice web source-control config](https://docs.microsoft.com/cli/azure/appservice/web/source-control#az_appservice_web_source_control_config) | Kopplar en funktionsapp till en Git- eller Mercurial-lagringsplats. |

## <a name="next-steps"></a>Nästa steg

Mer information om Azure CLI finns i [Azure CLI-dokumentationen](https://docs.microsoft.com/cli/azure/overview).

Ytterligare CLI-skriptexempel för Azure Functions finns i [Azure Functions-dokumentationen](../functions-cli-samples.md).
