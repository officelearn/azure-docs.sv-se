---
title: Live transkription
titleSuffix: Azure Media Services
description: Läs mer om Azure Media Services live transkription.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79499870"
---
# <a name="live-transcription-preview"></a>Live transkription (förhandsvisning)

Azure Media Service levererar video, ljud och text i olika protokoll. När du publicerar din livestream med MPEG-DASH eller HLS/CMAF, sedan tillsammans med video och ljud, levererar vår tjänst den transkriberade texten i IMSC1.1-kompatibel TTML. Leveransen är förpackad i MPEG-4 Part 30 (ISO/IEC 14496-30) fragment. Om du använder leverans via HLS/TS levereras texten som segmenterad VTT.

I den här artikeln beskrivs hur du aktiverar direktion när du streamar en Live Event med Azure Media Services v3. Innan du fortsätter kontrollerar du att du är bekant med användningen av Media Services v3 REST API:er (se den [här självstudien](stream-files-tutorial-with-rest.md) för mer information). Du bör också känna till [live streaming](live-streaming-overview.md) konceptet. Vi rekommenderar att [streama live med mediatjänsters](stream-live-tutorial-with-api.md) handledning slutförs.

> [!NOTE]
> För närvarande är live transkription endast tillgänglig som en förhandsgranskningsfunktion i regionen Västra USA 2. Den stöder transkription av talade ord på engelska till text. API-referensen för den här funktionen finns nedan – eftersom den är i förhandsversion är informationen inte tillgänglig med våra REST-dokument.

## <a name="creating-the-live-event"></a>Skapa livehändelsen

Om du vill skapa livehändelsen skickar du PUT-åtgärden till förhandsversionen 2019-05-01, till exempel:

```
PUT https://management.azure.com/subscriptions/:subscriptionId/resourceGroups/:resourceGroupName/providers/Microsoft.Media/mediaServices/:accountName/liveEvents/:liveEventName?api-version=2019-05-01-preview&autoStart=true 
```

Åtgärden har följande brödtext (där en direkt livehändelse skapas med RTMP som intagsprotokoll). Observera tillägget av en transkriptionsegenskap. Det enda tillåtna värdet för språket är en-US.

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

Avsök statusen för Live Event tills den går in i tillståndet "Kör", vilket indikerar att du nu kan skicka ett bidrag RTMP-flöde. Du kan nu följa samma steg som i den här självstudien, som att kontrollera förhandsgranskningsflödet och skapa liveutdata.

## <a name="transcription-delivery-and-playback"></a>Leverans och uppspelning av transkription

Läs [översiktsartikeln för dynamisk paketering](dynamic-packaging-overview.md#to-prepare-your-source-files-for-delivery) om hur vår tjänst använder dynamiska förpackningar för att leverera video, ljud och text i olika protokoll. När du publicerar din livestream med MPEG-DASH eller HLS/CMAF, sedan tillsammans med video och ljud, levererar vår tjänst den transkriberade texten i IMSC1.1-kompatibel TTML. Denna leverans är förpackad i MPEG-4 Del 30 (ISO/IEC 14496-30) fragment. Om du använder leverans via HLS/TS levereras texten som segmenterad VTT. Du kan använda en webbspelare som [Azure Media Player](use-azure-media-player.md) för att spela upp strömmen.  

> [!NOTE]
> Om du använder Azure Media Player använder du version 2.3.3 eller senare.

## <a name="known-issues"></a>Kända problem

För förhandsgranskning är följande kända problem med live transkription:

* Funktionen är endast tillgänglig i västra USA 2.
* Appar måste använda förhandsversionen API:er som beskrivs i [Media Services v3 OpenAPI-specifikationen](https://github.com/Azure/azure-rest-api-specs/blob/master/specification/mediaservices/resource-manager/Microsoft.Media/preview/2019-05-01-preview/streamingservice.json).
* Det enda språk som stöds är engelska (en-us).
* Med innehållsskydd stöds endast AES-kuvertkryptering.

## <a name="next-steps"></a>Nästa steg

* [Översikt över Media Services](media-services-overview.md)
