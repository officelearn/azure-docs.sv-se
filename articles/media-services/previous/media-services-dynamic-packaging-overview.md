---
title: Översikt över dynamisk paketering av Azure Media Services | Microsoft-dokument
description: I de här artiklarna finns en översikt över dynamisk paketering av Microsoft Azure Media Services.
author: Juliako
manager: femila
editor: ''
services: media-services
documentationcenter: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/21/2019
ms.author: juliako
ms.openlocfilehash: 079094965775c140c0343da98e40fd008995d45a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74901192"
---
# <a name="dynamic-packaging"></a>Dynamisk paketering

> [!div class="op_single_selector" title1="Välj den version av Media Services som du använder:"]
> * [Version 3](../latest/dynamic-packaging-overview.md)
> * [Version 2](media-services-dynamic-packaging-overview.md)

> [!NOTE]
> Inga nya funktioner läggs till i Media Services v2. <br/>Kolla in den senaste versionen, [Media Services v3](https://docs.microsoft.com/azure/media-services/latest/). Se även [migreringsvägledning från v2 till v3](../latest/migrate-from-v2-to-v3.md)

Microsoft Azure Media Services kan användas för att leverera många filformat för mediekällor, mediestreamingformat och innehållsskyddsformat till en mängd olika klienttekniker (till exempel iOS, XBOX, Silverlight, Windows 8). Dessa klienter förstår olika protokoll, till exempel iOS kräver en HTTP Live Streaming (HLS) V4-format och Silverlight och Xbox kräver smooth streaming. Om du har en uppsättning adaptiva bithastigheter (multibitrat) MP4-filer (ISO Base Media 14496-12) eller en uppsättning adaptiva bithastighets-Smooth Streaming-filer som du vill betjäna klienter som förstår MPEG DASH, HLS eller Smooth Streaming, bör du dra nytta av Media Tjänster dynamisk förpackning.

Med dynamiska förpackningar allt, du behöver är att skapa en tillgång som innehåller en uppsättning adaptiva bitrate MP4-filer eller adaptiv bitrate Smooth Streaming filer. Baserat på det angivna formatet i manifest- eller fragmentbegäran ser servern för direktuppspelning på begäran till att du får strömmen i det protokoll som du har valt. Detta innebär att du bara behöver lagra och betala för filerna i ett enda lagringsformat, och Media Services-tjänsten skapar och ger lämplig respons baserat på begäranden från en klient.

Följande diagram visar det traditionella arbetsflödet för kodning och statisk förpackning.

![Statisk kodning](./media/media-services-dynamic-packaging-overview/media-services-static-packaging.png)

I följande diagram visas arbetsflödet för dynamiska förpackningar.

![Dynamisk kodning](./media/media-services-dynamic-packaging-overview/media-services-dynamic-packaging.png)

## <a name="common-scenario"></a>Vanligt scenario

1. Ladda upp en indatafil (kallas en mezzaninfil). H.264, MP4 eller WMV (för listan över format som stöds finns till exempel [Format som stöds av Media Encoder Standard](media-services-media-encoder-standard-formats.md).
2. Koda din mezzanine fil till H.264 MP4 adaptiva bitrate uppsättningar.
3. Publicera tillgången som innehåller den adaptiva bitrate MP4-uppsättningen genom att skapa locator på begäran.
4. Skapa strömmande webbadresser för att komma åt och strömma ditt innehåll.

## <a name="preparing-assets-for-dynamic-streaming"></a>Förbereda tillgångar för dynamisk direktuppspelning

Om du vill förbereda din tillgång för dynamisk direktuppspelning har du följande alternativ:

- [Ladda upp en huvudfil](media-services-dotnet-upload-files.md).
- [Använd Media Encoder Standard-kodaren för att skapa H.264 MP4 adaptiva bitrate-uppsättningar](media-services-dotnet-encode-with-media-encoder-standard.md).
- [Strömma ditt innehåll](media-services-deliver-content-overview.md).

## <a name="audio-codecs-supported-by-dynamic-packaging"></a>Ljudcodec-enheter som stöds av dynamisk förpackning

Dynamic Packaging stöder MP4-filer, som innehåller ljud som kodas med [AAC](https://en.wikipedia.org/wiki/Advanced_Audio_Coding) (AAC-LC, HE-AAC v1, HE-AAC v2), [Dolby Digital Plus](https://en.wikipedia.org/wiki/Dolby_Digital_Plus)(Enhanced AC-3 eller E-AC3), Dolby Atmos eller [DTS](https://en.wikipedia.org/wiki/DTS_%28sound_system%29) (DTS Express, DTS LBR, DTS HD, DTS HD Lossless). Streaming av Dolby Atmos-innehåll stöds för standarder som MPEG-DASH-protokoll med antingen CSF (Common Streaming Format) eller CMAF -fragmenterad MP4 (Common Media Application Format) och via HTTP Live Streaming (HLS) med CMAF.

> [!NOTE]
> Dynamisk förpackning stöder inte filer som innehåller [Dolby Digital](https://en.wikipedia.org/wiki/Dolby_Digital) (AC3) ljud (det är en äldre codec).

## <a name="media-services-learning-paths"></a>Sökvägar för Media Services-utbildning

[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Ge feedback

[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

