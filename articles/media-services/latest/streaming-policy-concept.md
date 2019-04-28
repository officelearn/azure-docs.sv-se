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
ms.date: 02/03/2019
ms.author: juliako
ms.openlocfilehash: 10600d8f3ff4e08b8d90f28ec15d3cb0c56bcae0
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "61230904"
---
# <a name="streaming-policies"></a>Principer för direktuppspelning

I Azure Media Services v3 [Streaming principer](https://docs.microsoft.com/rest/api/media/streamingpolicies) gör det möjligt att definiera strömningsprotokoll och alternativ för kryptering för din [positionerare för direktuppspelning](streaming-locators-concept.md). Du kan antingen använda en av principerna för direktuppspelning fördefinierade eller skapat en anpassad princip. De fördefinierade Streaming principerna tillgänglig för tillfället är: 'Predefined_DownloadOnly', 'Predefined_ClearStreamingOnly', 'Predefined_DownloadAndClearStreaming', 'Predefined_ClearKey', 'Predefined_MultiDrmCencStreaming' and 'Predefined_MultiDrmStreaming'.

> [!IMPORTANT]
> * Egenskaper för **Streaming principer** som är av typen är alltid i UTC-format för datum/tid.
> * Du bör utforma en begränsad uppsättning principer för ditt Media Services-konto och återanvända dem för din positionerare för direktuppspelning när samma alternativ behövs. 

## <a name="examples"></a>Exempel

### <a name="not-encrypted"></a>Inte krypterat

Om du vill att strömma din fil i-the-klartext (okrypterat), anger den fördefinierade tydlig policy som strömmande: att ”Predefined_ClearStreamingOnly” (i .NET, du kan använda PredefinedStreamingPolicy.ClearStreamingOnly).

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

### <a name="encrypted"></a>Krypterade 

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
* [Använda dynamisk AES-128-kryptering och tjänsten för nyckelleverans](protect-with-aes128.md)
* [Använda DRM dynamisk kryptering och licens video-on-demand](protect-with-drm.md)
