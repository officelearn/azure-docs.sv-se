---
title: Skriptexempel för Azure CLI – Skapa en transformering | Microsoft Docs
description: Transformeringar beskriver ett enkelt arbetsflöde av uppgifter för att bearbeta video- eller ljudfiler (kallas ofta ”recept”). Azure CLI-skriptet i den här artikeln visar hur du skapar en transformering.
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
ms.openlocfilehash: c21a16d043f972042949d6340985774741b3df6a
ms.sourcegitcommit: 8bd85510aee664d40614655d0ff714f61e6cd328
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/06/2019
ms.locfileid: "74888623"
---
# <a name="cli-example-create-a-transform"></a>CLI-exempel: Skapa en transformering

Azure CLI-skriptet i den här artikeln visar hur du skapar en transformering. Transformeringar beskriver ett enkelt arbetsflöde av uppgifter för att bearbeta video- eller ljudfiler (kallas ofta ”recept”). Du bör alltid kontrollera om det redan finns en transformering med önskat namn och ”recept”. Om så är fallet bör du återanvända den.

## <a name="prerequisites"></a>Krav 

[Skapa ett Media Services-konto](create-account-cli-how-to.md).

[!INCLUDE [media-services-cli-instructions.md](../../../includes/media-services-cli-instructions.md)]

> [!NOTE]
> Du kan bara ange en sökväg till en anpassad standardkodad JSON-fil för [StandardEncoderPreset](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#standardencoderpreset)i [koda med ett exempel på en anpassad transformering](custom-preset-cli-howto.md) .
>
> Du kan inte skicka ett fil namn när du använder [BuiltInStandardEncoderPreset](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#builtinstandardencoderpreset).

## <a name="example-script"></a>Exempelskript

[!code-azurecli-interactive[main](../../../cli_scripts/media-services/create-transform/Create-Transform.sh "Create a transform")]

## <a name="next-steps"></a>Nästa steg

[AZ AMS Transform (CLI)](https://docs.microsoft.com/cli/azure/ams/transform?view=azure-cli-latest)
