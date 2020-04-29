---
title: Live transkription
titleSuffix: Azure Media Services
description: Lär dig mer om Azure Media Services Live-avskrift.
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
ms.date: 11/19/2019
ms.author: juliako
ms.openlocfilehash: b364b6e70e3b5723c483bc3435f0c3a152c03aa9
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "79499870"
---
# <a name="live-transcription-preview"></a>Direkt avskrift (för hands version)

Azure Media service levererar video, ljud och text i olika protokoll. När du publicerar din Live Stream med MPEG-streck eller HLS/CMAF, tillsammans med video och ljud, levererar vår tjänst den inskrivna texten i IMSC 1.1-kompatibla TTML. Leveransen är paketerad i MPEG-4 del 30 (ISO/IEC 14496-30) fragment. Om du använder leverans via HLS/TS levereras texten som segmenterad VTT.

I den här artikeln beskrivs hur du aktiverar direkt avskrift när du strömmar en Live-händelse med Azure Media Services v3. Innan du fortsätter bör du kontrol lera att du är van att använda Media Services v3 REST-API: er (se [den här självstudien](stream-files-tutorial-with-rest.md) för mer information). Du bör också vara bekant med [Live streaming](live-streaming-overview.md) -konceptet. Vi rekommenderar att du slutför [Stream Live med Media Services](stream-live-tutorial-with-api.md) själv studie kurs.

> [!NOTE]
> Direkt avskrift är för närvarande endast tillgängligt som en förhands gransknings funktion i regionen USA, västra 2. Det stöder avskrift av talade ord på engelska till text. API-referensen för den här funktionen finns nedan – eftersom den är i för hands version. informationen är inte tillgänglig med våra REST-dokument.

## <a name="creating-the-live-event"></a>Skapa Live-händelsen

Om du vill skapa en Live-händelse skickar du åtgärden placera till 2019-05-01 – för hands version, till exempel:

```
PUT https://management.azure.com/subscriptions/:subscriptionId/resourceGroups/:resourceGroupName/providers/Microsoft.Media/mediaServices/:accountName/liveEvents/:liveEventName?api-version=2019-05-01-preview&autoStart=true 
```

Åtgärden har följande text (där en direkt sändnings händelse skapas med RTMP som inmatnings protokoll). Observera tillägget av en avskrifts egenskap. Det enda tillåtna värdet för språket är en-US.

```
{ 
  "properties": { 
    "description": "Demonstrate how to enable live transcriptions", 
    "input": { 
      "streamingProtocol": "RTMP", 
      "accessControl": { 
        "ip": { 
          "allow": [ 
            { 
              "name": "Allow All", 
              "address": "0.0.0.0", 
              "subnetPrefixLength": 0 
            } 
          ] 
        } 
      } 
    }, 
    "preview": { 
      "accessControl": { 
        "ip": { 
          "allow": [ 
            { 
              "name": "Allow All", 
              "address": "0.0.0.0", 
              "subnetPrefixLength": 0 
            } 
          ] 
        } 
      } 
    }, 
    "encoding": { 
      "encodingType": "None" 
    }, 
    "transcriptions": [ 
      { 
        "language": "en-US" 
      } 
    ], 
    "vanityUrl": false, 
    "streamOptions": [ 
      "Default" 
    ] 
  }, 
  "location": "West US 2" 
} 
```

Avsök status för direkt sändningen tills den hamnar i tillståndet "körs", vilket innebär att du nu kan skicka ett bidrags-RTMP-flöde. Du kan nu följa samma steg som i den här självstudien, t. ex. kontrol lera förhands visningen och skapa Live-utdata.

## <a name="transcription-delivery-and-playback"></a>Avskrifts leverans och uppspelning

Läs artikeln [Översikt över dynamisk paketering](dynamic-packaging-overview.md#to-prepare-your-source-files-for-delivery) i hur tjänsten använder dynamisk paketering för att leverera video, ljud och text i olika protokoll. När du publicerar din Live Stream med MPEG-streck eller HLS/CMAF, tillsammans med video och ljud, levererar vår tjänst den inskrivna texten i IMSC 1.1-kompatibla TTML. Den här leveransen paketeras i MPEG-4 del 30-fragment (ISO/IEC 14496-30). Om du använder leverans via HLS/TS levereras texten som segmenterad VTT. Du kan använda en webb spelare som [Azure Media Player](use-azure-media-player.md) för att spela upp strömmen.  

> [!NOTE]
> Om du använder Azure Media Player använder du version 2.3.3 eller senare.

## <a name="known-issues"></a>Kända problem

För för hands versionen är följande kända problem med direkt avskriftering:

* Funktionen är endast tillgänglig i USA, västra 2.
* Apparna måste använda API: erna för för hands versionen som beskrivs i [specifikationen Media Services v3 openapi](https://github.com/Azure/azure-rest-api-specs/blob/master/specification/mediaservices/resource-manager/Microsoft.Media/preview/2019-05-01-preview/streamingservice.json).
* Det enda språk som stöds är engelska (en-US).
* Med Content Protection stöds endast kryptering med AES-kuvert.

## <a name="next-steps"></a>Nästa steg

* [Översikt över Media Services](media-services-overview.md)
