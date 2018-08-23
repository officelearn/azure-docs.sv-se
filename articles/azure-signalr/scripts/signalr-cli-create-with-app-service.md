---
title: Azure CLI-skriptexempel – Skapa en SignalR Service i en App Service | Microsoft Docs
description: Azure CLI-skriptexempel – Skapa en SignalR Service i en App Service
services: signalr
documentationcenter: signalr
author: wesmc7777
manager: cfowler
editor: ''
tags: azure-service-management
ms.service: signalr
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: na
ms.workload: signalr
ms.date: 04/20/2018
ms.author: wesmc
ms.custom: mvc
ms.openlocfilehash: 0532cb81c766c2d039b978ab4ba79c812915bbc0
ms.sourcegitcommit: a2ae233e20e670e2f9e6b75e83253bd301f5067c
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/13/2018
ms.locfileid: "41920374"
---
# <a name="create-a-signalr-service-with-an-app-service"></a>Skapa en SignalR Service med en App Service

Det här exempelskriptet skapar en ny Azure SignalR Service-resurs som används för att skicka innehållsuppdateringar i realtid till klienter. Skriptet lägger också till en ny webbapp och App Service-plan som värd för din ASP.NET Core-webbapp som använder SignalR-tjänsten. Webbappen konfigureras med en appinställning som heter *AzureSignalRConnectionString* för att ansluta till den nya SignalR Service-resursen.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Om du väljer att installera och använda CLI lokalt måste du köra Azure CLI version 2.0 eller senare. Kör `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI 2.0]( /cli/azure/install-azure-cli). 

## <a name="sample-script"></a>Exempelskript

Det här skriptet använder tillägget *signalr* för Azure CLI. Utför följande kommando för att installera *signalr*-tillägget för Azure CLI innan du använder det här exempelskriptet:

```azurecli-interactive
az extension add -n signalr
```

[!code-azurecli-interactive[main](../../../cli_scripts/azure-signalr/create-signalr-with-app-service/create-signalr-with-app-service.sh "Create a new Azure SignalR Service and Web App")]

Anteckna det faktiska namnet som genereras för den nya resursgruppen. Det visas i utdata. Du använder resursgruppens namn när du vill ta bort alla gruppresurser.

[!INCLUDE [cli-script-clean-up](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>Förklaring av skript

Varje kommando i tabellen länkar till kommandospecifik dokumentation. I det här skriptet används följande kommandon:

| Kommando | Anteckningar |
|---|---|
| [az group create](/cli/azure/group#az-group-create) | Skapar en resursgrupp där alla resurser lagras. |
| [az signalr create](/cli/azure/ext/signalr/signalr#ext-signalr-az-signalr-create) | Skapar en Azure SignalR-tjänstresurs. |
| [az signalr key list](/cli/azure/ext/signalr/signalr/key#ext-signalr-az-signalr-key-list) | Listar nycklarna som kommer att användas av programmet när det skickar uppdateringar av innehåll i realtid med SignalR. |
| [az appservice plan create](/cli/azure/appservice/plan#az-appservice-plan-create) | Skapar en Azure App Service-Plan som värd för webbprogram. |
| [az webapp create](/cli/azure/webapp#az-webapp-create) | Skapar en Azure-webbapp med hjälp av App Service som värd för planen. |
| [az webapp config appsettings set](/cli/azure/webapp/config/appsettings#az-webapp-config-appsettings-set) | Lägger till en ny appinställning för webbappen. Den här appinställningen används för att lagra SignalR-anslutningssträngen. |

## <a name="next-steps"></a>Nästa steg

Mer information om Azure CLI finns i [Azure CLI-dokumentationen](/cli/azure).

Ytterligare Azure SignalR Service CLI-skriptexempel finns i [Azure SignalR Service-dokumentationen](../signalr-cli-samples.md).
