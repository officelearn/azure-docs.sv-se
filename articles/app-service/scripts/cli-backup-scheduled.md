---
title: 'CLI: skapa en schemalagd säkerhets kopiering'
description: Lär dig hur du använder Azure CLI för att automatisera distribution och hantering av din App Service-app. Det här exemplet visar hur du skapar en schemalagd säkerhets kopiering för en app.
author: msangapu-msft
tags: azure-service-management
ms.devlang: azurecli
ms.topic: sample
ms.date: 12/11/2017
ms.author: msangapu
ms.reviewer: cephalin
ms.custom: mvc, seodec18
ms.openlocfilehash: 327b48052e0134ae97f0a97f0a43f36e75f2e003
ms.sourcegitcommit: c5021f2095e25750eb34fd0b866adf5d81d56c3a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/25/2020
ms.locfileid: "80058779"
---
# <a name="create-a-scheduled-backup-for-an-app-service-app-using-cli"></a>Skapa en schemalagd säkerhetskopiering för en App Service-app med hjälp av CLI

Det här exempelskriptet skapar en app i App Service med dess relaterade resurser, och skapar sedan en schemalagd säkerhetskopia för appen. 

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Om du väljer att installera och använda CLI-gränssnittet lokalt måste du ha Azure CLI version 2.0 eller senare. Kör `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa informationen i [Installera Azure CLI]( /cli/azure/install-azure-cli). 

## <a name="sample-script"></a>Exempelskript

[!code-azurecli-interactive[main](../../../cli_scripts/app-service/backup-scheduled/backup-scheduled.sh?highlight=3-7 "Create a scheduled backup for an app")]

[!INCLUDE [cli-script-clean-up](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>Förklaring av skript

Det här skriptet använder följande kommandon. Varje kommando i tabellen länkar till kommandospecifik dokumentation.

| Kommando | Kommentarer |
|---|---|
| [`az group create`](/cli/azure/group?view=azure-cli-latest#az-group-create) | Skapar en resursgrupp där alla resurser lagras. |
| [`az storage account create`](/cli/azure/storage/account?view=azure-cli-latest#az-storage-account-create) | Skapar ett lagringskonto. |
| [`az storage container create`](/cli/azure/storage/container?view=azure-cli-latest#az-storage-container-create) | Skapar en Azure-lagringscontainer. |
| [`az storage container generate-sas`](/cli/azure/storage/container?view=azure-cli-latest#az-storage-container-generate-sas) | Genererar en SAS-token för en Azure-lagringscontainer.  |
| [`az appservice plan create`](/cli/azure/appservice/plan?view=azure-cli-latest#az-appservice-plan-create) | Skapar en App Service-plan. |
| [`az webapp create`](/cli/azure/webapp?view=azure-cli-latest#az-webapp-create) | Skapar en App Service-app. |
| [`az webapp config backup update`](/cli/azure/webapp/config/backup?view=azure-cli-latest#az-webapp-config-backup-update) | Konfigurerar ett nytt schema för säkerhetskopiering för en App Service-app. |
| [`az webapp config backup show`](/cli/azure/webapp/config/backup?view=azure-cli-latest#az-webapp-config-backup-show) | Visar schemat för säkerhetskopiering för en App Service-app. |
| [`az webapp config backup list`](/cli/azure/webapp/config/backup?view=azure-cli-latest#az-webapp-config-backup-list) | Hämtar en lista med säkerhetskopior för en App Service-app. |

## <a name="next-steps"></a>Nästa steg

Mer information om Azure CLI finns i [Azure CLI-dokumentationen](https://docs.microsoft.com/cli/azure).

Fler App Service CLI-skriptexempel finns i [dokumentationen för Azure App Service](../samples-cli.md).
