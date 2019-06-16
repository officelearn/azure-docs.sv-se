---
title: Strömning principer i Azure Media Services | Microsoft Docs
description: Den här artikeln innehåller en förklaring av vad Streaming principer är och hur de används av Azure Media Services.
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
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/13/2019
ms.locfileid: "66392928"
---
# <a name="streaming-policies"></a>Principer för direktuppspelning

I Azure Media Services v3 [Streaming principer](https://docs.microsoft.com/rest/api/media/streamingpolicies) gör det möjligt att definiera strömningsprotokoll och alternativ för kryptering för din [positionerare för direktuppspelning](streaming-locators-concept.md). Media Services v3 innehåller vissa fördefinierade Streaming principer så att du kan använda dem direkt för utvärderingsversionen eller produktion. 

De tillgängliga fördefinierade Streaming principer:<br/>
* 'Predefined_DownloadOnly'
* 'Predefined_ClearStreamingOnly'
* 'Predefined_DownloadAndClearStreaming'
* 'Predefined_ClearKey'
* 'Predefined_MultiDrmCencStreaming' 
* 'Predefined_MultiDrmStreaming'

Följande ”beslutsträdet” hjälper dig att välja en fördefinierad Streaming princip för ditt scenario.

> [!IMPORTANT]
> * Egenskaper för **Streaming principer** som är av typen är alltid i UTC-format för datum/tid.
> * Du bör utforma en begränsad uppsättning principer för ditt Media Services-konto och återanvända dem för din positionerare för direktuppspelning när samma alternativ behövs. Mer information finns i [kvoter och begränsningar](limits-quotas-constraints.md).

## <a name="decision-tree"></a>Beslutsträd

Klicka på bilden för att visa den i full storlek.  

<a href="./media/streaming-policy/large.png" target="_blank"><img src="./media/streaming-policy/large.png"></a> 

Om kryptera ditt innehåll, måste du skapa en [innehåll nyckel princip](content-key-policy-concept.md), **innehåll nyckel princip** behövs inte för Rensa streaming eller ladda ned. 

Om du har särskilda krav (t.ex, om du vill ange olika protokoll, måste du använda en anpassad nyckelleveranstjänst eller måste du använda en Rensa ljudspår), kan du [skapa](https://docs.microsoft.com/rest/api/media/streamingpolicies/create) en anpassad princip för direktuppspelning. 

## <a name="get-a-streaming-policy-definition"></a>Hämta en strömmande principdefinition  

Om du vill se definitionen av en princip för direktuppspelning använder [hämta](https://docs.microsoft.com/rest/api/media/streamingpolicies/get) och ange namnet på principen. Exempel:

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

## <a name="filtering-ordering-paging"></a>Filtrering, skrivordning, växling

Se [filtrering, sortering, växling av Media Services entiteter](entities-overview.md).

## <a name="next-steps"></a>Nästa steg

* [Strömma en fil](stream-files-dotnet-quickstart.md)
* [Använda dynamisk kryptering för AES-128 och nyckelleveranstjänst](protect-with-aes128.md)
* [Använda DRM dynamisk kryptering och licens video-on-demand](protect-with-drm.md)
