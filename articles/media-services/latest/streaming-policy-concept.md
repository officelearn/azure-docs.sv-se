---
title: Principer för direktuppspelning i Azure Media Services | Microsoft-dokument
description: Den här artikeln ger en förklaring av vad streamingprinciper är och hur de används av Azure Media Services.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 05/28/2019
ms.author: juliako
ms.openlocfilehash: a813c77e81e51bfe13e75ed6c8d0e24b4d0fa645
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "66392928"
---
# <a name="streaming-policies"></a>Principer för direktuppspelning

I Azure Media Services v3 kan du med [streamingprinciper](https://docs.microsoft.com/rest/api/media/streamingpolicies) definiera direktuppspelningsprotokoll och krypteringsalternativ för dina [streamingpositionerare](streaming-locators-concept.md). Media Services v3 innehåller vissa fördefinierade streamingprinciper så att du kan använda dem direkt för utvärderingsversion eller produktion. 

De för närvarande tillgängliga fördefinierade streamingprinciperna:<br/>
* "Predefined_DownloadOnly"
* "Predefined_ClearStreamingOnly"
* "Predefined_DownloadAndClearStreaming"
* "Predefined_ClearKey"
* "Predefined_MultiDrmCencStreaming" 
* "Predefined_MultiDrmStreaming"

Följande "Beslutsträd" hjälper dig att välja en fördefinierad streamingpolicy för ditt scenario.

> [!IMPORTANT]
> * Egenskaper för **direktuppspelningsprinciper** som är av typen Datetime är alltid i UTC-format.
> * Du bör utforma en begränsad uppsättning principer för ditt Media Service-konto och återanvända dem för dina streamingpositionerare när samma alternativ behövs. Mer information finns i [Kvoter och begränsningar](limits-quotas-constraints.md).

## <a name="decision-tree"></a>Beslutsträd

Klicka på bilden för att visa den i full storlek.  

<a href="./media/streaming-policy/large.png" target="_blank"><img src="./media/streaming-policy/large.png"></a> 

Om du krypterar ditt innehåll måste du skapa en princip för [innehållsnyckel](content-key-policy-concept.md), **innehållsnyckelpolicyn** behövs inte för att rensa direktuppspelning eller nedladdning. 

Om du har särskilda krav (till exempel om du vill ange olika protokoll, måste använda en anpassad nyckelleveranstjänst eller behöver använda ett tydligt ljudspår) kan du [skapa](https://docs.microsoft.com/rest/api/media/streamingpolicies/create) en anpassad direktuppspelningsprincip. 

## <a name="get-a-streaming-policy-definition"></a>Hämta en definition av streamingprincip  

Om du vill se definitionen av en streamingprincip använder du [Hämta](https://docs.microsoft.com/rest/api/media/streamingpolicies/get) och anger principnamnet. Ett exempel:

### <a name="rest"></a>REST

Begäran:

```
GET https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/contoso/providers/Microsoft.Media/mediaServices/contosomedia/streamingPolicies/clearStreamingPolicy?api-version=2018-07-01
```

Svar:

```
{
  "name": "clearStreamingPolicy",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/contoso/providers/Microsoft.Media/mediaservices/contosomedia/streamingPolicies/clearStreamingPolicy",
  "type": "Microsoft.Media/mediaservices/streamingPolicies",
  "properties": {
    "created": "2018-08-08T18:29:30.8501486Z",
    "noEncryption": {
      "enabledProtocols": {
        "download": true,
        "dash": true,
        "hls": true,
        "smoothStreaming": true
      }
    }
  }
}
```

## <a name="filtering-ordering-paging"></a>Filtrering, beställning, växling

Se [Filtrering, beställning, växling av Media Services-entiteter](entities-overview.md).

## <a name="next-steps"></a>Nästa steg

* [Strömma en fil](stream-files-dotnet-quickstart.md)
* [Använd dynamisk AES-128-kryptering och nyckelleveranstjänsten](protect-with-aes128.md)
* [Använda dynamisk DRM-kryptering och tjänsten för licensleverans](protect-with-drm.md)
