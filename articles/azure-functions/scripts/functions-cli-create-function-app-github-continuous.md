---
title: "Skapa en Funktionsapp och distribuera Funktionskoden från GitHub | Microsoft Docs"
description: "Skapa en Funktionsapp och distribuera Funktionskoden från GitHub"
services: functions
ms.service: functions
keywords: 
ms.devlang: azurecli
author: syntaxc4
ms.author: cfowler
ms.date: 04/27/2017
ms.topic: sample
ms.custom: mvc
ms.openlocfilehash: 8b148651d041bbc27e2deccec57b1759ce0095d0
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="create-an-app-service"></a>Skapa en App Service

Det här exempelskriptet skapar en funktion app med hjälp av den [förbrukning plan](../functions-scale.md#consumption-plan) med dess relaterade resurser och kontinuerligt distribuerar Funktionskoden från en GitHub-databas. I det här exemplet behöver du:

* En GitHub-databas med funktioner kod som du har administrativa behörigheter för.
* En [personlig åtkomst-Token (PATRIK)](https://help.github.com/articles/creating-an-access-token-for-command-line-use) för ditt GitHub-konto.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Om du väljer att installera och använda CLI lokalt måste du köra Azure CLI version 2.0 eller senare. Kör `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI 2.0]( /cli/azure/install-azure-cli). 

## <a name="sample-script"></a>Exempelskript

Det här exemplet skapar en app i Azure-funktion och distribuerar Funktionskoden från GitHub.

[!code-azurecli-interactive[main](../../../cli_scripts/azure-functions/deploy-function-app-with-function-github-continuous/deploy-function-app-with-function-github-continuous.sh?highlight=3-4 "Azure Service")]

[!INCLUDE [cli-script-clean-up](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>Skriptet förklaring

Varje kommando i tabellen länkar till kommandot viss dokumentation. Det här skriptet använder följande:

| Kommando | Anteckningar |
|---|---|
| [Skapa AZ grupp](https://docs.microsoft.com/cli/azure/group#az_group_create) | Skapar en resursgrupp som är lagrade i alla resurser. |
| [Skapa AZ storage-konto](https://docs.microsoft.com/cli/azure/appservice/plan#az_appservice_plan_create) | Skapar en App Service-plan. |
| [Skapa AZ functionapp](https://docs.microsoft.com/cli/azure/appservice/web#az_appservice_web_delete) |
| [AZ apptjänst Webbkonfiguration källkontroll](https://docs.microsoft.com/cli/azure/appservice/web/source-control#az_appservice_web_source_control_config) | Associerar en funktionsapp med en Git eller ett. |

## <a name="next-steps"></a>Nästa steg

Mer information om Azure CLI finns [Azure CLI dokumentationen](https://docs.microsoft.com/cli/azure/overview).

Ytterligare Azure Functions CLI skriptexempel finns i den [Azure Functions dokumentationen](../functions-cli-samples.md).
