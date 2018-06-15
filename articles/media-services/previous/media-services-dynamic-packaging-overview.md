---
title: Azure Media Services dynamisk paketering översikt | Microsoft Docs
description: Avsnittet ger och översikt över dynamisk paketering.
author: Juliako
manager: cfowler
editor: ''
services: media-services
documentationcenter: ''
ms.assetid: 0d9e4f54-5daa-45c1-bfaa-cf09ca89b812
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/29/2017
ms.author: juliako
ms.openlocfilehash: 8f05015da1f66331413086c0e27c25cd5da75f5c
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/07/2018
ms.locfileid: "33788391"
---
# <a name="dynamic-packaging"></a>Dynamisk paketering
## <a name="overview"></a>Översikt
Microsoft Azure Media Services kan användas för att leverera många media källa filformat, direktuppspelning format, och innehållsskydd filformat till en mängd olika tekniker för klient (till exempel iOS, XBOX, Silverlight, Windows 8). Dessa klienter förstå olika protokoll, till exempel iOS kräver en HTTP Live Streaming (HLS) V4-format och Silverlight och Xbox kräver Smooth Streaming. Om du har en uppsättning med anpassningsbar bithastighet (multibithastighet) MP4 (ISO Base 14496 12) mediefiler eller en uppsättning med anpassningsbar bithastighet Smooth Streaming-filer som du vill ska fungera för klienter som förstå MPEG DASH, HLS eller Smooth Streaming, bör du dra nytta av Media Services dynamisk paketering.

Med dynamisk paketering är allt du behöver för att skapa en tillgång som innehåller en uppsättning med anpassningsbar bithastighet MP4-filer eller Smooth Streaming-filer. Sedan, baserat på formatet som anges i begäran manifest eller fragment strömning på begäran server säkerställer att du får dataströmmen i protokollet som du har valt. Detta innebär att du bara behöver lagra och betala för filerna i ett enda lagringsformat, och Media Services-tjänsten skapar och ger lämplig respons baserat på begäranden från en klient.

Följande diagram visar traditionella kodning och statiska paketering arbetsflöde.

![Statisk kodning](./media/media-services-dynamic-packaging-overview/media-services-static-packaging.png)

Följande diagram visar dynamisk paketering arbetsflödet.

![Dynamisk kodning](./media/media-services-dynamic-packaging-overview/media-services-dynamic-packaging.png)


## <a name="common-scenario"></a>Vanligt scenario
1. Överför en indatafil (kallas en mezzaninfil). Till exempel H.264 MP4 eller WMV (lista över de format som stöds finns [format som stöds av Media Encoder Standard](media-services-media-encoder-standard-formats.md).
2. Koda din mezzaninfil till H.264 MP4 med anpassad bithastighet uppsättningar.
3. Publicera tillgången som innehåller anpassad bithastighet MP4-uppsättningen genom att skapa den på begäran-positionerare.
4. Skapa strömmande URL: er för att komma åt och strömma ditt innehåll.

## <a name="preparing-assets-for-dynamic-streaming"></a>Förbereda tillgångar för dynamiska strömning
För att förbereda din tillgång för dynamiska streaming har du två alternativ:

1. [Överföra en fil med master](media-services-dotnet-upload-files.md).
2. [Om du använder Media Encoder Standard-kodare som ger H.264 MP4 med anpassad bithastighet anger](media-services-dotnet-encode-with-media-encoder-standard.md).
3. [Strömma ditt innehåll](media-services-deliver-content-overview.md).

## <a id="unsupported_formats"></a>Format som inte stöds av dynamisk paketering
Följande datakälla format stöds inte av dynamisk paketering.

* Dolby digital mp4-filer.
* Dolby digitala smooth filer.

## <a name="media-services-learning-paths"></a>Sökvägar för Media Services-utbildning
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Ge feedback
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

