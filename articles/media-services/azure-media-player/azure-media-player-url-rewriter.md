---
title: Url-brännare för Azure Media Player
description: Azure Media Player skriver om en viss URL från Azure Media Services för att tillhandahålla strömmar för SMOOTH, DASH, HLS v3 och HLS v4.
author: IngridAtMicrosoft
ms.author: inhenkel
ms.service: media-services
ms.topic: overview
ms.date: 04/20/2020
ms.openlocfilehash: f238a2a3c499cf1e36f5e7c40e087375b7db0a70
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/21/2020
ms.locfileid: "81726461"
---
# <a name="url-rewriter"></a>Url-brännare #

Som standard skriver Azure Media Player om en viss URL från Azure Media Services för att tillhandahålla strömmar för SMOOTH, DASH, HLS v3 och HLS v4. Om källan till exempel anges på följande sätt kommer Azure Media Player att se till att den försöker spela upp alla ovanstående protokoll:

```html
    <video id="vid1" class="azuremediaplayer amp-default-skin">
        <source src="//example/path/to/myVideo.ism/manifest" type="application/vnd.ms-sstr+xml" />
    </video>
```

Om du däremot inte vill använda url-brännaren kan `disableUrlRewriter` du göra det genom att lägga till egenskapen i parametern. Detta innebär att all information som skickas till källorna skickas direkt till spelaren utan ändringar.  Här är ett exempel på att lägga till två källor till spelaren, på DASH och en SMOOTH Streaming.

```html
    <video id="vid1" class="azuremediaplayer amp-default-skin">
        <source src="//example/path/to/myVideo.ism/manifest(format=mpd-time-csf)" type="application/dash+xml" data-setup='{"disableUrlRewriter": true}'/>
        <source src="//example/path/to/myVideo.ism/manifest" type="application/vnd.ms-sstr+xml" data-setup='{"disableUrlRewriter": true}'/>
    </video>
```

eller

```javascript
    myPlayer.src([
        { src: "//example/path/to/myVideo.ism/manifest(format=mpd-time-csf)", type: "application/dash+xml", disableUrlRewriter: true },
        { src: "//example/path/to/myVideo.ism/manifest", type: "application/vnd.ms-sstr+xml", disableUrlRewriter: true }
    ]);
```

Om du vill kan du också ange de specifika direktuppspelningsformat som du `streamingFormats` vill att Azure Media Player ska skriva om till med parametern. Alternativen `DASH`inkluderar `SMOOTH` `HLSv3`, `HLSv4` `HLS`, , , . Skillnaden mellan HLS och HLSv3 & v4 är att HLS-formatet stöder uppspelning av FairPlay-innehåll. v3 och v4 stöder inte FairPlay. Detta är användbart om du inte har en leveransprincip för ett visst protokoll tillgängligt.  Här är ett exempel på när ett DASH-protokoll inte är aktiverat med din tillgång.

```html
    <video id="vid1" class="azuremediaplayer amp-default-skin">
        <source src="//example/path/to/myVideo.ism/manifest" type="application/vnd.ms-sstr+xml" data-setup='{"streamingFormats": ["SMOOTH", "HLS","HLS-V3", "HLS-V4"] }'/>
    </video>
```

eller

```javascript
    myPlayer.src([
        { src: "//example/path/to/myVideo.ism/manifest", type: "application/vnd.ms-sstr+xml", streamingFormats: ["SMOOTH", "HLS","HLS-V3", "HLS-V4"]},
    ]);
```

Ovanstående två kan användas i kombination med varandra för flera omständigheter baserat på din tillgång.

> [!NOTE]
> Widevine skyddsinformation kvarstår endast på DASH-protokollet.

## <a name="next-steps"></a>Nästa steg ##

- [Snabbstart för Azure Media Player](azure-media-player-quickstart.md)