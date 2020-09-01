---
title: Så här skapar du en medie processor med Azure Media Services SDK för .NET | Microsoft Docs
description: Lär dig hur du skapar en medie processor komponent för att koda, konvertera format, kryptera eller dekryptera medie innehåll för Azure Media Services. Kod exempel skrivs i C# och använder Media Services SDK för .NET.
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
ms.custom: devx-track-csharp
ms.openlocfilehash: df89acb7d3686a478c87c12bbf8a42962597dca6
ms.sourcegitcommit: bcda98171d6e81795e723e525f81e6235f044e52
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/01/2020
ms.locfileid: "89269394"
---
# <a name="how-to-get-a-media-processor-instance"></a>Gör så här: Hämta en Media processor instans

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

> [!div class="op_single_selector"]
> * [.NET](media-services-get-media-processor.md)
> * [REST](media-services-rest-get-media-processor.md)
> 
> 

## <a name="overview"></a>Översikt
I Media Services en medie processor en komponent som hanterar en speciell bearbetnings aktivitet, till exempel kodning, format konvertering, kryptering eller dekryptering av medie innehåll. Du skapar vanligt vis en medie processor när du skapar en aktivitet för att koda, kryptera eller konvertera formatet för medie innehåll.

## <a name="azure-media-processors"></a>Azure Media-processorer 

Följande avsnitt innehåller listor över medie processorer:

* [Mediebearbetare för kodning](scenarios-and-availability.md#encoding-media-processors)
* [Mediebearbetare för analys](scenarios-and-availability.md#analytics-media-processors)

## <a name="get-media-processor"></a>Hämta medie processor

Följande metod visar hur du hämtar en medie processor instans. I kod exemplet antas användningen av en variabel på modulnivå med namnet **_context** referera till Server kontexten enligt beskrivningen i avsnittet [så här: Anslut till Media Services program mässigt](media-services-use-aad-auth-to-access-ams-api.md).

```csharp
private static IMediaProcessor GetLatestMediaProcessorByName(string mediaProcessorName)
{
    var processor = _context.MediaProcessors.Where(p => p.Name == mediaProcessorName).
    ToList().OrderBy(p => new Version(p.Version)).LastOrDefault();

    if (processor == null)
    throw new ArgumentException(string.Format("Unknown media processor", mediaProcessorName));

    return processor;
}
```


## <a name="media-services-learning-paths"></a>Sökvägar för Media Services-utbildning
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Ge feedback
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="next-steps"></a>Efterföljande moment
Nu när du vet hur du hämtar en medie processor instans går du till avsnittet [så här kodar du ett till gångs](media-services-dotnet-encode-with-media-encoder-standard.md) ämne som visar hur du använder Media Encoder Standard för att koda en till gång.

