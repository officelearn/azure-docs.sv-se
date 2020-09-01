---
title: Skriptexempel för Azure CLI – Skapa en transformering | Microsoft Docs
description: Transformeringar beskriver ett enkelt arbetsflöde av uppgifter för att bearbeta video- eller ljudfiler (kallas ofta ”recept”). Azure CLI-skriptet i den här artikeln visar hur du skapar en transformering.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.assetid: ''
ms.service: media-services
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 08/31/2020
ms.author: inhenkel
ms.custom: devx-track-azurecli
ms.openlocfilehash: dd2efc820bd2f35dee8e27737e7de919e6af2f30
ms.sourcegitcommit: bcda98171d6e81795e723e525f81e6235f044e52
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/01/2020
ms.locfileid: "89269785"
---
# <a name="cli-example-create-a-transform"></a>CLI-exempel: Skapa en transformering

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Azure CLI-skriptet i den här artikeln visar hur du skapar en transformering. Transformeringar beskriver ett enkelt arbetsflöde av uppgifter för att bearbeta video- eller ljudfiler (kallas ofta ”recept”). Du bör alltid kontrollera om det redan finns en transformering med önskat namn och ”recept”. Om så är fallet bör du återanvända den.

## <a name="prerequisites"></a>Förutsättningar 

[Skapa ett Media Services-konto](./create-account-howto.md).

[!INCLUDE [media-services-cli-instructions.md](../../../includes/media-services-cli-instructions.md)]

> [!NOTE]
> Du kan bara ange en sökväg till en anpassad standardkodad JSON-fil för [StandardEncoderPreset](/rest/api/media/transforms/createorupdate#standardencoderpreset)i [koda med ett exempel på en anpassad transformering](custom-preset-cli-howto.md) .
>
> Du kan inte skicka ett fil namn när du använder [BuiltInStandardEncoderPreset](/rest/api/media/transforms/createorupdate#builtinstandardencoderpreset).

## <a name="example-script"></a>Exempelskript

[!code-azurecli-interactive[main](../../../cli_scripts/media-services/create-transform/Create-Transform.sh "Create a transform")]

## <a name="next-steps"></a>Nästa steg

[AZ AMS Transform (CLI)](/cli/azure/ams/transform?view=azure-cli-latest)
