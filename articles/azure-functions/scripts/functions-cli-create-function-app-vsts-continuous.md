---
title: Skapa en funktion i Azure som distribueras från Visual Studio Team Services | Microsoft Docs
description: Skapa en funktionsapp och distribuera funktionskod från Visual Studio Team Services
services: functions
keywords: ''
author: syntaxc4
ms.author: glenga
ms.date: 07/03/2018
ms.topic: sample
ms.service: functions
ms.custom: mvc
ms.openlocfilehash: 1b54cfebd3ae36fc8025aeb4ea9c91d336bc5343
ms.sourcegitcommit: df50934d52b0b227d7d796e2522f1fd7c6393478
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/12/2018
ms.locfileid: "38988960"
---
# <a name="create-a-function-app-and-deploy-function-code-from-visual-studio-team-services"></a>Skapa en funktionsapp och distribuera funktionskod från Visual Studio Team Services

Det här avsnittet beskrivs hur du använder Azure Functions för att skapa en [serverlös](https://azure.microsoft.com/overview/serverless-computing/) funktionsapp med hjälp av [förbrukningsplanen](../functions-scale.md#consumption-plan). Funktionsappen, som blir en container för dina funktioner, distribueras kontinuerligt från en VSTS-lagringsplats (Visual Studio Team Services). 

[!INCLUDE [upgrade runtime](../../../includes/functions-cli-version-note.md)]

För att slutföra det här avsnittet måste du ha följande:

* En VSTS-lagringsplats som innehåller ditt funktionsappsprojekt och där du har administratörsbehörighet.
* En [personlig åtkomsttoken (PAT)](https://docs.microsoft.com/vsts/accounts/use-personal-access-tokens-to-authenticate) till VSTS-lagringsplatsen.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Om du föredrar att använda Azure CLI lokalt måste du installera och använda version 2.0 eller senare. Avgör vilken version av Azure CLI du har genom att köra `az --version`. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI 2.0]( /cli/azure/install-azure-cli). 

## <a name="sample-script"></a>Exempelskript

Det här exemplet skapar en funktionsapp och distribuerar funktionskod från Visual Studio Team Services.

[!code-azurecli-interactive[main](../../../cli_scripts/azure-functions/deploy-function-app-with-function-vsts/deploy-function-app-with-function-vsts.sh?highlight=3-4 "Azure Service")]

[!INCLUDE [cli-script-clean-up](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>Förklaring av skript

I det här skriptet används följande kommandon för att skapa en resursgrupp, ett lagringskonto, en funktionsapp och alla relaterade resurser. Varje kommando i tabellen länkar till kommandospecifik dokumentation.

| Kommando | Anteckningar |
|---|---|
| [az group create](https://docs.microsoft.com/cli/azure/group#az-group-create) | Skapar en resursgrupp där alla resurser lagras. |
| [az storage account create](https://docs.microsoft.com/cli/azure/storage/account#az-storage-account-create) | Skapar lagringskontot för funktionsappen. |
| [az functionapp create](https://docs.microsoft.com/cli/azure/functionapp#az-functionapp-create) | Skapar en funktionsapp i den serverfria [förbrukningsplanen](../functions-scale.md#consumption-plan). |
| [az functionapp deployment source config](https://docs.microsoft.com/cli/azure/functionapp/deployment/source#az-functionapp-deployment-source-config) | Kopplar en funktionsapp till en Git- eller Mercurial-lagringsplats. |

## <a name="next-steps"></a>Nästa steg

Mer information om Azure CLI finns i [Azure CLI-dokumentationen](https://docs.microsoft.com/cli/azure).

Ytterligare CLI-skriptexempel för Azure Functions finns i [Azure Functions-dokumentationen](../functions-cli-samples.md).
