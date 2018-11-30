---
title: LiveEvent svarstid i Azure Media Services | Microsoft Docs
description: Det här avsnittet ger en översikt över LiveEvent svarstid och visar hur du ställer in låg latens.
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
ms.date: 11/16/2018
ms.author: juliako
ms.openlocfilehash: a74f2e53127b506f42ff49018c3df2985396646d
ms.sourcegitcommit: eba6841a8b8c3cb78c94afe703d4f83bf0dcab13
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/29/2018
ms.locfileid: "52619832"
---
# <a name="liveevent-latency-in-media-services"></a>LiveEvent svarstid i Media Services

Den här artikeln visar hur du anger med låg latens för en **LiveEvent**. Det innehåller också information om typiska resultat som visas när du använder med låg latens-inställningar i olika spelare. Resultatet varierar beroende på CDN och nätverksfördröjning. 

Du använder den nya **LowLatency** funktionen kan du ange den **StreamOptionsFlag** till **LowLatency** på den **LiveEvent**. När den är igång, kan du använda den [Azure Media Player](http://ampdemo.azureedge.net/) (AMP) demonstration sidan och ange uppspelningsalternativ att använda ”låg latens heuristik profilen”.

I följande .NET-exempel visas hur du ställer in **LowLatency** på den **LiveEvent**:

```csharp
LiveEvent liveEvent = new LiveEvent(
            location: mediaService.Location, 
            description: "Sample LiveEvent for testing",
            vanityUrl: false,
            encoding: new LiveEventEncoding(
                        // Set this to Basic to enable a transcoding LiveEvent, and None to enable a pass-through LiveEvent
                        encodingType:LiveEventEncodingType.None, 
                        presetName:null
                    ),
            input: new LiveEventInput(LiveEventInputProtocol.RTMP,liveEventInputAccess), 
            preview: liveEventPreview,
            streamOptions: new List<StreamOptionsFlag?>()
            {
                // Set this to Default or Low Latency
                // To use low latency optimally, you should tune your encoder settings down to 1 second GOP size instead of 2 seconds.
                StreamOptionsFlag.LowLatency
            }
        );
```                

Se fullständiga exempel: [MediaV3LiveApp](https://github.com/Azure-Samples/media-services-v3-dotnet-core-tutorials/blob/master/NETCore/Live/MediaV3LiveApp/Program.cs#L126).

## <a name="pass-through-liveevents-latency"></a>Direkt LiveEvents svarstid

Tabellen nedan visar typiska resultat för svarstid (när LowLatency-flaggan är aktiverad) i Media Services, mätt från dess bidrag feeden når tjänsten när en spelare kan begära uppspelning.

||2S GOP låg latens som aktiverats|1s GOP låg latens som aktiverats|
|---|---|---|
|BINDESTRECK i AMP|10-tal|8S|
|HLS på interna iOS player|14s|10-tal|

> [!NOTE]
> Svarstiden slutpunkt till slutpunkt kan variera beroende på lokala nätverket eller genom att introducera ett CDN-cachelagring lager. Du bör testa dina exakta konfigurationer.

## <a name="next-steps"></a>Nästa steg

- [Direktsänd strömning översikt](live-streaming-overview.md)
- [Live direktuppspelning självstudien](stream-live-tutorial-with-api.md)

