---
title: Skriptexempel för Azure CLI – Binda ett anpassat SSL-certifikat till en funktionsapp | Microsoft Docs
description: Skriptexempel för Azure CLI – Binda ett anpassat SSL-certifikat till en funktionsapp i Azure
services: functions
documentationcenter: ''
author: ggailey777
manager: jeconnoc
ms.assetid: eb95d350-81ea-4145-a1e2-6eea3b7469b2
ms.service: azure-functions
ms.devlang: azurecli
ms.topic: sample
ms.date: 07/03/2013
ms.author: glenga
ms.custom: mvc
ms.openlocfilehash: 8d8b96cec202edd1e93e9f7c04a5e508ed68a40e
ms.sourcegitcommit: 2d961702f23e63ee63eddf52086e0c8573aec8dd
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/07/2018
ms.locfileid: "44162741"
---
# <a name="bind-a-custom-ssl-certificate-to-a-function-app"></a>Binda ett anpassat SSL-certifikat till en funktionsapp

Det här exempelskriptet skapar en funktionsapp i App Service med dess relaterade resurser och binder sedan SSL-certifikatet för ett anpassat domännamn till den. För det här exemplet behöver du följande:

* Få åtkomst till din domänregistrators DNS-konfigurationssida.
* En giltig .PFX-fil och dess lösenord för SSL-certifikatet du vill ladda upp och binda.
* En konfigurerad A-post i den anpassade domänen som pekar på standarddomännamnet för webbappen. Mer information finns i [Mappa instruktioner om anpassad domän till Azure App Service](https://aka.ms/appservicecustomdns).

Om du vill binda ett SSL-certifikat måste funktionsappen skapas i en App Service-plan och inte i en användningsplan.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Om du väljer att installera och använda CLI lokalt måste du köra Azure CLI version 2.0 eller senare. Kör `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI 2.0]( /cli/azure/install-azure-cli). 

## <a name="sample-script"></a>Exempelskript

[!code-azurecli-interactive[main](../../../cli_scripts/azure-functions/configure-ssl-certificate/configure-ssl-certificate.sh?highlight=3-5 "Bind a custom SSL certificate to a web app")]

[!INCLUDE [cli-script-clean-up](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>Förklaring av skript

Det här skriptet använder följande kommandon. Varje kommando i tabellen länkar till kommandospecifik dokumentation.

| Kommando | Anteckningar |
|---|---|
| [az group create](https://docs.microsoft.com/cli/azure/group#az-group-create) | Skapar en resursgrupp där alla resurser lagras. |
| [az storage account create](https://docs.microsoft.com/cli/azure/storage/account#az-storage-account-create) | Skapar ett lagringskonto som krävs av funktionsappen. |
| [az appservice plan create](https://docs.microsoft.com/cli/azure/appservice/plan#az-appservice-plan-create) | Skapar en App Service-plan som krävs för att binda SSL-certifikat. |
| [az functionapp create](https://docs.microsoft.com/cli/azure/functionapp#az-functionapp-create) | Skapar en funktionsapp i App Service-planen. |
| [az functionapp config hostname add](https://docs.microsoft.com/cli/azure/functionapp/config/hostname#az-functionapp-config-hostname-add) | Mappar en anpassad domän till en funktionsapp. |
| [az functionapp config ssl upload](https://docs.microsoft.com/cli/azure/functionapp/config/ssl#az-functionapp-config-ssl-upload) | Överför ett SSL-certifikat till en funktionsapp. |
| [az functionapp config ssl bind](https://docs.microsoft.com/cli/azure/functionapp/config/ssl#az-functionapp-config-ssl-bind) | Binder ett överfört SSL-certifikat till en funktionsapp. |

## <a name="next-steps"></a>Nästa steg

Mer information om Azure CLI finns i [Azure CLI-dokumentationen](https://docs.microsoft.com/cli/azure).

Fler App Service CLI-skriptexempel finns i [dokumentationen för Azure App Service](../functions-cli-samples.md).
