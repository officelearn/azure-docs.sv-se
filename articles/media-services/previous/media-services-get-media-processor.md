---
title: Skapa en medieprocessor med Hjälp av SDK för Azure Media Services för .NET| Microsoft-dokument
description: Lär dig hur du skapar en medieprocessorkomponent för att koda, konvertera format, kryptera eller dekryptera medieinnehåll för Azure Media Services. Kodexempel skrivs i C# och använder Media Services SDK för .NET.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "61463862"
---
# <a name="how-to-get-a-media-processor-instance"></a>Så här hämtar du en mediaprocessorinstans
> [!div class="op_single_selector"]
> * [.NET](media-services-get-media-processor.md)
> * [Resten](media-services-rest-get-media-processor.md)
> 
> 

## <a name="overview"></a>Översikt
I Media Services är en medieprocessor en komponent som hanterar en viss bearbetningsuppgift, till exempel kodning, formatkonvertering, kryptering eller dekryptering av medieinnehåll. Du skapar vanligtvis en medieprocessor när du skapar en uppgift för att koda, kryptera eller konvertera formatet för medieinnehåll.

## <a name="azure-media-processors"></a>Azure-mediebehandlare 

Följande avsnitt innehåller listor över medieprocessorer:

* [Mediebearbetare för kodning](scenarios-and-availability.md#encoding-media-processors)
* [Mediebearbetare för analys](scenarios-and-availability.md#analytics-media-processors)

## <a name="get-media-processor"></a>Hämta medieprocessor

Följande metod visar hur du skaffar en medieprocessorinstans. Kodexemplet förutsätter att en variabel på modulnivå med namnet **_context** refererar till serverkontexten enligt beskrivningen i avsnittet [Så här ansluter du till Media Services Programmatiskt](media-services-use-aad-auth-to-access-ams-api.md).

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

## <a name="next-steps"></a>Efterföljande moment
Nu när du vet hur du hämtar en medieprocessorinstans går du till avsnittet [Så här kodar du ett tillgångsämne](media-services-dotnet-encode-with-media-encoder-standard.md) som visar hur du använder Media Encoder Standard för att koda en tillgång.

