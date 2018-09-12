---
title: Skapa en Azure-funktion som ansluter till Azure Storage | Microsoft Docs
description: Exempel på Azure CLI-skript – Skapa en Azure-funktion som ansluter till Azure Storage
services: functions
documentationcenter: functions
author: ggailey777
manager: jeconnoc
ms.assetid: ''
ms.service: azure-functions
ms.devlang: azurecli
ms.topic: sample
ms.date: 04/20/2017
ms.author: glenga
ms.custom: mvc
ms.openlocfilehash: aa25c0e636c36422cb6b576d0ca8ebc899b2a2dc
ms.sourcegitcommit: 2d961702f23e63ee63eddf52086e0c8573aec8dd
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/07/2018
ms.locfileid: "44163081"
---
# <a name="create-a-function-app-that-connects-to-an-azure-storage-account"></a>Skapa en funktionsapp som ansluter till ett Azure Storage-konto

Det här exempelskriptet för Azure Functions skapar en funktionsapp och ansluter funktionen till ett Azure Storage-konto. Appinställningen som skapas och som innehåller anslutningen kan användas med en [lagringsutlösare eller lagringsbindning](..\functions-bindings-storage-blob.md). 

[!INCLUDE [upgrade runtime](../../../includes/functions-cli-version-note.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Om du använder CLI lokalt kontrollerar du att du har Azure CLI version 2.0 eller senare. Kör `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI 2.0](/cli/azure/install-azure-cli). 

## <a name="sample-script"></a>Exempelskript

Det här exemplet skapar en Azure-funktionsapp och lägger till anslutningssträngen för lagring i en appinställning.

[!code-azurecli-interactive[main](../../../cli_scripts/azure-functions/create-function-app-connect-to-storage/create-function-app-connect-to-storage-account.sh "Integrate Function App into Azure Storage Account")]


## <a name="clean-up-deployment"></a>Rensa distribution

När skriptet har körts kan följande kommando köras för att ta bort resursgruppen och alla relaterade resurser:

[!INCLUDE [cli-script-clean-up](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>Förklaring av skript

Det här skriptet använder följande kommandon. Varje kommando i tabellen länkar till kommandospecifik dokumentation.

| Kommando | Anteckningar |
|---|---|
| [az group create](https://docs.microsoft.com/cli/azure/group#az-group-create) | Skapa en resursgrupp med en plats. |
| [az storage account create](https://docs.microsoft.com/cli/azure/storage/account#az-storage-account-create) | Skapa ett lagringskonto. |
| [az functionapp create](https://docs.microsoft.com/cli/azure/functionapp#az-functionapp-create) | Skapar en funktionsapp i den serverfria [förbrukningsplanen](../functions-scale.md#consumption-plan). |

## <a name="next-steps"></a>Nästa steg

Mer information om Azure CLI finns i [Azure CLI-dokumentationen](https://docs.microsoft.com/cli/azure).

Ytterligare CLI-skriptexempel för Azure Functions finns i [Azure Functions-dokumentationen](../functions-cli-samples.md).
