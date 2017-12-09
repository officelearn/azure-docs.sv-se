---
title: Skriptexempel Azure CLI - bindning som ett anpassat SSL-certifikatet till en funktionsapp | Microsoft Docs
description: Skriptexempel Azure CLI - Bind ett anpassat SSL-certifikatet till en funktionsapp i Azure
services: functions
documentationcenter: 
author: ggailey777
manager: cfowler
editor: 
tags: azure-service-management
ms.assetid: eb95d350-81ea-4145-a1e2-6eea3b7469b2
ms.service: functions
ms.workload: na
ms.devlang: azurecli
ms.tgt_pltfrm: na
ms.topic: sample
ms.date: 04/10/2017
ms.author: glenga
ms.custom: mvc
ms.openlocfilehash: fbc94734f3c49e7318734b05b30e0ef6b8767004
ms.sourcegitcommit: b07d06ea51a20e32fdc61980667e801cb5db7333
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/08/2017
---
# <a name="bind-a-custom-ssl-certificate-to-a-function-app"></a>Bind ett anpassade SSL-certifikat till en funktionsapp

Det här exempelskriptet skapar en funktionsapp i App Service med dess relaterade resurser och sedan binda SSL-certifikatet för ett anpassat domännamn till den. För det här exemplet behöver du:

* Åtkomst till din domänregistrator DNS-konfigurationssidan.
* En giltig. PFX-filen och lösenordet för SSL-certifikatet som du vill ladda upp och binda.

Om du vill binda ett SSL-certifikat, måste funktionen appen skapas i en apptjänstplan och inte i en plan för användning.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Om du väljer att installera och använda CLI lokalt måste du köra Azure CLI version 2.0 eller senare. Kör `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI 2.0]( /cli/azure/install-azure-cli). 

## <a name="sample-script"></a>Exempelskript

[!code-azurecli-interactive[main](../../../cli_scripts/azure-functions/configure-ssl-certificate/configure-ssl-certificate.sh?highlight=3-5 "Bind a custom SSL certificate to a web app")]

[!INCLUDE [cli-script-clean-up](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>Skriptet förklaring

Det här skriptet använder följande kommandon. Varje kommando i tabellen länkar till kommandot viss dokumentation.

| Kommando | Anteckningar |
|---|---|
| [Skapa AZ grupp](https://docs.microsoft.com/cli/azure/group#az_group_create) | Skapar en resursgrupp som är lagrade i alla resurser. |
| [Skapa AZ programtjänstplan](https://docs.microsoft.com/cli/azure/appservice/plan#az_appservice_plan_create) | Skapar en App Service-plan som krävs för att binda SSL-certifikat. |
| [Skapa AZ functionapp]() | Skapar en funktionsapp. |
| [Lägg till AZ apptjänst web config värdnamn](https://docs.microsoft.com/cli/azure/appservice/web/config/hostname#az_appservice_web_config_hostname_add) | Mappar en anpassad domän för funktionen appen. |
| [AZ apptjänst web config ssl överför](https://docs.microsoft.com/cli/azure/appservice/web/config/ssl#az_appservice_web_config_ssl_upload) | Överför ett SSL-certifikat till en funktionsapp. |
| [AZ apptjänst web config ssl-bindning](https://docs.microsoft.com/cli/azure/appservice/web/config/ssl#az_appservice_web_config_ssl_bind) | Binder en överförda SSL-certifikatet till en funktionsapp. |

## <a name="next-steps"></a>Nästa steg

Mer information om Azure CLI finns [Azure CLI dokumentationen](https://docs.microsoft.com/cli/azure/overview).

Ytterligare App Service CLI skriptexempel finns i den [dokumentation för Azure App Service]().
