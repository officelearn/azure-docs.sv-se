---
title: 'CLI: skapa ASP.NET Core app från ACR'
description: Lär dig hur du använder Azure CLI för att automatisera distribution och hantering av din App Service-app. Det här exemplet visar hur du skapar en Linux ASP.NET Core-app från ACR.
author: msangapu-msft
tags: azure-service-management
ms.assetid: 3a2d1983-ff7b-476a-ac44-49ec2aabb31a
ms.devlang: azurecli
ms.topic: sample
ms.date: 12/13/2018
ms.author: msangapu
ms.custom: devx-track-dotnet, mvc, seodec18
ms.openlocfilehash: 1b5f6988a7f217669b2b2dfe90dd5152b880b41c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/09/2020
ms.locfileid: "88960436"
---
# <a name="create-an-aspnet-core-app-in-a-docker-container-in-app-service-from-azure-container-registry"></a>Skapa en ASP.NET Core-app i en Docker-container i App Service från Azure Container Registry

Det här exempelskriptet skapar en resursgrupp, en Linux App Service plan och en app. Det distribuerar sedan ett ASP.NET Core-program med en Docker-behållare från Azure Container Registry.


[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

Du måste ha Azure CLI version 2.0.52 eller senare. Kör `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa informationen i [Installera Azure CLI]( /cli/azure/install-azure-cli).

## <a name="sample-script"></a>Exempelskript

[!code-azurecli-interactive[main](../../../cli_scripts/app-service/deploy-linux-acr/deploy-linux-acr.sh "Linux Azure Container Registry")]

[!INCLUDE [cli-script-clean-up](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>Förklaring av skript

I det här skriptet används följande kommandon för att skapa en resursgrupp, App Service-appen och alla relaterade resurser. Varje kommando i tabellen länkar till kommandospecifik dokumentation.

| Kommando | Obs! |
|---|---|
| [`az group create`](/cli/azure/group?view=azure-cli-latest#az-group-create) | Skapar en resursgrupp där alla resurser lagras. |
| [`az appservice plan create`](/cli/azure/appservice/plan?view=azure-cli-latest#az-appservice-plan-create) | Skapar en App Service-plan. |
| [`az webapp create`](/cli/azure/webapp?view=azure-cli-latest#az-webapp-create) | Skapar en App Service-app. |
| [`az webapp config container set`](/cli/azure/webapp/config/container?view=azure-cli-latest#az-webapp-config-container-set) | Konfigurerar Docker-containern för App Service-appen. |

## <a name="next-steps"></a>Nästa steg

Mer information om Azure CLI finns i [Azure CLI-dokumentationen](/cli/azure).

Fler App Service CLI-skriptexempel finns i [dokumentationen för Azure App Service](../samples-cli.md).