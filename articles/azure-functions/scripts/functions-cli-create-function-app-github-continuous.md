---
title: Skapa en funktion i Azure som distribueras från GitHub | Microsoft Docs
description: Skapa en funktionsapp och distribuera funktionskoden från en GitHub-lagringsplats med hjälp av Azure Functions.
services: functions
ms.service: functions
keywords: ''
ms.devlang: azurecli
author: syntaxc4
ms.author: glenga
ms.date: 07/03/2018
ms.topic: sample
ms.custom: mvc
ms.openlocfilehash: 3090058236212d1baca17e2886a76f31ee1b2718
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/02/2018
ms.locfileid: "39424477"
---
# <a name="create-a-function-app-in-azure-that-is-deployed-from-github"></a>Skapa en funktionsapp i Azure som distribueras från GitHub

Det här exempelskriptet för Azure Functions skapar en funktionsapp med hjälp av [förbrukningsplanen](../functions-scale.md#consumption-plan), tillsammans med relaterade resurser. Skriptet konfigurerar också funktionskoden för kontinuerlig distribution från en GitHub-lagringsplats. 

[!INCLUDE [upgrade runtime](../../../includes/functions-cli-version-note.md)]

För det här exemplet behöver du följande:

* En GitHub-lagringsplats med funktionskod som du har administratörsbehörighet för.
* En [personlig åtkomsttoken (PAT)](https://help.github.com/articles/creating-an-access-token-for-command-line-use) för ditt GitHub-konto.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Om du föredrar att använda Azure CLI lokalt måste du installera och använda version 2.0 eller senare. Avgör vilken version av Azure CLI du har genom att köra `az --version`. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI 2.0]( /cli/azure/install-azure-cli). 

## <a name="sample-script"></a>Exempelskript

Det här exemplet skapar en funktionsapp och distribuerar funktionskod från GitHub.

[!code-azurecli-interactive[main](../../../cli_scripts/azure-functions/deploy-function-app-with-function-github-continuous/deploy-function-app-with-function-github-continuous.sh?highlight=3-4 "Azure Service")]

[!INCLUDE [cli-script-clean-up](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>Förklaring av skript

Varje kommando i tabellen länkar till kommandospecifik dokumentation. I det här skriptet används följande kommandon:

| Kommando | Anteckningar |
|---|---|
| [az group create](https://docs.microsoft.com/cli/azure/group#az-group-create) | Skapar en resursgrupp där alla resurser lagras. |
| [az storage account create](https://docs.microsoft.com/cli/azure/storage/account#az-storage-account-create) | Skapar lagringskontot för funktionsappen. |
| [az functionapp create](https://docs.microsoft.com/cli/azure/functionapp#az-functionapp-create) | Skapar en funktionsapp i den serverfria [förbrukningsplanen](../functions-scale.md#consumption-plan) och kopplar den till en Git- eller Mercurial-lagringsplats. |

## <a name="next-steps"></a>Nästa steg

Mer information om Azure CLI finns i [Azure CLI-dokumentationen](https://docs.microsoft.com/cli/azure).

Ytterligare CLI-skriptexempel för Azure Functions finns i [Azure Functions-dokumentationen](../functions-cli-samples.md).
