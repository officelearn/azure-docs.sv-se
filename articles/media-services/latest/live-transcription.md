---
title: Azure Media Services Live-avskrift | Microsoft Docs
description: Den här artikeln förklarar vad Azure Media Services Live-avskriften är.
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
ms.openlocfilehash: 95cd759a5ef4f5f67ecf56e60595e709bbc8b44f
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/29/2020
ms.locfileid: "76845693"
---
# <a name="live-transcription-preview"></a>Direkt avskrift (för hands version)

Azure Media service levererar video, ljud och nu text i olika protokoll. När du publicerar din Live Stream med MPEG-streck eller HLS/CMAF, tillsammans med video och ljud, kommer vår tjänst att leverera den inskrivna texten i IMSC 1.1-kompatibla TTML, förpackad i MPEG-4 del 30-fragment (ISO/IEC 14496-30). Om du använder leverans via HLS/TS levereras texten som segmenterad VTT. 

I den här artikeln beskrivs hur du aktiverar direkt avskrift när du strömmar en Live-händelse med Azure Media Services v3. Innan du fortsätter bör du kontrol lera att du är van att använda Media Services v3 REST-API: er (se [den här självstudien](stream-files-tutorial-with-rest.md) för mer information). Du bör också vara bekant med [Live streaming](live-streaming-overview.md) -konceptet. Vi rekommenderar att du slutför [Stream Live med Media Services](stream-live-tutorial-with-api.md) själv studie kurs. 

> [!NOTE]
> Direkt avskrift är för närvarande endast tillgängligt som en förhands gransknings funktion i regionen USA, västra 2. Det stöder avskrift av talade ord på engelska till text. API-referensen för den här funktionen finns i det här dokumentet – eftersom det är en för hands version är informationen inte tillgänglig i våra REST-dokument. 

## <a name="creating-the-live-event"></a>Skapa Live-händelsen 

Om du vill skapa en Live-händelse skickar du åtgärden skicka till 2019-05-01 – för hands version, till exempel: 

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

Du bör avsöka status för direkt sändningen tills den hamnar i tillståndet "körs", vilket innebär att du nu kan skicka ett bidrags-RTMP-flöde. Du kan nu följa samma steg som i den här självstudien, till exempel för att kontrol lera förhands gransknings flödet och skapa Live-utdata. 

## <a name="delivery-and-playback"></a>Leverans och uppspelning 

Läs artikeln [Översikt över dynamisk paketering](dynamic-packaging-overview.md#to-prepare-your-source-files-for-delivery) i hur tjänsten använder dynamisk paketering för att leverera video, ljud och nu text i olika protokoll. När du publicerar din Live Stream med MPEG-streck eller HLS/CMAF, tillsammans med video och ljud, kommer vår tjänst att leverera den inskrivna texten i IMSC 1.1-kompatibla TTML, förpackad i MPEG-4 del 30-fragment (ISO/IEC 14496-30). Om du använder leverans via HLS/TS levereras texten som segmenterad VTT. Du kan använda en webb spelare som [Azure Media Player](use-azure-media-player.md) för att spela upp strömmen.  

> [!NOTE]
>  Om du använder Azure Media Player använder du version 2.3.3 eller senare.

## <a name="known-issues"></a>Kända problem 

I för hands versionen följer de kända problemen med direkt avskriftering 

* Funktionen är endast tillgänglig i USA, västra 2.
* Program måste använda API: erna för för hands versionen som beskrivs i specifikationen [Media Services v3 openapi Specification](https://github.com/Azure/azure-rest-api-specs/blob/master/specification/mediaservices/resource-manager/Microsoft.Media/preview/2019-05-01-preview/streamingservice.json) .
* Det enda språk som stöds är engelska (en-US).
* Med avseende på innehålls skydd stöds endast kryptering med AES-kuvert.

## <a name="next-steps"></a>Nästa steg

[Översikt över Media Services](media-services-overview.md)
