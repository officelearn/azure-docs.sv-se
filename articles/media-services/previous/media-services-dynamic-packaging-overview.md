---
title: Azure Media Services dynamisk paketering översikt | Microsoft Docs
description: Avsnittet ger och översikt över dynamisk paketering.
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
ms.date: 03/18/2019
ms.author: juliako
ms.openlocfilehash: 15599beb47b7f6e72b89e7776196de8e6b94844f
ms.sourcegitcommit: f331186a967d21c302a128299f60402e89035a8d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2019
ms.locfileid: "58189180"
---
# <a name="dynamic-packaging"></a>Dynamisk paketering

Microsoft Azure medietjänster kan användas för att leverera många källa mediefilformat, format, för direktuppspelning och innehållsskydd formaterar till en mängd olika tekniker för klient (till exempel iOS, XBOX, Silverlight, Windows 8). Dessa klienter förstå olika protokoll, till exempel iOS kräver ett HTTP Live Streaming (HLS) V4-format och Silverlight och Xbox kräver Smooth Streaming. Om du har en uppsättning med anpassningsbar bithastighet (multibithastighet) MP4 (ISO Base 14496-12) mediefiler eller en uppsättning med anpassningsbar bithastighet Smooth Streaming-filer som du vill ska fungera för klienter som förstå MPEG DASH, HLS och Smooth Streaming, bör du dra nytta av Media Tjänster för dynamisk paketering.

Med dynamisk paketering är allt du behöver för att skapa en tillgång som innehåller en uppsättning MP4-filer eller Smooth Streaming-filer. Sedan, baserat på formatet som anges i begäran manifest- eller fragmentdel strömning på begäran kommer servern att säkerställa att du får dataströmmen i protokollet som du har valt. Detta innebär att du bara behöver lagra och betala för filerna i ett enda lagringsformat, och Media Services-tjänsten skapar och ger lämplig respons baserat på begäranden från en klient.

Följande diagram visar traditionella kodning och statisk paketering arbetsflöde.

![Statisk kodning](./media/media-services-dynamic-packaging-overview/media-services-static-packaging.png)

Följande diagram visar arbetsflödet för dynamisk paketering.

![Dynamic Encoding](./media/media-services-dynamic-packaging-overview/media-services-dynamic-packaging.png)

## <a name="common-scenario"></a>Vanligt scenario
1. Ladda upp en indatafil (kallas en mezzaninfil). Till exempel H.264 MP4 eller WMV (lista över de format som stöds finns i [format som stöds av Media Encoder Standard](media-services-media-encoder-standard-formats.md).
2. Koda din mezzaninfil för H.264 MP4 med anpassningsbar bithastighet uppsättningar.
3. Publicera tillgången som innehåller med MP4-uppsättningen genom att skapa på begäran-positionerare för anpassningsbar bithastighet.
4. Skapa strömmande URL: er för att komma åt och strömma ditt innehåll.

## <a name="preparing-assets-for-dynamic-streaming"></a>Förbereda tillgångar för dynamisk strömning
För att förbereda din tillgång för dynamisk streaming har följande alternativ:

- [Ladda upp en huvudfilen](media-services-dotnet-upload-files.md).
- [Använd Media Encoder Standard-kodare för att producera H.264 MP4 med anpassningsbar bithastighet uppsättningar](media-services-dotnet-encode-with-media-encoder-standard.md).
- [Stream innehållet](media-services-deliver-content-overview.md).

## <a name="audio-codecs-supported-by-dynamic-packaging"></a>Ljud-codec som stöds av dynamisk paketering

Dynamisk paketering har stöd för MP4-filer (eller Smooth Streaming-filer) som innehåller ljud kodad med [AAC](https://en.wikipedia.org/wiki/Advanced_Audio_Coding) (AAC-LC, HE-AAC v1, HE-AAC v2), [Dolby Digital Plus](https://en.wikipedia.org/wiki/Dolby_Digital_Plus) (förbättrad AC-3 eller E-AC3) eller [ DTS](https://en.wikipedia.org/wiki/DTS_%28sound_system%29) (DTS Express, DTS LBR, DTS HD, DTS HD förlustfri).

> [!Note]
> Dynamisk paketering har inte stöd för filer som innehåller [Dolby Digital](https://en.wikipedia.org/wiki/Dolby_Digital) (AC3) ljud (det är en äldre codec).

## <a name="media-services-learning-paths"></a>Sökvägar för Media Services-utbildning
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Ge feedback
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

