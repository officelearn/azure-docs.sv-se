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
ms.date: 05/01/2019
ms.author: juliako
ms.openlocfilehash: 5fd7c41301aa2ece1b5cf62dd294ecc1970521be
ms.sourcegitcommit: 0568c7aefd67185fd8e1400aed84c5af4f1597f9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/06/2019
ms.locfileid: "65209618"
---
# <a name="cli-example-upload-a-local-file-to-a-container"></a>CLI-exempel: Överföra en lokal fil till en container 

Azure CLI-skriptet i den här artikeln visar hur du laddar upp en lokal fil till en lagringscontainer.

## <a name="prerequisites"></a>Nödvändiga komponenter 

[Skapa ett Media Services-konto](create-account-cli-how-to.md).

[!INCLUDE [media-services-cli-instructions.md](../../../includes/media-services-cli-instructions.md)]

## <a name="example-script"></a>Exempelskript

[!code-azurecli-interactive[main](../../../cli_scripts/media-services/upload-file-asset/UploadFile-Asset.sh "Upload a file")]

## <a name="next-steps"></a>Nästa steg

[Översikt över Media Services](media-services-overview.md)
