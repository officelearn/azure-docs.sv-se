---
title: Skapa en Function-app med ansluten lagring – Azure CLI
description: Exempel på Azure CLI-skript – Skapa en Azure-funktion som ansluter till Azure Storage
ms.topic: sample
ms.date: 04/20/2017
ms.custom: mvc
ms.openlocfilehash: 833b9223d473c8bfc62485e9e47ba662a4f0e154
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/29/2020
ms.locfileid: "75922678"
---
# <a name="create-a-function-app-with-a-named-storage-account-connection"></a>Skapa en Function-app med en namngiven lagrings konto anslutning 

Det här exempelskriptet för Azure Functions skapar en funktionsapp och ansluter funktionen till ett Azure Storage-konto. Appinställningen som skapas och som innehåller anslutningen kan användas med en [lagringsutlösare eller lagringsbindning](../functions-bindings-storage-blob.md). 

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Om du använder CLI lokalt kontrollerar du att du har Azure CLI version 2.0 eller senare. Kör `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa informationen i [Installera Azure CLI](/cli/azure/install-azure-cli). 

## <a name="sample-script"></a>Exempelskript

Det här exemplet skapar en Azure-funktionsapp och lägger till anslutningssträngen för lagring i en appinställning.

[!code-azurecli-interactive[main](../../../cli_scripts/azure-functions/create-function-app-connect-to-storage/create-function-app-connect-to-storage-account.sh "Integrate Function App into Azure Storage Account")]

[!INCLUDE [cli-script-clean-up](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>Förklaring av skript

Det här skriptet använder följande kommandon. Varje kommando i tabellen länkar till kommandospecifik dokumentation.

| Kommando | Obs! |
|---|---|
| [az group create](/cli/azure/group#az-group-create) | Skapa en resursgrupp med en plats. |
| [az storage account create](/cli/azure/storage/account#az-storage-account-create) | Skapa ett lagringskonto. |
| [az functionapp create](/cli/azure/functionapp#az-functionapp-create) | Skapar en Function-app i [förbruknings planen](../functions-scale.md#consumption-plan)utan server. |
| [AZ lagrings konto show-Connection-String](/cli/azure/storage/account#az-storage-account-show-connection-string) | Hämtar anslutningssträngen för kontot. |
| [AZ functionapp config appSettings set](/cli/azure/functionapp/config/appsettings#az-functionapp-config-appsettings-set) | Anger anslutnings strängen som en app-inställning i Function-appen. |

## <a name="next-steps"></a>Nästa steg

Mer information om Azure CLI finns i [Azure CLI-dokumentationen](/cli/azure).

Ytterligare CLI-skriptexempel för Azure Functions finns i [Azure Functions-dokumentationen](../functions-cli-samples.md).
