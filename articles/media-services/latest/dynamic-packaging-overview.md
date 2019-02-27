---
title: Azure Media Services dynamisk paketering översikt | Microsoft Docs
description: Avsnittet ger en översikt över dynamisk paketering i Media Services.
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
ms.date: 02/25/2019
ms.author: juliako
ms.openlocfilehash: f20a242fc5f674738cde6af7d2797205f8298514
ms.sourcegitcommit: 50ea09d19e4ae95049e27209bd74c1393ed8327e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/26/2019
ms.locfileid: "56871323"
---
# <a name="dynamic-packaging"></a>Dynamisk paketering

Microsoft Azure medietjänster kan användas för att leverera många källa mediefilformat, format, för direktuppspelning och innehållsskydd formaterar till en mängd olika tekniker för klient (till exempel iOS och XBOX). Dessa klienter förstå olika protokoll, till exempel iOS kräver en HTTP Live Streaming (HLS)-format och Xbox kräver Smooth Streaming. Om du har en uppsättning med anpassningsbar bithastighet (multibithastighet) MP4 (ISO Base 14496-12) mediefiler eller en uppsättning med anpassningsbar bithastighet Smooth Streaming-filer som du vill ska fungera för klienter som förstå HLS, MPEG DASH och Smooth Streaming, kan du dra nytta av dynamisk Paketering. Förpackningen är oberoende av video lösning, SD/HD/UHD - 4K stöds.

[Slutpunkter för direktuppspelning](streaming-endpoint-concept.md) är tjänsten dynamisk paketering i Media Services som används för att leverera medieinnehåll till klienten spelare. Dynamisk paketering är en funktion som levereras som standard på alla slutpunkter för direktuppspelning (Standard eller Premium). Det tillkommer ingen extra kostnad som hör till den här funktionen i Media Services v3. Med dynamisk paketering allt du behöver är en tillgång som innehåller en uppsättning MP4-filer med manifestfiler. Sedan, baserat på formatet som anges i manifestet- eller fragmentdel förfrågan, du får dataströmmen i protokollet som du har valt. Detta innebär att du bara behöver lagra och betala för filerna i ett enda lagringsformat, och Media Services-tjänsten skapar och ger lämplig respons baserat på begäranden från en klient.

I Media Services används dynamisk paketering oavsett om du strömning på begäran eller live.

Följande diagram visar strömning på begäran med dynamisk paketering arbetsflöde.

![Dynamic Encoding](./media/dynamic-packaging-overview/media-services-dynamic-packaging.svg)

## <a name="common-video-on-demand-workflow"></a>Vanligt arbetsflöde för video på begäran

Följande är en gemensam Media Services streaming arbetsflöde där dynamisk paketering används.

1. Ladda upp en indatafil (kallas en mezzaninfil). Till exempel H.264 MP4 eller WMV (lista över de format som stöds finns i [format som stöds av Media Encoder Standard](media-encoder-standard-formats.md).
2. Koda din mezzaninfil för H.264 MP4 med anpassningsbar bithastighet uppsättningar.
3. Publicera tillgången med anpassningsbar bithastighet MP4-uppsättningen.
4. Skapa URL: er som är riktade till olika format (HLS, Dash och Smooth Streaming). Slutpunkt för direktuppspelning skulle ta hand om betjänar rätt manifestet och begäranden för dessa olika format. Exempel:

    - HLS: `https://amsv3account-usw22.streaming.media.azure.net/21b17732-0112-4d76-b526-763dcd843449/ignite.ism/manifest(format=m3u8-aapl)`
    - Dash: `https://amsv3account-usw22.streaming.media.azure.net/21b17732-0112-4d76-b526-763dcd843449/ignite.ism/manifest(format=mpd-time-csf)`
    - Smooth: `https://amsv3account-usw22.streaming.media.azure.net/21b17732-0112-4d76-b526-763dcd843449/ignite.ism/manifest`

## <a name="video-codecs-supported-by-dynamic-packaging"></a>Codec för indatavideo stöds av dynamisk paketering

Dynamisk paketering stöder MP4-filer som innehåller video kodad med [H.264](https://en.m.wikipedia.org/wiki/H.264/MPEG-4_AVC) (MPEG-4 AVC eller AVC1) [H.265](https://en.m.wikipedia.org/wiki/High_Efficiency_Video_Coding) (– HEVC, hev1 eller hvc1).

## <a name="audio-codecs-supported-by-dynamic-packaging"></a>Ljud-codec som stöds av dynamisk paketering

Dynamisk paketering stöder MP4-filer som innehåller ljud kodad med [AAC](https://en.wikipedia.org/wiki/Advanced_Audio_Coding) (AAC-LC, HE-AAC v1, HE-AAC v2), [Dolby Digital Plus](https://en.wikipedia.org/wiki/Dolby_Digital_Plus) (förbättrad AC-3 eller E-AC3) eller [DTS](https://en.wikipedia.org/wiki/DTS_%28sound_system%29) DTS ( Express, DTS LBR, DTS HD, DTS HD förlustfri).

> [!NOTE]
> Dynamisk paketering har inte stöd för filer som innehåller [Dolby Digital](https://en.wikipedia.org/wiki/Dolby_Digital) (AC3) ljud (det är en äldre codec).

## <a name="next-steps"></a>Nästa steg

[Överför, koda, strömma videor](stream-files-tutorial-with-api.md)

