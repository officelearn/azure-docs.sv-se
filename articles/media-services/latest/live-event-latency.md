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
ms.date: 04/22/2019
ms.author: juliako
ms.openlocfilehash: 393b87aeed759950b946ccb45a008da9af4b7ebe
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2019
ms.locfileid: "64702794"
---
# <a name="live-event-latency-in-media-services"></a>Live-händelse svarstid i Media Services

Den här artikeln visar hur du anger med låg latens för en [direktsänd händelse](https://docs.microsoft.com/rest/api/media/liveevents). Det innehåller också information om typiska resultat som visas när du använder med låg latens-inställningar i olika spelare. Resultatet varierar beroende på CDN och nätverksfördröjning.

Du använder den nya **LowLatency** funktionen kan du ange den **StreamOptionsFlag** till **LowLatency** på den **LiveEvent**. När du skapar [LiveOutput](https://docs.microsoft.com/rest/api/media/liveoutputs) HLS uppspelning ange [LiveOutput.Hls.fragmentsPerTsSegment](https://docs.microsoft.com/rest/api/media/liveoutputs/create#hls) till 1. När den är igång, kan du använda den [Azure Media Player](https://ampdemo.azureedge.net/) (AMP demo sidan) och ange uppspelningsalternativ att använda ”låg latens heuristik profilen”.

> [!NOTE]
> För närvarande LowLatency HeuristicProfile i Azure Media Player har utformats för att spela strömmar i MPEG-DASH-protokollet, med CSF eller CMAF format (till exempel `format=mdp-time-csf` eller `format=mdp-time-cmaf`). 

I följande .NET-exempel visas hur du ställer in **LowLatency** på den **LiveEvent**:

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

Se fullständiga exempel: [MediaV3LiveApp](https://github.com/Azure-Samples/media-services-v3-dotnet-core-tutorials/blob/master/NETCore/Live/MediaV3LiveApp/Program.cs#L126).

## <a name="live-events-latency"></a>Direktsända händelser svarstid

Följande tabeller visar vanliga resultat för svarstid (när LowLatency-flaggan är aktiverad) i Media Services, mätt från dess bidrag feeden når tjänsten när en tittare ser uppspelningen i spelaren. Om du vill använda med låg latens optimalt, bör du finjustera inställningarna encoder ned till 1 sekund ”grupp av bilder” (GOP) längd. När du använder en högre GOP-längd kan du minimera bandbreddsanvändning och minska bithastighet under samma bildfrekvens. Det är särskilt bra i videofilmer med mindre rörelse.

### <a name="pass-through"></a>Direkt 

||2S GOP låg latens som aktiverats|1s GOP låg latens som aktiverats|
|---|---|---|
|BINDESTRECK i AMP|10-tal|8S|
|HLS på interna iOS player|14s|10-tal|

### <a name="live-encoding"></a>Live Encoding

||2S GOP låg latens som aktiverats|1s GOP låg latens som aktiverats|
|---|---|---|
|BINDESTRECK i AMP|14s|10-tal|
|HLS på interna iOS player|18s|13s|

> [!NOTE]
> Svarstiden slutpunkt till slutpunkt kan variera beroende på lokala nätverket eller genom att introducera ett CDN-cachelagring lager. Du bör testa dina exakta konfigurationer.

## <a name="next-steps"></a>Nästa steg

- [Direktsänd strömning översikt](live-streaming-overview.md)
- [Live direktuppspelning självstudien](stream-live-tutorial-with-api.md)

