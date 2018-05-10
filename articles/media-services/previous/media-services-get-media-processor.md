---
title: Så här skapar du en medieprocessor med Azure Media Services SDK för .NET | Microsoft Docs
description: Lär dig hur du skapar ett media processorkomponenten för att koda, konvertera formatet, kryptera eller dekryptera medieinnehåll för Azure Media Services. Kodexemplen är skrivna i C# och använder Media Services SDK för .NET.
services: media-services
documentationcenter: ''
author: juliako
manager: cfowler
editor: ''
ms.assetid: dbf9496f-c6f0-42a7-aa36-70f89dcb8ea2
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/31/2017
ms.author: juliako
ms.openlocfilehash: 60da450c11a2e65d96c15798854adfef371a694f
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/07/2018
---
# <a name="how-to-get-a-media-processor-instance"></a>Så här: hämta en Media-processor
> [!div class="op_single_selector"]
> * [.NET](media-services-get-media-processor.md)
> * [REST](media-services-rest-get-media-processor.md)
> 
> 

## <a name="overview"></a>Översikt
Formatera konvertering krypterar eller dekrypterar medieinnehåll i Media Services en medieprocessor är en komponent som hanterar en specifik bearbetning aktivitet, till exempel kodning. Du skapar en medieprocessor normalt när du skapar en uppgift att koda, kryptera eller konvertera formatet för medieinnehåll.

## <a name="azure-media-processors"></a>Azure media-processorer 

Följande avsnitt innehåller en lista över media processorer:

* [Kodning media-processorer](scenarios-and-availability.md#encoding-media-processors)
* [Analytics-medieprocessorer](scenarios-and-availability.md#analytics-media-processors)

## <a name="get-media-processor"></a>Hämta Medieprocessor

Följande metod visar hur du hämtar en media-processor. Kodexemplet förutsätter användning av en modul-nivå variabel med namnet **_context** refererar serverkontext enligt beskrivningen i avsnittet [så här: ansluta till Media Services programmässigt](media-services-use-aad-auth-to-access-ams-api.md).

    private static IMediaProcessor GetLatestMediaProcessorByName(string mediaProcessorName)
    {
        var processor = _context.MediaProcessors.Where(p => p.Name == mediaProcessorName).
        ToList().OrderBy(p => new Version(p.Version)).LastOrDefault();

        if (processor == null)
        throw new ArgumentException(string.Format("Unknown media processor", mediaProcessorName));

        return processor;
    }


## <a name="media-services-learning-paths"></a>Sökvägar för Media Services-utbildning
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Ge feedback
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="next-steps"></a>Nästa steg
Nu när du vet hur du hämtar en media processor, gå till den [koda en tillgång](media-services-dotnet-encode-with-media-encoder-standard.md) avsnittet som visar hur du använder Media Encoder Standard för att koda en tillgång.

