---
title: "Azure CLI-skript Sample - skapa en Funktionsapp i en apptjänstplan | Microsoft Docs"
description: "Azure CLI-skript Sample - skapa en Funktionsapp i en apptjänstplan"
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
ms.date: 04/11/2017
ms.author: cfowler
ms.custom: mvc
ms.openlocfilehash: c7868dda1e00882a944ac61d838c8b8987d5e740
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="create-a-function-app-in-an-app-service-plan"></a>Skapa en Funktionsapp i en apptjänstplan

Det här exempelskriptet skapar en Azure-Funktionsapp, vilket är en behållare för dina funktioner. Funktionen appen skapas med hjälp av en dedikerad programtjänstplan, vilket innebär att serverresurserna alltid är aktiva.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Om du väljer att installera och använda CLI lokalt måste du köra Azure CLI version 2.0 eller senare. Kör `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI 2.0]( /cli/azure/install-azure-cli). 

## <a name="sample-script"></a>Exempelskript

Det här skriptet skapar en Azure-funktion app med en dedikerad [programtjänstplanen](../functions-scale.md#app-service-plan).

[!code-azurecli-interactive[main](../../../cli_scripts/azure-functions/create-function-app-app-service-plan/create-function-app-app-service-plan.sh "Create an Azure Function on an App Service plan")]

[!INCLUDE [cli-script-clean-up](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>Skriptet förklaring

Varje kommando i tabellen länkar till kommandot viss dokumentation. Det här skriptet använder följande kommandon:

| Kommando | Anteckningar |
|---|---|
| [Skapa AZ grupp](https://docs.microsoft.com/cli/azure/group#az_group_create) | Skapar en resursgrupp som är lagrade i alla resurser. |
| [Skapa AZ storage-konto](https://docs.microsoft.com/cli/azure/storage/account#az_storage_account_create) | Skapar ett Azure Storage-konto. |
| [Skapa AZ programtjänstplan](https://docs.microsoft.com/cli/azure/appserviceplan#az_appserviceplan_create) | Skapar en App Service-plan. |
| [Skapa AZ functionapp](https://docs.microsoft.com/cli/azure/functionapp#az_functionapp_delete) | Skapar en funktionsapp i Azure. |

## <a name="next-steps"></a>Nästa steg

Mer information om Azure CLI finns [Azure CLI dokumentationen](https://docs.microsoft.com/cli/azure/overview).

Ytterligare Azure Functions CLI skriptexempel finns i den [Azure Functions dokumentationen](../functions-cli-samples.md).
