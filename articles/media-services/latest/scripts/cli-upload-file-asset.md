---
title: Exempel på Azure CLI skript – Ladda upp en fil till en container | Microsoft Docs
description: Använd Azure CLI-skript för att ladda upp en lokal fil till en lagringscontainer.
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
ms.date: 11/11/2018
ms.author: juliako
ms.openlocfilehash: 5120d938d137efef77eeb0b69a5bf571bd4c509b
ms.sourcegitcommit: b62f138cc477d2bd7e658488aff8e9a5dd24d577
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/13/2018
ms.locfileid: "51614505"
---
# <a name="cli-example-upload-a-local-file-to-a-container"></a>CLI-exempel: Ladda upp en lokal fil till en container 

Azure CLI-skriptet i den här artikeln visar hur du laddar upp en lokal fil till en lagringscontainer.

## <a name="prerequisites"></a>Nödvändiga komponenter 

- Installera och använd CLI lokalt – du måste ha Azure CLI version 2.0 eller senare. Kör `az --version` för att se vilken version du har. Om du behöver installera eller uppgradera kan du läsa informationen i [Installera Azure CLI](/cli/azure/install-azure-cli). 

    För närvarande fungerar inte alla [Media Services v3 CLI](https://aka.ms/ams-v3-cli-ref)-kommandon i Azure Cloud Shell. Vi rekommenderar att du använder CLI lokalt.

- [Skapa ett Media Services-konto](../create-account-cli-how-to.md).

## <a name="example-script"></a>Exempelskript

[!code-azurecli-interactive[main](../../../../cli_scripts/media-services/upload-file-asset/UploadFile-Asset.sh "Upload a file")]

## <a name="next-steps"></a>Nästa steg

Fler exempel finns i [Azure CLI-exempel](../cli-samples.md).
