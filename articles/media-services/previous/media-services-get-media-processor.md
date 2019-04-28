---
title: Så här skapar du en medieprocessor med Azure Media Services SDK för .NET | Microsoft Docs
description: Lär dig hur du skapar ett media processorkomponenten för att koda, konvertera formatet, kryptera eller dekryptera medieinnehåll för Azure Media Services. Kodexemplen är skrivna C# och använda Media Services SDK för .NET.
services: media-services
documentationcenter: ''
author: juliako
manager: femila
editor: ''
ms.assetid: dbf9496f-c6f0-42a7-aa36-70f89dcb8ea2
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/18/2019
ms.author: juliako
ms.openlocfilehash: af6badda426f1bb81d8528cfda9b8c02d55712b3
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "61463862"
---
# <a name="how-to-get-a-media-processor-instance"></a>Anvisningar: Hämta en Medieprocessor-instans
> [!div class="op_single_selector"]
> * [NET](media-services-get-media-processor.md)
> * [REST](media-services-rest-get-media-processor.md)
> 
> 

## <a name="overview"></a>Översikt
Formatera konvertering, kryptering eller dekryptering medieinnehåll i en medieprocessor är en komponent som hanterar en specifik bearbetning aktivitet, till exempel kodning, medietjänster. Du skapar vanligtvis en medieprocessor när du skapar en uppgift att koda, kryptera eller konvertera formatet för medieinnehåll.

## <a name="azure-media-processors"></a>Azure media-processorer 

Följande avsnitt innehåller en lista över mediebearbetare:

* [Mediebearbetare för kodning](scenarios-and-availability.md#encoding-media-processors)
* [Mediebearbetare för analys](scenarios-and-availability.md#analytics-media-processors)

## <a name="get-media-processor"></a>Hämta Mediebearbetare

Följande metod som visar hur du hämtar en media processorinstans. Kodexemplet förutsätter användning av en modul på servernivå variabel med namnet **_context** referera serverkontext enligt beskrivningen i avsnittet [så här: Ansluta till Media Services programmässigt](media-services-use-aad-auth-to-access-ams-api.md).

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
Nu när du vet hur du hämtar en media processorinstans går du till den [koda en tillgång](media-services-dotnet-encode-with-media-encoder-standard.md) avsnittet som visar hur du använder Media Encoder Standard för att koda en tillgång.

