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
ms.date: 05/15/2019
ms.author: juliako
ms.openlocfilehash: 510899e44e4ea4a90e21473ee6af546744c2be2a
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/23/2019
ms.locfileid: "66120206"
---
# <a name="streaming-policies"></a>Principer för direktuppspelning

I Azure Media Services v3 [Streaming principer](https://docs.microsoft.com/rest/api/media/streamingpolicies) gör det möjligt att definiera strömningsprotokoll och alternativ för kryptering för din [positionerare för direktuppspelning](streaming-locators-concept.md). Media Services v3 innehåller vissa fördefinierade Streaming principer så att du kan använda dem direkt för utvärderingsversionen eller produktion. 

De tillgängliga fördefinierade Streaming principer:<br/>'Predefined_DownloadOnly', 'Predefined_ClearStreamingOnly', 'Predefined_DownloadAndClearStreaming', 'Predefined_ClearKey', 'Predefined_MultiDrmCencStreaming' and 'Predefined_MultiDrmStreaming'.

Om du har särskilda krav (t.ex, om du vill ange olika protokoll, måste du använda en anpassad nyckelleveranstjänst eller måste du använda en Rensa ljudspår) kan skapa du en anpassad princip för direktuppspelning. 

 
> [!IMPORTANT]
> * Egenskaper för **Streaming principer** som är av typen är alltid i UTC-format för datum/tid.
> * Du bör utforma en begränsad uppsättning principer för ditt Media Services-konto och återanvända dem för din positionerare för direktuppspelning när samma alternativ behövs. Mer information finns i [kvoter och begränsningar](limits-quotas-constraints.md).

## <a name="decision-tree"></a>Beslutsträd

Följande beslutsträd kan du välja en fördefinierad Streaming princip för ditt scenario.

Klicka på bilden för att visa den i full storlek.  <br/>
<a href="./media/streaming-policy/large.png" target="_blank"><img src="./media/streaming-policy/small.png"></a> 

## <a name="examples"></a>Exempel

### <a name="not-encrypted"></a>Inte krypterat

Om du vill att strömma din fil i-the-klartext (okrypterat), anger den fördefinierade tydlig policy som strömmande: att ”Predefined_ClearStreamingOnly” (i .NET, du kan använda PredefinedStreamingPolicy.ClearStreamingOnly enum).

```csharp
StreamingLocator locator = await client.StreamingLocators.CreateAsync(
    resourceGroup,
    accountName,
    locatorName,
    new StreamingLocator
    {
        AssetName = assetName,
        StreamingPolicyName = PredefinedStreamingPolicy.ClearStreamingOnly
    });
```

### <a name="encrypted"></a>Krypterad 

Om du vill kryptera ditt innehåll med kuvert och cenc kryptering kan du ställa in principen till 'Predefined_MultiDrmCencStreaming'. Den här principen indikerar att du vill att två innehållsnycklar (kuvert och CENC) ska skapas och ställas in för positioneraren. Krypteringarna för kuvert, PlayReady och Widevine tillämpas därför (nyckeln levereras till uppspelningsklienten utifrån de konfigurerade DRM-licenserna).

```csharp
StreamingLocator locator = await client.StreamingLocators.CreateAsync(
    resourceGroup,
    accountName,
    locatorName,
    new StreamingLocator
    {
        AssetName = assetName,
        StreamingPolicyName = "Predefined_MultiDrmCencStreaming",
        DefaultContentKeyPolicyName = contentPolicyName
    });
```

Om du vill kryptera dataströmmen med CBCS (FairPlay), använder du 'Predefined_MultiDrmStreaming'.

## <a name="filtering-ordering-paging"></a>Filtrering, skrivordning, växling

Se [filtrering, sortering, växling av Media Services entiteter](entities-overview.md).

## <a name="next-steps"></a>Nästa steg

* [Strömma en fil](stream-files-dotnet-quickstart.md)
* [Använda dynamisk kryptering för AES-128 och nyckelleveranstjänst](protect-with-aes128.md)
* [Använda DRM dynamisk kryptering och licens video-on-demand](protect-with-drm.md)
