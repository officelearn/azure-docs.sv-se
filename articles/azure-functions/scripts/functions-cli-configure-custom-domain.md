---
title: "Skriptexempel Azure CLI - karta en anpassad domän till en funktionsapp | Microsoft Docs"
description: "Azure CLI skriptexempel - karta en anpassad domän till en funktion i Azure."
services: functions
documentationcenter: 
author: ggailey777
manager: cfowler
editor: 
tags: azure-service-management
ms.assetid: d127e347-7581-47d7-b289-e0f51f2fbfbc
ms.service: functions
ms.workload: na
ms.devlang: azurecli
ms.tgt_pltfrm: na
ms.topic: sample
ms.date: 06/01/2017
ms.author: glenga
ms.custom: mvc
ms.openlocfilehash: 87b79d6222f40e3dc1306ecace51bae50b06e484
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="map-a-custom-domain-to-a-function-app"></a>Mappa en anpassad domän till en funktionsapp

Det här exempelskriptet skapar en funktionsapp med relaterade resurser och sedan mappa `www.<yourdomain>` till den. Om du vill mappa till en anpassad domän, måste appen funktionen skapas i en apptjänstplan och inte i en plan för användning. Azure Functions stöder bara mappa en anpassad domän med en A-post.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Om du väljer att installera och använda CLI lokalt, måste du använda Azure CLI version 2.0 eller senare. Kör `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI 2.0]( /cli/azure/install-azure-cli). 


## <a name="sample-script"></a>Exempelskript

[!code-azurecli-interactive[main](../../../cli_scripts/azure-functions/configure-custom-domain/configure-custom-domain.sh?highlight=3 "Map a custom domain to a function app")]

[!INCLUDE [cli-script-clean-up](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>Skriptet förklaring

Det här skriptet använder följande kommandon: varje kommando i tabellen länkar till kommandot viss dokumentation.

| Kommando | Anteckningar |
|---|---|
| [Skapa AZ grupp](https://docs.microsoft.com/cli/azure/group#az_group_create) | Skapar en resursgrupp som är lagrade i alla resurser. |
| [Skapa AZ storage-konto](https://docs.microsoft.com/cli/azure/storage/account#az_storage_account_create) | Skapar ett lagringskonto som krävs av funktionen appen. |
| [Skapa AZ programtjänstplan](https://docs.microsoft.com/cli/azure/appservice/plan#az_appservice_plan_create) | Skapar en App Service-plan som behövs för att mappa en anpassad domän. |
| [Skapa AZ functionapp]() | Skapar en funktionsapp. |
| [Lägg till AZ apptjänst web config värdnamn](https://docs.microsoft.com/cli/azure/appservice/web/config/hostname#az_appservice_web_config_hostname_add) | Avbildar en anpassad domän till en funktionsapp. |

## <a name="next-steps"></a>Nästa steg

Mer information om Azure CLI finns [Azure CLI dokumentationen](https://docs.microsoft.com/cli/azure/overview).

Ytterligare funktioner CLI skriptexempel finns i den [Azure Functions dokumentationen]().
