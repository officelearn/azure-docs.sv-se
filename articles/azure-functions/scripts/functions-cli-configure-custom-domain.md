---
title: Skriptexempel för Azure CLI – Mappa en anpassad domän till en funktionsapp | Microsoft Docs
description: Skriptexempel för Azure CLI – Mappa en anpassad domän till en funktionsapp i Azure.
services: functions
documentationcenter: ''
author: ggailey777
manager: jeconnoc
ms.assetid: d127e347-7581-47d7-b289-e0f51f2fbfbc
ms.service: azure-functions
ms.devlang: azurecli
ms.topic: sample
ms.date: 07/04/2018
ms.author: glenga
ms.custom: mvc
ms.openlocfilehash: c5ae5ef52d60fd96a9398835b109475c08549f52
ms.sourcegitcommit: 2d961702f23e63ee63eddf52086e0c8573aec8dd
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/07/2018
ms.locfileid: "44157252"
---
# <a name="map-a-custom-domain-to-a-function-app"></a>Mappa en anpassad domän till en funktionsapp

I det här exempelskriptet skapar du en funktionsapp i en App Service-plan och mappar den till en anpassad domän du anger. När din funktionsapp finns i en [App Service-plan](../functions-scale.md#app-service-plan) kan du mappa en anpassad domän med antingen en CNAME-post eller en A-post. För funktionsappar i en [förbrukningsplan](../functions-scale.md#consumption-plan) stöds endast alternativet CNAME. I det här exemplet skapar du en App Service-plan, och det behövs en A-post till att mappa domänen. 

Om du vill köra det här exempelskriptet måste du redan ha konfigurerat en A-post i din anpassade domän som pekar på standarddomännamnet för webbappen. Mer information finns i [Mappa instruktioner om anpassad domän till Azure App Service](https://aka.ms/appservicecustomdns). 

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
| [az group create](https://docs.microsoft.com/cli/azure/group#az-group-create) | Skapar en resursgrupp där alla resurser lagras. |
| [az storage account create](https://docs.microsoft.com/cli/azure/storage/account#az-storage-account-create) | Skapar ett lagringskonto som krävs av funktionsappen. |
| [az appservice plan create](https://docs.microsoft.com/cli/azure/appservice/plan#az-appservice-plan-create) | Skapar en App Service-plan som krävs för mappning av en anpassad domän. |
| [az functionapp create](https://docs.microsoft.com/cli/azure/functionapp#az-functionapp-create) | Skapar en funktionsapp i App Service-planen. |
| [az functionapp config hostname add](https://docs.microsoft.com/cli/azure/functionapp/config/hostname#az-functionapp-config-hostname-add) | Mappar en anpassad domän till en funktionsapp. |

## <a name="next-steps"></a>Nästa steg

Mer information om Azure CLI finns i [Azure CLI-dokumentationen](https://docs.microsoft.com/cli/azure).

Ytterligare Functions CLI-skriptexempel för finns i [Azure Functions-dokumentationen](../functions-cli-samples.md).
