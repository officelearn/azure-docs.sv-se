---
title: Strömning positionerare i Azure Media Services | Microsoft Docs
description: Den här artikeln innehåller en förklaring av vad positionerare för direktuppspelning är och hur de används av Azure Media Services.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 05/26/2019
ms.author: juliako
ms.openlocfilehash: 5897b7df2460257784c40eb974c473573ec4003d
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/13/2019
ms.locfileid: "66299171"
---
# <a name="streaming-locators"></a>Positionerare för direktuppspelning

För att skapa videor i utdatatillgångar som klienter ska kunna spela upp måste du skapa en [positionerare för direktuppspelning](https://docs.microsoft.com/rest/api/media/streaminglocators) och sedan skapa direktuppspelnings-URL:er. Om du vill skapa en URL, måste du sammanfoga värdnamn för slutpunkt för direktuppspelning och Strömningspositionerare sökvägen. .NET-exempel finns i [Hämta en positionerare för direktuppspelning](stream-files-tutorial-with-api.md#get-a-streaming-locator).

Processen att skapa en **positionerare för direktuppspelning** kallas för publicering. Som standard kan din **positionerare för direktuppspelning** användas omedelbart efter API-anropen. Den fungerar tills den tas bort, såvida du inte konfigurerar valfria start- och sluttider. 

När du skapar en **Strömningspositionerare**, måste du ange en **tillgången** namn och en **Streaming princip** namn. Mer information finns i följande avsnitt:

* [Tillgångar](assets-concept.md)
* [Principer för direktuppspelning](streaming-policy-concept.md)
* [Principer för innehållsnycklar](content-key-policy-concept.md)

Du kan också ange start- och tiden på din Strömningspositionerare, bara så att användarna spela upp innehåll mellan dessa tider (till exempel mellan 5/1/2019 till 5/5/2019).  

## <a name="considerations"></a>Överväganden

* **Positionerare för direktuppspelning** kan inte uppdateras. 
* Egenskaper för **positionerare för direktuppspelning** som är av typen är alltid i UTC-format för datum/tid.
* Du bör utforma en begränsad uppsättning principer för ditt Media Services-konto och återanvända dem för din positionerare för direktuppspelning när samma alternativ behövs. Mer information finns i [kvoter och begränsningar](limits-quotas-constraints.md).

## <a name="create-streaming-locators"></a>Skapa positionerare för direktuppspelning  

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

### <a name="encrypted"></a>Krypterade 

Om du vill kryptera ditt innehåll med CENC kryptering kan du ställa in principen till 'Predefined_MultiDrmCencStreaming'. Widevine-krypteringen kommer att gälla till en DASH-strömmar och PlayReady Smooth. Nyckeln levereras till en uppspelning klient baserat på de konfigurera DRM-licenserna.

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

Om du vill kryptera din HLS-dataström med CBCS (FairPlay), använder du 'Predefined_MultiDrmStreaming'.

## <a name="associate-filters-with-streaming-locators"></a>Associera filter med positionerare för direktuppspelning

Se [filter: Koppla med strömning positionerare](filters-concept.md#associating-filters-with-streaming-locator).

## <a name="filter-order-page-streaming-locator-entities"></a>Filtrera, ordning, sidan Strömningspositionerare entiteter

Se [filtrering, sortering, växling av Media Services entiteter](entities-overview.md).

## <a name="list-streaming-locators-by-asset-name"></a>Lista Streaming positionerare av Tillgångsnamn

Hämta Streaming positionerare baserat på den associerade Tillgångsnamn med följande åtgärder:

|Språk|API|
|---|---|
|REST|[liststreaminglocators](https://docs.microsoft.com/rest/api/media/assets/liststreaminglocators)|
|CLI|[AZ ams tillgången lista-streaming-positionerare](https://docs.microsoft.com/cli/azure/ams/asset?view=azure-cli-latest#az-ams-asset-list-streaming-locators)|
|.NET|[ListStreamingLocators](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.media.assetsoperationsextensions.liststreaminglocators?view=azure-dotnet#Microsoft_Azure_Management_Media_AssetsOperationsExtensions_ListStreamingLocators_Microsoft_Azure_Management_Media_IAssetsOperations_System_String_System_String_System_String_)|
|Java|[AssetStreamingLocator](https://docs.microsoft.com/java/api/com.microsoft.azure.management.mediaservices.v2018_07_01.assetstreaminglocator?view=azure-java-stable)|
|Node.js|[listStreamingLocators](https://docs.microsoft.com/javascript/api/azure-arm-mediaservices/assets?view=azure-node-latest#liststreaminglocators-string--string--string--object-)|

## <a name="also-see"></a>Se även

* [Tillgångar](assets-concept.md)
* [Principer för direktuppspelning](streaming-policy-concept.md)
* [Principer för innehållsnycklar](content-key-policy-concept.md)

## <a name="next-steps"></a>Nästa steg

[Självstudie: Ladda upp, koda och strömma videor med hjälp av .NET](stream-files-tutorial-with-api.md)
