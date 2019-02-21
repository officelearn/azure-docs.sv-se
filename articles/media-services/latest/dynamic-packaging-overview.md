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
ms.date: 02/19/2019
ms.author: juliako
ms.openlocfilehash: d1d07402bca5f01cf63d0b039c085e46bb0f0d62
ms.sourcegitcommit: 6cab3c44aaccbcc86ed5a2011761fa52aa5ee5fa
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/20/2019
ms.locfileid: "56447930"
---
# <a name="dynamic-packaging"></a>Dynamisk paketering

Microsoft Azure medietjänster kan användas för att leverera många källa mediefilformat, format, för direktuppspelning och innehållsskydd formaterar till en mängd olika tekniker för klient (till exempel iOS och XBOX). Dessa klienter förstå olika protokoll, till exempel iOS kräver en HTTP Live Streaming (HLS)-format och Xbox kräver Smooth Streaming. Om du har en uppsättning med anpassningsbar bithastighet (multibithastighet) MP4 (ISO Base 14496-12) mediefiler eller en uppsättning med anpassningsbar bithastighet Smooth Streaming-filer som du vill ska fungera för klienter som förstå HLS, MPEG DASH och Smooth Streaming, bör du dra nytta av Media Tjänster för dynamisk paketering.

Med dynamisk paketering är allt du behöver för att skapa en tillgång som innehåller en uppsättning MP4-filer. Sedan, baserat på formatet som anges i begäran manifest- eller fragmentdel strömning på begäran kommer servern att säkerställa att du får dataströmmen i protokollet som du har valt. Detta innebär att du bara behöver lagra och betala för filerna i ett enda lagringsformat, och Media Services-tjänsten skapar och ger lämplig respons baserat på begäranden från en klient.

Följande diagram visar traditionella kodning och statisk paketering arbetsflöde.

![Statisk kodning](./media/dynamic-packaging-overview/media-services-static-packaging.png)

Följande diagram visar arbetsflödet för dynamisk paketering.

![Dynamic Encoding](./media/dynamic-packaging-overview/media-services-dynamic-packaging.png)

## <a name="dynamic-packaging-workflow"></a>Arbetsflöde för dynamisk paketering

1. Ladda upp en indatafil (kallas en mezzaninfil). Till exempel H.264 MP4 eller WMV (lista över de format som stöds finns i [format som stöds av Media Encoder Standard](media-encoder-standard-formats.md).
2. Koda din mezzaninfil för H.264 MP4 med anpassningsbar bithastighet uppsättningar.
3. Publicera tillgången med anpassningsbar bithastighet MP4-uppsättningen.
4. Skapa strömmande URL: er för att komma åt och strömma ditt innehåll.

## <a name="audio-codecs-supported-by-dynamic-packaging"></a>Ljud-codec som stöds av dynamisk paketering

Dynamisk paketering stöder MP4-filer som innehåller ljud kodad med [AAC](https://en.wikipedia.org/wiki/Advanced_Audio_Coding) (AAC-LC, HE-AAC v1, HE-AAC v2), [Dolby Digital Plus](https://en.wikipedia.org/wiki/Dolby_Digital_Plus) (förbättrad AC-3 eller E-AC3) eller [DTS](https://en.wikipedia.org/wiki/DTS_%28sound_system%29) DTS ( Express, DTS LBR, DTS HD, DTS HD förlustfri).

> [!NOTE]
> Dynamisk paketering har inte stöd för filer som innehåller [Dolby Digital](https://en.wikipedia.org/wiki/Dolby_Digital) (AC3) ljud (det är en äldre codec).

## <a name="next-steps"></a>Nästa steg

[Överför, koda, strömma videor](stream-files-tutorial-with-api.md)
