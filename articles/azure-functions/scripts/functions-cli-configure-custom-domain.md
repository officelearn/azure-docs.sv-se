---
title: Skriptexempel för Azure CLI – Mappa en anpassad domän till en funktionsapp | Microsoft Docs
description: Skriptexempel för Azure CLI – Mappa en anpassad domän till en funktionsapp i Azure.
services: functions
documentationcenter: ''
author: ggailey777
manager: cfowler
editor: ''
tags: azure-service-management
ms.assetid: d127e347-7581-47d7-b289-e0f51f2fbfbc
ms.service: functions
ms.workload: na
ms.devlang: azurecli
ms.tgt_pltfrm: na
ms.topic: sample
ms.date: 06/26/2018
ms.author: glenga
ms.custom: mvc
ms.openlocfilehash: 7d3fc71bc53e85fa7555dbee5ee79b3f06f27fe8
ms.sourcegitcommit: 0408c7d1b6dd7ffd376a2241936167cc95cfe10f
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/26/2018
ms.locfileid: "36960346"
---
# <a name="map-a-custom-domain-to-a-function-app"></a>Mappa en anpassad domän till en funktionsapp

Det här skriptexemplet skapar en funktionsapp med relaterade resurser och mappar sedan `www.<yourdomain>` till den. När din funktionsapp finns i en [App Service-plan](../functions-scale.md#app-service-plan) kan du mappa en anpassad domän med antingen en CNAME-post eller en A-post. För funktionsappar i en [förbrukningsplan](../functions-scale.md#consumption-plan) stöds endast alternativet CNAME.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Om du väljer att använda CLI lokalt måste du använda Azure CLI version 2.0 eller senare. Kör `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI 2.0]( /cli/azure/install-azure-cli). 


## <a name="sample-script"></a>Exempelskript

[!code-azurecli-interactive[main](../../../cli_scripts/azure-functions/configure-custom-domain/configure-custom-domain.sh?highlight=3 "Map a custom domain to a function app")]

[!INCLUDE [cli-script-clean-up](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>Förklaring av skript

Det här skriptet använder följande kommandon: varje kommando i tabellen länkar till kommandospecifik dokumentation.

| Kommando | Anteckningar |
|---|---|
| [az group create](https://docs.microsoft.com/cli/azure/group#az_group_create) | Skapar en resursgrupp där alla resurser lagras. |
| [az storage account create](https://docs.microsoft.com/cli/azure/storage/account#az_storage_account_create) | Skapar ett lagringskonto som krävs av funktionsappen. |
| [az appservice plan create](https://docs.microsoft.com/cli/azure/appservice/plan#az_appservice_plan_create) | Skapar en App Service-plan som krävs för mappning av en anpassad domän. |
| [az functionapp create]() | Skapar en funktionsapp. |
| [az appservice web config hostname add](https://docs.microsoft.com/cli/azure/appservice/web/config/hostname#az_appservice_web_config_hostname_add) | Mappar en anpassad domän till en funktionsapp. |

## <a name="next-steps"></a>Nästa steg

Mer information om Azure CLI finns i [Azure CLI-dokumentationen](https://docs.microsoft.com/cli/azure).

Ytterligare Functions CLI-skriptexempel för finns i [Azure Functions-dokumentationen]().
