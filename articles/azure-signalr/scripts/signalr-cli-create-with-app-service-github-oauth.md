---
title: Azure CLI-skriptexempel – Skapa en webbapp som använder SignalR Service- och GitHub-autentisering | Microsoft Docs
description: Azure CLI-skriptexempel – Skapa en webbapp som använder SignalR Service- och GitHub-autentisering
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
ms.date: 04/22/2018
ms.author: wesmc
ms.custom: mvc
ms.openlocfilehash: ec095572dc8fe20c913d543ffcf926355b5715df
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/07/2018
---
# <a name="create-a-web-app-that-uses-signalr-service-and-github-authentication"></a>Skapa en webbapp som använder SignalR Service- och GitHub-autentisering

Det här exempelskriptet skapar en ny Azure SignalR Service-resurs som används för att skicka innehållsuppdateringar i realtid till klienter. Skriptet lägger också till en ny webbapp och App Service-plan som värd för din ASP.NET Core-webbapp som använder SignalR-tjänsten. Webbappen är konfigurerad med appinställningar för att ansluta till den nya SignalR Service-resursen och autentisera med [GitHub-autentisering](https://developer.github.com/v3/guides/basics-of-authentication/). Webbappen är också konfigurerad för att använda en lokal distributionskälla för Git-lagringsplatsen.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Om du väljer att installera och använda CLI lokalt måste du köra Azure CLI version 2.0 eller senare. Kör `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI 2.0]( /cli/azure/install-azure-cli). 

## <a name="sample-script"></a>Exempelskript

Det här skriptet använder tillägget *signalr* för Azure CLI. Utför följande kommando för att installera *signalr*-tillägget för Azure CLI innan du använder det här exempelskriptet:

```azurecli-interactive
az extension add -n signalr
```

[!code-azurecli-interactive[main](../../../cli_scripts/azure-signalr/create-signalr-with-app-service-github-oauth/create-signalr-with-app-service-github-oauth.sh "Create a new SignalR Service and Web App configured to use SignalR, GitHub OAuth, and local Git repository deployment source.")]

Anteckna det faktiska namnet som genereras för den nya resursgruppen. Det visas i utdata. Du använder resursgruppens namn när du vill ta bort alla gruppresurser.

[!INCLUDE [cli-script-clean-up](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>Förklaring av skript

Varje kommando i tabellen länkar till kommandospecifik dokumentation. I det här skriptet används följande kommandon:

| Kommando | Anteckningar |
|---|---|
| [az group create](/cli/azure/group#az-group-create) | Skapar en resursgrupp där alla resurser lagras. |
| [az signalr create](/cli/azure/signalr#az-signalr-create) | Skapar en Azure SignalR-tjänstresurs. |
| [az signalr key list](/cli/azure/signalr/key#az-signalr-key-list) | Lista nycklarna som kommer att användas av programmet när det skickar uppdateringar av innehåll i realtid med SignalR. |
| [az appservice plan create](/cli/azure/appservice/plan#az-appservice-plan-create) | Skapar en Azure App Service-Plan som värd för webbprogram. |
| [az webapp create](/cli/azure/webapp#az-webapp-create) | Skapar en Azure-webbapp med hjälp av App Service som värd för planen. |
| [az webapp config appsettings set](/cli/azure/webapp/config/appsettings#az-webapp-config-appsettings-set) | Lägger till nya appinställningar för webbappen. De här appinställningarna används för att lagra SignalR-anslutningssträngen och GitHub OAuth-apphemligheterna. |
| [az webapp deployment user set](/cli/azure/webapp/deployment/user#az-webapp-deployment-user-set) | Uppdatera autentiseringsuppgifter för distribution. |
| [az webapp deployment source config-local-git](/cli/azure/webapp/deployment/source#az-webapp-deployment-source-config-local-git) | Hämta en URL för en git-lagringsplatsslutpunkt för att klona och skicka till vid webbappdistribution. |

## <a name="next-steps"></a>Nästa steg

Mer information om Azure CLI finns i [Azure CLI-dokumentationen](/cli/azure).

Ytterligare Azure SignalR Service CLI-skriptexempel finns i [Azure SignalR Service-dokumentationen](../signalr-cli-samples.md).
