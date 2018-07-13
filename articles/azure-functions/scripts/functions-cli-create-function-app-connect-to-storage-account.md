---
title: Skapa en Azure-funktion som ansluter till Azure Storage | Microsoft Docs
description: Exempel på Azure CLI-skript – Skapa en Azure-funktion som ansluter till Azure Storage
services: functions
documentationcenter: functions
author: ggailey777
manager: cfowler
editor: ''
tags: functions
ms.assetid: ''
ms.service: functions
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: na
ms.workload: ''
ms.date: 04/20/2017
ms.author: glenga
ms.custom: mvc
ms.openlocfilehash: cbe7bf95574ca7a77d666981691da05357ce9a0d
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/11/2018
ms.locfileid: "38467834"
---
# <a name="create-a-function-app-that-connects-to-an-azure-storage-account"></a>Skapa en funktionsapp som ansluter till ett Azure Storage-konto

Det här exempelskriptet för Azure Functions skapar en funktionsapp och ansluter funktionen till ett Azure Storage-konto. Appinställningen som skapas och som innehåller anslutningen kan användas med [[lagringsutlösare eller bindning](..\functions-bindings-storage-blob.md). 

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
| [az login](https://docs.microsoft.com/cli/azure/reference-index#az_login) | Logga in på Azure. |
| [az group create](https://docs.microsoft.com/cli/azure/group#az_group_create) | Skapa en resursgrupp med plats |
| [az storage account create](https://docs.microsoft.com/cli/azure/storage/account) | skapar ett lagringskonto |
| [az functionapp create](https://docs.microsoft.com/cli/azure/functionapp#az_functionapp_create) | Skapa en ny funktionsapp |
| [az group delete](https://docs.microsoft.com/cli/azure/group#az_group_delete) | Rensa |

## <a name="next-steps"></a>Nästa steg

Mer information om Azure CLI finns i [Azure CLI-dokumentationen](https://docs.microsoft.com/cli/azure).

Ytterligare CLI-skriptexempel för Azure Functions finns i [Azure Functions-dokumentationen](../functions-cli-samples.md).
