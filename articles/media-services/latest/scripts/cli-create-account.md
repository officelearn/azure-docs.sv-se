---
title: Skapa ett Azure Media Services-konto – Azure CLI | Microsoft Docs
description: Använda Azure CLI-skriptet till att skapa ett Azure Media Services-konto.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.assetid: ''
ms.service: media-services
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 01/15/2019
ms.author: juliako
ms.openlocfilehash: 2eeb47c2e0f96eca1ca9b852a2be6ca3102ba71e
ms.sourcegitcommit: a1cf88246e230c1888b197fdb4514aec6f1a8de2
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/16/2019
ms.locfileid: "54353353"
---
# <a name="cli-example-create-an-azure-media-services-account"></a>CLI-exempel: Skapa ett Azure Media Services-konto

Azure CLI-skriptet i det här avsnittet visar hur du skapar ett Azure Media Services-konto. Media Services-kontot och lagringskontot som associerats med det måste vara en del av samma datacenter och samma resursgrupp.

## <a name="prerequisites"></a>Nödvändiga komponenter 

Installera och använd CLI lokalt – du måste ha Azure CLI version 2.0 eller senare. Kör `az --version` för att se vilken version du har. Om du behöver installera eller uppgradera kan du läsa informationen i [Installera Azure CLI](/cli/azure/install-azure-cli). 

För närvarande fungerar inte alla [Media Services v3 CLI](https://aka.ms/ams-v3-cli-ref)-kommandon i Azure Cloud Shell. Vi rekommenderar att du använder CLI lokalt.

## <a name="example-script"></a>Exempelskript

[!code-azurecli-interactive[main](../../../../cli_scripts/media-services/media-services-create-account/Create-Account.sh "Create Account")]

## <a name="clean-up-deployment"></a>Rensa distribution

Kör följande kommando för att ta bort resursgruppen och alla resurser som är kopplade till den.

```azurecli
az group delete --name amsResourceGroup
```

## <a name="script-explanation"></a>Förklaring av skript

Det här skriptet använder följande kommandon. Varje kommando i tabellen länkar till kommandospecifik dokumentation.

| Kommando | Anteckningar |
|---|---|
| [az group create](/cli/azure/group#az-group-create) | Skapar en resursgrupp där alla resurser lagras. |
| [az storage account create](/cli/azure/storage/account#az-storage-account-create) | Skapar ett lagringskonto. |
| [az ams account create](https://docs.microsoft.com/cli/azure/ams/account?view=azure-cli-latest#az-ams-account-create) | Skapar ett Media Services-konto. |
| [az ams account sp create](https://docs.microsoft.com/cli/azure/ams/account/sp?view=azure-cli-latest#az-ams-account-sp-create) | Skapar ett huvudnamn för tjänsten med lösenord och konfigurerar åtkomsten till ett Azure Media Services-konto. 
| [az group delete](/cli/azure/group#az-group-delete) | Tar bort en resursgrupp, inklusive alla kapslade resurser. |


## <a name="next-steps"></a>Nästa steg

Fler exempel finns i [Azure CLI-exempel](../cli-samples.md).
