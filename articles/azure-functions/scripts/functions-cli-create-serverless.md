---
title: "Azure CLI-skript Sample - skapa en Funktionsapp för serverlösa körning | Microsoft Docs"
description: "Azure CLI-skript Sample - skapa en Funktionsapp för serverlösa körning"
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
ms.openlocfilehash: 9436cafa775f2ad658c0c9901f7f8eae769c7052
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="create-a-function-app-for-serverless-execution"></a>Skapa en Funktionsapp för serverlösa körning

Det här exempelskriptet skapar en Azure-Funktionsapp, vilket är en behållare för dina funktioner. Funktionen appen skapas med hjälp av den [förbrukning plan](../functions-scale.md#consumption-plan), vilket är idealiskt för händelsedriven serverlösa arbetsbelastningar.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Om du väljer att installera och använda CLI lokalt kräver i den här artikeln att du använder Azure CLI version 2.0 eller senare. Kör `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI 2.0]( /cli/azure/install-azure-cli). 

## <a name="sample-script"></a>Exempelskript

Det här skriptet skapar en Azure-funktion app med hjälp av den [förbrukning plan](../functions-scale.md#consumption-plan).

[!code-azurecli-interactive[main](../../../cli_scripts/azure-functions/create-function-app-consumption/create-function-app-consumption.sh "Create an Azure Function on a consumption plan")]

[!INCLUDE [cli-script-clean-up](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>Skriptet förklaring

Varje kommando i tabellen länkar till kommandot viss dokumentation. Det här skriptet använder följande kommandon:

| Kommando | Anteckningar |
|---|---|
| [Skapa AZ grupp](/cli/azure/group#az_group_create) | Skapar en resursgrupp som är lagrade i alla resurser. |
| [Skapa AZ storage-konto](/cli/azure/storage/account#az_storage_account_create) | Skapar ett Azure Storage-konto. |
| [Skapa AZ functionapp](/cli/azure/functionapp#az_functionapp_create) | Skapar en funktionsapp. |

## <a name="next-steps"></a>Nästa steg

Mer information om Azure CLI finns [Azure CLI dokumentationen](/cli/azure/overview).

Ytterligare Azure Functions CLI skriptexempel finns i den [Azure Functions dokumentationen](../functions-cli-samples.md).
