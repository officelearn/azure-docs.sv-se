---
title: Exempel på Azure CLI skript – Ladda upp en fil till en container | Microsoft Docs
description: Använd Azure CLI-skript för att ladda upp en lokal fil till en lagringscontainer.
services: media-services
documentationcenter: ''
author: Juliako
manager: cfowler
editor: ''
ms.assetid: ''
ms.service: media-services
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 05/11/2018
ms.author: juliako
ms.openlocfilehash: 61a5a59336878976a20a6ffd7b4e4d6d27061453
ms.sourcegitcommit: e8f443ac09eaa6ef1d56a60cd6ac7d351d9271b9
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/12/2018
ms.locfileid: "35764036"
---
# <a name="cli-example-upload-a-local-file-to-a-container"></a>CLI-exempel: Ladda upp en lokal fil till en container 

Azure CLI-skriptet i den här artikeln visar hur du laddar upp en lokal fil till en lagringscontainer.

[!INCLUDE [cloud-shell-try-it.md](../../../../includes/cloud-shell-try-it.md)]

Om du väljer att installera och använda CLI lokalt måste du köra Azure CLI version 2.0.20 eller senare. Kör `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa informationen i [Installera Azure CLI](/cli/azure/install-azure-cli). 

## <a name="example-script"></a>Exempelskript

[!code-azurecli-interactive[main](../../../../cli_scripts/media-services/upload-file-asset/UploadFile-Asset.sh "Upload a file")]

## <a name="next-steps"></a>Nästa steg

Fler exempel finns i [Azure CLI-exempel](../cli-samples.md).
