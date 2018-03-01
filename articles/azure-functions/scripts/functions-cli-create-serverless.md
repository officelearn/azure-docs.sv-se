---
title: "Azure CLI-skriptexempel – Skapa en funktionsapp för serverlös körning | Microsoft Docs"
description: "Azure CLI-skriptexempel – Skapa en funktionsapp för serverlös körning"
services: functions
documentationcenter: functions
author: syntaxc4
manager: cfowler
editor: 
tags: azure-service-management
ms.assetid: 0e221db6-ee2d-4e16-9bf6-a456cd05b6e7
ms.service: functions
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: na
ms.workload: web
ms.date: 10/04/2017
ms.author: cfowler
ms.custom: mvc
ms.openlocfilehash: f3e4ce63a8b418b68494262df8a771ac553ad22d
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/09/2018
---
# <a name="create-a-function-app-for-serverless-code-execution"></a>Skapa en funktionsapp för serverlös kodkörning

Det här exempelskriptet för Azure Functions skapar en funktionsapp som blir behållare åt dina funktioner. Funktionsappen skapas med hjälp av [förbrukningsplanen](../functions-scale.md#consumption-plan), vilket är idealiskt för händelsedrivna, serverlösa arbetsbelastningar.

[!INCLUDE [upgrade runtime](../../../includes/functions-cli-version-note.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Om du väljer att installera och använda CLI lokalt måste du köra Azure CLI version 2.0 eller senare. Kör `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI 2.0]( /cli/azure/install-azure-cli). 

## <a name="sample-script"></a>Exempelskript

Det här skriptet skapar en Azure Function-app med hjälp av [förbrukningsplanen](../functions-scale.md#consumption-plan).

[!code-azurecli-interactive[main](../../../cli_scripts/azure-functions/create-function-app-consumption/create-function-app-consumption.sh "Create an Azure Function on a consumption plan")]

[!INCLUDE [cli-script-clean-up](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>Förklaring av skript

Varje kommando i tabellen länkar till kommandospecifik dokumentation. I det här skriptet används följande kommandon:

| Kommando | Anteckningar |
|---|---|
| [az group create](/cli/azure/group#az_group_create) | Skapar en resursgrupp där alla resurser lagras. |
| [az storage account create](/cli/azure/storage/account#az_storage_account_create) | Konfigurerar ett Azure Storage-konto. |
| [az functionapp create](/cli/azure/functionapp#az_functionapp_create) | Skapar en funktionsapp. |

## <a name="next-steps"></a>Nästa steg

Mer information om Azure CLI finns i [Azure CLI-dokumentationen](/cli/azure/overview).

Ytterligare CLI-skriptexempel för Azure Functions finns i [Azure Functions-dokumentationen](../functions-cli-samples.md).
