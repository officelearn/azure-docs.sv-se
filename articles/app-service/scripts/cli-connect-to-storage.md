---
title: Skriptexempel för Azure CLI – Ansluta en app till ett lagringskonto | Microsoft Docs
description: Skriptexempel för Azure CLI – Ansluta en app till ett lagringskonto
services: appservice
documentationcenter: appservice
author: msangapu
manager: jeconnoc
editor: ''
tags: azure-service-management
ms.assetid: bc8345b2-8487-40c6-a91f-77414e8688e6
ms.service: app-service
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: na
ms.workload: web
ms.date: 12/11/2017
ms.author: msangapu
ms.custom: seodec18
ms.openlocfilehash: 57a1e9ad6bf377fa5148a4d5c716be00db786c84
ms.sourcegitcommit: c94cf3840db42f099b4dc858cd0c77c4e3e4c436
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/19/2018
ms.locfileid: "53632288"
---
# <a name="connect-an-app-service-app-to-a-storage-account-using-cli"></a>Ansluta en App Service-app till ett lagringskonto med hjälp av CLI

Det här exempelskriptet skapar ett Azure-lagringskonto och en App Service-app. Därefter länkar det lagringskontot till appen med hjälp av app-inställningar.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Om du väljer att installera och använda CLI-gränssnittet lokalt måste du ha Azure CLI version 2.0 eller senare. Kör `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa informationen i [Installera Azure CLI]( /cli/azure/install-azure-cli).


## <a name="sample-script"></a>Exempelskript

[!code-azurecli-interactive[main](../../../cli_scripts/app-service/connect-to-storage/connect-to-storage.sh "Azure Storage")]

[!INCLUDE [cli-script-clean-up](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>Förklaring av skript

I det här skriptet används följande kommandon för att skapa en resursgrupp, App Service-app, lagringskonto och alla relaterade resurser. Varje kommando i tabellen länkar till kommandospecifik dokumentation.

| Kommando | Anteckningar |
|---|---|
| [`az group create`](/cli/azure/group?view=azure-cli-latest#az-group-create) | Skapar en resursgrupp där alla resurser lagras. |
| [`az appservice plan create`](/cli/azure/appservice/plan?view=azure-cli-latest#az-appservice-plan-create) | Skapar en App Service-plan. |
| [`az webapp create`](/cli/azure/webapp?view=azure-cli-latest#az-webapp-create) | Skapar en App Service-app. |
| [`az storage account create`](/cli/azure/storage/account?view=azure-cli-latest#az-storage-account-create) | Skapar ett lagringskonto. |
| [`az storage account show-connection-string`](/cli/azure/storage/account?view=azure-cli-latest#az-storage-account-show-connection-string) | Hämta anslutningssträngen för ett lagringskonto. |
| [`az webapp config appsettings set`](/cli/azure/webapp/config/appsettings?view=azure-cli-latest#az-webapp-config-appsettings-set) | Skapar eller uppdaterar en app-inställning för en App Service-app. Appinställningarna visas som miljövariabler för din app. |

## <a name="next-steps"></a>Nästa steg

Mer information om Azure CLI finns i [Azure CLI-dokumentationen](https://docs.microsoft.com/cli/azure).

Fler App Service CLI-skriptexempel finns i [dokumentationen för Azure App Service](../samples-cli.md).
