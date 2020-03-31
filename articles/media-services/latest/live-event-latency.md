---
title: Inställningar för låg latens i LiveEvent i Azure Media Services | Microsoft-dokument
description: Det här avsnittet innehåller en översikt över inställningarna för låg latens i LiveEvent och visar hur du ställer in låg latens.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: ne
ms.topic: article
ms.date: 04/22/2019
ms.author: juliako
ms.openlocfilehash: a82a0644fac099b568ab86ea213b98cd8e7d5c22
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "78199656"
---
# <a name="live-event-low-latency-settings"></a>Inställningar för låg latens i Live Event

Den här artikeln visar hur du ställer in låg latens på en [Live Event](https://docs.microsoft.com/rest/api/media/liveevents). Den diskuterar också typiska resultat som du ser när du använder låg latens inställningar i olika spelare. Resultaten varierar beroende på CDN och nätverksfördröjning.

Om du vill använda den nya **LowLatency-funktionen** ställer du in **StreamOptionsFlag** till **LowLatency** på **LiveEvent**. När du skapar [LiveOutput](https://docs.microsoft.com/rest/api/media/liveoutputs) för HLS-uppspelning ställer du in [LiveOutput.Hls.fragmentsPerTsSegment](https://docs.microsoft.com/rest/api/media/liveoutputs/create#hls) till 1. När strömmen är igång kan du använda [demosidan för Azure Media Player](https://ampdemo.azureedge.net/) (AMP) och ange uppspelningsalternativen för att använda profilen "Låg latens heuristik".

> [!NOTE]
> För närvarande är LowLatency HeuristicProfile i Azure Media Player utformad för att spela upp strömmar i MPEG-DASH-protokollet, med antingen CSF- eller CMAF-format (till exempel `format=mdp-time-csf` eller `format=mdp-time-cmaf`). 

Följande .NET-exempel visar hur du ställer in **LowLatency** på **LiveEvent:**

```csharp
LiveEvent liveEvent = new LiveEvent(
            location: mediaService.Location, 
            description: "Sample LiveEvent for testing",
            vanityUrl: false,
            encoding: new LiveEventEncoding(
                        // Set this to Standard to enable a transcoding LiveEvent, and None to enable a pass-through LiveEvent
                        encodingType:LiveEventEncodingType.None, 
                        presetName:null
                    ),
            input: new LiveEventInput(LiveEventInputProtocol.RTMP,liveEventInputAccess), 
            preview: liveEventPreview,
            streamOptions: new List<StreamOptionsFlag?>()
            {
                // Set this to Default or Low Latency
                // To use low latency optimally, you should tune your encoder settings down to 1 second "Group Of Pictures" (GOP) length instead of 2 seconds.
                StreamOptionsFlag.LowLatency
            }
        );
```                

Se hela exemplet: [MediaV3LiveApp](https://github.com/Azure-Samples/media-services-v3-dotnet-core-tutorials/blob/master/NETCore/Live/MediaV3LiveApp/Program.cs#L126).

## <a name="live-events-latency"></a>Svarstid för livehändelser

Följande tabeller visar typiska resultat för svarstid (när flaggan LowLatency är aktiverad) i Media Services, mätt från den tidpunkt då bidragsflödet når tjänsten till när en tittare ser uppspelningen på spelaren. Om du vill använda den låga latensen optimalt bör du justera kodarinställningarna till 1 sekunds "Grupp av bilder" (GOP) längd. När du använder en högre GOP-längd minimerar du bandbreddsförbrukningen och minskar bithastigheten under samma bildhastighet. Det är särskilt fördelaktigt i videor med mindre rörelse.

### <a name="pass-through"></a>Direkt 

||2s GOP låg latens aktiverad|1s GOP låg latens aktiverad|
|---|---|---|
|DASH i AMP|10-talet|8s (8)|
|HLS på inbyggt iOS-spelare|14s|10-talet|

### <a name="live-encoding"></a>Live Encoding

||2s GOP låg latens aktiverad|1s GOP låg latens aktiverad|
|---|---|---|
|DASH i AMP|14s|10-talet|
|HLS på inbyggt iOS-spelare|18s|13s|

> [!NOTE]
> Svarstiden från slutna till kan variera beroende på lokala nätverksförhållanden eller genom att införa ett CDN-cachelagringslager. Du bör testa dina exakta konfigurationer.

## <a name="next-steps"></a>Nästa steg

- [Översikt över livestreaming](live-streaming-overview.md)
- [Självstudiekurs för livestreaming](stream-live-tutorial-with-api.md)

