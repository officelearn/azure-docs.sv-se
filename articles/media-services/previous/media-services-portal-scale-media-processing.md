---
title: Skala mediebearbetning med Azure portal | Microsoft Docs
description: Den här självstudien vägleder dig genom stegen för skala mediebearbetning med Azure portal.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.assetid: e500f733-68aa-450c-b212-cf717c0d15da
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/19/2019
ms.author: juliako
ms.openlocfilehash: c840764dc978a8dacb3450c0aca5e5d93284b8a6
ms.sourcegitcommit: aa3be9ed0b92a0ac5a29c83095a7b20dd0693463
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2019
ms.locfileid: "58260119"
---
# <a name="change-the-reserved-unit-type"></a>Ändra den reserverade enhetstypen
> [!div class="op_single_selector"]
> * [NET](media-services-dotnet-encoding-units.md)
> * [Portal](media-services-portal-scale-media-processing.md)
> * [REST](https://docs.microsoft.com/rest/api/media/operations/encodingreservedunittype)
> * [Java](https://github.com/southworkscom/azure-sdk-for-media-services-java-samples)
> * [PHP](https://github.com/Azure/azure-sdk-for-php/tree/master/examples/MediaServices)
> 
> 

## <a name="overview"></a>Översikt

Ett Media Services-konto är kopplat till en typ av reserverad enhet som bestämmer hur snabbt mediebearbetningsuppgifter ska bearbetas. Du kan välja mellan följande typer av reserverade enheter: **S1**, **S2** eller **S3**. Samma kodningsjobb körs till exempel snabbare om du använder typen **S2** än om du använder typen **S1**.

Förutom att ange typ av reserverad enhet kan du etablera **reserverade enheter** (RU:er) för ditt konto. Antalet etablerade RU:er anger antalet medieuppgifter som kan bearbetas samtidigt i en viss konto.

>[!NOTE]
>RU:er fungerar för parallellisera all bearbetning av media, inklusive indexeringsjobb med hjälp av Azure Media Indexer. Men till skillnad från kodning bearbetas inte indexeringsjobb snabbare med snabbare reserverade enheter.

> [!IMPORTANT]
> Se till att granska den [översikt](media-services-scale-media-processing-overview.md) så får du mer information om att skala mediebearbetning avsnittet.
> 
> 

## <a name="scale-media-processing"></a>Skala mediebearbetning
Om du vill ändra typ av reserverad enhet och antalet reserverade enheter måste du göra följande:

1. Välj ditt Azure Media Services-konto i [Azure-portalen](https://portal.azure.com/).
2. I den **inställningar** väljer **mediereserverade enheter**.
   
    Du kan ändra antalet reserverade enheter för den valda reserverade enhetstypen med den **Media hanteras enheter** skjutreglaget överst på skärmen.
   
    Ändra den **typ av reserverad enhet**, klicka på den **hastighet för reserverade bearbetningsenheter** fältet. Välj den prisnivå som du behöver: S1, S2 eller S3.
   
3. Tryck på knappen SPARA för att spara ändringarna.
   
    De nya reserverade enheterna tilldelas när du trycker på Spara.

## <a name="next-steps"></a>Nästa steg
Granska sökvägarna för Media Services-utbildning.

[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Ge feedback
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

