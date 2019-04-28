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
ms.date: 01/25/2019
ms.author: juliako
ms.openlocfilehash: 9acb2d7db1d8cb30fb853b79e9627a1b50da2aaa
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "61466892"
---
# <a name="cli-example-upload-a-local-file-to-a-container"></a>CLI-exempel: Överföra en lokal fil till en container 

Azure CLI-skriptet i den här artikeln visar hur du laddar upp en lokal fil till en lagringscontainer.

## <a name="prerequisites"></a>Nödvändiga komponenter 

[Skapa ett Media Services-konto](../create-account-cli-how-to.md).

[!INCLUDE [media-services-cli-instructions.md](../../../../includes/media-services-cli-instructions.md)]

## <a name="example-script"></a>Exempelskript

[!code-azurecli-interactive[main](../../../../cli_scripts/media-services/upload-file-asset/UploadFile-Asset.sh "Upload a file")]

## <a name="next-steps"></a>Nästa steg

Fler exempel finns i [Azure CLI-exempel](../cli-samples.md).
