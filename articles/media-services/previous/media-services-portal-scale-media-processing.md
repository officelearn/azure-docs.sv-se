---
title: Skala mediebearbetning med Azure-portalen | Microsoft-dokument
description: Den här självstudien går igenom stegen för att skala mediebearbetning med Hjälp av Azure-portalen.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "61127557"
---
# <a name="change-the-reserved-unit-type"></a>Ändra den reserverade enhetstypen
> [!div class="op_single_selector"]
> * [.NET](media-services-dotnet-encoding-units.md)
> * [Portal](media-services-portal-scale-media-processing.md)
> * [Resten](https://docs.microsoft.com/rest/api/media/operations/encodingreservedunittype)
> * [Java](https://github.com/southworkscom/azure-sdk-for-media-services-java-samples)
> * [Php](https://github.com/Azure/azure-sdk-for-php/tree/master/examples/MediaServices)
> 
> 

## <a name="overview"></a>Översikt

Ett Media Services-konto är kopplat till en typ av reserverad enhet som bestämmer hur snabbt mediebearbetningsuppgifter ska bearbetas. Du kan välja mellan följande typer av reserverade enheter: **S1**, **S2** och **S3**. Samma kodningsjobb körs till exempel snabbare om du använder typen **S2** än om du använder typen **S1**.

Förutom att ange den reserverade enhetstypen kan du ange att ditt konto ska etableras med reserverade enheter ( RU: **er).** Antalet etablerade RU:er anger antalet medieuppgifter som kan bearbetas samtidigt i en viss konto.

>[!NOTE]
>RU:er fungerar för parallellisera all bearbetning av media, inklusive indexeringsjobb med hjälp av Azure Media Indexer. Men till skillnad från kodning bearbetas inte indexeringsjobb snabbare med snabbare reserverade enheter.

> [!IMPORTANT]
> Se till att granska [översiktsavsnittet](media-services-scale-media-processing-overview.md) för att få mer information om hur du skalar mediebearbetningsämnet.
> 
> 

## <a name="scale-media-processing"></a>Skala mediebearbetning
Så här ändrar du den reserverade enhetstypen och antalet reserverade enheter:

1. Välj ditt Azure Media Services-konto i [Azure-portalen](https://portal.azure.com/).
2. Välj **Mediereserverade enheter**i fönstret **Inställningar** .
   
    Om du vill ändra antalet reserverade enheter för den valda reserverade enhetstypen använder du skjutreglaget **Media Served Units** högst upp på skärmen.
   
    Om du vill ändra den **reserverade ENHETSTYPEN**klickar du på **fältet Hastighet för reserverade bearbetningsenheter.** Välj sedan den prisnivå du behöver: S1, S2 eller S3.
   
3. Tryck på knappen SPARA för att spara ändringarna.
   
    De nya reserverade enheterna fördelas när du trycker på SPARA.

## <a name="next-steps"></a>Nästa steg
Granska sökvägarna för Media Services-utbildning.

[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Ge feedback
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

